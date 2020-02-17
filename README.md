# Win32 API Fetch Number of Cores
通过Win32 API获取CPU的核心个数

<br />

通过Win32 API来获取CPU的核心个数主要分两步：第一步是通过调用 `GetSystemInfo` 函数来获取逻辑核心的总个数；第二步则是调用 `GetLogicalProcessorInformationEx` 函数来获取逻辑核与物理核的关联信息，最终即可判定出当前处理器有多少核心了。

代码如下所示：

```c
#include <Windows.h>
#include <stdio.h>

int main(void)
{
    SYSTEM_INFO sysInfo;
    // 获取系统信息，
    // SYSTEM_INFO结构体中可获取当前系统的逻辑处理器的个数
    GetSystemInfo(&sysInfo);

    // 方便起见，假定我们当前系统最多有128个关系信息
    SYSTEM_LOGICAL_PROCESSOR_INFORMATION_EX logicalInfos[128];

    DWORD paramSize = (DWORD)sizeof(logicalInfos);

    printf("Number of logical processors: %u\n", sysInfo.dwNumberOfProcessors);

    // 获取指定逻辑处理器所关联的处理器核心的信息
    if (GetLogicalProcessorInformationEx(RelationProcessorCore, logicalInfos, &paramSize))
    {
        DWORD nCores = sysInfo.dwNumberOfProcessors;
        // 当前x86架构的处理器中，一个核心最多只有两个逻辑核
        if (logicalInfos[0].Processor.Flags == LTP_PC_SMT)
            nCores /= 2;

        printf("Number of Cores: %u\n", nCores);
    }

    return 0;
}
```


