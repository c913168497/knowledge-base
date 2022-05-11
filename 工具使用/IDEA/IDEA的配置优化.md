#### 启动不自动打开上次打开的项目
```
IDEA:File -> Settings -> Appearance&Behavior-> System Settings-> Reopen last project on startup 对钩去掉就可以了
```

#### Maven 编译优化
```
File -> Settings -> Build, Execution, Deployment -> Maven -> Thread count 设置为 5，提升同时编译项目数量

ile -> Settings -> Build, Execution, Deployment -> Maven -> VM options for importer: 设置为 -Xmx2048m 提升编译时 内存可使用量

```

#### vm option
```
-ea
-server
-Xms2G
-Xmx4096M
-Xss2m
-XX:MaxMetaspaceSize=2G
-XX:ReservedCodeCacheSize=1G
-XX:MetaspaceSize=512m
-XX:+UseConcMarkSweepGC
-XX:+DoEscapeAnalysis
-XX:SoftRefLRUPolicyMSPerMB=50
-XX:+UnlockExperimentalVMOptions
-Djava.net.preferIPv4Stack=true
-Dsun.io.useCanonCaches=false
-XX:LargePageSizeInBytes=256m
-XX:+UseCodeCacheFlushing
-XX:ParallelGCThreads=8
-XX:+DisableExplicitGC
-XX:+ExplicitGCInvokesConcurrent
-XX:+PrintGCDetails
-XX:+PrintFlagsFinal
-XX:+AggressiveOpts
-XX:+CMSClassUnloadingEnabled
-XX:CMSInitiatingOccupancyFraction=60
-XX:+CMSClassUnloadingEnabled
-XX:+CMSParallelRemarkEnabled
-XX:+UseAdaptiveGCBoundary
-Dfile.encoding=UTF-8
-XX:CompileThreshold=10000
-Dawt.useSystemAAFontSettings=lcd
-Dawt.java2d.opengl=true
-Dsun.java2d.renderer=sun.java2d.marlin.MarlinRenderingEngine
```

