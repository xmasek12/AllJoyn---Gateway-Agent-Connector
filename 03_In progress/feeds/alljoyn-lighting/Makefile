AJ_SERVICE:=lighting
AJ_SERVICE_NAME:=Lighting

include $(TOPDIR)/rules.mk

PKG_NAME:=alljoyn-$(AJ_SERVICE)
PKG_VERSION:=14.12.00
PKG_RELEASE:=1
PKG_BUILD_DIR:=$(BUILD_DIR)/$(PKG_NAME)-$(PKG_VERSION)-src
PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION)-src.tar.gz
#PKG_SOURCE_URL:=file:///home/tomi/Dokumenty/OpenWRT/barrier_breaker/dl/
PKG_SOURCE_URL:=https://allseenalliance.org/releases/alljoyn/$(PKG_VERSION)/
PKG_MD5SUM:=

# SCons supports parallel builds but does not support make's jobserver
PKG_BUILD_PARALLEL:=$(if $(CONFIG_PKG_BUILD_USE_JOBSERVER),0,1)

include $(INCLUDE_DIR)/package.mk

ifneq ($(CONFIG_CCACHE),)
	TARGET_CC=$(TARGET_CC_NOCACHE)
	TARGET_CXX=$(TARGET_CXX_NOCACHE)
endif

ALLJOYN_BINDINGS:=cpp,

define Package/$(PKG_NAME)
  TITLE:=AllJoyn $(AJ_SERVICE_NAME)
  SECTION:=net
  CATEGORY:=Network
  DEPENDS:=alljoyn \
    +alljoyn-about \
    +alljoyn-config \
    +alljoyn-services_common \
    +alljoyn-notification\
    +libstdcpp \
    +libpthread \
    +librt
  URL:=http://www.allseenalliance.org
  MAINTAINER:=AllSeen Alliance <allseen-core@lists.allseenalliance.org>
  MENU:=1
endef

define Package/$(PKG_NAME)/description
AllJoyn $(AJ_SERVICE_NAME)
endef


ifeq ($(CONFIG_DEBUG),y)
  ALLJOYN_BUILD_VARIANT:=debug
else
  ALLJOYN_BUILD_VARIANT:=release
endif

TARGET_CFLAGS+=-fPIC
TARGET_CPPFLAGS+=-fPIC

define Build/Configure/Default
# Override default to do nothing
endef

define Build/Compile
	scons -C $(PKG_BUILD_DIR)/core/service_framework \
		$(PKG_JOBS) \
		"LIBXML2_BASE=$$(STAGING_DIR)/usr/include/libxml2" \
		BT=off \
		WS=off \
		V=1 \
		CPU=openwrt \
		OS=openwrt \
		BR=off \
		ICE=off \
		"VARIANT=$(ALLJOYN_BUILD_VARIANT)" \
		"BINDINGS=$(ALLJOYN_BINDINGS)" \
		"TARGET_CC=$$(TARGET_CC)" \
		"TARGET_CXX=$$(TARGET_CXX)" \
		"TARGET_CFLAGS=$$(TARGET_CFLAGS)" \
		"TARGET_CPPFLAGS=$$(TARGET_CPPFLAGS)" \
		"TARGET_PATH=$$(TARGET_PATH)" \
		"TARGET_LINKFLAGS=$$(TARGET_LDFLAGS)" \
		"TARGET_LINK=$$(TARGET_CC)" \
		"TARGET_AR=$$(TARGET_AR)" \
		"TARGET_RANLIB=$$(TARGET_RANLIB)" \
		"STAGING_DIR=$$(STAGING_DIR)"
endef

define Package/$(PKG_NAME)/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/core/service_framework/build/linux/standard_core_library/lighting_controller_service/bin/lighting_controller_service $(1)/usr/bin
endef

$(eval $(call BuildPackage,$(PKG_NAME)))

