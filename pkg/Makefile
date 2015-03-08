# Copyright (c) 2013-2015 PipelineDB
VERSION = 0.1a

TMP_BUILD = ./tmp
GEOS = geos-3.4.2
GDAL = gdal-1.11.2
PROJ = proj-4.8.0
PREFIX = $(shell pwd)/staging
TOP_BUILDDIR = ../

all: geos gdal proj pipeline gis
	echo "PipelineDB binaries successfully built and ready for packaging."

geos:
	mkdir -p $(TMP_BUILD)
	wget -P $(TMP_BUILD) http://download.osgeo.org/geos/$(GEOS).tar.bz2
	(cd $(TMP_BUILD) && tar -xf $(GEOS).tar.bz2)
	(cd $(TMP_BUILD)/$(GEOS) && ./configure --prefix=$(PREFIX))
	(cd $(TMP_BUILD)/$(GEOS) && make && make install)

gdal:
	mkdir -p $(TMP_BUILD)
	wget -P $(TMP_BUILD) ftp://ftp.remotesensing.org/gdal/1.11.2/$(GDAL).tar.gz
	(cd $(TMP_BUILD) && tar -xf $(GDAL).tar.gz)
	(cd $(TMP_BUILD)/$(GDAL) && ./configure --prefix=$(PREFIX) \
            --with-threads \
            --with-ogr \
            --with-geos \
            --without-libtool \
            --with-libz=internal \
            --with-libtiff=internal \
            --with-geotiff=internal \
            --without-gif \
            --without-pg \
            --without-grass \
            --without-libgrass \
            --without-cfitsio \
            --without-pcraster \
            --without-netcdf \
            --without-png \
            --without-jpeg \
            --without-gif \
            --without-ogdi \
            --without-fme \
            --without-hdf4 \
            --without-hdf5 \
            --without-jasper \
            --without-ecw \
            --without-kakadu \
            --without-mrsid \
            --without-jp2mrsid \
            --without-bsb \
            --without-grib \
            --without-mysql \
            --without-ingres \
            --without-xerces \
            --without-expat \
            --without-odbc \
            --without-curl \
            --without-sqlite3 \
            --without-dwgdirect \
            --without-panorama \
            --without-idb \
            --without-sde \
            --without-perl \
            --without-php \
            --without-ruby \
            --without-python \
            --without-ogpython \
            --with-hide-internal-symbols)
	(cd $(TMP_BUILD)/$(GDAL) && make && make install)

proj:
	mkdir -p $(TMP_BUILD)
	wget -P $(TMP_BUILD) http://download.osgeo.org/proj/$(PROJ).tar.gz
	(cd $(TMP_BUILD) && tar -xf $(PROJ).tar.gz)
	(cd $(TMP_BUILD)/$(PROJ) && ./configure --prefix=$(PREFIX))
	(cd $(TMP_BUILD)/$(PROJ) && make && make install)

pipeline:
	(cd $(TOP_BUILDDIR) && ./configure --prefix=$(PREFIX) CFLAGS="$(CFLAGS)")
	(cd $(TOP_BUILDDIR) && make && make install)

gis:
	(cd $(TOP_BUILDDIR)/src/gis && ./autogen.sh)
	(cd $(TOP_BUILDDIR)/src/gis && ./configure \
		--with-pgconfig=$(PREFIX)/bin/pipeline-config \
		--with-geosconfig=$(PREFIX)/bin/geos-config \
		--with-gdalconfig=$(PREFIX)/bin/gdal-config \
		--with-projdir=$(PREFIX))
	(cd $(TOP_BUILDDIR)/src/gis && make && make install)	

deb: all
	cp postinst $(PREFIX)
	(cd $(PREFIX) && fpm -s dir -t deb -n pipelinedb -v $(VERSION) --prefix=/opt/pipelinedb --after-install postinst .)
	cp $(PREFIX)/*.deb .
	
rpm: all
	cp post-install $(PREFIX)
	(cd $(PREFIX) && fpm -s dir -t rpm -n pipelinedb -v $(VERSION) --epoch 0 --prefix=/opt/pipelinedb --after-install post-install .)
	cp $(PREFIX)/*.rpm .

clean:
	rm -rf $(GEOS)* $(GDAL)* $(PROJ)* $(TMP_BUILD) $(PREFIX)
	rm -rf *.deb *.rpm