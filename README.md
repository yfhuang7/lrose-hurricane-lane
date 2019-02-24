# Using LROSE to study the precip from Hurricane Lane, Hawaii, August 2018

This git repository contains the parameters and scripts needed for running a QPE analysis for Lane.

Things will work best if you can set up the directory structures are indicated.

## Checking out this repository

Check out the repository into $HOME/git.

```
  cd
  mkdir -p git
  cd git
  git clone https://github.com/ncar/lrose-hurricane-lane
```

## Setting up $DATA_DIR

You need to designate a top-level directory, under which the data will reside.

In your shell environment, you need to set the environment variable DATA_DIR to point to that directory.

For example, in bash:

```
  export DATA_DIR=$HOME/data
```

and in csh or tcsh:

```
  set DATA_DIR $HOME/data
```

You do not need to use $HOME/data - that is just an example.

You could, for example, use an external USB drive, for example:

```
  /run/media/mdtest/LANE
```

or on a mac:

```
  /Volumes/LANE
```

## Data directory structure

Set up your data directories as follows:

### Terrain data tiles from SRTM:

```
  $DATA_DIR/hawaii/srtm30
```

### NEXRAD data from NCDC

Download the data you want from NCDC, or Amazon.

Store in:

```
  $DATA_DIR/hawaii/raw/nexrad/PHKI
  $DATA_DIR/hawaii/raw/nexrad/PHKM
  $DATA_DIR/hawaii/raw/nexrad/PHMO
  $DATA_DIR/hawaii/raw/nexrad/PHWA

```

### Sounding data from U-Wyoming

Download the sounding data for your cases from the University of Wyoming web site:

```
  http://weather.uwyo.edu/upperair/sounding.html
```

You will need the PHLI and PHTO soundings, in 'RAW' format. This is the standard TTAA, TTBB, TTCC etc format.

Select the date range you need, and then name the files with the date range.

For example:

```
 $DATA_DIR/hawaii/raw/sounding/nws/20180820_20180830.phli.ttaa
 $DATA_DIR/hawaii/raw/sounding/nws/20180820_20180830.phto.ttaa

```

If your data spans more than one month, create a file for each month separately. This is required because the TTAA messages do not contain the year and month, so we have to determine that from the file name.

### Directories that will be created by the QPE analysis steps

As you run the analysis, the following directory structure will be created:

#### Beam block files

```
  $DATA_DIR/hawaii/BeamBlock/PHKI
  $DATA_DIR/hawaii/BeamBlock/PHKM
  $DATA_DIR/hawaii/BeamBlock/PHMO
  $DATA_DIR/hawaii/BeamBlock/PHWA

```

#### Sounding data in SPDB format:

```
  $DATA_DIR/hawaii/spdb/sounding
```

#### CfRadial polar-coordinate files - radar moments

```
  $DATA_DIR/hawaii/cfradial/phki/moments/
  $DATA_DIR/hawaii/cfradial/phkm/moments/
  $DATA_DIR/hawaii/cfradial/phmo/moments/
  $DATA_DIR/hawaii/cfradial/phwa/moments/
```

#### CfRadial polar-coordinate files - precip rate in 3D

```
  $DATA_DIR/hawaii/cfradial/phki/rate/
  $DATA_DIR/hawaii/cfradial/phkm/rate/
  $DATA_DIR/hawaii/cfradial/phmo/rate/
  $DATA_DIR/hawaii/cfradial/phwa/rate/
```

#### CfRadial polar-coordinate files - QPE rate at the surface

```
  $DATA_DIR/hawaii/cfradial/phki/qpe/
  $DATA_DIR/hawaii/cfradial/phkm/qpe/
  $DATA_DIR/hawaii/cfradial/phmo/qpe/
  $DATA_DIR/hawaii/cfradial/phwa/qpe/
```

#### CF-NetCDF Cartesian files - QPE rate at the surface

```
  $DATA_DIR/hawaii/mdv/phki/qpe
  $DATA_DIR/hawaii/mdv/phkm/qpe
  $DATA_DIR/hawaii/mdv/phmo/qpe
  $DATA_DIR/hawaii/mdv/phwa/qpe
```

#### CF-NetCDF Cartesian files - merged QPE rate for Hawaii domain

```
   $DATA_DIR/hawaii/mdv/qpe_merge/
```

#### CF-NetCDF Cartesian files - QPE accumulation depth

```
   $DATA_DIR/hawaii/mdv/qpe_accum_1hr
   $DATA_DIR/hawaii/mdv/qpe_accum_2hr
   $DATA_DIR/hawaii/mdv/qpe_accum_3hr
   $DATA_DIR/hawaii/mdv/qpe_accum_daily
```




