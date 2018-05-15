# custom gdal2tiles.py

original(supported multi process)  
https://svn.osgeo.org/gdal/trunk/gdal/swig/python/scripts/gdal2tiles.py  

## customized point
support "-x" option for output XYZ tile  
no create blank file (RGB 0,0,0)  

# usage
gdalbuildvrt --config GDAL_CACHEMAX 10240 merge.vrt *.tif  
python gdal2tiles.py --processes 5 -s epsg:6678 -z 10-20 -a "0,0,0" -x merge.vrt output  

## remove empty folder after gdal2tiles
at windows console  

bash  
find . -type d -empty -delete  
