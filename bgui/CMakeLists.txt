# This file is part of the Computer Vision Toolkit (cvkit).
#
# Author: Heiko Hirschmueller
#
# Copyright (c) 2014, Institute of Robotics and Mechatronics, German Aerospace Center
# All rights reserved.
#
# Redistribution and use in source and binary forms, with or without
# modification, are permitted provided that the following conditions are met:
#
# 1. Redistributions of source code must retain the above copyright notice,
# this list of conditions and the following disclaimer.
#
# 2. Redistributions in binary form must reproduce the above copyright notice,
# this list of conditions and the following disclaimer in the documentation
# and/or other materials provided with the distribution.
#
# 3. Neither the name of the copyright holder nor the names of its contributors
# may be used to endorse or promote products derived from this software without
# specific prior written permission.
#
# THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS"
# AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE
# IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE
# ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE
# LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR
# CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF
# SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS
# INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN
# CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE)
# ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE
# POSSIBILITY OF SUCH DAMAGE.

project(bgui CXX)

set(bgui_src
  imagewindow.cc
  fileimagewindow.cc
  listimagewindow.cc
)

set(bgui_hh
  basewindow.h
  fileimagewindow.h
  imageadapterbase.h
  imageadapter.h
  imagewindow.h
  listimagewindow.h
)

if (USE_GDAL)
  add_definitions(-DINCLUDE_GDAL)
endif ()

if (JPEG_FOUND)
  add_definitions(-DINCLUDE_JPEG)
endif ()

if (PNG_FOUND)
  include_directories(${PNG_INCLUDE_DIRS})
  add_definitions(-DINCLUDE_PNG)
  add_definitions(${PNG_DEFINITIONS})
endif ()

if (X11_FOUND AND CMAKE_USE_PTHREADS_INIT)
  add_definitions(-DINCLUDE_X11)
  include_directories(${X11_INCLUDE_DIR})
  set(bgui_src ${bgui_src} basewindow_x11.cc)
endif ()

if (WIN32)
  add_definitions(-DWINVER=0x0500)
  add_definitions(-D_WIN32_IE=0x0500)
  set(bgui_src ${bgui_src} basewindow_win32.cc)
endif ()

add_library(bgui_static STATIC ${bgui_src})

if (X11_FOUND AND CMAKE_USE_PTHREADS_INIT)
  target_link_libraries(bgui_static ${X11_LIBRARIES} ${CMAKE_THREAD_LIBS_INIT})
endif ()

if (INSTALL_DEVELOPMENT)
  add_library(bgui SHARED ${bgui_src})
  target_link_libraries(bgui LINK_PRIVATE gimage gutil)

  if (WIN32)
    target_link_libraries(bgui LINK_PRIVATE gdi32 comctl32)
  endif ()

  if (X11_FOUND AND CMAKE_USE_PTHREADS_INIT)
    target_link_libraries(bgui LINK_PRIVATE ${X11_LIBRARIES} ${CMAKE_THREAD_LIBS_INIT})
  endif ()

  install(TARGETS bgui_static bgui EXPORT CVKITTargets
          RUNTIME DESTINATION bin
          LIBRARY DESTINATION lib
          ARCHIVE DESTINATION lib)
  install(FILES ${bgui_hh} DESTINATION include/bgui)
endif ()
