#
# Copyright (c) 2015-2017, Lab A Part
# All rights reserved.
#
# Redistribution and use in source and binary forms, with or without
# modification, are permitted provided that the following conditions are met:
#
# * Redistributions of source code must retain the above copyright notice, this
#  list of conditions and the following disclaimer.
#
# * Redistributions in binary form must reproduce the above copyright notice,
#   this list of conditions and the following disclaimer in the documentation
#   and/or other materials provided with the distribution.
#
# THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS"
# AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE
# IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE
# DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE
# FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL
# DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR
# SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER
# CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY,
# OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE
# OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
#

cmake_minimum_required(VERSION 2.6)

find_package(Board)
find_package(CMSIS)
if (SUPPORT_RTOS)
  find_package(RTOS)
endif()

set(board_nordic_SRCS bsp/bsp.c)

if(SUPPORT_TIMER)
  find_package(PolyMCU)

  list(APPEND board_nordic_SRCS polymcu_timer.c)
endif()

if (NORDIC_SOFT_DEVICE_VERSION)
  list(APPEND board_nordic_SRCS bsp/bsp_btn_ble.c)
endif()

if (BOARD STREQUAL "Nordic/nRF51DK")
  list(APPEND board_nordic_SRCS nRF51DK/board.c)
elseif (BOARD STREQUAL "Nordic/nRF52DK")
  list(APPEND board_nordic_SRCS nRF52DK/board.c)
endif()

if(SUPPORT_RTOS STREQUAL "RTX")
  add_definitions(-DRTC1_CONFIG_FREQUENCY=${RTOS_CLOCK})

  if (BOARD STREQUAL "Nordic/nRF51DK")
    list(APPEND board_nordic_SRCS nRF51DK/rtx.c nRF51DK/rtx_helper.s)
  elseif (BOARD STREQUAL "Nordic/nRF51DK")
    list(APPEND board_nordic_SRCS nRF52DK/rtx.c nRF52DK/rtx_helper.s)
  endif()
endif()

add_library(board_nordic STATIC ${board_nordic_SRCS})
