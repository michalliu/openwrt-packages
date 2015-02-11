#
# Copyright (C) 2007-2014 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=seafile-seahub
PKG_VERSION:=3.1.7
PKG_RELEASE=$(PKG_SOURCE_VERSION)
PKG_LICENSE:=Apache-2.0

PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL:=https://github.com/haiwen/seahub.git
PKG_SOURCE_SUBDIR:=$(PKG_NAME)-$(PKG_VERSION)
PKG_SOURCE_VERSION:=a984c86442e7fc10c3bcf6d92b4843f7263547eb
PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION)-$(PKG_SOURCE_VERSION).tar.gz

include $(INCLUDE_DIR)/package.mk
$(call include_mk, python-package.mk)

define Package/seafile-seahub
    SECTION:=net
    CATEGORY:=Network
    TITLE:=Seafile server - seahub component
    MAINTAINER:=Gergely Kiss <mail.gery@gmail.com>
    URL:=http://seafile.com/
    DEPENDS:=+python +simplejson +python-imglib +python-setuptools
endef

define Package/seafile-seahub/description
   The web end of seafile server.

   NOTE: in order to have better performance, language support is turned off by default.
   Please set 'USE_I18N = True' in seahub_settings.py to support multiple languages.
endef

PKG_BUILD_DEPENDS:=python-setuptools
PYTHONPATH:=$(PYTHONPATH):$(PKG_BUILD_DIR)/thirdpart

define Build/Configure
endef

define Build/Compile
	# Install python dependencies
	if [ ! -f $(DL_DIR)/Django-1.5.8.tar.gz ];then \
	$(SCRIPT_DIR)/download.pl "$(DL_DIR)" "Django-1.5.8.tar.gz"           "675fc736e2c29090f005e217ccf90b5b" "https://www.djangoproject.com/m/releases/1.5/"; \
	fi
	if [ ! -f $(DL_DIR)/djblets-release-0.6.14.tar.gz ];then \
	$(SCRIPT_DIR)/download.pl "$(DL_DIR)" "djblets-release-0.6.14.tar.gz" "3ac8ebd83d1543dd3b84b4bf3b748aa4" "https://github.com/djblets/djblets/archive/"; \
	fi
	if [ ! -f $(DL_DIR)/gunicorn-0.16.1.tar.gz ];then \
	$(SCRIPT_DIR)/download.pl "$(DL_DIR)" "gunicorn-0.16.1.tar.gz"        "d53d5d04d941f2a3089e814e753a218f" "https://pypi.python.org/packages/source/g/gunicorn/"; \
	fi
	if [ ! -f $(DL_DIR)/six-1.4.1.tar.gz ];then \
	$(SCRIPT_DIR)/download.pl "$(DL_DIR)" "six-1.4.1.tar.gz"              "bdbb9e12d3336c198695aa4cf3a61d62" "https://pypi.python.org/packages/source/s/six/"; \
	fi
	if [ ! -f $(DL_DIR)/chardet-2.1.1.tar.gz ];then \
	$(SCRIPT_DIR)/download.pl "$(DL_DIR)" "chardet-2.1.1.tar.gz"          "295367fd210d20f3febda615a88e1ef0" "https://pypi.python.org/packages/source/c/chardet/"; \
	fi
	if [ ! -f $(DL_DIR)/flup-1.0.2-py2.6.egg ];then \
	$(SCRIPT_DIR)/download.pl "$(DL_DIR)" "flup-1.0.2-py2.6.egg"          "93ec6e3baeee3e5649a8456105178d4e" "https://pypi.python.org/packages/2.6/f/flup/"; \
	fi
	if [ ! -f $(DL_DIR)/lockfile-0.9.1.tar.gz ];then \
	$(SCRIPT_DIR)/download.pl "$(DL_DIR)" "lockfile-0.9.1.tar.gz"         "ce61468d4c1263e3005737bbed2641f0" "https://pypi.python.org/packages/source/l/lockfile/"; \
	fi
	if [ ! -f $(DL_DIR)/python-daemon-1.5.5.tar.gz ];then \
	$(SCRIPT_DIR)/download.pl "$(DL_DIR)" "python-daemon-1.5.5.tar.gz"    "1f6cd41473c2e201021a0aeef395b2b1" "https://pypi.python.org/packages/source/p/python-daemon/"; \
	fi
	if [ ! -f $(DL_DIR)/python-dateutil-1.5.tar.gz ];then \
	$(SCRIPT_DIR)/download.pl "$(DL_DIR)" "python-dateutil-1.5.tar.gz"    "0dcb1de5e5cad69490a3b6ab63f0cfa5" "https://pypi.python.org/packages/source/p/python-dateutil/"; \
	fi
	$(call HostPython,, \
		$(STAGING_DIR_ROOT)/usr/bin/easy_install -d $(PKG_BUILD_DIR)/thirdpart -Z -N \
		file:///$(DL_DIR)/Django-1.5.8.tar.gz)
	$(call HostPython,, \
		$(STAGING_DIR_ROOT)/usr/bin/easy_install -d $(PKG_BUILD_DIR)/thirdpart -Z -N \
		file:///$(DL_DIR)/djblets-release-0.6.14.tar.gz)
	$(call HostPython,, \
		$(STAGING_DIR_ROOT)/usr/bin/easy_install -d $(PKG_BUILD_DIR)/thirdpart -Z -N \
		file:///$(DL_DIR)/gunicorn-0.16.1.tar.gz)
	$(call HostPython,, \
		$(STAGING_DIR_ROOT)/usr/bin/easy_install -d $(PKG_BUILD_DIR)/thirdpart -Z -N \
		file:///$(DL_DIR)/six-1.4.1.tar.gz)
	$(call HostPython,, \
		$(STAGING_DIR_ROOT)/usr/bin/easy_install -d $(PKG_BUILD_DIR)/thirdpart -Z -N \
		file:///$(DL_DIR)/chardet-2.1.1.tar.gz)
	$(call HostPython,, \
		$(STAGING_DIR_ROOT)/usr/bin/easy_install -d $(PKG_BUILD_DIR)/thirdpart -Z -N \
		file:///$(DL_DIR)/flup-1.0.2-py2.6.egg)
	$(call HostPython,, \
		$(STAGING_DIR_ROOT)/usr/bin/easy_install -d $(PKG_BUILD_DIR)/thirdpart -Z -N \
		file:///$(DL_DIR)/lockfile-0.9.1.tar.gz)
	$(call HostPython,, \
		$(STAGING_DIR_ROOT)/usr/bin/easy_install -d $(PKG_BUILD_DIR)/thirdpart -Z -N \
		file:///$(DL_DIR)/python-daemon-1.5.5.tar.gz)
	$(call HostPython,, \
		$(STAGING_DIR_ROOT)/usr/bin/easy_install -d $(PKG_BUILD_DIR)/thirdpart -Z -N \
		file:///$(DL_DIR)/python-dateutil-1.5.tar.gz)
endef

define Package/seafile-seahub/install
	$(INSTALL_DIR) $(1)/usr/share/seafile/seafile-server/seahub
	$(CP) $(PKG_BUILD_DIR)/{locale,media,seahub,sql,subdomain,tests,thirdpart,tools} $(1)/usr/share/seafile/seafile-server/seahub/
	$(CP) $(PKG_BUILD_DIR)/*.{sh,template,py,txt} $(1)/usr/share/seafile/seafile-server/seahub/
	$(CP) $(PKG_BUILD_DIR)/{CONTRIBUTORS,HACKING,README.markdown} $(1)/usr/share/seafile/seafile-server/seahub/
	$(CP) $(PKG_BUILD_DIR)/pylintrc* $(1)/usr/share/seafile/seafile-server/seahub/
	# fix python exec path in scripts
	sed -i 's/#!.*/#!\/usr\/bin\/python/g' $(1)/usr/share/seafile/seafile-server/seahub/thirdpart/chardetect.py
	sed -i 's/#!.*/#!\/usr\/bin\/python/g' $(1)/usr/share/seafile/seafile-server/seahub/thirdpart/django-admin.py
	sed -i 's/#!.*/#!\/usr\/bin\/python/g' $(1)/usr/share/seafile/seafile-server/seahub/thirdpart/gunicorn
	sed -i 's/#!.*/#!\/usr\/bin\/python/g' $(1)/usr/share/seafile/seafile-server/seahub/thirdpart/gunicorn_django
	sed -i 's/#!.*/#!\/usr\/bin\/python/g' $(1)/usr/share/seafile/seafile-server/seahub/thirdpart/gunicorn_paster
	find $(1) -name "*\.pyc" -o -name "*\.pyo" | xargs rm -f
	sed -i "s/\(SEAFILE_VERSION\s*=\s*\)'\([0-9]\.[0-9]\.[0-9]\)'/\1'$(PKG_VERSION)'/g" $(1)/usr/share/seafile/seafile-server/seahub/seahub/settings.py
endef

$(eval $(call BuildPackage,seafile-seahub))
