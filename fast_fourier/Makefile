

# for this makefile to work, you'll need these:
#
# sudo apt-get install libboost-all-dev
# sudo apt-get install libcgal-dev
#

# Add .d to Make's recognized suffixes.
SUFFIXES += .d

CXX = g++


CFLAGS = -O3 -I. -std=c++14

SNDFILE_PATH=libsndfile-1.0.28/install/lib/pkgconfig
CFLAGS += $(shell export PKG_CONFIG_PATH=$(SNDFILE_PATH) && pkg-config --cflags sndfile)
LFLAGS =  $(shell PKG_CONFIG_PATH=$(SNDFILE_PATH) && pkg-config --libs sndfile)

FFTW_PATH=fftw-3.3.6-pl2/install/lib/pkgconfig
CFLAGS += $(shell export PKG_CONFIG_PATH=$(FFTW_PATH) && pkg-config --cflags fftw3)
LFLAGS += $(shell PKG_CONFIG_PATH=$(FFTW_PATH)    && pkg-config --libs fftw3)


TARGETS = sine_test

all: $(TARGETS)

libsndfile-1.0.28/install/lib/libsndfile.a:
	wget http://www.mega-nerd.com/libsndfile/files/libsndfile-1.0.28.tar.gz
	tar -xf libsndfile-1.0.28.tar.gz
	mkdir libsndfile-1.0.28/install
	cd libsndfile-1.0.28 && ./configure prefix=`realpath install`
	cd libsndfile-1.0.28 && make install

fftw-3.3.6-pl2/install/lib/libfftw3.a:
	wget ftp://ftp.fftw.org/pub/fftw/fftw-3.3.6-pl2.tar.gz
	tar -xf fftw-3.3.6-pl2.tar.gz
	mkdir -p fftw-3.3.6-pl2
	cd fftw-3.3.6-pl2 && ./configure prefix=`realpath install` && make install


$(TARGETS): %: %.o libsndfile-1.0.28/install/lib/libsndfile.a fftw-3.3.6-pl2/install/lib/libfftw3.a
	$(CXX) -o $@ $^  $(LFLAGS)


%.o: %.cxx
	$(CXX) -M $(CFLAGS) $< > $*.d
	$(CXX) -c $(CFLAGS) $< -o $@

clean:
	rm -f *.o $(TARGETS) *.d

test: $(TARGETS)
	# generate a 1k sine wav and the the fft of it. The results go into channels 3 (R) and 4 (I).
	./sine_test sine.wav


# pull in dependency info for *existing* .o files
# got this hint here: http://scottmcpeak.com/autodepend/autodepend.html
# except I'm using wildcard instead of a hard list of obj/d files.
# it's important for this to be after the first rule. Otherwise,
# make with no arguments will use one of these.
-include $(wildcard *.d)