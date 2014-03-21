cmake_minimum_required(VERSION 2.8)

include_directories(BEFORE ${CMAKE_SOURCE_DIR}/option)
include_directories(BEFORE ${CMAKE_SOURCE_DIR}/utils)
include_directories(BEFORE ${CMAKE_SOURCE_DIR}/src)
include_directories(BEFORE ${CMAKE_SOURCE_DIR}/include)

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -D_GNU_SOURCE -D__DEBUG")

if (NOT WIN32)
    set(PTHREAD_LIB pthread)
    set(LIBM m)
    set(FORESTDB_FILE_OPS "src/filemgr_ops_linux.cc")
else (NOT WIN32)
    set(FORESTDB_FILE_OPS "src/filemgr_ops_windows.cc")
    set(GETTIMEOFDAY_VS "utils/gettimeofday_vs.cc")
endif(NOT WIN32)

if ("${CMAKE_C_COMPILER_ID}" STREQUAL "Clang")
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Qunused-arguments -g -fomit-frame-pointer -pthread")
elseif ("${CMAKE_C_COMPILER_ID}" STREQUAL "GNU")
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -g -fomit-frame-pointer -pthread")
elseif ("${CMAKE_CXX_COMPILER_ID}" STREQUAL "MSVC")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /wd4716 /W0")
endif()

add_library(forestdb SHARED
            src/avltree.cc
            src/blockcache.cc
            src/btree.cc
            src/btree_kv.cc
            src/btreeblock.cc
            src/docio.cc
            src/filemgr.cc
            src/filemgr_ops.cc
            ${FORESTDB_FILE_OPS}
            src/forestdb.cc
            src/hash_functions.cc
            src/hbtrie.cc
            src/iterator.cc
            src/list.cc
            src/hash.cc
            src/wal.cc
            utils/crc32.cc
            utils/debug.cc
            utils/memleak.cc)

add_executable(forestdb_test
               src/avltree.cc
               tests/forestdb_test.cc
               ${GETTIMEOFDAY_VS}
               utils/memleak.cc)
target_link_libraries(forestdb_test forestdb ${PTHREAD_LIB} ${LIBM})

add_executable(hash_test
               src/avltree.cc
               src/hash.cc
               src/hash_functions.cc
               src/list.cc
               ${GETTIMEOFDAY_VS}
               tests/hash_test.cc
               utils/memleak.cc)
target_link_libraries(hash_test ${PTHREAD_LIB} ${LIBM})

add_executable(bcache_test
               tests/bcache_test.cc
               src/avltree.cc
               src/blockcache.cc
               src/filemgr.cc
               src/filemgr_ops.cc
               ${FORESTDB_FILE_OPS}
               ${GETTIMEOFDAY_VS}
               src/hash.cc
               src/hash_functions.cc
               src/list.cc
               src/wal.cc
               utils/crc32.cc
               utils/debug.cc
               utils/memleak.cc)
target_link_libraries(bcache_test ${PTHREAD_LIB} ${LIBM})


add_executable(filemgr_test
               tests/filemgr_test.cc
               src/avltree.cc
               src/blockcache.cc
               src/filemgr.cc
               src/filemgr_ops.cc
               ${FORESTDB_FILE_OPS}
               ${GETTIMEOFDAY_VS}
               src/hash.cc
               src/hash_functions.cc
               src/list.cc
               src/wal.cc
               utils/crc32.cc
               utils/debug.cc
               utils/memleak.cc)
target_link_libraries(filemgr_test ${PTHREAD_LIB} ${LIBM})

add_executable(btreeblock_test
               tests/btreeblock_test.cc
               src/avltree.cc
               src/blockcache.cc
               src/btree.cc
               src/btree_kv.cc
               src/btreeblock.cc
               src/filemgr.cc
               src/filemgr_ops.cc
               ${FORESTDB_FILE_OPS}
               ${GETTIMEOFDAY_VS}
               src/hash.cc
               src/hash_functions.cc
               src/list.cc
               src/wal.cc
               utils/crc32.cc
               utils/debug.cc
               utils/memleak.cc)
target_link_libraries(btreeblock_test ${PTHREAD_LIB} ${LIBM})

add_executable(docio_test
               tests/docio_test.cc
               src/avltree.cc
               src/blockcache.cc
               src/docio.cc
               src/filemgr.cc
               src/filemgr_ops.cc
               ${FORESTDB_FILE_OPS}
               ${GETTIMEOFDAY_VS}
               src/hash.cc
               src/hash_functions.cc
               src/list.cc
               src/wal.cc
               utils/crc32.cc
               utils/debug.cc
               utils/memleak.cc)
target_link_libraries(docio_test ${PTHREAD_LIB} ${LIBM})

add_executable(hbtrie_test
               tests/hbtrie_test.cc
               src/avltree.cc
               src/blockcache.cc
               src/btree.cc
               src/btree_kv.cc
               src/btreeblock.cc
               src/docio.cc
               src/filemgr.cc
               src/filemgr_ops.cc
               ${FORESTDB_FILE_OPS}
               ${GETTIMEOFDAY_VS}
               src/hash.cc
               src/hash_functions.cc
               src/hbtrie.cc
               src/list.cc
               src/wal.cc
               utils/crc32.cc
               utils/debug.cc
               utils/memleak.cc)
target_link_libraries(hbtrie_test ${PTHREAD_LIB} ${LIBM})

add_executable(crc_test
               tests/crc_test.cc
               src/avltree.cc
               src/hash_functions.cc
               ${GETTIMEOFDAY_VS}
               utils/crc32.cc
               utils/memleak.cc)
target_link_libraries(crc_test ${PTHREAD_LIB} ${LIBM})

enable_testing()
add_test(forestdb_test forestdb_test)
add_test(hash_test hash_test)
add_test(bcache_test bcache_test)
add_test(filemgr_test filemgr_test)
add_test(btreeblock_test btreeblock_test)
add_test(docio_test docio_test)
add_test(hbtrie_test hbtrie_test)
add_test(crc_test crc_test)