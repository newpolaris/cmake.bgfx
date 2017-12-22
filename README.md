Test bgfx

1. git clone https://github.com/newpolaris/bgfxTest && cd bgfxTest
2. git submodule init
3. git submodule update
4. cmake -Bbuild -H. && make -C build/ -j4
5. ./build/cubes

Problems

1. Can't detect shader header file changes (.sh)
2. Can't detect varying.def.sc file changes
3. Has bugs on EXIT button
