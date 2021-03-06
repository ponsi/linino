# 
# Copyright (C) 2006-2008 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.

include $(TOPDIR)/rules.mk

PKG_NAME:=opkg
PKG_REV:=4564
PKG_VERSION:=$(PKG_REV)
PKG_RELEASE:=3

PKG_SOURCE_PROTO:=svn
PKG_SOURCE_VERSION:=$(PKG_REV)
PKG_SOURCE_SUBDIR:=opkg-$(PKG_VERSION)
PKG_SOURCE_URL:=http://svn.openmoko.org/trunk/src/target/opkg/
PKG_SOURCE:=$(PKG_SOURCE_SUBDIR).tar.gz
PKG_FIXUP = libtool
include $(INCLUDE_DIR)/package.mk

define Package/opkg
  SECTION:=base
  CATEGORY:=Base system
  TITLE:=opkg package management system
  URL:=http://wiki.openmoko.org/wiki/Opkg
endef

define Package/opkg/description
  Lightweight package management system
  opkg is the opkg Package Management System, for handling
  installation and removal of packages on a system. It can
  recursively follow dependencies and download all packages
  necessary to install a particular package.

  opkg knows how to install both .ipk and .deb packages.
endef

define Package/opkg/conffiles
/etc/opkg.conf
endef

TARGET_CFLAGS += $(FPIC)
EXTRA_CFLAGS+=-I$(STAGING_DIR)/usr/include -I$(STAGING_DIR)/include -I$(STAGING_DIR)/usr/lib
EXTRA_LDFLAGS+=-L$(STAGING_DIR)/usr/lib -Wl,-rpath-link,$(STAGING_DIR)/usr/lib

CONFIGURE_ARGS += \
	--disable-curl \
	--disable-gpg \

define Build/Configure
	(cd $(PKG_BUILD_DIR); \
		autoreconf -v --install || exit 1 \
	);
	$(call Build/Configure/Default)
endef

define Build/Compile
	$(MAKE) -C $(PKG_BUILD_DIR) \
		CC="$(TARGET_CC)" \
		DESTDIR="$(PKG_INSTALL_DIR)" \
		HOST_CPU="$(ARCH)" \
		all install
endef

define Package/opkg/install
	$(INSTALL_DIR) $(1)/usr/lib/opkg
	$(INSTALL_DIR) $(1)/bin
	$(INSTALL_DIR) $(1)/etc
	$(INSTALL_DATA) ./files/opkg.conf $(1)/etc/
	$(SED) 's,$$$$S,$(BOARD),g' -e 's,$$$$A,$(ARCH),g' $(1)/etc/opkg.conf
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/opkg-cl $(1)/bin/opkg
	$(INSTALL_DIR) $(1)/usr/lib
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libopkg.so.* $(1)/usr/lib/
endef

define Build/InstallDev
	mkdir -p $(1)/usr/include
	$(CP) $(PKG_INSTALL_DIR)/usr/include/libopkg $(1)/usr/include/
endef

$(eval $(call BuildPackage,opkg))
