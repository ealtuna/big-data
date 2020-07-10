Using Topcat and Vaex to pre-process the CSV dataset (with Vaex you get the chance to load big data files for analysis, and in general you get execution times 10X plus faster than Pandas).


```
#!/bin/bash

#Variables
csv_files='ls /path/to/csv/files/*.csv'
out_file='/path/to/output/folder'
file_locs=$out_file/files.txt
fits_out=$out_file/merged.fits
hdf5_out=$out_file/merged.hdf5

# Create Txt File of Locations
> $file_locs
for f in $csv_files
do
    if [ $f = 'ls' ]; then
        continue
    fi
    echo "$f" >> $file_locs
done

#Create .fits Files and Convert to hdf5
echo 'Compiling Fits Files For Conversion to HDF5'
./topcat_jar/topcat -stilts -Djava.io.tmpdir=/tmp tcat in=@$file_locs ifmt=csv out=$fits_out ofmt=colfits

#Use Vaex to convert .fits to HDF5
echo 'Converting .fits to HDF5'
source  /path/to/venv # if applicable
vaex convert file $fits_out $hdf5_out
rm $fits_out
rm $file_locs
echo 'Process Complete: Deleting .txt file and .fits intermediary file'
echo 'HDF5 saved to: $hdf5_out'
```

Operations over datasets (similar APIs in Pandas and Vaex)

```
import pandas as pd
import vaex

pandas_df = pd.read_csv("/path/to/file")
vaex_df = vaex.open("/path/to/hdf5")

// select in NYC
nyc_lat = [a, b]
nyc_long = [c, d]
vaes_df[(vaex_df.latitude > nyc_lat[0]) && /*other conditions*/]
```

## References

https://towardsdatascience.com/vaex-out-of-core-dataframes-for-python-and-fast-visualization-12c102db044a
