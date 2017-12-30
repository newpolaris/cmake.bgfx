Test bgfx cmake integration

1. git clone https://github.com/newpolaris/bgfxTest
2. cd bgfxTest
3. git submodule update --init --recursive
4. cmake -Bbuild -H. && make -C build/ -j4
5. ./build/cubes

Problems

1. Can't detect shader header file changes (.sh)
2. Can't detect varying.def.sc file changes

Notes for llvm c++ compiler

https://github.com/bkaradzic/bgfx/issues/1290

diff --git a/examples/common/entry/entry.cpp b/examples/common/entry/entry.cpp
index 500f6fc..57d8279 100644
--- a/examples/common/entry/entry.cpp
+++ b/examples/common/entry/entry.cpp
@@ -930,7 +930,7 @@ restart:
 
        bx::AllocatorI* getAllocator()
        {
-               return g_allocator;
+               return getDefaultAllocator();
        }
