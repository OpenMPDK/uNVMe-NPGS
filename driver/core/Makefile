include $(KV_DIR)/mk/unvme.mk

KV_LIB = libkvnvmedd.a
LIB = lib_kv_interface.a
LIB_BDEV = lib_bdev_mpdk.a
C_SRCS = kv_driver.c kv_interface.c kv_cmd.c lba_cmd.c kv_io.c lba_io.c kv_util.c kv_version.c

buildtime=$(shell date +%Y_%m%d_%H%M)
hash=$(shell git log -1 --format="%H")
os=$(shell uname -o)
processor=$(shell uname -p)
kernel=$(shell uname -r)


all : version $(LIB_BDEV) $(LIB)
	cp $(KV_DIR)/driver/external/$(DPDK_PATH)/$(MPDK_TARGET)/lib/librte_eal.a .
	cp $(KV_DIR)/driver/external/$(DPDK_PATH)/$(MPDK_TARGET)/lib/librte_mempool.a .
	cp $(KV_DIR)/driver/external/$(DPDK_PATH)/$(MPDK_TARGET)/lib/librte_ring.a .
	cp $(KV_DIR)/driver/external/$(DPDK_PATH)/$(MPDK_TARGET)/lib/librte_pci.a .
	cp $(KV_DIR)/driver/external/$(DPDK_PATH)/$(MPDK_TARGET)/lib/librte_bus_pci.a .
	cp $(KV_DIR)/driver/external/$(SPDK_PATH)/build/lib/libspdk_env_dpdk.a .
	cp $(KV_DIR)/driver/external/$(SPDK_PATH)/build/lib/libspdk_log.a .
	cp $(KV_DIR)/driver/external/$(SPDK_PATH)/build/lib/libspdk_nvme.a .
	cp $(KV_DIR)/driver/external/$(SPDK_PATH)/build/lib/libspdk_util.a .
	cp $(KV_DIR)/driver/external/$(SPDK_PATH)/build/lib/libspdk_json.a .
	cp $(KV_DIR)/driver/external/$(SPDK_PATH)/build/lib/libspdk_bdev_nvme.a .
	ar -x libspdk_bdev_nvme.a
	ar -r libspdk_bdev_nvme.a bdev*.o lba_bdev.o
	rm -f bdev*.o lba_bdev.o
	mv libspdk_bdev_nvme.a $(KV_DIR)/driver/external/$(SPDK_PATH)/build/lib/
	ar -x $(LIB)
	ar -x librte_eal.a
	ar -x librte_mempool.a
	ar -x librte_ring.a
	ar -x librte_pci.a
	ar -x librte_bus_pci.a
	mv pci.o pci_bus.o
	ar -x libspdk_env_dpdk.a
	ar -x libspdk_log.a
	ar -x libspdk_nvme.a
	ar -x libspdk_util.a
	ar -x libspdk_json.a
	ar -r $(KV_LIB) *.o
	rm -f librte_eal.a librte_mempool.a librte_ring.a librte_pci.a librte_bus_pci.a libspdk_env_dpdk.a libspdk_log.a libspdk_nvme.a libspdk_util.a libspdk_json.a $(LIB)
	rm -f *.o

version :
	$(shell echo '#include "kv_version.h"' > kv_version.c)
	$(shell echo '' >> kv_version.c)
	$(shell echo 'const char* buildtime="$(buildtime)";' >> kv_version.c)
	$(shell echo 'const char* hash="$(hash)";' >> kv_version.c)
	$(shell echo 'const char* os="$(os)";' >> kv_version.c)
	$(shell echo 'const char* processor="$(processor)";' >> kv_version.c)
	$(shell echo 'const char* kernel="$(kernel)";' >> kv_version.c)
	$(shell echo 'const char* dpdk_version="$(DPDK_PATH)";' >> kv_version.c)
	$(shell echo 'const char* spdk_version="$(SPDK_PATH)";' >> kv_version.c)
	$(CC) $(CFLAGS) -c kv_version.c

$(LIB_BDEV) :
	$(CC) $(CFLAGS) -c lba_bdev.c

clean :
	rm -f *.a *.o

ifeq ($(DEBUG),y)
	rm -f $(KV_DIR)/driver/build/debug/lib/$(KV_LIB)
	rm -f $(KV_DIR)/driver/build/debug/include/*.h
else
	rm -f $(KV_DIR)/driver/build/release/lib/$(KV_LIB)
	rm -f $(KV_DIR)/driver/build/release/include/*.h
endif

install:
ifeq ($(DEBUG),y)
	cp $(KV_LIB) $(KV_DIR)/driver/build/debug/lib/
	cp $(KV_DIR)/driver/include/* $(KV_DIR)/driver/build/debug/include/
else
	cp $(KV_LIB) $(KV_DIR)/build/release/lib/
	cp $(KV_DIR)/driver/include/* $(KV_DIR)/driver/build/release/include/
endif

$(LIB) : $(OBJS)
	$(LIB_C)
