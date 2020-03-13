### installing doxygen
- git clone https://github.com/doxygen/doxygen.git
- sudo apt-get install flex
- sudo apt-get install bison
- cd doxygen
- mkdir build
- cd build
- cmake -G "Unix Makefiles" ..
- make
- sudo make install
### creating a configuration file
- doxygen -g <config-file>
- Modify the config file, such as RECURSIVE set to YES
### running doxygen
- doxygen <config-file>
- set output configuration OUTPUT_DIRECTORY
### visit the html output index.html using chrome