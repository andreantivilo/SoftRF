#
# Makefile
# Copyright (C) 2019 Linar Yusupov
#
# This program is free software: you can redistribute it and/or modify
# it under the terms of the GNU General Public License as published by
# the Free Software Foundation, either version 3 of the License, or
# (at your option) any later version.
#
# This program is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.
#
# You should have received a copy of the GNU General Public License
# along with this program.  If not, see <http://www.gnu.org/licenses/>.

SKETCH        = UATbridge.ino

BIN_ELF       := $(SKETCH:.ino=.ino.elf)
BIN_IHEX      := $(BIN_ELF:.elf=.ihex)

SOFTRF_DIR    = ../SoftRF
BINDIR        = /tmp/energia

ENERGIA       = energia
LN            = ln -s
RM            = rm -f
TC_PATH       = $(HOME)/.energia15/packages/energia/tools/arm-none-eabi-gcc/4.8.4-20140725/bin
OBJCOPY       = arm-none-eabi-objcopy -O ihex -R .empty -R .vtable \
                 -R .dmaSpi0RxControlTableEntry -R .dmaSpi0TxControlTableEntry \
                 -R .dmaSpi1RxControlTableEntry -R .dmaSpi1TxControlTableEntry
SED           = sed -i 's/:10FFD800FFFFFF00FFFFFFFF00FFFFFFC500C5FF9A/:10FFD800C501FEC5FFFFFFFF00FFFFFFC500C5FF0E/'

CPPS          := Platform_CC13XX.cpp SoCHelper.cpp \
                 RFHelper.cpp GNSSHelper.cpp \
                 Protocol_Legacy.cpp Protocol_P3I.cpp Protocol_FANET.cpp \
                 Protocol_OGNTP.cpp Protocol_UAT978.cpp \
                 GDL90Helper.cpp

HFILES        := SoftRF.h $(CPPS:.cpp=.h) \
                 Platform_ESP8266.h Platform_ESP32.h Platform_RPi.h \
                 BaroHelper.h WebHelper.h BluetoothHelper.h JSONHelper.h \
                 D1090Helper.h LEDHelper.h EEPROMHelper.h SoundHelper.h \
                 MAVLinkHelper.h TimeHelper.h NMEAHelper.h TrafficHelper.h \
                 WiFiHelper.h

links:  $(foreach f, $(CPPS) $(HFILES), link-file-$(f))

link-file-%: $(SOFTRF_DIR)/%
				@if [ -e $* ]; then echo $* "symlink is already present"; \
				  else echo "Creating symlink" $* " --> " $< ; $(LN) $< ; fi

build:
				$(ENERGIA) --verify --verbose-build $(SKETCH)

upload:
				$(ENERGIA) --upload --verbose-build --verbose-upload  $(SKETCH)

#
# "Intel HEX" type of the firmware binary is necessary input format
#    for cc2538-bsl.py serial firmware uploader. 
#
ihex:
				$(TC_PATH)/$(OBJCOPY) $(BINDIR)/$(BIN_ELF) $(BINDIR)/$(BIN_IHEX)
				$(SED) $(BINDIR)/$(BIN_IHEX)
clean:
				$(RM) $(CPPS) $(HFILES)
