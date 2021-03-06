The RHEAS database
=================================

The back-end of RHEAS is a `PostGIS <http://postgis.net>`_ database (a spatially-enabled `PostgreSQL <http://www.postgresql.org>`_ database) that stores the necessary data to run RHEAS including model parameters, meteorological data (both forecasts and nowcasts), and remote sensing as well as in-situ observations.

The VIC and DSSAT model parameters are contained under the ``vic`` and ``dssat`` schemas. Depending on the requested spatial resolution for the models, appropriate model parameters and corresponding files are chosen. Tables in the ``vic`` schema include ``soils`` which contains the soil parameters, and ``input`` which contains the necessary files to run the VIC model. Each record in the ``input`` table should contain the following columns:

* **resolution**: model spatial resolution   
* **snowbandfile**: VIC elevation band file
* **vegparam**: VIC vegetation parameter file
* **veglib**: VIC vegetation library file
* **soilfile**: VIC soil parameter file
* **rootzones**: number of root zones
* **basefile**: DSSAT input file template

The requirements for running DSSAT also include the type of cultivar(s), planting start dates and soil properties which have been ingested in the database from various sources. Soil properties and cultivar information are stored as vector tables (``soils`` and ``cultivar`` respectively), while planting start dates are stored as raster maps with the type of crop as an additional column in the ``crops`` table.

Multiple remote sensing datasets can be ingested into the database and are available either to be used as input (i.e. precipitation, temperature, wind speed) or assimilated (i.e. soil moisture, water storage, LAI, evapotranspiration). The following table summarizes the available datasets, their characteristics and the database schema/table they will be installed in.

.. _database:

+------------------+---------+----------+-----------------+-----------------+----------------+----------------+------+
|Variable          |Dataset  |Time      |Temporal         |Spatial          |Spatial Coverage|Table           |Mode  |
|                  |         |Coverage  |Resolution       |Resolution       |                |                |      |
+==================+=========+==========+=================+=================+================+================+======+
|Precipitation     |CHIRPS   |  1981-   |      Daily      |      5km        |     Africa     |precip.chirps   |  IN  |
+------------------+---------+----------+-----------------+-----------------+----------------+----------------+------+
|Precipitation     |TRMM     |  1998-   |      Daily      |      ~25km      |     Global     |precip.trmm     |  IN  |
+------------------+---------+----------+-----------------+-----------------+----------------+----------------+------+
|Precipitation     |RFE2     |  2001-   |      Daily      |      ~1km       |     Africa     |precip.rfe2     |  IN  |
+------------------+---------+----------+-----------------+-----------------+----------------+----------------+------+
|Precipitation     |CMORPH   |  1998-   |      Daily      |      ~25km      |     Global     |precip.cmorph   |  IN  |
+------------------+---------+----------+-----------------+-----------------+----------------+----------------+------+
|Meteorology       |NCEP     |1981-     |      Daily      |      ~180km     |     Global     |\*.ncep         |  IN  |
+------------------+---------+----------+-----------------+-----------------+----------------+----------------+------+
+------------------+---------+----------+-----------------+-----------------+----------------+----------------+------+
|Soil moisture     |AMSR-E   |2002-2011 |      Daily      |      ~25km      |     Global     |soilm.amsre     |  AS  |
+------------------+---------+----------+-----------------+-----------------+----------------+----------------+------+
|Soil moisture     |SMOS     |  2009-   |      Daily      |      ~40km      |     Global     |soilm.smos      |  AS  |
+------------------+---------+----------+-----------------+-----------------+----------------+----------------+------+
|Soil moisture     |SMAP     |  2015-   |    3/1 days     |      3/9km      |     Global     |soilm.smap      |  AS  |
+------------------+---------+----------+-----------------+-----------------+----------------+----------------+------+
|Evapotranspiration|MOD16    |2000-     |     8 days      |       1km       |     Global     |evap.modis      |  AS  |
+------------------+---------+----------+-----------------+-----------------+----------------+----------------+------+
|Water storage     |GRACE    |2002-     |     Monthly     |      ~150km     |     Global     |tws.grace       |  AS  |
|                  |         |          |                 |                 |                |                |      |
+------------------+---------+----------+-----------------+-----------------+----------------+----------------+------+
|Leaf Area Index   |MCD15    |2002-     |     8 days      |       1km       |     Global     |lai.modis       |  AS  |
|                  |         |          |                 |                 |                |                |      |
+------------------+---------+----------+-----------------+-----------------+----------------+----------------+------+
|Meteorology       |IRI      |2000-     |     Monthly     |       ~250km    |     Global     |\*.iri          |  FC  |
|                  |         |          |                 |                 |                |                |      |
+------------------+---------+----------+-----------------+-----------------+----------------+----------------+------+

There are three modes for the datasets: ``IN`` corresponds to datasets being used as inputs to the model, ``AS`` refers to datasets being assimilated, and ``FC`` are datasets that are used to provide the meteorological (i.e. precipitation, temperature, and in some cases wind speed) forecasts.
