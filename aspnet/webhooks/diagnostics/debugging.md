---
uid: webhooks/diagnostics/debugging
title: "调试 ASP.NET Webhook |Microsoft 文档"
author: rick-anderson
description: "如何调试 ASP.NET Webhook。"
ms.author: aspnetcontent
manager: wpickett
ms.date: 01/17/2012
ms.topic: article
ms.assetid: 467da78b-3c35-4c51-8b08-77a32379e4a8
ms.technology: 
ms.prod: .net-framework
ms.openlocfilehash: 566ee353f6a947e3ef0efdfd0af3a81dff2147c7
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2017
---
# <a name="aspnet-webhooks-debugging"></a><span data-ttu-id="31516-103">调试 ASP.NET Webhook</span><span class="sxs-lookup"><span data-stu-id="31516-103">ASP.NET WebHooks debugging</span></span>  

## <a name="debugging-in-azure"></a><span data-ttu-id="31516-104">在 Azure 中调试</span><span class="sxs-lookup"><span data-stu-id="31516-104">Debugging in Azure</span></span>

<span data-ttu-id="31516-105">若要在 Azure 中运行时调试 Web 应用程序，请参阅本教程[对 Azure App Service 中使用 Visual Studio 中的 web 应用进行故障排除](https://azure.microsoft.com/en-us/documentation/articles/web-sites-dotnet-troubleshoot-visual-studio/#webserverlogs)。</span><span class="sxs-lookup"><span data-stu-id="31516-105">To debug your Web Application while running in Azure, please see the tutorial [Troubleshoot a web app in Azure App Service using Visual Studio](https://azure.microsoft.com/en-us/documentation/articles/web-sites-dotnet-troubleshoot-visual-studio/#webserverlogs).</span></span>

## <a name="debugging-with-source-and-symbols"></a><span data-ttu-id="31516-106">使用的源和符号进行调试</span><span class="sxs-lookup"><span data-stu-id="31516-106">Debugging with Source and Symbols</span></span>

<span data-ttu-id="31516-107">除了调试你自己的代码，就可以调试 Microsoft ASP.NET webhook 启动，直接在和中的事实所有.NET。</span><span class="sxs-lookup"><span data-stu-id="31516-107">In addition to debugging your own code, it is possible to debug directly into Microsoft ASP.NET WebHooks, and in fact all of .NET.</span></span> <span data-ttu-id="31516-108">这适用无论是否在本地或远程调试。</span><span class="sxs-lookup"><span data-stu-id="31516-108">This works regardless of whether you debug locally or remotely.</span></span> <span data-ttu-id="31516-109">首先，将 Visual Studio 查找源文件和符号通过转到配置为**调试**然后**选项和设置**。</span><span class="sxs-lookup"><span data-stu-id="31516-109">First, configure Visual Studio to find the source and symbols by going to **Debug** and then **Options and Settings**.</span></span> <span data-ttu-id="31516-110">设置的选项如下：</span><span class="sxs-lookup"><span data-stu-id="31516-110">Set the options like this:</span></span>

![选项和设置](_static/SourceSymbols.png)

<span data-ttu-id="31516-112">然后添加一个链接到[symbolsource.org](http://symbolsource.org)下载的源和符号。</span><span class="sxs-lookup"><span data-stu-id="31516-112">Then add a link to [symbolsource.org](http://symbolsource.org) for downloading the source and symbols.</span></span> <span data-ttu-id="31516-113">转到**符号**上方的菜单选项卡，添加以下内容作为符号位置：</span><span class="sxs-lookup"><span data-stu-id="31516-113">Go to the **Symbols** tab of the menu above and add the following as a symbol location:</span></span>

```
http://srv.symbolsource.org/pdb/Public
```

<span data-ttu-id="31516-114">此外，请确保缓存目录具有一个短名称;否则文件名称可以太长这将导致要加载的符号。</span><span class="sxs-lookup"><span data-stu-id="31516-114">In addition, make sure that the cache directory has a short name; otherwise the file names can get too long which will cause the symbols to not load.</span></span> <span data-ttu-id="31516-115">示例路径为：</span><span class="sxs-lookup"><span data-stu-id="31516-115">A sample path is:</span></span>

```
C:\SymCache
```

<span data-ttu-id="31516-116">设置应类似于此：</span><span class="sxs-lookup"><span data-stu-id="31516-116">The settings should look similar to this:</span></span>

![选项符号文件位置示例](_static/SymSource.png)