
# Install OpenCV FROM THE UBUNTU REPOSITORY

You can install OpenCV from the Ubuntu or Debian repository or from the official site.

	sudo apt install libopencv-dev python-opencv -y
	
	
# INSTALL OPENCV FROM THE OFFICIAL SITE

https://www.pyimagesearch.com/2016/10/24/ubuntu-16-04-how-to-install-opencv/

https://milq.github.io/install-opencv-ubuntu-debian/


```sh?linenums
######################################
# INSTALL OPENCV ON UBUNTU OR DEBIAN #
######################################

# VERSION TO BE INSTALLED
cd /home/lqdai/Workbench/Build/
OPENCV_VERSION='3.4.1'
INSTALL_PREFIX=~/Workbench/App/usr/opencv-${OPENCV_VERSION}
FOLDER_NAME=opencv-${OPENCV_VERSION}



# 1. KEEP UBUNTU OR DEBIAN UP TO DATE

sudo apt-get -y update
sudo apt-get -y upgrade
#sudo apt-get -y dist-upgrade
sudo apt-get -y autoremove


# 2. INSTALL THE DEPENDENCIES

# Build tools:
sudo apt-get install -y build-essential cmake libprotobuf-dev  libmesa-dev  freeglut3-dev


# GUI (if you want to use GTK instead of Qt, replace 'qt5-default' with 'libgtkglext1-dev' and remove '-DWITH_QT=ON' option in CMake):
sudo apt-get install -y qt5-default libvtk6-dev

# Media I/O:
sudo apt-get install -y zlib1g-dev libjpeg-dev libwebp-dev libpng-dev libtiff5-dev libopenexr-dev libgdal-dev #libjasper-dev 

# Video I/O:
sudo apt-get install -y libdc1394-22-dev libavcodec-dev libavformat-dev libswscale-dev libtheora-dev libvorbis-dev libxvidcore-dev libx264-dev yasm libopencore-amrnb-dev libopencore-amrwb-dev libv4l-dev libxine2-dev

# Parallelism and linear algebra libraries:
sudo apt-get install -y libtbb-dev libeigen3-dev

# Python:
#sudo apt-get install -y python-dev python-tk python-numpy python3-dev python3-tk python3-numpy

# Java:
sudo apt-get install -y ant default-jdk

# Documentation:
sudo apt-get install -y doxygen


# 3. INSTALL THE LIBRARY

sudo apt-get install -y unzip wget

# Create a new folder for storing the source code
mkdir ${FOLDER_NAME}
 
# Change directory
cd ${FOLDER_NAME}

wget https://github.com/opencv/opencv/archive/${OPENCV_VERSION}.zip
unzip ${OPENCV_VERSION}.zip
#rm ${OPENCV_VERSION}.zip
#mv opencv-${OPENCV_VERSION} OpenCV
cd opencv-${OPENCV_VERSION}
mkdir build
cd build
cmake -D CMAKE_INSTALL_PREFIX=${INSTALL_PREFIX} -DWITH_QT=ON -DWITH_OPENGL=ON -DFORCE_VTK=ON -DWITH_TBB=ON -DWITH_GDAL=ON -DWITH_XINE=ON -DBUILD_EXAMPLES=ON -DENABLE_PRECOMPILED_HEADERS=OFF -DWITH_CUDA=OFF ..
make -j4
make install
sudo ldconfig


# 4. EXECUTE SOME OPENCV EXAMPLES AND COMPILE A DEMONSTRATION

# To complete this step, please visit 'http://milq.github.io/install-opencv-ubuntu-debian'.
```