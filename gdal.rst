Working on output data with GDAL & SQL
=====================================
Let's say you want to create a graph of areal percentage of a country under drought since 1981, similar to that found .. _here: http://i.imgur.com/Uy8uxQS.png .
(Wardlow, B., et al, 2012).

A graph by decade can be done manually in ArcGIS. By year can be done with a batch, perhaps. By month can be done with model 
builder or ArcPy (but it will take a lot of time). By week will simply take too long. The immensity of data produced by RHEAS
requires GDAL.

Common SQL commands
----------------------------

GDAL looks to your 'public.raster_columns' view for constraints. Without adding constraints to your table, nothing will work. 
Constraints are added in psql.

To add constraints to the table 'zambiadrought.spi3201603' type:

```
SELECT AddRasterConstraints('zambia'::name, 'spi3201603'::name, 'rast'::name);
```

To create a new table for a certain date & layer of soil, type:

```
CREATE TABLE sm20150101 AS (SELECT rid, rast from zambiadrought.soil_moist WHERE fdate = date'2015-1-1' AND layer = 1)
```

To combine multiple rasters from a single table to one new raster with multibands:

```
CREATE TABLE spi3multiband AS (SELECT ST_AddBand(NULL, array_agg(rast), 1) As rast 
FROM zambiadrought.sri3);
```

Common GDAL commands
---------------

To view more information on 'zambiadrought.spi3201603', type:

```
gdalinfo  "PG:host=localhost port=5432 dbname='rheas' schema='zambiadrought' table=spi3201603"
```

To export the table to a Geotiff, type:

```
gdal_translate -of Gtiff "PG:host=localhost port=5432 dbname='rheas' schema='zambiadrought' table='spi3201603'" {PATH}/spi3201603.tif
```

To clip the Geotiff to a shapefile:

```
gdalwarp -dstnodata -9999 -cutline LusakaProvince.shp spi3201603.tif spi3201603_Lusaka.tif
```
