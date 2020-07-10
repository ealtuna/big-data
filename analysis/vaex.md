Using Topcat and Vaex to pre-process the CSV dataset.


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
