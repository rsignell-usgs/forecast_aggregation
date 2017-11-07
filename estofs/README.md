Update: These scripts no longer work because they rely on netcdf files being distributed for ESTOFS on the NCEP ftp site.  It appears that at some point (perhaps April 25, 2017), ESTOFS began distributing grib2 files instead of netcdf on their ftp site: ftp://ftpprd.ncep.noaa.gov/pub/data/nccf/com/estofs/prod/
Also there is now an ESTOFS pacific, which did not exist when these scripts were written, so if the netcdf files were available these scripts would have to be modified to access slightly different file names (e.g. `estofs_atl` or `estofs_pac` instead of just `estofs`).


ESTOFS is an unstructured grid (UGRID) model, and since FMRC aggregation currently requires that the featureType be `grid`, needs another approach to create a "best time series" dataset.

The approach we take here is to use a `joinExisting` aggregation to join a bunch a bunch of pieces clipped out of older forecasts with the latest forecast.

Since ESTOFS forecasts are every 6 hours a day, we clip the 1-6 hour data from each of the old forecasts and put them in the aggregation directory using a datestamp in the filename like: `estofs.atl.2015081100.fields.cwl.nc`, with the latest forecast getting a name like: `estofs.atl.9999060606.fields.cwl.nc`. 

There are 4 different bash scripts that get run at a specific time each day.   Each script does basically this:

```bash
# RPS clipping for aggregation
# copy file clipped at last cycle from stage directory to agg directory
mv $STAGE_DIR/*.nc $AGG_DIR
# remove full forecast from agg directory
/bin/rm $AGG_DIR/*9999*.nc
# create new clipped file, but save to stage directory
ncks -O -F -d time,1,$NTIME $FILE1 $STAGE_DIR/$FILE_AGG
# place new full forcast in agg directory
mv $FILE1 $AGG_DIR/$FILE_LATEST
```

My file structure on gam looks like this:
```
rsignell@gam:/usgs/data2/rsignell/estofs$ ls
agg_dir      estofs_00z.sh      estofs.ncml  odaasenvironmentvariables.sh
catalog.xml  estofs_06z.sh      estofs.sh    stage_dir
clip.sh      estofs_12z.sh      foo.sh       threddsConfig.xml
cronthredds  estofs_18z.sh      ftp          thredds-daily-check.sh
data         ESTOFSCatalog.xml  $HOMERPS
```

and my crontab looks like this:
```
20 01 * * * /usgs/data2/rsignell/estofs/estofs_00z.sh
20 07 * * * /usgs/data2/rsignell/estofs/estofs_06z.sh
20 13 * * * /usgs/data2/rsignell/estofs/estofs_12z.sh
20 19 * * * /usgs/data2/rsignell/estofs/estofs_18z.sh
```
