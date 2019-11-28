# DeviceOwnerDemo
MDM device Admin &amp; device owner Demo


Device Owner
概述
DeviceOwner 译为设备所有者，在Android5.0系统推出。DeviceOwner涵盖了DeviceAdmin用户的所有管理能力，也涵盖了ProfileOwner的所有管理能力，并且在这些基础上额外添加了一些管理权限，如重启设备、禁用状态栏等。Android提供的三种权限管理策略的能力大小依次为 DeviceAdmin < ProfileOwner < DeviceOwner。

Android系统只能设置一个DeviceOwner程序，并且该程序在设置为DeviceOwner后不能取消，应用不能卸载，唯一可以取消的途径是恢复出厂设置。并且，DeviceOwner应用和ProfileOwner也会产生冲突，系统只能有一个DeviceOwner应用或者ProfileOwner应用。

DeviceOwner 的设置和能力
要使一个应用成为DeviceOwner，首先这个程序必须是一个DeviceAdmin，按照DeviceAdmin的标准流程配置一个程序，回顾往期文章Android Device Administration 应用的能力。
将配置好的程序设置为DeviceOwner之前，不必刻意去激活DeviceAdmin，系统在设置DeviceOwner的过程中会自动先激活DeviceAdmin，这也是DeviceOwner拥有DeviceAdmin所有能力的原因。
第三方应用和系统应用都没有权限设置DeviceOwner，Android官方值提供两种设置DeviceOwner应用的方法：

通过终端adb shell
通过NFC
了解官方方法和自定义实现方案，请跳转至一键设置 DeviceAdmin/ProfileOwner/DeviceOwner 应用

系统成功设置DeviceOwner后会生成/data/system/device_owner_2.xml 文件，该文件记录了系统最高管理权限程序的基本信息：

<?xml version='1.0' encoding='utf-8' standalone='yes' ?>
<root>
<device-owner package="com.action.deviceadmin" name="Test Device Owner" component="com.action.deviceadmin/com.action.deviceadmin.DPMTestReceiver" userRestrictionsMigrated="true" />
<device-owner-context userId="0" />
</root>
1
2
3
4
5
是否为DeviceOwner
// 获取设备管理服务
mDevicePolicyManager = (DevicePolicyManager) getSystemService(Context.DEVICE_POLICY_SERVICE);
// 需要激活的DeviceAdminReceiver组件
mComponentName = new ComponentName(this, DPMTestReceiver.class);

isDeviceOwnerApp = mDevicePolicyManager.isDeviceOwnerApp(mComponentName.getPackageName());
Log.d(TAG, "isDeviceOwnerApp: " + isDeviceOwnerApp);
1
2
3
4
5
6
7
启用或禁用备份服务
private void setBackupServiceEnabled(ComponentName admin, boolean enabled) {
	    if (isDeviceOwnerApp) {
	        mDevicePolicyManager.setBackupServiceEnabled(admin, enabled);
	    }
	}
1
2
3
4
5
备份服务是否开启
private boolean isBackupServiceEnabled(ComponentName admin) {
	    boolean res = false;
	    if (isDeviceOwnerApp) {
	        res = mDevicePolicyManager.isBackupServiceEnabled(admin);
	    }
	    return res;
	}
1
2
3
4
5
6
7
重启设备
private void reboot(ComponentName admin) {
	    if (isDeviceOwnerApp) {
	        mDevicePolicyManager.reboot(admin);
	    }
	}
1
2
3
4
5
获取wifi Mac地址
private String getWifiMacAddress(ComponentName admin) {
	    String res = null;
	    if (isDeviceOwnerApp) {
	        res = mDevicePolicyManager.getWifiMacAddress(admin);
	    }
	    return res;
	}
1
2
3
4
5
6
7
设置状态栏的禁用或启用
private boolean setStatusBarDisabled(ComponentName admin, boolean disabled) {
	    boolean res = false;
	    if (isDeviceOwnerApp) {
	        res = mDevicePolicyManager.setStatusBarDisabled(admin, disabled);
	    }
	    return res;
	}
1
2
3
4
5
6
7
将锁屏模式设置为None，当用户设置了密码时无效
private boolean setKeyguardDisabled(ComponentName admin, boolean disabled) {
	    boolean res = false;
	    if (isDeviceOwnerApp) {
	        res = mDevicePolicyManager.setKeyguardDisabled(admin, disabled);
	    }
	    return res;
	}
1
2
3
4
5
6
7
设置系统更新策略
private void setSystemUpdatePolicy(ComponentName admin, SystemUpdatePolicy policy) {
	    if (isDeviceOwnerApp) {
	        mDevicePolicyManager.setSystemUpdatePolicy(admin, policy);
	    }
	}
1
2
3
4
5
获取系统更新策略
private SystemUpdatePolicy getSystemUpdatePolicy() {
	    SystemUpdatePolicy res = null;
	    if (isDeviceOwnerApp) {
	        res = mDevicePolicyManager.getSystemUpdatePolicy();
	    }
	    return res;
	}
1
2
3
4
5
6
7
设置系统设置中Global相关的属性
private void setGlobalSetting(ComponentName admin, String setting, String value) {
	    if (isDeviceOwnerApp) {
	        mDevicePolicyManager.setGlobalSetting(admin, setting, value);
	    }
	}
1
2
3
4
5
切换用户
private boolean switchUser(ComponentName admin, UserHandle userHandle) {
	    boolean res = false;
	    if (isDeviceOwnerApp) {
	        res = mDevicePolicyManager.switchUser(admin, userHandle);
	    }
	    return res;
	}
1
2
3
4
5
6
7
删除用户
private boolean removeUser(ComponentName admin, UserHandle userHandle) {
	    boolean res = false;
	    if (isDeviceOwnerApp) {
	        res = mDevicePolicyManager.removeUser(admin, userHandle);
	    }
	    return res;
	}
1
2
3
4
5
6
7
创建一个用户
private UserHandle createAndManageUser(ComponentName admin, String name, ComponentName profileOwner, PersistableBundle adminExtras,
            int flags) {
	    UserHandle res = null;
	    if (isDeviceOwnerApp) {
	        res = mDevicePolicyManager.createAndManageUser(admin, name, profileOwner, adminExtras, flags);
	    }
	    return res;
	}
1
2
3
4
5
6
7
8
设置锁屏界面显示的提示消息–如“小明的Device Owner设备”
private void setDeviceOwnerLockScreenInfo(ComponentName admin, CharSequence info) {
	    if (isDeviceOwnerApp) {
	        mDevicePolicyManager.setDeviceOwnerLockScreenInfo(admin, info);
	    }
	}
1
2
3
4
5
获取锁屏界面显示消息
private CharSequence getDeviceOwnerLockScreenInfo() {
	    CharSequence res = null;

	    if (isDeviceOwnerApp) {
	        res = mDevicePolicyManager.getDeviceOwnerLockScreenInfo();
	    }
	    return res;
	}
1
2
3
4
5
6
7
8
设置一个独立于网络的全局HTTP代理
private void setRecommendedGlobalProxy(ComponentName admin, ProxyInfo proxyInfo) {
	    if (isDeviceOwnerApp) {
	        mDevicePolicyManager.setRecommendedGlobalProxy(admin, proxyInfo);
	    }
	}
1
2
3
4
5
禁止/允许截屏
private void setScreenCaptureDisabled(ComponentName admin, boolean disabled) {
        if(isProfileOwnerApp) {
	        mDevicePolicyManager.setScreenCaptureDisabled(admin, disabled);
	    }
    }
1
2
3
4
5
是否禁止截图
private boolean getScreenCaptureDisabled(ComponentName admin) {
	    boolean res = false;

	    if (isProfileOwnerApp) {
	        res = mDevicePolicyManager.getScreenCaptureDisabled(admin);
	    }
	    return res;
	}
1
2
3
4
5
6
7
8
设置组织名
private void setOrganizationName(ComponentName admin, CharSequence title) {
	    if(isProfileOwnerApp) {
	        mDevicePolicyManager.setOrganizationName(admin, title);
	    }
	}
1
2
3
4
5
获取组织名
private CharSequence getOrganizationName(ComponentName admin) {
	    CharSequence res = null;

	    if (isProfileOwnerApp) {
	        res = mDevicePolicyManager.getOrganizationName(admin);
	    }
	    return res;
	}
1
2
3
4
5
6
7
8
通过包名设置应用程序的运行时权限状态
private boolean setPermissionGrantState(ComponentName admin, String packageName,
            String permission, int grantState) {
	    boolean res = false;

	    if (isProfileOwnerApp) {
	        res = mDevicePolicyManager.setPermissionGrantState(admin, packageName, permission, grantState);
	    }
	    return res;
	}
1
2
3
4
5
6
7
8
9
通过包名获取应用程序的运行时权限状态
private int getPermissionGrantState(ComponentName admin, String packageName,
            String permission) {
	    int res = 0;

	    if (isProfileOwnerApp) {
	        res = mDevicePolicyManager.getPermissionGrantState(admin, packageName, permission);
	    }
	    return res;
	}
1
2
3
4
5
6
7
8
9
允许应用程序自动授予或拒绝运行时权限请求
private void setPermissionPolicy(ComponentName admin, int policy) {
	    if(isProfileOwnerApp) {
	        mDevicePolicyManager.setPermissionPolicy(admin, policy);
	    }
	}
1
2
3
4
5
返回设备或配置文件所有者设置的当前运行时权限策略
private int getPermissionPolicy(ComponentName admin) {
	    int res = 0;

	    if (isProfileOwnerApp) {
	        res = mDevicePolicyManager.getPermissionPolicy(admin);
	    }
	    return res;
	}
1
2
3
4
5
6
7
8
设置用户图片
private void setUserIcon(ComponentName admin, Bitmap icon) {
	    if(isProfileOwnerApp) {
	        mDevicePolicyManager.setUserIcon(admin, icon);
	    }
	}
1
2
3
4
5
设置应用程序不可卸载或者可以卸载
private void setUninstallBlocked(ComponentName admin, String packageName,
            boolean uninstallBlocked) {
	    if(isProfileOwnerApp) {
	        mDevicePolicyManager.setUninstallBlocked(admin, packageName, uninstallBlocked);
	    }
	}
1
2
3
4
5
6
返回应用程序是否可卸载
private boolean isUninstallBlocked(ComponentName admin, String packageName) {
	    boolean res = false;

	    if (isProfileOwnerApp) {
	        res = mDevicePolicyManager.isUninstallBlocked(admin, packageName);
	    }
	    return res;
	}
1
2
3
4
5
6
7
8
设置静音
private void setMasterVolumeMuted(ComponentName admin, boolean on) {
	    if(isProfileOwnerApp) {
	        mDevicePolicyManager.setMasterVolumeMuted(admin, on);
	    }
	}
1
2
3
4
5
是否静音
private boolean isMasterVolumeMuted(ComponentName admin) {
	    boolean res = false;

	    if (isProfileOwnerApp) {
	        res = mDevicePolicyManager.isMasterVolumeMuted(admin);
	    }
	    return res;
	}
1
2
3
4
5
6
7
8
指定特定的服务组件作为内容提供者，用于向用户的本地或远程管理员发出权限请求
private void setRestrictionsProvider(ComponentName admin, ComponentName provider) {
	    if(isProfileOwnerApp) {
	        mDevicePolicyManager.setRestrictionsProvider(admin, provider);
	    }
	}
1
2
3
4
5
设置系统设置中安全相关的属性
private void setSecureSetting(ComponentName admin, String setting, String value) {
	    if(isProfileOwnerApp) {
	        mDevicePolicyManager.setSecureSetting(admin, setting, value);
	    }
	}
1
2
3
4
5
设置哪些应用程序能够在锁定界面显示
private void setLockTaskPackages(ComponentName admin, String[] packages) {
	    if (packages == null) return;

	    if(isProfileOwnerApp) {
	        mDevicePolicyManager.setLockTaskPackages(admin, packages);
	    }
	}
1
2
3
4
5
6
7
返回允许在锁定界面显示的包列表
private String[] getLockTaskPackages(ComponentName admin) {
	    String[] res = null;

	    if (isProfileOwnerApp) {
	        res = mDevicePolicyManager.getLockTaskPackages(admin);
	    }
	    return res;
	}
1
2
3
4
5
6
7
8
查询一个应用是否能够在锁定界面显示
private boolean isLockTaskPermitted(String packageName) {
	    boolean res = false;

	    if (isProfileOwnerApp) {
	        res = mDevicePolicyManager.isLockTaskPermitted(packageName);
	    }
	    return res;
	}
1
2
3
4
5
6
7
8
禁用特定类型的帐户
private void setAccountManagementDisabled(ComponentName admin, String accountType,
            boolean disabled) {
	    if(isProfileOwnerApp) {
	        mDevicePolicyManager.setAccountManagementDisabled(admin, accountType, disabled);
	    }
	}
1
2
3
4
5
6
获取禁用的账户列表
private String[] getAccountTypesWithManagementDisabled() {
	    String[] res = null;

	    if (isProfileOwnerApp) {
	        res = mDevicePolicyManager.getAccountTypesWithManagementDisabled();
	    }
	    return res;
	}
1
2
3
4
5
6
7
8
重新启用用户初始化时默认禁用的系统应用程序
private void enableSystemApp(ComponentName admin, String packageName) {
	    if(isProfileOwnerApp) {
	        mDevicePolicyManager.enableSystemApp(admin, packageName);
	    }
	}
1
2
3
4
5
隐藏或者启用应用
private boolean setApplicationHidden(ComponentName admin, String packageName, boolean hidden) {
	    boolean res = false;

	    if (isProfileOwnerApp) {
	        res = mDevicePolicyManager.setApplicationHidden(admin, packageName, hidden);
	    }
	    return res;
	}
1
2
3
4
5
6
7
8
查询一个应用是否被隐藏
private boolean isApplicationHidden(ComponentName admin, String packageName) {
	    boolean res = false;

	    if (isProfileOwnerApp) {
	        res = mDevicePolicyManager.isApplicationHidden(admin, packageName);
	    }
	    return res;
	}
1
2
3
4
5
6
7
8
添加用户限制
private void addUserRestriction(ComponentName admin, String key) {
	    if(isProfileOwnerApp) {
	        mDevicePolicyManager.addUserRestriction(admin, key);
	    }
	}
1
2
3
4
5
清除用户限制
private void clearUserRestriction(ComponentName admin, String key) {
	    if(isProfileOwnerApp) {
	        mDevicePolicyManager.clearUserRestriction(admin, key);
	    }
	}
1
2
3
4
5
获取用户限制
private Bundle getUserRestrictions(ComponentName admin) {
	    Bundle res = null;

	    if (isProfileOwnerApp) {
	        res = mDevicePolicyManager.getUserRestrictions(admin);
	    }
	    return res;
	}
1
2
3
4
5
6
7
8
默认情况下，用户可以使用任何输入法。当添加了零个或多个包时，用户无法启用不在列表中的输入法
private boolean setPermittedInputMethods(ComponentName admin, List<String> packageNames) {
	    boolean res = false;

	    if (isProfileOwnerApp) {
	        res = mDevicePolicyManager.setPermittedInputMethods(admin, packageNames);
	    }
	    return res;
	}
1
2
3
4
5
6
7
8
获取受信任的输入法包列表
private List<String> getPermittedInputMethods(ComponentName admin) {
	    List<String> res = null;
	    if(isProfileOwnerApp) {
	        res = mDevicePolicyManager.getPermittedInputMethods(admin);
	    }
	    return res;
	}
1
2
3
4
5
6
7
设置允许的可访问性服务。默认情况下，用户可以使用任何可访问性服务。当添加了零个或多个包时，用户无法启用列表中非系统部分的可访问性服务
private boolean setPermittedAccessibilityServices(ComponentName admin, List<String> packageNames) {
	    boolean res = false;

	    if (isProfileOwnerApp) {
	        res = mDevicePolicyManager.setPermittedAccessibilityServices(admin, packageNames);
	    }
	    return res;
	}
1
2
3
4
5
6
7
8
获取所有不受信任的服务列表
private List<String> getPermittedAccessibilityServices(ComponentName admin) {
	    List<String> res = null;
	    if(isProfileOwnerApp) {
	        res = mDevicePolicyManager.getPermittedAccessibilityServices(admin);
	    }
	    return res;
	}
1
2
3
4
5
6
7
设置蓝牙是否可以访问联系人
private void setBluetoothContactSharingDisabled(ComponentName admin, boolean disabled) {
	    if(isProfileOwnerApp) {
	        mDevicePolicyManager.setBluetoothContactSharingDisabled(admin, disabled);
	    }
	}
1
2
3
4
5
获取蓝牙访问联系人状态
private boolean getBluetoothContactSharingDisabled(ComponentName admin) {
	    boolean res = false;

	    if (isProfileOwnerApp) {
	        res = mDevicePolicyManager.getBluetoothContactSharingDisabled(admin);
	    }
	    return res;
	}
1
2
3
4
5
6
7
8
禁止或者开启搜索联系人功能
private void setCrossProfileContactsSearchDisabled(ComponentName admin, boolean disabled) {
	    if(isProfileOwnerApp) {
	        mDevicePolicyManager.setCrossProfileContactsSearchDisabled(admin, disabled);
	    }
	}
1
2
3
4
5
获取搜索联系人状态
private boolean getCrossProfileContactsSearchDisabled(ComponentName admin) {
	    boolean res = false;

	    if (isProfileOwnerApp) {
	        res = mDevicePolicyManager.getCrossProfileContactsSearchDisabled(admin);
	    }
	    return res;
	}
1
2
3
4
5
6
7
8
禁止或者开启来电显示功能
private void setCrossProfileCallerIdDisabled(ComponentName admin, boolean disabled) {
	    if(isProfileOwnerApp) {
	        mDevicePolicyManager.setCrossProfileCallerIdDisabled(admin, disabled);
	    }
	}
1
2
3
4
5
获取禁止来电显示状态
private boolean getCrossProfileCallerIdDisabled(ComponentName admin) {
	    boolean res = false;

	    if (isProfileOwnerApp) {
	        res = mDevicePolicyManager.getCrossProfileCallerIdDisabled(admin);
	    }
	    return res;
	}
1
2
3
4
5
6
7
8
设置应用限制
private void setApplicationRestrictions(ComponentName admin, String packageName,
            Bundle settings) {
	    if(isProfileOwnerApp) {
	        mDevicePolicyManager.setApplicationRestrictions(admin, packageName, settings);
	    }
	}
1
2
3
4
5
6
获取应用程序受限信息
private Bundle getApplicationRestrictions(ComponentName admin, String packageName) {
	    Bundle res = null;

	    if (isProfileOwnerApp) {
	        res = mDevicePolicyManager.getApplicationRestrictions(admin, packageName);
	    }
	    return res;
	}
1
2
3
4
5
6
7
8
设置应用程序挂起，挂起的程序将无法启动任何活动
private String[] setPackagesSuspended(ComponentName admin, String[] packageNames, boolean suspended) {
	    String[] res = null;

	    if (isProfileOwnerApp) {
	        res = mDevicePolicyManager.setPackagesSuspended(admin, packageNames, suspended);
	    }
	    return res;
	}
1
2
3
4
5
6
7
8
是否为挂起应用
private boolean isPackageSuspended(ComponentName admin, String packageName) {
	    boolean res = false;

	    if (isProfileOwnerApp) {
	        try {
	        	res = mDevicePolicyManager.isPackageSuspended(admin, packageName);
	        } catch (NameNotFoundException e) {
            	Log.w(TAG, "Error getting appName for package: " + packageName, e);
        	}
	    }
	    return res;
	}
1
2
3
4
5
6
7
8
9
10
11
12
指定特定应用程序始终打开的VPN连接。此连接在重新启动后自动授予并持久化
private void setAlwaysOnVpnPackage(ComponentName admin, String vpnPackage,
            boolean lockdownEnabled) {
	    if(isProfileOwnerApp) {
	        try {
	        	mDevicePolicyManager.setAlwaysOnVpnPackage(admin, vpnPackage, lockdownEnabled);
	        } catch (NameNotFoundException | UnsupportedOperationException e) {
            	Log.w(TAG, "Error getting appName for package: " + vpnPackage, e);
        	}
	    }
	}
1
2
3
4
5
6
7
8
9
10
获取打开VPN连接的应用
private String getAlwaysOnVpnPackage(ComponentName admin) {
	    String res = null;

	    if (isProfileOwnerApp) {
	        res = mDevicePolicyManager.getAlwaysOnVpnPackage(admin);
	    }
	    return res;
	}
1
2
3
4
5
6
7
8
授予对另一个应用程序的特权API的访问权
private void setDelegatedScopes(ComponentName admin, String delegatePackage,
            List<String> scopes) {
	    if(isProfileOwnerApp) {
	        mDevicePolicyManager.setDelegatedScopes(admin, delegatePackage, scopes);
	    }
	}
1
2
3
4
5
6
获取特权应用的所有权限
private List<String> getDelegatedScopes(ComponentName admin, String delegatedPackage) {
	    List<String> res = null;
	    if(isProfileOwnerApp) {
	        res = mDevicePolicyManager.getDelegatedScopes(admin, delegatedPackage);
	    }
	    return res;
	}
1
2
3
4
5
6
7
安装证书和相应的私钥
private boolean installKeyPair(ComponentName admin, PrivateKey privKey, Certificate cert, String alias) {
	    boolean res = false;

	    if (isProfileOwnerApp) {
	        res = mDevicePolicyManager.installKeyPair(admin, privKey, cert, alias);
	    }
	    return res;
	}
1
2
3
4
5
6
7
8
删除密匙
private boolean removeKeyPair(ComponentName admin, String alias) {
	    boolean res = false;

	    if (isProfileOwnerApp) {
	        res = mDevicePolicyManager.removeKeyPair(admin, alias);
	    }
	    return res;
	}
1
2
3
4
5
6
7
8
此证书是否安装为可信CA
private boolean hasCaCertInstalled(ComponentName admin, byte[] certBuffer) {
	    boolean res = false;

	    if (isProfileOwnerApp) {
	        res = mDevicePolicyManager.hasCaCertInstalled(admin, certBuffer);
	    }
	    return res;
	}
1
2
3
4
5
6
7
8
卸载所有自定义的可信CA证书。除系统CA证书外，通过设备策略以外的方式安装的证书也将被删除
private void uninstallAllUserCaCerts(ComponentName admin) {
	    if(isProfileOwnerApp) {
	        mDevicePolicyManager.uninstallAllUserCaCerts(admin);
	    }
	}
1
2
3
4
5
返回当前受信任的所有CA证书，不包括系统CA证书。如果用户通过除设备策略之外的其他方式安装了任何证书，这些证书也将包括在内。
private List<byte[]> getInstalledCaCerts(ComponentName admin) {
	    List<byte[]> res = null;

	    if (isProfileOwnerApp) {
	        res = mDevicePolicyManager.getInstalledCaCerts(admin);
	    }
	    return res;
	}
1
2
3
4
5
6
7
8
从可信用户CAs卸载给定的证书
private void uninstallCaCert(ComponentName admin, byte[] certBuffer) {
	    if(isProfileOwnerApp) {
	        mDevicePolicyManager.uninstallCaCert(admin, certBuffer);
	    }
	}
1
2
3
4
5
将给定证书安装为用户可信CA
private boolean installCaCert(ComponentName admin, byte[] certBuffer) {
	    boolean res = false;

	    if (isProfileOwnerApp) {
	        res = mDevicePolicyManager.installCaCert(admin, certBuffer);
	    }
	    return res;
	}

设置超时时间，超时后用户必须使用身份验证才能进入系统，比如指纹、密码等
private void setRequiredStrongAuthTimeout(ComponentName admin, long timeoutMs) {
	    if(isProfileOwnerApp) {
	        mDevicePolicyManager.setRequiredStrongAuthTimeout(admin, timeoutMs);
	    }
	}

获取超时时间
private long getRequiredStrongAuthTimeout(ComponentName admin) {
	    long res = 0;

	    if (isProfileOwnerApp) {
	        res = mDevicePolicyManager.getRequiredStrongAuthTimeout(admin);
	    }
	    return res;
	}

重置设备锁屏密码
private boolean setResetPasswordToken(ComponentName admin, byte[] token) {
	    boolean res = false;

	    if (isProfileOwnerApp) {
	        res = mDevicePolicyManager.setResetPasswordToken(admin, token);
	    }
	    return res;
	}

清除重置设备密码Token
private boolean clearResetPasswordToken(ComponentName admin) {
	    boolean res = false;

	    if (isProfileOwnerApp) {
	        res = mDevicePolicyManager.clearResetPasswordToken(admin);
	    }
	    return res;
	}

重置设备密码Token激活状态
private boolean isResetPasswordTokenActive(ComponentName admin) {
	    boolean res = false;

	    if (isProfileOwnerApp) {
	        res = mDevicePolicyManager.isResetPasswordTokenActive(admin);
	    }
	    return res;
	}

重置设备锁屏密码，在Token激活的状态下有效
private boolean resetPasswordWithToken(ComponentName admin, String password,
            byte[] token, int flags) {
	    boolean res = false;

	    if (isProfileOwnerApp) {
	        res = mDevicePolicyManager.resetPasswordWithToken(admin, password, token, flags);
	    }
	    return res;
	}
————————————————
原文链接：https://blog.csdn.net/visionliao/article/details/84767383









关于DeviceOwner需要注意的几点：
只在Android5.0以上有效。
一个设备只能存在一个DeviceOwner。
DeviceOwner应用不能被卸载，也无法在设置->安全中关闭其权限。（具体办法下面讲）。
因为我的英语稀烂，导致很多文档和博客中的某些点可能理解的不是很透彻，所以总结的不是很到位。大家谨慎观看和使用。

创建DeviceOwner3种方式
1. 利用NFC功能在手机初始化的时候发送一个DeviceOwner应用到手机上。参考 链接 。（未验证）
2. 利用ADB命令。（已验证）
先把你的DeviceOwner应用安装到设备上。然后运行下面这条ADB命令。

$adb shell dpm set-device-owner floatingmuseum.devicemanagersample/.MyDeviceAdminReceiver
如果出现错误提示

java.lang.IllegalStateException: Trying to set device owner but device is already provisioned.
可尝试到设置-账户中删光所有账户。然后重新尝试ADB设置，看到下面结果即设置成功。

Success: Device owner set to package floatingmuseum.devicemanagersample
Active admin set to component {floatingmuseum.devicemanagersample/floatingmuseum.devicemanagersample.MyDeviceAdminReceiver}
3. 在已root设备上进行。（已验证）
先在/data/system/目录下创建一个device_owner.xml文件。

内容如下

<?xml version="1.0" encoding="utf-8" standalone="yes" ?>
<device-owner package="your owner app packagename" />
然后重启设备，一定要注意在重启设备之前安装好你的DeviceOwner应用，否则会出现无法启动的BUG。


-----------------------------------------------------------------------------------------------------------------------------------


部分API的测试使用
1 隐藏应用 setApplicationHidden()

被隐藏的应用可以在设置应用列表中找到。被隐藏的应用重新安装后，依然无法显示。未测试隐藏系统应用后有何效果。



2 设置固定屏幕 setLockTaskPackages()

这个可以用来将屏幕锁定在你的应用上面，例如车站的电子展牌，可触摸的电子屏广告等等。通常我们可以在系统设置-安全-屏幕固定中来设置固定屏幕，或者使用ActivityManager的startLockTask()方法，以上方式会给用户发出信息通知用户即将固定屏幕，并提供退出固定屏幕的方法。

而如果在使用startLockTask之前调用setLockTaskPackages方法，会不发送通知直接固定屏幕，并且隐藏home键和菜单键（如果是虚拟按键的话）。无法通过长按返回键退出固定屏幕（测试系统为氧os安卓5.0上依然可以长按返回键退出）。在无法使用长按返回键的情况下有两种方法退出，重启设备或者使用Activity.stopLockTask()。



3 设置用户限制 addUserRestrictions()

可以配置的功能有很多，包括限制音量调节，应用安装卸载，WIFI蓝牙设置，恢复出厂，添加删除用户，电话短信等等，很强大的的方法。

测试了限制音量调节和禁止应用安装卸载，都运行良好。

具体参数参考UserManager的 SUMMARY 。



4 设置可以使用辅助功能的应用 setPermittedAccessibilityServices()

使用此方法后，所有不在此方法设置中的应用都不得使用辅助功能，在系统设置-辅助功能里的选项会变为灰色，但是对系统应用无效。

参数传null可以取消所有限定。



5 设置可以使用的输入法 setPermittedInputMethods()

此方法只对第三方输入法有效，系统输入法不会被禁用，被禁用的输入法在设置里呈灰色显示。

参数传null可以取消限定。



6 设置禁止屏幕捕获 setScreenCaptureDisabled()

使用此方法后，屏幕录像软件只能录取到黑屏，类似vysor这类屏幕捕获软件也只能看到黑屏。不过依然可以看到状态栏以及toast提示。


7 全局设置 setGlobalSetting()

包括ADB开关，流量开关，手机连接电脑时是否可以开启USB存储设备模式，开发者模式开关等等。

ADB开关设置没有问题。而USB存储模式大多数新设备都已经没有了，开发者模式打开没问题，关闭没效果。


8 安全设置 setSecureSetting()

包括默认输入法，阻止安装未知来源应用，地理位置等等。





------------------------------------------------------------
在6.0上的变化
在Android6.0版本上谷歌新增加了运行时权限的申请，因此DevicePolicyManager的API中也多出了两个对于权限申请的操作，分别为单一应用权限限制，以及全局权限限制。

1. 单一应用权限设置 setPermissionGrantState()
说实话这个API不知道是我测试的方法不对，还是他有自已的特定顺序，使用起来非常麻烦，建议大家暂时放弃此方法。

测试场景

首先这里有一个与此方法对应的用来获取指定应用的指定权限的状态getPermissionGrantState()。然后新安装一个APP，需要写SD卡权限。

设定此应用写SD卡权限为自动拒绝。

获取状态显示依然为PERMISSION_GRANT_STATE_DEFAULT。进入应用设置中查看权限，写SD卡权限为灰色不可选，并且处于拒绝状态。进入应用中去申请权限，不弹申请权限提示框，直接失败。

好在以上步骤下我们继续，设定此应用写SD卡权限为自动获取。

获取状态仍旧显示为PERMISSION_GRANT_STATE_DEFAULT。应用设置查看权限为灰色，但是处于权限已给予的状态。进入应用中去申请权限，不弹框，提示失败。

继续以上步骤，设定写SD卡权限为默认

获取状态为PERMISSION_GRANT_STATE_DEFAULT，应用设置中可操作，并因为上一步是自动获取，这里显示权限已给予状态。应用中点申请，告知有开启此权限。

这只是其中一种情况，其他诡异BUG也会时常出现，所以如果有人需要使用权限限制，请谨慎。

2. 全局动态权限设置 setPermissionPolicy()
对全部应用的权限进行初始限制，如果应用的权限在此限制之前已被设置过，无论是拒绝还是赋予，都不会再受此设置的限制，简单说就是只对未初始化过的权限进行限制。

三种状态，PERMISSION_POLICY_PROMPT，PERMISSION_POLICY_AUTO_GRANT，PERMISSION_POLICY_AUTO_DENY，申请，自动赋予，自动拒绝。

此设置状态为永久的，即你设置了自动拒绝，应用安装好所有动态权限默认拒绝无法修改，即使你该全局设置为自动赋予或者申请，应用依然遵照被安装时的权限设置。

这里需要注意文档里这句话：

This also applies to new permissions declared by app updates. When a permission is denied or granted this way, the effect is equivalent to setting the permission grant state via setPermissionGrantState(ComponentName, String, String, int).

先说一个例子吧。

假如我安装一个应用之前设置了全局限制为PERMISSION_POLICY_PROMPT,然后安装此应用后，为申请权限前修改全局限制为PERMISSION_POLICY_GRANT,然后应用申请权限，会自动赋予，并且无法在设置里修改，类似设置单一应用权限的效果。

总的来说就是在我应用安装后权限未申请前，全局权限设置发生了改变，变更为自动赋予或者自动拒绝的话，我再申请权限，效果会和单一应用权限设置一样。全局设置是自动拒绝则我申请自动拒绝并且设置变灰无法修改，全局设置自动赋予，则我我申请自动赋予并且设置变灰无法修改。

3.禁用状态栏 setStatusBarDisabled()
这个没问题可以放心使用。





















-----------------------------------------------------------------------------------------------------

Android提供了三种设备管理方案，Device Administration(设备管理员), ProfileOwner(配置文件所有者)和 DeviceOwner(设备所有者)。这三种管理方案对应三种等级的管理权限，相对的，等级越高所拥有的管理权限越高，面临的风险也对大，所以，要将一个应用设置成为这些管理设备，也需要不同的权限等级。

设置 Device Administration
要设置一个DeviceAdmin 所需要权限相对来说是最小的，Android系统提供了一种方案：

Intent intent = new Intent(
	DevicePolicyManager.ACTION_ADD_DEVICE_ADMIN);
intent.putExtra(DevicePolicyManager.EXTRA_DEVICE_ADMIN,
	mComponentName);
intent.putExtra(DevicePolicyManager.EXTRA_ADD_EXPLANATION, "提示文字");
startActivityForResult(intent, 1);
1
2
3
4
5
6
该方法将激活DeviceAdmin的动作委托给系统Settings，有界面提示用户是否激活/停用/卸载一个设备管理应用。这种方案其实是一种动态权限，由用户决定是否启用设备管理。在特殊行业中，有些操作不应该让用户决定，由管理平台在后台一键设置。
如果一个应用的进程所属system，那么就和系统Settings具有相同的权限，我们可以在系统中添加一个这样的程序，用来一键设置自己的DeviceAdmin程序。

/**
 * 设置DeviceAdmin
 * packageName: 应用程序包名
 * policyReceiver: 继承了DeviceAdminReceiver的类名
 */
public static void setActiveAdmin(String packageName, String policyReceiver) {
    // 1. 将包名和类名转换为ComponentName
    ComponentName component = new ComponentName(packageName, policyReceiver);
    // 2. 通过ComponentName获取ActivityInfo，如果为空，说明参数传递有误，系统中根本不存在这个应用，直接返回
    ActivityInfo ai = null;
    try {
        ai = iPackageManager.getReceiverInfo(component,
                PackageManager.GET_META_DATA |
                PackageManager.GET_DISABLED_UNTIL_USED_COMPONENTS |
                PackageManager.MATCH_DIRECT_BOOT_UNAWARE |
                PackageManager.MATCH_DIRECT_BOOT_AWARE, getMyUserId());
    } catch (RemoteException e) {
        debug("Unable to load component: " + component);
    }
    // 3. 调用DevicePolicyManager的现有方法setActiveAdmin(系统级应用有权限)
    mDevicePolicyManager.setActiveAdmin(component, true /*refreshing*/, mUserId);
}
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
/**
 * 通过包名判断一个程序是否为激活的DeviceAdmin
 * packageName: 应用程序包名
 * return
 *      true: 是
 *      false: 不是
 */
public static boolean packageHasActiveAdmins(String packageName) {
    // packageHasActiveAdmins方法标记为@hide，只有系统应用可以调用
    DevicePolicyManager devicePolicyManager = (DevicePolicyManager) getSystemService(Context.DEVICE_POLICY_SERVICE);
    return devicePolicyManager.packageHasActiveAdmins(packageName);
}
1
2
3
4
5
6
7
8
9
10
11
12
了解更多DeviceAdmin的权限以及API的使用详情，请转到往期文章Android Device Administration 应用的能力

设置 ProfileOwner
之前介绍过，ProfileOwner 在国内可能不适用(Android DevicePolicyManager 设备管理中已做出说明)，所以设置一个ProfileOwner程序有几条途径不做深入讲解，这里只给出一个方案:
属于system进程的系统应用，有设置ProfileOwner程序的能力，可以在系统中实现一个系统应用，暴露一个一键设置ProfileOwner的接口：

/**
 * 设置ProfileOwner
 * packageName: 应用程序包名
 * policyReceiver: 继承了DeviceAdminReceiver的类
 * deviceUserName: 为ProfileOwner设置一个名字，如不设置传null
 * return
 *      true:  设置成功
 *      false: 设置失败
 * (限于篇幅这里只给出了关键代码，一部分逻辑、变量代码省略)
 */
public static boolean setProfileOwner(String packageName, String policyReceiver, String deviceUserName) {
    // 1. 将包名和类名转换为ComponentName
    ComponentName component = new ComponentName(packageName, policyReceiver);
    // 2. 通过ComponentName获取ActivityInfo，如果为空，说明参数传递有误，系统中根本不存在这个应用，直接返回
    ActivityInfo ai = null;
    try {
        ai = iPackageManager.getReceiverInfo(component,
                PackageManager.GET_META_DATA |
                PackageManager.GET_DISABLED_UNTIL_USED_COMPONENTS |
                PackageManager.MATCH_DIRECT_BOOT_UNAWARE |
                PackageManager.MATCH_DIRECT_BOOT_AWARE, getMyUserId());
    } catch (RemoteException e) {
        debug("Unable to load component: " + component);
    }
    // 3. 调用DevicePolicyManager的现有方法setActiveAdmin(系统级应用有权限)
    mDevicePolicyManager.setActiveAdmin(component, true /*refreshing*/, mUserId);
    // 4. 设置setProfileOwner
    try {
        result = mDevicePolicyManager.setProfileOwner(component, deviceUserName, mUserId);
        debug("set package " + component + " as profile owner result: " + result);
    } catch (RemoteException | IllegalArgumentException | IllegalStateException e) {
        debug("Error: setProfileOwner failed to " + component.flattenToShortString() + " Error Info: " + e);
        // 如果设置ProfileOwner异常，则将设置的DeviceAdmin程序也一并取消
        try {
            mDevicePolicyManager.removeActiveAdmin(component, mUserId);
        } catch (RemoteException e2) {
            debug("Error: removeActiveAdmin failed to " + component.toString() + " Error Info: " + e2);
        }
    }
    if (result) {
        try {
            mDevicePolicyManager.setUserProvisioningState(DevicePolicyManager.STATE_USER_SETUP_FINALIZED, mUserId);
        } catch (RemoteException e) {
            debug("Error: setUserProvisioningState failed to " + component.toString() + " Error Info: " + e);
        }
        debug("Success: Active admin and profile owner set to " + component.toShortString() + " for user " + mUserId);
    }
}
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
当一个应用成为了ProfileOwner应用，它就拥有了所有的ProfileOwner的能力。了解更多ProfileOwner的权限以及API的使用详情，请转至往期文章Android ProfileOwner 应用的能力。

设置 DeviceOwner
DeviceOwner可以使一个第三方应用程序拥有系统最高管理权限，面临的风险也是最大的。 要设置一个DeviceOwner程序, 需要很高的权限，系统提供两种方式：

adb shell
msm8953_64:/ # 
msm8953_64:/ # dpm set-device-owner --name Test com.action.deviceadmin/.DPMTestReceiver
-----------------------mName Test
mComponent: {com.action.deviceadmin/com.action.deviceadmin.DPMTestReceiver}, mName: Test, mUserId: 0
Success: Device owner set to package ComponentInfo{com.action.deviceadmin/com.action.deviceadmin.DPMTestReceiver}
Active admin set to component {com.action.deviceadmin/com.action.deviceadmin.DPMTestReceiver}
msm8953_64:/ # 
1
2
3
4
5
6
7
通过dpm命令设置一个DeviceOwner。
————————————————
原文链接：https://blog.csdn.net/visionliao/article/details/84768035


