#Linux Makefile

include $(SRCBASE)/router/.config

INCLUDE += -I. -I.. -I$(SRCBASE)/include -I$(SRCBASE)/common/include -I$(SRCBASE)/shared -I$(SRCBASE)/router/shared -I$(SRCBASE)/router/nas${BCMEX}

CFLAGS += $(INCLUDE)
CFLAGS += $(WLAN_ComponentIncPath)
CFLAGS += -DEAPD_WKSP_AUTO_CONFIG
#CFLAGS += -DNAS_GTK_PER_STA
CFLAGS += -Wall -DEAPDDUMP

CFLAGS  += -s


ifneq ($(LINUXDIR),)
CFLAGS += -Dlinux
endif

LDFLAGS += -L$(TOP_PLATFORM)/nvram${BCMEX} -L$(INSTALLDIR)/nvram/usr/lib -lnvram
LDFLAGS += -L$(TOP)/shared -L$(INSTALLDIR)/shared/usr/lib -lshared
LDFLAGS += -lgcc_s

ifeq ($(RTCONFIG_QTN),y)
LDFLAGS += -L$(TOP)/libqcsapi_client -lqcsapi_client
endif

vpath %.c $(SRCBASE)/router/eapd${BCMEX} $(SRCBASE)/shared

OBJS = eapd.o eapd_linux.o
OBJS += nas_eap.o wps_eap.o ses_eap.o


#ifdef BCM_DCS
ifeq ($(RTCONFIG_BCMDCS),y)
OBJS += dcs_eap.o
CFLAGS += -DBCM_DCS
endif
#endif

#ifdef BCM_MEVENT
ifeq ($(CONFIG_BCM_MEVENT),y)
OBJS += mevent_eap.o
CFLAGS += -DBCM_MEVENT
endif
#endif

#ifdef BCM_BSD
ifeq ($(CONFIG_BCMBSD),y)
OBJS += bsd_eap.o
CFLAGS += -DBCM_BSD
endif
#endif

#ifdef BCM_EVENTD
OBJS += eventd_eap.o
CFLAGS += -DBCM_EVENTD
#endif

OBJS += wlceventd_eap.o
CFLAGS += -DBCM_WLCEVENTD

ifeq ($(wildcard $(SRCBASE)/router/eapd${BCMEX}/*.c),)
all:
	-cp prebuilt/eapd eapd
else
all: eapd
endif

eapd: $(OBJS)
	$(CC) -o $@ $^ $(LDFLAGS)

install: all
	install -d $(INSTALLDIR)/bin
	install eapd $(INSTALLDIR)/bin
	$(STRIP) $(INSTALLDIR)/bin/eapd

clean:
	rm -f *.o eapd
