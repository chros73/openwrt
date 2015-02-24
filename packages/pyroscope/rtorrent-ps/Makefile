#
# Copyright (C) 2007-2013 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=rtorrent-ps
RT_VERSION:=0.9.4
PKG_VERSION:=$(RT_VERSION)-git
PKG_RELEASE:=1

PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL:=https://github.com/rakshasa/rtorrent.git
PKG_SOURCE_SUBDIR:=$(PKG_NAME)-$(PKG_VERSION)
PKG_SOURCE_VERSION:=6a3234eaa79f15857260df31f98711ef24266191
PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION)-$(PKG_SOURCE_VERSION).tar.gz

# version and URL of the pyroscope patches
RT_HEX_VERSION:=0x000904
RT_PS_SOURCE_VERSION:=d8cd15d49803ab0eb8e6781a5471ad00115046e8
RT_PS_SOURCE_URL:=https://raw.githubusercontent.com/pyroscope/rtorrent-ps/$(RT_PS_SOURCE_VERSION)/patches

# the name of the pyroscope patch files and their MD5sum
RT_PS_P1:=command_pyroscope.cc
RT_PS_M1:=ba7634da91480021330e6fa8084b50d5
RT_PS_P2:=ui_pyroscope.cc
RT_PS_M2:=951b40c6d43caf0a3bffc5fffcc557d5
RT_PS_P3:=ui_pyroscope.h
RT_PS_M3:=1258acfc82c50a8f452ace87fef0b416
RT_PS_P4:=ps-ui_pyroscope_0.8.8.patch
RT_PS_M4:=7a88f8ab5d41242fdf1428de0e2ca182
RT_PS_P5:=pyroscope.patch
RT_PS_M5:=bd04a0699b80c8042e1cf63a7e0e4222
RT_PS_P6:=ui_pyroscope.patch
RT_PS_M6:=0a2bbaf74c7160ba33876dcc2f050f14

PKG_LICENSE:=GPL-2.0
PKG_LICENSE_FILES:=COPYING

PKG_FIXUP:=autoreconf
PKG_BUILD_PARALLEL:=1
PKG_INSTALL:=1

PKG_BUILD_DIR:=$(BUILD_DIR)/$(PKG_NAME)/$(PKG_NAME)-$(PKG_VERSION)

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/nls.mk

define Package/rtorrent-ps
  SUBMENU:=BitTorrent
  SECTION:=net
  CATEGORY:=Network
  TITLE:=BitTorrent client for ncurses
  URL:=http://libtorrent.rakshasa.no/
#  DEPENDS:=+libcurl +libtorrent-ps +libncursesw +libsigcxx +libpthread +xmlrpc-c-server
  DEPENDS:=+libcurl +libtorrent-ps +libncursesw +libsigcxx +libpthread +xmlrpc-c-server $(ICONV_DEPENDS)
  MAINTAINER:=Peter Wagner <tripolar@gmx.at>, Krisztian Kovacs <chros73@gmail.com>
endef

define Package/rtorrent-ps/description
 rTorrent-PS is the pyroscope patched version of rTorrent, called rTorrent 
 Extended. It's not the same as the pyroscope command line utilities.
 More info: https://code.google.com/p/pyroscope/wiki/RtorrentExtended
 rTorrent is a BitTorrent client for ncurses, using the libtorrent library.
 The client and library is written in C++ with emphasis on speed and
 efficiency, while delivering equivalent features to those found in GUI based
 clients in an ncurses client. This package is built with xmlrpc support.
endef

#TARGET_LDFLAGS += -lz -lpthread -Wl,-rpath-link=$(STAGING_DIR)/usr/lib
TARGET_LDFLAGS += -lz -lpthread -Wl,-rpath-link=$(STAGING_DIR)/usr/lib -liconv

CONFIGURE_ARGS+= \
	--enable-shared \
	--disable-static \
	--disable-debug \
	--with-xmlrpc-c

define Download/patches
 define Download/$(1)
  FILE:=$(1)
  URL:=$(RT_PS_SOURCE_URL)
  MD5SUM:=$(2)
 endef
 define Prepare/$(1)
  $(if $(3),$(LN) $(DL_DIR)/$(1) $(3),$(STAGING_DIR_HOST)/bin/patch -p1 -d "$(PKG_BUILD_DIR)" < $(DL_DIR)/$(1))
 endef
 $$(eval $$(call Download,$(1)))
endef

define Build/Prepare
	$(call Build/Prepare/Default)
	$(eval $(call Download/patches,$(RT_PS_P1),$(RT_PS_M1),$(PKG_BUILD_DIR)/src))
	$(eval $(call Download/patches,$(RT_PS_P2),$(RT_PS_M2),$(PKG_BUILD_DIR)/src))
	$(eval $(call Download/patches,$(RT_PS_P3),$(RT_PS_M3),$(PKG_BUILD_DIR)/src))
	$(eval $(call Download/patches,$(RT_PS_P4),$(RT_PS_M4),))
	$(eval $(call Download/patches,$(RT_PS_P5),$(RT_PS_M5),))
	$(eval $(call Download/patches,$(RT_PS_P6),$(RT_PS_M6),))
	$(call Prepare/$(RT_PS_P1))
	$(call Prepare/$(RT_PS_P2))
	$(call Prepare/$(RT_PS_P3))
	$(call Prepare/$(RT_PS_P4))
	$(call Prepare/$(RT_PS_P5))
	$(call Prepare/$(RT_PS_P6))
	$(STAGING_DIR_HOST)/bin/sed -i "s:\\(AC_DEFINE(HAVE_CONFIG_H.*\\):\1  AC_DEFINE(RT_HEX_VERSION, $(RT_HEX_VERSION), for CPP if checks):" "$(PKG_BUILD_DIR)/configure.ac"
	$(STAGING_DIR_HOST)/bin/sed -i 's/rTorrent \" VERSION/rTorrent-PS " VERSION/' "$(PKG_BUILD_DIR)/src/ui/download_list.cc"
	$(STAGING_DIR_HOST)/bin/sed -i "$(PKG_BUILD_DIR)/src/$(RT_PS_P1)" -e 's:view_filter:view.filter:'
endef

define Build/Configure
	( cd $(PKG_BUILD_DIR); ./autogen.sh );
	$(call Build/Configure/Default)
endef

define Package/rtorrent-ps/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/rtorrent $(1)/usr/bin/
	$(CP) ./files/* $(1)/
endef

$(eval $(call BuildPackage,rtorrent-ps))
