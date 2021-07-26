# Introduction
The data is provided in two formats: total PM2.5 source impacts and coal unit-specific source impacts.

The data is stored on an Open Science Framework project site: [https://osf.io/8gdau/]

## Data storage format
The data is stored as compressed `.fst` files (fst is an (R package)[https://www.fstpackage.org/]).

The files are stored on an 

## Reproducilibity using renv
To ensure packages and formats are consistent, we have used the renv package[https://rstudio.github.io/renv/articles/renv.html]. To use the same packages used to create these data, install the renv package (`install.packages('renv')`), download the `renv.lock` file to your working directory, and run `renv::restore()`. 

# Download the files
To retrieve the files,  download the files directly from the (OSF site)[https://osf.io/8gdau/]. Define the `source_impacts_dir` as the location to which you saved the files.
```
source_impacts_dir <- 'Your/Source/Impacts/Directory

```

# Reading in the files
To read in the files, execute the following. 

## Total coal PM2.5 source impacts
The following code will read in total source impacts, i.e., the summed source impacts from all coal units.
```
#coordinate reference system projection string for spatial data
p4s <- "+proj=lcc +lat_1=33 +lat_2=45 +lat_0=40 +lon_0=-97 +a=6370000 +b=6370000"

# get the names of the gridded HyADS output files
grid.files.yr <- list.files( source_impacts_dir,
                             pattern = 'grids_pm25_total_\\d{4}\\.fst',
                             full.names = TRUE)

# read select files
grid.dat <- lapply( grid.files.yr,
                    function( f){
                      year.f <- gsub( '^.*_|\\.fst', '', f)
                      
                      in.f <- read.fst( f, as.data.table = T)
                      setnames( in.f, 'V3', 'coal_pm25')
                      in.f[, year := year.f]
                    }) %>% rbindlist

# summarize the data
summary( grid.dat)

```


## Unit coal PM2.5 source impacts
These files are substantially larger, as each coal unit is assigned coal impacts in each year.
```
# get the names of the gridded HyADS output files
grid.files.unit.yr <- list.files( source_impacts_dir,
                                  pattern = 'grids_pm25_byunit_\\d{4}\\.fst',
                                  full.names = TRUE)

# read select files
grid.unit.dat <- lapply( grid.files.unit.yr,
                         function( f){
                           print( f)
                           year.f <- gsub( '^.*_|\\.fst', '', f) %>%
                             as( 'integer')
                           
                           in.f <- read.fst( f, as.data.table = T)
                           in.f[, year := year.f]
                           
                           in.f[ is.na( in.f)] <- 0
                           return (in.f)
                         }) %>% rbindlist( fill = T)






```












