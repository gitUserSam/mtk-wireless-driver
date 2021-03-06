# All rights reserved.
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.

include $(TOPDIR)/rules.mk
include $(INCLUDE_DIR)/kernel.mk

PKG_NAME:=mt7615e
P4REV:=
#PKG_VERSION:=MT7615_LinuxAP_V4.4.0.2_20160228
#PKG_SOURCE:=MT7615_LinuxAP_V4.4.0.2_20160228.tar.bz2
PKG_VERSION:=MT7622_7615_SoftAP_v5.0.2.0_20180430
PKG_SOURCE:=MT7622_7615_SoftAP_v5.0.2.0_20180430.tar.xz
PKG_SOURCE_URL:=http://luhu.vn:85/
PKG_HASH:=6b15c73d60c97e7f29f174491dd36946d3ecc516599b8196776aa0b12bd1af89
PKG_BUILD_DIR:=$(KERNEL_BUILD_DIR)/$(PKG_NAME)
PKG_KCONFIG:= \
	AP_SUPPORT RT_FIRST_CARD RT_SECOND_CARD RT_FIRST_IF_RF_OFFSET RT_SECOND_IF_RF_OFFSET \
	MT_WIFI WIFI_BASIC_FUNC MT_WIFI_PATH FIRST_IF_NONE FIRST_IF_EEPROM_FLASH RIRST_IF_EEPROM_EFUSE \
	RT_FIRST_CARD_EEPROM SECOND_IF_NONE SECOND_IF_EEPROM_FLASH SECOND_IF_EEPROM_PROM \
	SECOND_IF_EEPROM_EFUSE RT_SECOND_CARD_EEPROM MULTI_INF_SUPPORT WIFI_BASIC_FUNC \
	WIRELESS_EXT WEXT_SPY WEXT_PRIV DOT11_N_SUPPORT DOT11_VHT_AC WIFI_DRIVER G_BAND_256QAM_SUPPORT \
	BRCM_256QAM_SUPPORT ICAP_SUPPORT MT_AP_SUPPORT BACKGROUND_SCAN_SUPPORT SMART_CARRIER_SENSE_SUPPORT \
	MT_DFS_SUPPORT HDR_TRANS_TX_SUPPORT CHIP_MT7615E HDR_TRANS_RX_SUPPORT DBDC_MODE \
	SUPPORT_DYNAMIC_TXOP WSC_INCLUDED MT_STA_SUPPORT WSC_V2_SUPPORT DOT11W_PMF_SUPPORT TXBF_SUPPORT \
	IGMP_SNOOP_SUPPORT RATE_ADAPTION RATE_ADAPT_AGBS_SUPPORT RTMP_FLASH_SUPPORT ATE_SUPPORT \
	UAPSD RLT_MAC RLT_BBP RLT_RF RTMP_MAC RTMP_BBP RTMP_RF RTMP_PCI_SUPPORT RTMP_USB_SUPPORT \
	RTMP_RBUS_SUPPORT WIFI_MODE_AP WIFI_MODE_STA WIRELESS_EXT WEXT_SPY WEXT_PRIV WDS_SUPPORT \
	MBSS_SUPPORT APCLI_SUPPORT APCLI_CERT_SUPPORT MAC_REPEATER_SUPPORT RALINK_RT6352 RALINK_MT7620 \
	RALINK_MT7603E MU_RA_SUPPORT CON_WPS_SUPPORT VOW_SUPPORT BAND_STEERING TXOP_ARBITER  \
	CFG_SUPPORT_DYNAMIC_TXOP WIFI_MODE_BOTH WIFI_RLT_MAC RLT_MAC WIFI_RTMP_MAC RTMP_MAC WIFI_MT_MAC \
	MT_MAC CHIP_MT7603E CHIP_MT7615E MT_MAC RATE_ADAPTION SUPPORT_OPENWRT SDK_USER_LIGHTY \
	MUMIMO_SUPPORT MU_RA_SUPPORT LED_CONTROL_SUPPORT RA_HW_NAT RA_HW_NAT_WIFI_NEW_ARCH WIFI_SPECTRUM_SUPPORT

#PKG_KCONFIG += $(file < configs.txt)
PKG_KCONFIG += $(shell cat configs.txt)

PKG_CONFIG_DEPENDS:=$(foreach c, $(PKG_KCONFIG),$(if $(CONFIG_$c),CONFIG_$(c)))

include $(INCLUDE_DIR)/package.mk

TAR_CMD=$(HOST_TAR) -C $(1)/ $(TAR_OPTIONS)

define KernelPackage/mt7615e
  CATEGORY:=MTK Properties
  TITLE:=MTK MT7615e wifi AP driver
  FILES:=$(PKG_BUILD_DIR)/mt_wifi_ap/mt7615e.ko
  SUBMENU:=Drivers
  MENU:=1
endef

define KernelPackage/mt7615e/config
#source "$(SOURCE)/config.in-4.4"
#source "$(SOURCE)/config.in"
	source "$(SOURCE)/Kconfig.mt_wifi"
endef

define Build/Compile
	#-mv $(PKG_BUILD_DIR)/wifi_driver $(PKG_BUILD_DIR)/mt_wifi
	#-mkdir $(PKG_BUILD_DIR)/mt_wifi_ap
	cp $(PKG_BUILD_DIR)/mt_wifi/os/linux/Makefile.mt_wifi_ap $(PKG_BUILD_DIR)/mt_wifi_ap/Makefile
	make -C $(PKG_BUILD_DIR)/mt_wifi/embedded build_tools	
	$(MAKE) -C "$(LINUX_DIR)" V=1 \
		CROSS_COMPILE="$(TARGET_CROSS)" \
		ARCH="$(LINUX_KARCH)" \
		SUBDIRS="$(PKG_BUILD_DIR)/mt_wifi_ap" \
		$(foreach c, $(PKG_KCONFIG),$(if $(CONFIG_$c),CONFIG_$(c)=$(CONFIG_$(c)))) \
		modules
endef

WIRELESS_DIR=etc/wireless/mt7615e/
define KernelPackage/mt7615e/install
	$(INSTALL_DIR) $(1)/etc/init.d/
	$(INSTALL_BIN) ./files/mt7615e $(1)/etc/init.d/
	$(INSTALL_DIR) $(1)/lib/firmware/
	$(INSTALL_BIN) ./files/mt7615e.eeprom.bin $(1)/lib/firmware/
	$(INSTALL_DIR) $(1)/$(WIRELESS_DIR)
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/mt_wifi/txpwr/sku_tables/7615_SingleSKU_1.dat $(1)/$(WIRELESS_DIR)/SingleSKU.dat ; \
	echo $(PKG_SOURCE) > $(1)/$(WIRELESS_DIR)/version

	if [ "$$(CONFIG_RT_FIRST_CARD)" = "7615" ] || [ "$$(CONFIG_RT_FIRST_CARD)" = "7615e" ]; then \
		cat $(PKG_BUILD_DIR)/wlan_cfg/mt7621/MT7615_1_ap.dat | sed 's/SSID1=.*/SSID1=openwrt-r3p/g ; s/WirelessMode=.*/WirelessMode=9/g ; s/Channel=.*/Channel=6/g ; s/E2pAccessMode=.*/E2pAccessMode=2/g ; s/FixedTxMode=.*/FixedTxMode=HT/g' > ./files/24g.dat ; \
		$(INSTALL_BIN) ./files/24g.dat $(1)/$(WIRELESS_DIR)/mt7615e.1.dat ; \
		$(INSTALL_BIN) ./files/mt7615e.1.eeprom.bin $(1)/lib/firmware/ ; \
	fi
	if [ "$$(CONFIG_RT_SECOND_CARD)" = "7615" ] || [ "$$(CONFIG_RT_SECOND_CARD)" = "7615e" ]; then \
		cat $(PKG_BUILD_DIR)/wlan_cfg/mt7621/MT7615_1_ap.dat | sed 's/SSID1=.*/SSID1=openwrt-r3p-5G/g ; s/WirelessMode=.*/WirelessMode=14/g ; s/Channel=.*/Channel=136/g ; s/E2pAccessMode=.*/E2pAccessMode=2/g' > ./files/5g.dat ; \
		$(INSTALL_BIN) ./files/5g.dat $(1)/$(WIRELESS_DIR)/mt7615e.2.dat ; \
		$(INSTALL_BIN) ./files/mt7615e.2.eeprom.bin $(1)/lib/firmware/ ; \
	fi
endef

$(eval $(call KernelPackage,mt7615e))
