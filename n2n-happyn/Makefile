# SPDX-License-Identifier: GPL-3.0-only
#
# Copyright (C) 2020 - ntop.org and contributors
# Copyright (C) 2021-2022 ImmortalWrt.org

include $(TOPDIR)/rules.mk

PKG_NAME:=n2n-happyn
PKG_VERSION:=3.1.1
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://github.com/happynlab/n2n/releases/download/happyn3.1.1/
PKG_HASH:=475ff94c02ea9a86ff172e84e066bf0bbc67020526099b7b1f2bb94a6d68c285

PKG_LICENSE:=GPL-3.0
PKG_LICENSE_FILES:=LICENSE
PKG_MAINTAINER:=support@happyn.net

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/cmake.mk

define Package/mesh/template
  SECTION:=net
  CATEGORY:=Network
  SUBMENU:=VPN
  TITLE:=Mesh Peer-to-peer VPN
  URL:=http://www.happyn.net
  DEPENDS:=+libopenssl +libpthread
endef

define Package/mesh
  $(call Package/mesh/template)
  DEPENDS+=+libcap +kmod-tun +resolveip
endef

define Package/mesh/description
  This package contains client node and supernode for the Mesh infrastructure.
endef

define Package/mesh/conffiles
/etc/config/mesh
endef

define Package/mesh-utils
  $(call Package/mesh/template)
  DEPENDS+=+mesh +libpcap
endef

define Package/mesh-utils/description
  This package contains extended utilities for the Mesh infrastructure.
endef

CMAKE_OPTIONS+= \
	-DCMAKE_BUILD_TYPE=Release \
	-DN2N_OPTION_USE_PTHREAD=ON \
	-DN2N_OPTION_USE_OPENSSL=ON \
	-DN2N_OPTION_USE_PCAPLIB=ON \
	-DN2N_OPTION_USE_ZSTD=OFF

define Package/mesh/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/sbin/edge $(1)/usr/bin/mesh  # 将二进制文件命名为 mesh
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/sbin/supernode $(1)/usr/bin/mesh-supernode

	$(INSTALL_DIR) $(1)/etc/config
	$(INSTALL_CONF) ./files/mesh.config $(1)/etc/config/mesh  # 将配置文件路径更新为 /etc/config/mesh
	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) ./files/mesh.init $(1)/etc/init.d/mesh
	$(INSTALL_DIR) $(1)/etc/uci-defaults
	$(INSTALL_BIN) ./files/n2n-migrate-conf.sh $(1)/etc/uci-defaults/50-mesh-migrate-conf
endef

define Package/mesh-utils/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/n2n-benchmark $(1)/usr/bin/mesh-benchmark
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/n2n-decode $(1)/usr/bin/mesh-decode
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/n2n-keygen $(1)/usr/bin/mesh-keygen
endef

$(eval $(call BuildPackage,mesh))
$(eval $(call BuildPackage,mesh-utils))
