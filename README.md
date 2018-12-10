# custom gdal2tiles.py

original(supported multi process)  
https://svn.osgeo.org/gdal/trunk/gdal/swig/python/scripts/gdal2tiles.py  

## customized point
- support "-x" option for output XYZ tile  
- no create blank file (RGB 0,0,0)  
- support leaflet basemap with gsi tiles
- support MaxNativeZoom on leaflet

# usage
set GDAL_PAM_ENABLED=NO #don't output aux.xml for png  
set GDAL_FILENAME_IS_UTF8=NO

#if black is nodata and don't have alpha band  
gdalbuildvrt --config GDAL_CACHEMAX 10240 merge.vrt *.tif    
python gdal2tiles.py --processes 5 -s epsg:6678 -z 10-20 -a "0,0,0" -x merge.vrt output  

#if white is nodata and don't have alpha band  
gdalbuildvrt --config GDAL_CACHEMAX 10240 merge.vrt *.tif  
gdalwarp -of VRT -srcnodata "255 255 255" -dstnodata "0 0 0" merge.vrt merge2.vrt  
python gdal2tiles.py --processes 5 -s epsg:6678 -z 10-20 -a "0,0,0" -x merge2.vrt output  

#if white is nodata and already have alpha band  
gdalbuildvrt --config GDAL_CACHEMAX 10240 merge.vrt *.tif  
python gdal2tiles.py --processes 5 -s epsg:6678 -z 10-20 -x merge.vrt output  

## remove empty folder after gdal2tiles
at windows console  

bash  
find . -type d -empty -delete  

## upload to s3
bash
pip install aws  
aws configure --profile MY_PROFILE  
	AWS Access Key ID [None]: MY_KEY  
	AWS Secret Access Key [None]: MY_SECRET_KEY  
	Default region name [None]: ap-northeast-1  
	Default output format [None]: json  

aws configure set default.s3.max_concurrent_requests 100  
aws configure set default.s3.max_queue_size 10000  

aws s3 sync --profile=MY_PROFILE --storage-class "REDUCED_REDUNDANCY" --content-type "image/png" kitakami2016 s3://MY_BUCKET/tiles/kitakami2016/ --quiet  

## make wmts.xml
copy from here  
https://map.ecoris.info/tiles/WMTSCapabilities.xml  

edit bounding box  

gdalt2tiles generate "tilemapresource.xml"
lat lon info is in it.

epsg:3857 boundingbox calculation  
gdaltransform -s_srs epsg:4326 -t_srs epsg:3857

 

