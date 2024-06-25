# CPack example

An example CMake project to demonstrate creating packages with CPack.

## Source Tree
```
├── cmake
│   ├── InstallingConfigs.cmake
│   ├── InstallingGeneral.cmake
│   └── Packing.cmake
├── libraries
│   ├── AnotherLibrary
│   │   ├── include
│   │   │   └── another.h
│   │   ├── src
│   │   │   ├── another.cpp
│   │   │   └── things.h
│   │   ├── CMakeLists.txt
│   │   └── Config.cmake.in
│   ├── SomeLibrary
│   │   ├── include
│   │   │   └── some.h
│   │   ├── src
│   │   │   ├── some.cpp
│   │   │   └── things.h
│   │   ├── CMakeLists.txt
│   │   └── Config.cmake.in
│   └── CMakeLists.txt
├── CMakeLists.txt
├── LICENSE
├── README.md
└── main.cpp
```

## Generate .deb package
```
cd cpack-example && \
mkdir build && cd build && \
cmake -G Ninja -DCMAKE_BUILD_TYPE=Release .. && \
cmake --build . && \
cpack -G DEB
```

## Some issues

- libraries are hardcoded to `STATIC`, which is rarely a good idea. But at least it is somewhat understandable in case of Windows, because there are no DLL exports;
- there is no real need to split the installation module into `InstallingGeneral.cmake` and `InstallingConfigs.cmake`, it should be just one `Installing.cmake`;
- the "*generate the export targets for the build tree*" installation step is rather useless, so it probably should be removed;
- the `DESTINATION` for CMake configs should probably be `share/${PROJECT_NAME}` instead of `cmake`, although both work equally fine;
- every library subproject sets `DEBUG_POSTFIX`, while it should be set just once in the top-level project via `CMAKE_DEBUG_POSTFIX` (*but also explicitly for every executable*);
- the libraries targets namespace is set in every subproject. Instead it should be set just once in the top-level project;
- `CMAKE_MODULE_PATH` is set in every subproject. There is no need for that, it is enough to set it once in the top-level project;
- there should be no checks for top-level project and its variables, as libraries are already part of the project, and they shouldn't be meant to be built "separately" (*there is `--target` for that*).
