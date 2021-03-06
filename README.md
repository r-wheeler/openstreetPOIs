![openstreetPOIs screenshot](https://raw.github.com/aaronlidman/openstreetPOIs/master/openstreetPOIs.png)

openstreetPOIs extracts and builds points of interest from OpenStreetMap data. It extracts nodes with tags but also builds areas (buildings, lakes, parks, etc..) and uses the centroid as the point for that feature. The aim is to have a scalable way of parsing any osm file for points, quickly and without the need of setting up a database like PostGIS.

settings.py contains a list of all the features to be extracted. It's made up of a sensible default of what I consider useful features but it is also easily edittable to your liking.

example-dc.geojson is an example of the default output from a 18mb .osm file of an area of Washington DC.

### Sample Data
These were all created from [Geofabrik extracts](http://download.geofabrik.de/) on July 12, 2013 with a slightly older version of openstreetPOIs.
- [California](https://s3.amazonaws.com/aaronlidman-mapping/california.geojson) - 252 mb
- [Western United States](https://s3.amazonaws.com/aaronlidman-mapping/west2.geojson) - 372 mb
- [North America](https://s3.amazonaws.com/aaronlidman-mapping/na.geojson) - 2072 mb

###Installation
1. `git clone https://github.com/aaronlidman/openstreetPOIs.git && cd openstreetPOIs`
2. Mac or Ubuntu? (homebrew or apt required)
	- Mac
		- `brew update`
		- `brew install python geos leveldb protobuf`
	- Ubuntu
		- 12.10 minimum required, plyvel has a problem with 12.04. [details](https://github.com/wbolster/plyvel/commit/16880a9d2143d53662d0d57ca2b3c7dcc6d6334f)
		- `apt-get update`
		- `apt-get -y install python-dev python-pip build-essential libprotobuf-dev protobuf-compiler libleveldb-dev libgeos-dev`
3. Optional. Setup your python virtualenv.
4. `pip install --requirement requirements.txt`

### Dependencies
- [LevelDB](https://code.google.com/p/leveldb/)
- [Imposm Parser](http://imposm.org/docs/imposm.parser/latest/)
- [Shapely](http://toblerity.github.io/shapely/)
- [UltraJSON](https://github.com/esnme/ultrajson)
- [Plyvel](https://github.com/wbolster/plyvel)

### Usage
Get your desired OSM data ([good starting point](http://wiki.openstreetmap.org/wiki/Planet.osm#Downloading)) in .pbf, .osm.bz2 or just .osm. With all the dependencies installed and python setup, run: `python osmpois.py YOUR_OSM_FILE.EXT` and add options (below).

### Options
- `-h, --help` - show the help message and exit
- `--output OUT` - Destination filename to create (no extension, .extension gets added on) (default: output)
- `--overwrite` - Overwrite any conflicting files.
- `--require-key` - Only output items that have the 'name' tag defined.
- `--groupsize` - How large of a group to use for coordinate lookup. (default: 20) lower = more RAM, higher = more disk.
- `--precache` - Precache all coordinates. Removes the coordinate lookup process which uses lots of RAM.
- `--max-nodes` - Maximum number of nodes in a way to consider for simplification. Anything over max is skipped. (default: 250) To include everything, no matter how large, set to 2000.

### Tips
Depending on the hardware you use and options you specify, processing time can vary a lot.
- This process relies very much on your hard drive, the faster your hard drive the better. My results are typically twice as fast with an SSD.
- RAM is the first limiting factor while parsing large files. Increase the groupsize option to help mitigate this. The larger the groupsize the less RAM that will be used but this comes at the cost of using your hard drive, which is slower. Anything from one to a couple million might help.
- The output is in the default OSM data projection, EPSG:4326 aka WGS84.
    - I might add reprojections later, through pyproj.
- For quickest results use PBF files.

### BSD License
