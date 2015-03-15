#
# Copyright (C) 2008 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#
#

include $(TOPDIR)/rules.mk

PKG_NAME:=gcc
ifeq ($(CONFIG_PACKAGE_gcc_version_4_8_3),y)
  PKG_VERSION:=4.8.3
  PKG_MD5SUM:=7c60f24fab389f77af203d2516ee110f
endif
ifeq ($(CONFIG_PACKAGE_gcc_version_4_9_2),y)
  PKG_VERSION:=4.9.2
  PKG_MD5SUM:=4df8ee253b7f3863ad0b86359cd39c43
endif
ifeq ($(CONFIG_PACKAGE_gcc_version_5_0_0),y)
  PKG_VERSION:=5.0.0
  PKG_MD5SUM:=bf54a676e81dadcba2cd9cf591a3e4d6
endif
PKG_RELEASE:=1
PKG_SOURCE_URL:=ftp://ftp.fu-berlin.de/unix/languages/gcc/releases/gcc-$(PKG_VERSION) \
     http://mirrors.rcn.net/pub/sourceware/gcc/releases/gcc-$(PKG_VERSION) \
    ftp://ftp.gnu.org/gnu/gcc/releases/gcc-$(PKG_VERSION)

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.bz2
PKG_INSTALL:=1
PKG_FIXUP:=libtool
PKG_BUILD_PARALLEL:=1
# PATCH_DIR must be defined before include package.mk
PATCH_DIR=./patches/$(PKG_VERSION)

include $(INCLUDE_DIR)/package.mk

SEP:=,
TARGET_LANGUAGES:="c,c++$(if $(CONFIG_PACKAGE_gcc_java_lang),$(SEP)java)$(if $(CONFIG_PACKAGE_gcc_fortran_lang),$(SEP)fortran)$(if $(CONFIG_PACKAGE_gcc_go_lang),$(SEP)go)"
BUGURL=https://dev.openwrt.org/
PKGVERSION=OpenWrt GCC $(PKG_VERSION)

# not using sstrip here as this fucks up the .so's somehow
STRIP:=$(TOOLCHAIN_DIR)/bin/$(TARGET_CROSS)strip
RSTRIP:= \
       NM="$(TOOLCHAIN_DIR)/bin/$(TARGET_CROSS)nm" \
       STRIP="$(STRIP)" \
       STRIP_KMOD="$(STRIP) --strip-debug" \
       $(SCRIPT_DIR)/rstrip.sh


define Package/gcc
  SECTION:=devel
  CATEGORY:=Development
  TITLE:=gcc
  MAINTAINER:=Christian Beier <cb@shoutrlabs.com>
  DEPENDS:= +binutils +libstdcpp
endef

define Package/gcc/description
	build a native toolchain for compiling on target
endef

define Package/gcc/config
  choice
    depends on PACKAGE_gcc
    prompt "GCC compiler Version"
    default PACKAGE_gcc_version_4_8_3

    config PACKAGE_gcc_version_4_8_3
      bool "gcc 4.8.3"

    config PACKAGE_gcc_version_4_9_2
      bool "gcc 4.9.2"

    config PACKAGE_gcc_version_5_0_0
      bool "gcc 5.0.0 (bleeding edge rev 221263, 20150309)"

  endchoice
  menu "GCC build options"
    depends on PACKAGE_gcc
    config PACKAGE_gcc_java_lang
      bool "supports java lang"
      default n
      help
        build gcc supports java.
    config PACKAGE_gcc_fortran_lang
      bool "supports fortran lang"
      default n
      help
        build gcc supports fortran.
    config PACKAGE_gcc_go_lang
      bool "supports go lang"
      default n
      help
        build gcc supports go.
  endmenu
endef

MPFR=mpfr-2.4.2.tar.bz2
GMP=gmp-4.3.2.tar.bz2
MPC=mpc-0.8.1.tar.gz
ISL=isl-0.12.2.tar.bz2
ISL_14=isl-0.14.tar.bz2
CLOOG=cloog-0.18.1.tar.gz
define Build/Prepare
	$(PKG_UNPACK)
# 	we have to download additional stuff before patching
	if [ -f $(DL_DIR)/$(MPFR) ];then \
		ln -s $(DL_DIR)/$(MPFR) $(PKG_BUILD_DIR)/$(MPFR); \
	fi; \
	if [ -f $(DL_DIR)/$(GMP) ];then \
		ln -s $(DL_DIR)/$(GMP) $(PKG_BUILD_DIR)/$(GMP); \
	fi; \
	if [ -f $(DL_DIR)/$(MPC) ];then \
		ln -s $(DL_DIR)/$(MPC) $(PKG_BUILD_DIR)/$(MPC); \
	fi; \
	if [ -f $(DL_DIR)/$(ISL) ];then \
		ln -s $(DL_DIR)/$(ISL) $(PKG_BUILD_DIR)/$(ISL); \
	fi; \
	if [ -f $(DL_DIR)/$(ISL_14) ];then \
		ln -s $(DL_DIR)/$(ISL_14) $(PKG_BUILD_DIR)/$(ISL_14); \
	fi; \
	if [ -f $(DL_DIR)/$(CLOOG) ];then \
		ln -s $(DL_DIR)/$(CLOOG) $(PKG_BUILD_DIR)/$(CLOOG); \
	fi
	(cd $(PKG_BUILD_DIR) && sed -i "s/wget/wget -nc/g" ./contrib/download_prerequisites)
	(cd $(PKG_BUILD_DIR) && ./contrib/download_prerequisites)
	$(Build/Patch)
endef


define Build/Configure
	(cd $(PKG_BUILD_DIR); rm -f config.cache; \
		SHELL="$(BASH)" \
		$(TARGET_CONFIGURE_OPTS) \
		$(PKG_BUILD_DIR)/configure \
			$(CONFIGURE_ARGS) \
			--build=$(GNU_HOST_NAME) \
			--host=$(REAL_GNU_TARGET_NAME) \
			--target=$(REAL_GNU_TARGET_NAME) \
			--enable-languages=$(TARGET_LANGUAGES) \
			--with-bugurl=$(BUGURL) \
			--with-pkgversion="$(PKGVERSION)" \
			--enable-shared \
			--disable-__cxa_atexit \
			--enable-target-optspace \
			--with-gnu-ld \
			--disable-nls \
			--disable-libmudflap \
			--disable-multilib \
			--disable-libgomp \
			--disable-libquadmath \
			--disable-libssp \
			--disable-decimal-float \
			--disable-libstdcxx-pch \
			--with-host-libstdcxx=-lstdc++ \
			--prefix=/usr \
			--libexecdir=/usr/lib \
			$(SOFT_FLOAT_CONFIG_OPTION) \
			$(call qstrip,$(CONFIG_EXTRA_GCC_CONFIG_OPTIONS)) \
			$(if $(CONFIG_mips64)$(CONFIG_mips64el),--with-arch=mips64 \
			--with-abi=$(subst ",,$(CONFIG_MIPS64_ABI))) \
	);
endef

define Build/Compile
	export SHELL="$(BASH)"; $(MAKE_VARS) $(MAKE) -C $(PKG_BUILD_DIR) \
			DESTDIR="$(PKG_INSTALL_DIR)" $(MAKE_ARGS) all install
endef

# $(1) build_dir/target-mips.../gcc-4.9.2/ipkg-ramips_1004kc/gcc
# $(PKG_INSTALL_DIR) build_dir/target-mips.../gcc-4.9.2/ipkg-install
#
define Package/gcc/install
	$(INSTALL_DIR) $(1)/usr/bin $(1)/usr/lib $(1)/usr/lib/$(PKG_NAME)/$(REAL_GNU_TARGET_NAME)/$(PKG_VERSION)
	cp -ar $(PKG_INSTALL_DIR)/usr/include $(1)/usr
	cp -a $(PKG_INSTALL_DIR)/usr/bin/{$(REAL_GNU_TARGET_NAME)-{g++,gcc},cpp,gcov} $(1)/usr/bin
	ln -s $(REAL_GNU_TARGET_NAME)-g++ $(1)/usr/bin/c++
	ln -s $(REAL_GNU_TARGET_NAME)-g++ $(1)/usr/bin/g++
	ln -s $(REAL_GNU_TARGET_NAME)-g++ $(1)/usr/bin/$(REAL_GNU_TARGET_NAME)-c++
	ln -s $(REAL_GNU_TARGET_NAME)-gcc $(1)/usr/bin/gcc
	ln -s $(REAL_GNU_TARGET_NAME)-gcc $(1)/usr/bin/$(REAL_GNU_TARGET_NAME)-gcc-$(PKG_VERSION)
	cp -ar $(PKG_INSTALL_DIR)/usr/lib/gcc $(1)/usr/lib
	$(RM) $(1)/usr/lib/$(PKG_NAME)/$(REAL_GNU_TARGET_NAME)/$(PKG_VERSION)/*.a
	cp -ar $(TOOLCHAIN_DIR)/include $(1)/usr/lib/$(PKG_NAME)/$(REAL_GNU_TARGET_NAME)/$(PKG_VERSION)
	cp -a $(TOOLCHAIN_DIR)/lib/*.{o,so*} $(1)/usr/lib/$(PKG_NAME)/$(REAL_GNU_TARGET_NAME)/$(PKG_VERSION)
	cp -a $(TOOLCHAIN_DIR)/lib/*nonshared*.a  $(1)/usr/lib/$(PKG_NAME)/$(REAL_GNU_TARGET_NAME)/$(PKG_VERSION)
	grep "GROUP.*-lgcc" $(1)/usr/lib/$(PKG_NAME)/$(REAL_GNU_TARGET_NAME)/$(PKG_VERSION)/libgcc_s.so && cp -a $(PKG_INSTALL_DIR)/usr/lib/gcc/$(REAL_GNU_TARGET_NAME)/$(PKG_VERSION)/libgcc.a $(1)/usr/lib/$(PKG_NAME)/$(REAL_GNU_TARGET_NAME)/$(PKG_VERSION)/ ; true
	if [ -f $(PKG_INSTALL_DIR)/usr/bin/$(REAL_GNU_TARGET_NAME)-gccgo ];then \
	cp -a $(PKG_INSTALL_DIR)/usr/bin/{$(REAL_GNU_TARGET_NAME)-gccgo $(1)/usr/bin;\
	ln -s $(REAL_GNU_TARGET_NAME)-gccgo $(1)/usr/bin/gccgo;\
	cp -ar $(PKG_INSTALL_DIR)/usr/lib/go $(1)/usr/lib/;\
	$(RM) $(1)/usr/lib/$(PKG_NAME)/$(REAL_GNU_TARGET_NAME)/$(PKG_VERSION)/libgo.so*;\
	cp -ar $(PKG_INSTALL_DIR)/usr/lib/libgo*.a $(1)/usr/lib/$(PKG_NAME)/$(REAL_GNU_TARGET_NAME)/$(PKG_VERSION);\
	cp -ar $(PKG_INSTALL_DIR)/usr/lib/libgo.so* $(1)/usr/lib/$(PKG_NAME)/$(REAL_GNU_TARGET_NAME)/$(PKG_VERSION);\
	fi
endef

$(eval $(call BuildPackage,gcc))
