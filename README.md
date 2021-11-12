*最新 objc4-824 版本的配置请参考[objc4-824](https://github.com/hubupc/objc4-824.git) *  
## 源码
1. 下载地址  
https://opensource.apple.com/tarballs/

2. Runtime   
  objc4-818.2

3. 依赖库
- xnu-7195.81.3
- dyld-832.7.3
- libplatform-254.80.2
- libpthread-454.80.2
- Libc-825.40.1
- Libc-1244.50.9
- libclosure-78

## IDE
- Xcode 12.4

## 设置依赖库目录
1. 在 Runtime 工程根目录下创建文件夹 PrivateHeaders
2. 打开 Runtime 源码，点击左侧项目导航窗口中的工程文件 objc，在右侧设置窗口中，TARGETS 栏目下选中 objc，
   点击 Build Settings, 搜索search path，在 Search Paths 栏目下，打开 Header Search Paths，新增 $(SRCROOT)/PrivateHeaders


## 编译错误处理
注：实测发现：cmd+B 编译报错，在没有任何修改的情况下，再次 cmd+B 编译，可能会优先报另一个错，所以实际操作过程中，报错顺序可能有些差异，按实际顺序修改即可。  


1. 问题：error: unable to find sdk 'macosx.internal' (in target 'objc-trampolines' from project 'objc')  
   方案：PROJECT 栏目下选中 objc，点击 Build Settings，在 Architetures 栏目下，将 Base SDK 由 macosx.internal 改为 macOS  

2. 问题：fatal error: 'sys/reason.h' file not found  
   文件：xnu-7195.81.3/bsd/sys/reason.h  
   方案：将 reason.h 拷贝到 objc4-818.2/PrivateHeaders/sys/  

3. 问题：fatal error: 'mach-o/dyld_priv.h' file not found  
   文件：dyld-832.7.3/include/mach-o/dyld_priv.h  
   方案：将 dyld_priv.h 拷贝到 objc4-818.2/PrivateHeaders/mach-o/  

4. 问题：error: expected ','  
    extern dyld_platform_t dyld_get_base_platform(dyld_platform_t platform) __API_AVAILABLE(macos(10.14), ios(12.0), watchos(5.0), tvos(12.0), bridgeos(3.0));  
   方案：删除 dyld_priv.h中的 , bridgeos(3.0)  

5. 问题：fatal error: 'os/lock_private.h' file not found  
   文件：libplatform-254.80.2/private/os/lock_private.h   
   方案：将 lock_private.h 拷贝到 objc4-818.2/PrivateHeaders/os/  

6. 问题：fatal error: 'os/base_private.h' file not found  
   文件：xnu-7195.81.3/libkern/os/base_private.h   
   方案：将 base_private.h 拷贝到 objc4-818.2/PrivateHeaders/os/  

7. 问题：error: expected ','  
   tvos(13.0), watchos(6.0), bridgeos(4.0)) = 0x00040000,  
   方案：删除 lock_private.h 中的 , bridgeos(4.0)  

8. 问题：fatal error: 'pthread/tsd_private.h' file not found  
   文件：libpthread-454.80.2/private/pthread/tsd_private.h   
   方案：将 tsd_private.h 拷贝到 objc4-818.2/PrivateHeaders/pthread/  

9. 问题：fatal error: 'System/machine/cpu_capabilities.h' file not found  
   文件：xnu-7195.81.3/osfmk/machine/cpu_capabilities.h   
   方案：将 cpu_capabilities.h 拷贝到 objc4-818.2/PrivateHeaders/System/machine/  

10. 问题：fatal error: 'os/tsd.h' file not found  
    文件：xnu-7195.81.3/libsyscall/os/tsd.h   
    方案：将 tsd.h 拷贝到 objc4-818.2/PrivateHeaders/os/   

11. 问题：fatal error: 'pthread/spinlock_private.h' file not found  
    文件：libpthread-454.80.2/private/pthread/spinlock_private.h   
    方案：将 spinlock_private.h 拷贝到 objc4-818.2/PrivateHeaders/pthread/  

12. 问题：fatal error: 'System/pthread_machdep.h' file not found  
    文件：Libc-825.40.1/pthreads/pthread_machdep.h   
    方案：将 pthread_machdep.h 拷贝到 objc4-818.2/PrivateHeaders/System/  

13. 问题：error: typedef redefinition with different types ('int' vs 'volatile OSSpinLock' (aka 'volatile int'))  
    typedef long pthread_lock_t;   
    error: static declaration of '_pthread_has_direct_tsd' follows non-static declaration     
    error: static declaration of '_pthread_getspecific_direct' follows non-static declaration  
    error: static declaration of '_pthread_setspecific_direct' follows non-static declaration  
    方案：注释掉 pthread_machdep.h  Line 61~299  

14. 问题：fatal error: 'CrashReporterClient.h' file not found  
    文件：Libc-825.40.1/include/CrashReporterClient.h   
    方案：将 CrashReporterClient.h 拷贝到 objc4-818.2/PrivateHeaders  
         在TARGETS objc: Build Settings: Preprocessor Macros 下新增：LIBC_NO_LIBCRASHREPORTERCLIENT  

15. 问题：fatal error: 'os/feature_private.h' file not found  
    方案： 注释：NSObject.mm 中 Line 42， objc-runtime.mm 中 Line 36, Line 444～446   

16. 问题：fatal error: 'objc-shared-cache.h' file not found  
    文件：dyld-832.7.3/include/objc-shared-cache.h  
    方案：将 objc-shared-cache.h 拷贝到 objc4-818.2/PrivateHeaders/  

17. 问题：error: use of undeclared identifier 'dyld_fall_2020_os_versions'  
    方案：注释：objc-runtime.mm 中 Line 379~380  

18. 问题：fatal error: 'Cambria/Traps.h' file not found  
    方案：注释：objc-cache.mm 中 Line 87~88,  Line 1121~1128 保留Line 1127  

19. 问题：fatal error: 'kern/restartable.h' file not found  
    文件：xnu-7195.81.3/osfmk/kern/restartable.h  
    方案：将 restartable.h 拷贝到 objc4-818.2/PrivateHeaders/kern/  

20. 问题：fatal error: 'objc-bp-assist.h' file not found  
    方案：工程里这个文件显示为红色，缺少文件，删除红色文件，注释：objc-os.mm Line 31  

21. 问题：error: use of undeclared identifier 'dyld_platform_version_macOS_10_13'  
    方案：注释：objc-os.mm Line 567~574  

22. 问题：fatal error: 'os/linker_set.h' file not found  
    文件：xnu-7195.81.3/bsd/sys/linker_set.h  
    方案：将 linker_set.h 拷贝到 objc4-818.2/PrivateHeaders/os/  

23. 问题：error: use of undeclared identifier 'dyld_platform_version_macOS_10_11'  
    方案：注释：objc-runtime-new.mm 中 Line 3547~3553  

24. 问题：error: use of undeclared identifier 'dyld_fall_2018_os_versions'  
    方案：注释：objc-runtime-new.mm 中 Line 8328~8350  
   
25. 问题：fatal error: '_simple.h' file not found  
    文件：libplatform-254.80.2/private/_simple.h  
    方案：将 _simple.h 拷贝到 objc4-818.2/PrivateHeaders/  

26. 问题：fatal error: 'Block_private.h' file not found  
    文件：libclosure-78/Block_private.h  
    方案：将 Block_private.h 拷贝到 objc4-818.2/PrivateHeaders/  

27. 问题：fatal error: 'os/reason_private.h' file not found  
    文件：xnu-7195.81.3/libkern/os/reason_private.h  
    方案：将 reason_private.h 拷贝到 objc4-818.2/PrivateHeaders/os/  

28. 问题：fatal error: 'os/variant_private.h' file not found  
    文件：Libc-1244.50.9/os/variant_private.h  
    方案：将 variant_private.h 拷贝到 objc4-818.2/PrivateHeaders/os/  

29. 问题：  
    error: use of undeclared identifier 'dyld_platform_version_macOS_10_12'  
    error: use of undeclared identifier 'dyld_platform_version_iOS_10_0'  
    error: use of undeclared identifier 'dyld_platform_version_tvOS_10_0'  
    error: use of undeclared identifier 'dyld_platform_version_watchOS_3_0'  
    error: use of undeclared identifier 'dyld_platform_version_bridgeOS_2_0'  
    方案：注释：objc-os.h 中 Line 1045~1050，  
         将 Line 1045 改为：#define sdkIsAtLeast(x, i, t, w, b) true   

30. 问题：error: '_static_assert' declared as an array with a negative size  
    方案：注释：objc-runtime-new.mm 中 Line 176~177  

31. 问题：can't open order file: /Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX11.1.sdk/AppleInternal/OrderFiles/libobjc.order  
    方案：TARGETS 栏目下选中 objc，点击 Build Settings, 搜索 order，将 Order File 修改:$(SRCROOT)/libobjc.order  

32. 问题：library not found for -lCrashReporterClient  
    方案：TARGETS 栏目下选中 objc，点击 Build Settings, 搜索 other link，在 Other Linker Flags 中删除 -lCrashReporterClient  

33. 问题：llibrary not found for -loah  
    方案：TARGETS 栏目下选中 objc，点击 Build Settings, 搜索 other link，在 Other Linker Flags 中删除 -loah  

34. 问题：error: SDK "macosx.internal" cannot be located  
    方案：TARGETS 栏目下选中 objc，点击 Build Phases，在 Run Script(markgc) 中将 macosx.internal 修改为 macosx  

    **至此，objc4-818.2 编译成功**


## 创建测试工程
1. TARGETS 栏目底部点击加号，创建一个 macOS  Command Line Tool 工程 objc_test  
2. TARGETS 栏目下选中 objc_test, 点击 Build Phases, 在 Dependencies 中添加依赖，选择objc 

## 断点不生效的问题
1. objc4-818.2 源代码 断点不生效：
   TARGETS 栏目下选中 objc_test，点击 Build Settings, 搜索 Signing， 在 Signing 下，将 Enable Hardened Runtime 由 YES 改为 NO

2. objc_test main.m 断点不生效：
   TARGETS 栏目下选中 objc_test，点击 Build Phases 在 Compile Source 中，将 main.m 文件移至第一位

   **至此，可以调试 objc4-818.2 源码了**   


