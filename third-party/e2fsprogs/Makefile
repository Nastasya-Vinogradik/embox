
ifeq ($(ROOT_DIR),)
$(error ROOT_DIR is not set.)
endif

AT=

E2FSPROGS_DIR       = $(ROOT_DIR)/build/e2fsprogs
E2FSPROGS_CMAKE_DIR = $(E2FSPROGS_DIR)/cmake
E2FSPROGS_LIB       += $(E2FSPROGS_DIR)/src/e2fsprogs-build/lib/ext2fs/libext2fs.a
E2FSPROGS_LIB       += $(E2FSPROGS_DIR)/src/e2fsprogs-build/lib/et/libcom_err.a
E2FSPROGS_LIB       += $(E2FSPROGS_DIR)/src/e2fsprogs-build/lib/e2p/libe2p.a
E2FSPROGS_LIB       += $(E2FSPROGS_DIR)/src/e2fsprogs-build/lib/intl/libintl.a
E2FSPROGS_LIB       += $(E2FSPROGS_DIR)/src/e2fsprogs-build/lib/uuid/libuuid.a
E2FSPROGS_FLAGS     = $(E2FSPROGS_CMAKE_DIR)/flags

EMBOX_IMPORTED_CPPFLAGS += -I$(abspath .)/include
EMBOX_IMPORTED_CPPFLAGS += -save-temps=obj
EMBOX_IMPORTED_CPPFLAGS += -include $(ROOT_DIR)/third-party/e2fsprogs/e2fsprogs_embox_compat.h
EMBOX_IMPORTED_CPPFLAGS += $(filter -I%,$(EMBOX_CPPFLAGS))
EMBOX_IMPORTED_CPPFLAGS += $(filter -nostdinc,$(EMBOX_CPPFLAGS))
EMBOX_IMPORTED_CPPFLAGS += $(filter -D__EMBOX__,$(EMBOX_CPPFLAGS))

EMBOX_IMPORTED_CFLAGS   += $(EMBOX_IMPORTED_CPPFLAGS)
EMBOX_IMPORTED_CXXFLAGS += $(EMBOX_IMPORTED_CPPFLAGS)

EMBOX_IMPORTED_CFLAGS   += $(filter -fno-common,$(EMBOX_CFLAGS))
EMBOX_IMPORTED_CFLAGS   += $(filter -march%,$(EMBOX_CFLAGS))
EMBOX_IMPORTED_CFLAGS   += $(filter -m32,$(EMBOX_CFLAGS))
EMBOX_IMPORTED_CFLAGS   += $(filter -fno-stack-protector,$(EMBOX_CFLAGS))
EMBOX_IMPORTED_CFLAGS   += $(filter -std=gnu99,$(EMBOX_CFLAGS))
EMBOX_IMPORTED_CFLAGS   += $(filter -g,$(EMBOX_CFLAGS))

EMBOX_IMPORTED_CXXFLAGS += $(subst .,$(ROOT_DIR),$(filter -I%,$(EMBOX_CXXFLAGS)))
EMBOX_IMPORTED_CXXFLAGS += $(filter -fno-common,$(EMBOX_CXXFLAGS))
EMBOX_IMPORTED_CXXFLAGS += $(filter -march%,$(EMBOX_CXXFLAGS))
EMBOX_IMPORTED_CXXFLAGS += $(filter -m32,$(EMBOX_CXXFLAGS))
EMBOX_IMPORTED_CXXFLAGS += $(filter -fno-stack-protector,$(EMBOX_CXXFLAGS))
EMBOX_IMPORTED_CXXFLAGS += $(filter -fno-rtti,$(EMBOX_CXXFLAGS))
EMBOX_IMPORTED_CXXFLAGS += $(filter -fno-exceptions,$(EMBOX_CXXFLAGS))
EMBOX_IMPORTED_CXXFLAGS += $(filter -fno-threadsafe-statics,$(EMBOX_CXXFLAGS))

#EMBOX_IMPORTED_CFLAGS   += -Wl,--relocatable
#EMBOX_IMPORTED_CXXFLAGS += -Wl,--relocatable

EMBOX_IMPORTED_MAKEFLAGS += $(filter -j,$(EMBOX_MAKEFLAGS))
ifneq ($(filter -j,$(EMBOX_MAKEFLAGS)),)
EMBOX_IMPORTED_MAKEFLAGS += $(shell nproc)
endif
# no wonder the following doesn't work
# EMBOX_IMPORTED_MAKEFLAGS += $(filter --jobserver-fds=%,$(EMBOX_MAKEFLAGS))

EMBOX_DERIVED_CFLAGS    += $(EMBOX_IMPORTED_CFLAGS)
EMBOX_DERIVED_CXXFLAGS  += $(EMBOX_IMPORTED_CXXFLAGS)
EMBOX_DERIVED_MAKEFLAGS += $(EMBOX_IMPORTED_MAKEFLAGS)

EMBOX_DERIVED_LDFLAGS   += -Wl,--relocatable -nostdlib
all:

.PHONY: all $(E2FSPROGS_LIB)

$(E2FSPROGS_CMAKE_DIR):
	$(AT)mkdir -p $@

$(E2FSPROGS_FLAGS): | $(E2FSPROGS_CMAKE_DIR)
	-$(AT)rm $@
	$(AT)echo EMBOX_DERIVED_CFLAGS=\"$(EMBOX_DERIVED_CFLAGS)\" >> $@
	$(AT)echo EMBOX_DERIVED_CXXFLAGS=\"$(EMBOX_DERIVED_CXXFLAGS)\" >> $@
	$(AT)echo EMBOX_DERIVED_LDFLAGS=\"$(EMBOX_DERIVED_LDFLAGS)\" >> $@
	$(AT)echo EMBOX_DERIVED_MAKEFLAGS=\"$(EMBOX_DERIVED_MAKEFLAGS)\" >> $@
	$(AT)echo EMBOX_ROOT=\"$(ROOT_DIR)\" >> $@

$(E2FSPROGS_CMAKE_DIR)/Makefile: CMakeLists.txt $(E2FSPROGS_FLAGS) | $(E2FSPROGS_CMAKE_DIR)
	$(AT)cd $(E2FSPROGS_CMAKE_DIR) && $(shell cat $(E2FSPROGS_FLAGS)) cmake -DCMAKE_BUILD_TYPE=Debug $(shell pwd)

$(E2FSPROGS_LIB): $(E2FSPROGS_CMAKE_DIR)/Makefile
	$(AT)cd $(E2FSPROGS_CMAKE_DIR) && $(MAKE) VERBOSE=1 # -j `nproc`

all: $(E2FSPROGS_LIB)