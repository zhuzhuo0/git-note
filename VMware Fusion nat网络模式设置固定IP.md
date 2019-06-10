## VMware Fusion nat网络模式设置固定IP

1. 设置vm虚拟机对应的静态IP

   ```
   vim /Library/Preferences/VMware\ Fusion/vmnet8/dhcpd.conf
   # 修改前看下当前用户对该文件的权限有哪些
   # 修改文件权限，所有用户对该文件可读可写
   sudo chmod 666 dhcpd.conf 
   ```

2. 在`####### VMNET DHCP Configuration. End of "DO NOT MODIFY SECTION" #######`这一行之后添加虚拟机静态IP配置，如图

   ![code.png](/Users/zhuzhu/Downloads/code.png)

   这里需要注意的，centos-node这个主机名要和虚拟机的主机名相对应，可以在vmware fusion中的虚拟机资源库中查看或修改，如图

   ![](/Users/zhuzhu/Desktop/屏幕快照 2019-06-02 19.26.45.png)

   ![example](/Users/zhuzhu/Desktop/屏幕快照 2019-06-02 19.24.41.png)

   

3. 刷新vm网络配置

   ```
   sudo /Applications/VMware\ Fusion.app/Contents/Library/vmnet-cli --configure
   sudo /Applications/VMware\ Fusion.app/Contents/Library/vmnet-cli --stop
   sudo /Applications/VMware\ Fusion.app/Contents/Library/vmnet-cli --start
   ```

   ![](/Users/zhuzhu/Desktop/屏幕快照 2019-06-02 19.29.46.png)

4. 虚拟机重新获取配置

   直接重启vmware fusion