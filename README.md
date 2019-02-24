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
  setenv DATA_DIR $HOME/data
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
  $DATA_DIR/hawaii/cfradial/phki/moments
  $DATA_DIR/hawaii/cfradial/phkm/moments
  $DATA_DIR/hawaii/cfradial/phmo/moments
  $DATA_DIR/hawaii/cfradial/phwa/moments
```

#### CfRadial polar-coordinate files - precip rate in 3D

```
  $DATA_DIR/hawaii/cfradial/phki/rate
  $DATA_DIR/hawaii/cfradial/phkm/rate
  $DATA_DIR/hawaii/cfradial/phmo/rate
  $DATA_DIR/hawaii/cfradial/phwa/rate
```

#### CfRadial polar-coordinate files - QPE rate at the surface

```
  $DATA_DIR/hawaii/cfradial/phki/qpe
  $DATA_DIR/hawaii/cfradial/phkm/qpe
  $DATA_DIR/hawaii/cfradial/phmo/qpe
  $DATA_DIR/hawaii/cfradial/phwa/qpe
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

#### CF-NetCDF Cartesian files - QPE accumulation depth, merged domain

```
   $DATA_DIR/hawaii/mdv/qpe_accum_1hr
   $DATA_DIR/hawaii/mdv/qpe_accum_2hr
   $DATA_DIR/hawaii/mdv/qpe_accum_3hr
   $DATA_DIR/hawaii/mdv/qpe_accum_daily
```

## Computing beam blockage

Download the SRTM 1degx1deg tiles, and write them to:

```
  $DATA_DIR/hawaii/srtm30
```

Then:

```
  cd ~/git/lrose-hurricane-lane/projDir/beamBlock/scripts/
```

and run:

```
  run_RadxBeamBlock.phki
  run_RadxBeamBlock.phkm
  run_RadxBeamBlock.phmo
  run_RadxBeamBlock.phwa
```

This will create the CfRadial beam blockage files in:

```
  $DATA_DIR/hawaii/BeamBlock/PHKI
  $DATA_DIR/hawaii/BeamBlock/PHKM
  $DATA_DIR/hawaii/BeamBlock/PHMO
  $DATA_DIR/hawaii/BeamBlock/PHWA
```

## Converting the NEXRAD RAW files into CfRadial files

Your NEXRAD files should be in:

```
  $DATA_DIR/hawaii/raw/nexrad/PHKI
  $DATA_DIR/hawaii/raw/nexrad/PHKM
  $DATA_DIR/hawaii/raw/nexrad/PHMO
  $DATA_DIR/hawaii/raw/nexrad/PHWA
```

To convert them into CfRadial:

```
  cd ~/git/lrose-hurricane-lane/projDir/ingest/scripts
  run_RadxConvert.phki
  run_RadxConvert.phkm
  run_RadxConvert.phmo
  run_RadxConvert.phwa
```

You should edit these scripts for the appropriate time range.

Alternatively you can specify the NEXRAD files you want to convert on the command line.

For example:

```
  cd ~/git/lrose-hurricane-lane/projDir/ingest/params
  export radar_name=phki
  export RADAR_NAME=PHKI
  RadxConvert -params RadxConvert.hawaii -f PHKI-raw-files
```

These steps should store the CfRadial nexrad files in:

```
  $DATA_DIR/hawaii/cfradial/phki/moments
  $DATA_DIR/hawaii/cfradial/phkm/moments
  $DATA_DIR/hawaii/cfradial/phmo/moments
  $DATA_DIR/hawaii/cfradial/phwa/moments
```

## Convert sounding data to SPDB format

For example:

```
  cd ~/git/lrose-hurricane-lane/projDir/ingest/params
  NWSsoundingIngest -params NWSsoundingIngest.hawaii -debug -f $DATA_DIR/hawaii/raw/sounding/nws/20180820_20180830.phli.ttaa
```

That should store the soundings in:

```
  $DATA_DIR/hawaii/spdb/sounding
```

## Compute precip rate

You run the RadxRate application to compute precip rate in 3D CfRadial files.

This also computes KDP and PID. PID requires the temperature profile from the soundings.

```
  cd ~/git/lrose-hurricane-lane/projDir/qpe/scripts/
  run_RadxRate.phki.lane
  run_RadxRate.phkm.lane
  run_RadxRate.phmo.lane
  run_RadxRate.phwa.lane
```

You will need to edit these scripts to set the appropriate time period.

These scripts write their output to log files.
You can view the progress by tailing the log files.
For example:

```
  tail -f /tmp/RadxRate.hawaii.phki.log
```

Alternatively, you can run these on the command line. For example for PHKI:

```
  cd ~/git/lrose-hurricane-lane/projDir/qpe/params/
  export radar_name=phki
  export RADAR_NAME=PHKI
  export SOUNDING_NAME=PHLI
  RadxRate -params RadxRate.hawaii -start "2018 08 20 00 00 00" -end "2018 08 30 00 00 00" 
```

The QPE rate files will be stored in:

```
  $DATA_DIR/hawaii/cfradial/phki/rate
  $DATA_DIR/hawaii/cfradial/phkm/rate
  $DATA_DIR/hawaii/cfradial/phmo/rate
  $DATA_DIR/hawaii/cfradial/phwa/rate
```

## Compute QPE rate at the surface

You run the RadxQpe application to compute 2D precip rate at the surface.

See [RadxQpe.pdf](./RadxQpe.pdf) for details.

```
  cd ~/git/lrose-hurricane-lane/projDir/qpe/scripts/
  run_RadxQpe.phki.lane
  run_RadxQpe.phkm.lane
  run_RadxQpe.phmo.lane
  run_RadxQpe.phwa.lane
```

You will need to edit these scripts to set the appropriate time period.

These scripts write their output to log files.
You can view the progress by tailing the log files.
For example:

```
  tail -f /tmp/RadxQpe.hawaii.phki.log
```

Alternatively, you can run these on the command line. For example for PHKI:

```
  cd ~/git/lrose-hurricane-lane/projDir/qpe/params/
  export radar_name=phki
  export RADAR_NAME=PHKI
  export SOUNDING_NAME=PHLI
  RadxQpe -params RadxQpe.hawaii -start "2018 08 20 00 00 00" -end "2018 08 30 00 00 00" 
```

The polar-coordinate QPE rate files will be stored in:

```
  $DATA_DIR/hawaii/cfradial/phki/qpe
  $DATA_DIR/hawaii/cfradial/phkm/qpe
  $DATA_DIR/hawaii/cfradial/phmo/qpe
  $DATA_DIR/hawaii/cfradial/phwa/qpe
```

The CF-NetCDF Cartesian QPE files will be stored in:

```
  $DATA_DIR/hawaii/mdv/phki/qpe
  $DATA_DIR/hawaii/mdv/phkm/qpe
  $DATA_DIR/hawaii/mdv/phmo/qpe
  $DATA_DIR/hawaii/mdv/phwa/qpe
```

## Merging the individual-radar Cartesian QPE files into a merged mosaic

You should use the MdvMerge2 application to merge the QPE rates into a mosaic, before computing the accumulation from the rates.

For overlapping areas, the mosaic contains the maximum value from the individual radars at each grid point.

To run the merge:

```
  cd ~/git/lrose-hurricane-lane/projDir/qpe/scripts
  run_MdvMerge2.qpe
```

As before, edit this script to set the time range appropriately.

Or run it on the command line:

```
  cd ~/git/lrose-hurricane-lane/projDir/qpe/params
  MdvMerge2 -params MdvMerge2.qpe -debug -start "2018 08 20 00 00 00" 
```

The merged Cartesian files will be written to:

```
  $DATA_DIR/hawaii/mdv/qpe_merge
```

## Compute Precipitation Accumulation over time

The RateAccum application integrates the precip rate over time, to give estimated precitation depth.

The configuration files are set up to compute running accumulation over 1, 2 and 3 hour periods, as well as a 24-hour accumulation at the end of each day.

You can edit the parameter files to modify this behavior as needed.

To run the accumulation step:

```
  cd ~/git/lrose-hurricane-lane/projDir/qpe/scripts/
  run_RateAccum.daily
  run_RateAccum.running
```

Or alternatively on the command line:

```
  cd ~/git/lrose-hurricane-lane/projDir/qpe/params
  RateAccum -params RateAccum.daily -debug -start "2018 08 20 00 00 00" -end "2018 08 30 00 00 00"
  RateAccum -params RateAccum.running -debug -start "2018 08 20 00 00 00" -end "2018 08 30 00 00 00"
```

The accumulation files will be stored in:

```
   $DATA_DIR/hawaii/mdv/qpe_accum_1hr
   $DATA_DIR/hawaii/mdv/qpe_accum_2hr
   $DATA_DIR/hawaii/mdv/qpe_accum_3hr
   $DATA_DIR/hawaii/mdv/qpe_accum_daily
```


