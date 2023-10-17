---
layout: post
title:  "PostgreSQL GIST performance with NREL alternate fuel data for nearest stations"
category: software
---

I was curious to set up an API similar to the [NREL nearest alternate fuel stations API](https://developer.nrel.gov/docs/transportation/alt-fuel-stations-v1/nearest/). For a table of ~58k stations, I compared performance time for the nearest 20 stations for unindexed, GIST, and SP-GIST indexes. I didn't try using PostGIS, which my understanding you only need if you want to account for the curvature of the earth, etc, in your distance measurements.

Results: 

| Index     | average execution time (milliseconds) |
|:---------:|:-------------------------------------:|
|  none     |    32 ms                              |
|  GIST     |    0.18 ms                            |
|  SP-GIST  |    0.09 ms                            |

The Postgres GIST indexing feature is really good at this! I was inspired by 2 crunchydata blog posts:
* [(The Many) Spatial Indexes of PostGIS](https://www.crunchydata.com/blog/the-many-spatial-indexes-of-postgis)
* [Won’t You Be My Neighbor? Quickly Finding Who is Nearby](https://www.crunchydata.com/blog/wont-you-be-my-neighbor-quickly-finding-who-is-nearby)

I got all the locations from [NREL all alternate fuel stations API](https://developer.nrel.gov/docs/transportation/alt-fuel-stations-v1/all/)

It took a couple of hours to wrangle that data into a format that was suitable for a postgres COPY FROM import. (short story: watch out for tabs, newlines, and random backslashes).

My stations table looked like this. Used a simple point for long/lat:

    CREATE TABLE stations (
        coordinates point NOT NULL,
        name text NOT NULL,
        address text NOT NULL,
        city_state_zip text NOT NULL,
        access_days_time text,
        ev_network text,
        ev_network_web text,
        dc_fast_chargers smallint,
        ev_level_1_chargers smallint,
        ev_level_2_chargers smallint,
        ev_connector_types text
    );

Imported stations to the database like this:

    COPY stations FROM '/tmp/stations.csv'; 

Query looks like this: (the long/lat location is the Space Needle)

    EXPLAIN ANALYZE SELECT *
    FROM stations
    ORDER BY POINT(-122.3493,47.6205) <-> coordinates
    LIMIT 20;

# Unindexed performance

                                                            QUERY PLAN
    ----------------------------------------------------------------------------------------------------------------------------
    Limit  (cost=5129.29..5129.34 rows=20 width=163) (actual time=30.057..30.060 rows=20 loops=1)
    ->  Sort  (cost=5129.29..5274.60 rows=58126 width=163) (actual time=30.055..30.057 rows=20 loops=1)
            Sort Key: (('(-122.3493,47.6205)'::point <-> coordinates))
            Sort Method: top-N heapsort  Memory: 34kB
            ->  Seq Scan on stations  (cost=0.00..3582.58 rows=58126 width=163) (actual time=0.008..12.517 rows=58126 loops=1)
    Planning Time: 0.069 ms
    Execution Time: 30.084 ms
    (7 rows)

I ran this a few times and usually around ~32 ms.

# GIST index

    CREATE INDEX coordinates_idx on stations USING gist(coordinates);
    VACUUM ANALYZE stations;
    <query>

                                                                    QUERY PLAN
    -------------------------------------------------------------------------------------------------------------------------------------------
    Limit  (cost=0.28..5.10 rows=20 width=163) (actual time=0.394..0.415 rows=20 loops=1)
    ->  Index Scan using coordinates_idx on stations  (cost=0.28..14006.80 rows=58126 width=163) (actual time=0.393..0.412 rows=20 loops=1)
            Order By: (coordinates <-> '(-122.3493,47.6205)'::point)
    Planning Time: 0.700 ms
    Execution Time: 0.644 ms
    (5 rows)

I ran this a few times and usually around ~0.18 ms.

# SP-GIST index

    DROP INDEX coordinates_idx;
    CREATE INDEX coordinates_idx on stations USING spgist(coordinates);
    VACUUM ANALYZE stations;
    <query>

                                                                    QUERY PLAN
    -------------------------------------------------------------------------------------------------------------------------------------------
    Limit  (cost=0.28..5.07 rows=20 width=163) (actual time=0.141..0.162 rows=20 loops=1)
    ->  Index Scan using coordinates_idx on stations  (cost=0.28..13938.80 rows=58126 width=163) (actual time=0.139..0.159 rows=20 loops=1)
            Order By: (coordinates <-> '(-122.3493,47.6205)'::point)
    Planning Time: 0.255 ms
    Execution Time: 0.246 ms
    (5 rows)

I ran this a few times and usually around ~0.09 ms.
