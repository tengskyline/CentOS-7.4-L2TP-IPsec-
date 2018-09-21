1>. 更新系统

yum -y update
1
2>. 使用以下命令快速搭建 IPsec VPN 服务器

wget https://git.io/vpnsetup-centos -O vpnsetup.sh && sudo sh vpnsetup.sh
1
该脚本会初始化一个预共享密匙（PSK），用户名以及密码， 如果需要的话，我们要根据自己的需求来定制一下，

a. 先修改 PSK，将 PSK后面的字符替换掉

vim /etc/ipsec.secrets
1
b. 修改用户名和密码 ，将用户名和密码修改为自已想要的字符

vim /etc/ppp/chap-secrets
1
c. 重启IPsec和xl2tpd服务

systemctl restart ipsec xl2tpd
1
当然了，我们也可能会有多人共享的需求， 操作步骤如下：

vim /etc/ppp/chap-secrets
#添加第二个用户，格式为： "username" l2tpd "password" * 
#重启服务
systemctl restart ipsec xl2tpd  
1
2
3
4
5
客户端配置：

一、Windows 10 and 8.x 
1. 右键单击系统托盘中的无线/网络图标。 
2. 选择 打开网络与共享中心。 
3. 单击 设置新的连接或网络。 
4. 选择 连接到工作区，然后单击 下一步。 
5. 单击 使用我的Internet连接 (VPN)。 
6. 在 Internet地址 字段中输入你的 VPN 服务器 IP。 
7. 在 目标名称 字段中输入任意内容。单击 创建。 
8. 返回 网络与共享中心。单击左侧的 更改适配器设置。 
9. 右键单击新创建的 VPN 连接，并选择 属性。 
10. 单击 安全 选项卡，从 VPN 类型 下拉菜单中选择 “使用 IPsec 的第 2 层隧道协议 (L2TP/IPSec)”。 
11. 单击 允许使用这些协议。确保选中 “质询握手身份验证协议 (CHAP)” （这一点很重要，否则会报：Windows 错误 628/在连接完成前，连接被远程计算机终止）复选框。 
12. 单击 高级设置 按钮。 
13. 单击 使用预共享密钥作身份验证 并在 密钥 字段中输入你的 VPN IPsec PSK。 
14. 单击 确定 关闭 高级设置。 
15. 单击 确定 保存 VPN 连接的详细信息。 
注： 在首次连接之前需要修改一次注册表。请参见下面的说明。 
Windows 错误 809：无法建立计算机与 VPN 服务器之间的网络连接，因为远程服务器未响应。

以管理员身份启用命令提示符：执行以下命令 
注：完成后必须重启计算机。

适用于 Windows Vista, 7, 8 和 10

REG ADD HKLM\SYSTEM\CurrentControlSet\Services\PolicyAgent /v AssumeUDPEncapsulationContextOnSendRule /t REG_DWORD /d 0x2 /f
1
另外，某些个别的 Windows 系统禁用了 IPsec 加密，此时也会导致连接失败。要重新启用它，可以运行以下命令

REG ADD HKLM\SYSTEM\CurrentControlSet\Services\RasMan\Parameters /v ProhibitIpSec /t REG_DWORD /d 0x0 /f
1
二、Android 
1. 启动 设置 应用程序。 
2. 在 无线和网络 部分单击 更多…。 
3. 单击 VPN。 
4. 单击 添加VPN配置文件 或窗口右上角的 +。 
5. 在 名称 字段中输入任意内容。 
6. 在 类型 下拉菜单选择 L2TP/IPSec PSK。 
7. 在 服务器地址 字段中输入你的 VPN 服务器 IP。 
8. 在 IPSec 预共享密钥 字段中输入你的 VPN IPsec PSK。 
9. 在 用户名 字段中输入你的 VPN 用户名。 
10. 在 密码 字段中输入你的 VPN 密码。 
11. 选中 保存帐户信息 复选框。 
12. 单击 连接。

以上两种客户端亲自测试可行

其它客户端请点击这里

参考资料： https://github.com/hwdsl2/setup-ipsec-vpn