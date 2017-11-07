Here's my plan to help aggregate the files at:
ftp://ftp.ncep.noaa.gov/pub/data/nccf/com/nos/prod/cbofs.20171106/

* Instead of trying to aggregate a ton of hourly files, each day, aggregate into a daily file
* Each week, aggregate daily files into weekly file
* Every month, aggregate weekly files into a monthly file
* Every year, aggregate monthly files into a yearly file

We should use chunking for these files following:

Then we aggregate a maximim of less than 100 files: 10 yearly files, 11 monthly files, 3 weekly files, 6 daily files + 48 forecast hourly files 

This way we that way you avoid the 1000s of netcdf files problems.

We can do this work using simply cron, bash and NCO 

The whole goal is to make aggregations like 
https://opendap.co-ops.nos.noaa.gov/thredds/cbofs_agg.html
work better
