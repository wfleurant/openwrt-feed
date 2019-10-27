#
# Copyright (C) 2015 Alex Kirhenshtein <alk@netxms.org>
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

# include config.log:
# > checking whether iconv supports //IGNORE... no
# > checking whether iconv supports state reset... no
# > checking for iconv declaration...
# > checking pcre.h usability... yes
# > checking pcre.h presence... yes
# > checking for pcre.h... yes
# > checking for pcre_compile in -lpcre... yes
# > checking for pcre32_compile in -lpcre32... no
# > configure: error: libpcre32 is requred
# > Makefile:153: recipe for target '/dev/shm/openwrt-trunk/build_dir/target-mips_24kc_musl/netxms-agent-nossl/netxms-3.0.2329/.configured_68b329da9893e34099c7d8ad5cb9c940' failed
# > make[2]: *** [/dev/shm/openwrt-trunk/build_dir/target-mips_24kc_musl/netxms-agent-nossl/netxms-3.0.2329/.configured_68b329da9893e34099c7d8ad5cb9c940] Error 1
# > make[2]: Leaving directory '/dev/shm/openwrt-trunk/feeds/netxms/admin/netxms'
# > time: package/feeds/netxms/netxms/nossl/compile#31.53#7.06#37.07
PKG_NAME:=netxms-agent
PKG_LICENSE=GPL-2.0+
PKG_VERSION:=3.0.2329
PKG_RELEASE:=1

PKG_SOURCE_URL:=https://www.netxms.org/download/releases/3.0/
PKG_SOURCE:=netxms-$(PKG_VERSION).tar.gz
PKG_MD5SUM:=41b00abf510003f456b842e53101ff51
PKG_BUILD_DIR:=$(BUILD_DIR)/$(PKG_NAME)-$(BUILD_VARIANT)/netxms-$(PKG_VERSION)
PKG_INSTALL:=1
PKG_BUILD_PARALLEL:=1

PKG_FIXUP:=autoreconf

include $(INCLUDE_DIR)/package.mk

define Package/netxms/Default
	SUBMENU:=NetXMS
	SECTION:=admin
	CATEGORY:=Administration
	TITLE:=NetXMS
	URL:=https://www.netxms.org
	MAINTAINER:=Alex Kirhenshtein <alk@netxms.org>
endef

define Package/netxms/Default/description
	Meta-package for NetXMS agent
endef

define Package/netxms-base
$(call Package/netxms/Default)
	SUBMENU:=
	SECTION:=libs
	CATEGORY:=Libraries
	VARIANT:=$(1)
	TITLE+= common libraries$(2)
	DEPENDS:=+libexpat +libpthread +libstdcpp +zlib +curl \
					 +libssh +libpcrecpp +libpcre2-16 +libpcre2-32 +libpcre \
					 +libpcre2 +libpcre16 \
					 +GCC_LIBSSP:libssp +USE_GLIBC:librt +USE_GLIBC:libpthread \
					 $(3)
endef

define Package/netxms-base/description
$(call Package/netxms/Default/description)
	Common NetXMS libraries and tools
endef

Package/netxms-base-ssl=$(call Package/netxms-base,ssl,,+libopenssl)
Package/netxms-base-nossl=$(call Package/netxms-base,nossl, (no SSL))

define Package/netxms-agent
$(call Package/netxms/Default)
	VARIANT:=$(1)
	TITLE+= agent$(2)
	DEPENDS:=+libexpat +libpthread +libstdcpp +zlib +curl \
					 +libssh +libpcrecpp +libpcre2-16 +libpcre2-32 +libpcre \
					 +libpcre2 +libpcre16 \
					 +GCC_LIBSSP:libssp +USE_GLIBC:librt +USE_GLIBC:libpthread \
					 +netxms-base-$(1) +libsqlite3
endef

define Package/netxms-agent/description
$(call Package/netxms/Default/description)
	NetXMS agent
endef

Package/netxms-agent-ssl=$(call Package/netxms-agent,ssl)
Package/netxms-agent-nossl=$(call Package/netxms-agent,nossl, (no SSL))

ifeq ($(BUILD_VARIANT),nossl)
	CONFIG_NXAGENTD_NOSSL:=y
endif

## see if pcre hax here
# CONFIGURE_VARS +=
#         ac_cv_file__proc_stat=yes
#         ac_cv_file__proc_meminfo=yes
#         ac_cv_func_malloc_0_nonnull=yes
#         ac_cv_func_realloc_0_nonnull=yes
##
# 		  ~   --with-pcre $(PKG_BUILD_DIR)/usr/lib
# 			~   --with-client
# 			~   --disable-64bit
# 			~   --disable-iconv
# 			~   --with-internal-libtre
##
#
# 	$(TARGET_CPPFLAGS) == ....
# 	$(TARGET_LDFLAGS) == ....
#   PKG_BUILD_DIR == netxms3.4.3.
#   ${BUILD_DIR} /dev/shm/openwrt-sdk-mvebu-cortexa53_gcc-8.3.0_musl.Linux-x86_64/build_dir/target-aarch64_cortex-a53_musl/
##
## 			Note:
##            >		--with-pcre=$(STAGING_DIR)/usr
##            configure.ac appends with-path + "/lib"
define Build/Configure
	$(call Build/Configure/Default, \
	--with-client --with-pcre=$(STAGING_DIR)/usr \
 		$(if $(CONFIG_NXAGENTD_NOSSL),--disable-encryption,) \
	)
endef

# TARGET_LDFLAGS += -lpcre32 -lpcre -lpcre2-32
# -flto=jobserver -fuse-linker-plugin
##
# MAKE_VARS  :=
# MAKE_FLAGS +=
#         EXTRA_CFLAGS="$(TARGET_CFLAGS) $(TARGET_CPPFLAGS)"
#         EXTRA_LDFLAGS="$(TARGET_LDFLAGS)"
#
# ac_cv_search_tgetent="$(TARGET_LDFLAGS) -lpcre32"
##
# CONFIGURE_VARS+=
# 	ac_cv_search_tgetent="$(TARGET_LDFLAGS) -lpcre32 -lpcre2-32"

# LDFLAGS+=$(LDFLAGS) -lpcre32 -lpcre2-32

define Package/netxms-base-$(BUILD_VARIANT)/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/nxencpasswd $(1)/usr/bin/
	$(INSTALL_DIR) $(1)/usr/lib
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libnetxms.so* $(1)/usr/lib/
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libnxclient.so* $(1)/usr/lib/
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libnxdb.so* $(1)/usr/lib/
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libnxsnmp.so* $(1)/usr/lib/
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libnxtre.so* $(1)/usr/lib/
endef

define Package/netxms-agent-$(BUILD_VARIANT)/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/nxagentd $(1)/usr/bin/
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/nxappget $(1)/usr/bin/
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/nxapush $(1)/usr/bin/
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/nxevent $(1)/usr/bin/
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/nxpush $(1)/usr/bin/
	$(INSTALL_DIR) $(1)/usr/lib
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libnxagent.so* $(1)/usr/lib/
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libappagent.so* $(1)/usr/lib/
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libnxlp.so* $(1)/usr/lib/
	$(INSTALL_DIR) $(1)/usr/lib/netxms
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/netxms/*.nsm $(1)/usr/lib/netxms/
	$(INSTALL_DIR) $(1)/usr/lib/netxms/dbdrv
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/netxms/dbdrv/sqlite.ddr $(1)/usr/lib/netxms/dbdrv/
endef

$(eval $(call BuildPackage,netxms-base-ssl))
$(eval $(call BuildPackage,netxms-base-nossl))
$(eval $(call BuildPackage,netxms-agent-ssl))
$(eval $(call BuildPackage,netxms-agent-nossl))
