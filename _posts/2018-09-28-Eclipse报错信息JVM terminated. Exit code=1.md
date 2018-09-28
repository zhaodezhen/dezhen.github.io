---
published: false
---
## 报错信息如下：
```
JVM terminated. Exit code=1
/usr/local/eclipse/jre/bin/java
-Dosgi.requiredJavaVersion=1.8
-Dosgi.instance.area.default=@user.home/eclipse-workspace
-XX:+UseG1GC
-XX:+UseStringDeduplication
-Dosgi.requiredJavaVersion=1.8
-Xms256m
-Xmx1024m
-jar /usr/local/eclipse//plugins/org.eclipse.equinox.launcher_1.4.0.v20161219-1356.jar
-os linux
-ws gtk
-arch x86_64
-showsplash /usr/local/eclipse//plugins/org.eclipse.epp.package.common_4.7.1.20170914-1200/splash.bmp
-launcher /usr/local/eclipse/eclipse
-name Eclipse
--launcher.library /usr/local/eclipse//plugins/org.eclipse.equinox.launcher.gtk.linux.x86_64_1.1.500.v20170531-1133/eclipse_1624.so
-startup /usr/local/eclipse//plugins/org.eclipse.equinox.launcher_1.4.0.v20161219-1356.jar
--launcher.appendVmargs
-exitdata 21000b
-product org.eclipse.epp.package.java.product
-vm /usr/local/eclipse/jre/bin/java
-vmargs
-Dosgi.requiredJavaVersion=1.8
-Dosgi.instance.area.default=@user.home/eclipse-workspace
-XX:+UseG1GC
-XX:+UseStringDeduplication
-Dosgi.requiredJavaVersion=1.8
-Xms256m
-Xmx1024m
-jar /usr/local/eclipse//plugins/org.eclipse.equinox.launcher_1.4.0.v20161219-1356.jar 
```
 第一种： eclipse.ini中内存设置过大的问题，修改了一下，256m改成128m，把512m   改为 256m，即可。  
 
 原因：大内存的配置导致的。  
          
 第二种：在eclipse.ini 中，增加了如下两行后，问题解决：                            
 vm                          /usr/local/Java/jdk1.8.0/bin/java   
 其中java是我的JDK安装路径。同时，完整的eclipse.ini如下  
``` 
-startup                          plugins/org.eclipse.equinox.launcher_1.0.101.R34x_v20081125.jar                   
showsplash                         org.eclipse.platform                         
launcher.XXMaxPermSize                         512m                        
vm                           /usr/local/Java/jdk1.8.0/bin/java                        
vmargs                         -Xms40m                        -Xmx256m                         
Djava.net.preferIPv4Stack=true
```
