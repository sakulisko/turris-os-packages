#
## Copyright (C) 2013 CZ.NIC z.s.p.o. (http://www.nic.cz/)
#
## This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
# #
#
include $(TOPDIR)/rules.mk

PKG_NAME:=turris-firewall-rules
PKG_VERSION:=47
PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL:=https://gitlab.labs.nic.cz/turris/fw-rules.git
PKG_SOURCE:=$(PKG_NAME).tar.gz
PKG_SOURCE_VERSION:=6982c6e496ca9be4569ddfd9af3e6856bc3fb2c0
PKG_SOURCE:=$(PKG_NAME)-$(PKG_SOURCE_VERSION).tar.gz
PKG_SOURCE_SUBDIR:=$(PKG_NAME)
PKG_MAINTAINER:=Stepan Henek <stepan.henek@nic.cz>
PKG_BUILD_DIR:=$(BUILD_DIR)/$(PKG_NAME)

include $(INCLUDE_DIR)/package.mk

define Package/$(PKG_NAME)
	TITLE:=$(PKG_NAME)
	DEPENDS:=+firewall +cznic-cacert-bundle +curl +openssl-util +ipset +iptables +ip6tables +ulogd +ulogd-mod-pcap +iptables-mod-nflog
endef

define Build/Configure
	$(SED) 's/^VERSION=.*/VERSION=$(PKG_VERSION)/' $(PKG_BUILD_DIR)/firewall-turris-apply.sh
	$(SED) 's/^VERSION=.*/VERSION=$(PKG_VERSION)/' $(PKG_BUILD_DIR)/firewall-turris-download.sh
endef

define Build/Compile
endef

define Package/$(PKG_NAME)/conffiles
/etc/config/firewall-turris
endef

define Package/$(PKG_NAME)/install
	$(INSTALL_DIR) $(1)/etc/config/
	$(INSTALL_CONF) $(PKG_BUILD_DIR)/firewall-turris.config $(1)/etc/config/firewall-turris
	$(INSTALL_DIR) $(1)/etc/logrotate.d/
	$(INSTALL_CONF) ./files/pcap-dump.logrotate $(1)/etc/logrotate.d/pcap-dumps
	$(INSTALL_DIR) $(1)/etc/ulogd.d/
	$(LN) /var/etc/ulogd-turris.conf $(1)/etc/ulogd.d/
	$(INSTALL_DIR) $(1)/usr/share/firewall
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/firewall-turris-apply.sh $(1)/usr/share/firewall/turris
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/firewall-turris-download.sh $(1)/usr/share/firewall/turris-download
	$(INSTALL_DIR) $(1)/etc/cron.d
	$(INSTALL_CONF) $(PKG_BUILD_DIR)/fw-rules.cron $(1)/etc/cron.d/fw-rules
	$(INSTALL_DIR) $(1)/etc/ssl
	$(INSTALL_DATA) $(PKG_BUILD_DIR)/turris-rules.pub $(1)/etc/ssl/turris-rules.pub
endef

define Package/$(PKG_NAME)/postinst
#!/bin/sh
# check if we are on real system
if [ -z "$${IPKG_INSTROOT}" ]; then
	index=`uci show firewall | grep /usr/share/firewall | sed 's/.*\[\([[:digit:]]*\)\].*/\1/'`
	if [ -n $${index} ]; then
		uci get firewall.@include["$${index}"].reload 1>/dev/null 2>&1
		res=$$?
		if [ $${res} = 1 ]; then
			echo -n 'Updating firewall... ' &&
			uci set firewall.@include["$${index}"].reload=1 &&
			uci commit &&
			echo 'OK' &&
			echo -n 'Reloading firewall... ' &&
			/etc/init.d/firewall reload 1>/dev/null 2>&1 &&
			echo 'OK'
		fi
	fi
	hit=false
	for zone in $$(uci show firewall | grep 'turris-0' | sed 's/.*\[\([[:digit:]]*\)\].*/\1/')
	do
		hit=true
		echo -n 'Removing deprecated turris logging mechanism...' &&
		uci delete firewall.@zone["$${zone}"].log_prefix && (
			(
				uci delete -q firewall.@zone["$${zone}"].log_limit;
				uci delete -q firewall.@zone["$${zone}"].log_level;
				uci delete -q firewall.@zone["$${zone}"].log_limit_burst;
				uci set -q firewall.@zone["$${zone}"].log=0;
			) || true
		) &&
		uci commit
	done
	if [ $${hit} == true ]; then
		echo -n 'Restarting firewall... ' &&
		/etc/init.d/firewall restart 1>/dev/null 2>&1 &&
		echo 'OK'
	fi
fi
exit 0
endef

$(eval $(call BuildPackage,$(PKG_NAME)))
