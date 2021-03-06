#
# Copyright (C) 2014 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=attr
PKG_REV:=50fc862d69984089ce09138b3350ee7762290403
PKG_VERSION:=20140610
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=git://git.sv.gnu.org/attr.git
PKG_SOURCE_PROTO:=git
PKG_SOURCE_SUBDIR:=$(PKG_NAME)-$(PKG_VERSION)
PKG_SOURCE_VERSION:=$(PKG_REV)
PKG_BUILD_DIR:=$(BUILD_DIR)/$(PKG_NAME)-$(PKG_VERSION)
PKG_MAINTAINER:=Maxim Storchak <m.storchak@gmail.com>
PKG_INSTALL:=1
PKG_FIXUP:=autoreconf libtool

include $(INCLUDE_DIR)/package.mk

define Package/attr/Default
  TITLE:=Extended attributes (xattr) manipulation
  URL:=http://savannah.nongnu.org/projects/attr
  SUBMENU:=Filesystem
endef

define Package/attr/Default/description
  Extended attributes support
endef

define Package/attr
$(call Package/attr/Default)
  SECTION:=utils
  CATEGORY:=Utilities
  TITLE+=utils
  DEPENDS:=+libattr
endef

define Package/libattr
$(call Package/attr/Default)
  SECTION:=libs
  CATEGORY:=Libraries
  TITLE+=library
  KCONFIG:= \
    CONFIG_CIFS_XATTR=y \
    CONFIG_EXT4_FS_XATTR=y \
    CONFIG_JFFS2_FS_XATTR=y \
    CONFIG_REISERFS_FS_XATTR=y \
    CONFIG_SQUASHFS_XATTR=y \
    CONFIG_TMPFS_XATTR=y \

endef

define Package/libattr/description
$(call Package/attr/Default/description)
 This package provides libattr
endef

define Package/attr/description
$(call Package/attr/Default/description)
 This package provides xattr manipulation utilities
 - attr
 - getfattr
 - setfattr
endef

CONFIGURE_ARGS += --enable-static --enable-shared

define Package/attr/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/* $(1)/usr/bin/
endef

define Package/libattr/install
	$(INSTALL_DIR) $(1)/usr/lib
	$(INSTALL_DIR) $(1)/etc
	$(CP) $(PKG_INSTALL_DIR)/etc $(1)/
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/*.so* $(1)/usr/lib/
endef

define Package/attr/conffiles
/etc/xattr.conf
endef

define Build/InstallDev
	mkdir -p $(1)/usr/include
	mkdir -p $(1)/usr/lib/pkgconfig
	$(CP) -r $(PKG_INSTALL_DIR)/usr/{include,lib} $(1)/usr/
endef

$(eval $(call BuildPackage,attr))
$(eval $(call BuildPackage,libattr))
