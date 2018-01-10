---
uid: web-api/overview/formats-and-model-binding/bson-support-in-web-api-21
title: "在 ASP.NET Web API 2.1 BSON 支持 |Microsoft 文档"
author: MikeWasson
description: 
ms.author: aspnetcontent
manager: wpickett
ms.date: 01/20/2014
ms.topic: article
ms.assetid: ce11b017-0ca6-4376-aa9d-a7f3288101de
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/formats-and-model-binding/bson-support-in-web-api-21
msc.type: authoredcontent
ms.openlocfilehash: 08ef1564b2f8f11294c3bb1ec0ff9a3d063895b6
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2017
---
<a name="bson-support-in-aspnet-web-api-21"></a><span data-ttu-id="47be5-102">在 ASP.NET Web API 2.1 BSON 支持</span><span class="sxs-lookup"><span data-stu-id="47be5-102">BSON Support in ASP.NET Web API 2.1</span></span>
====================
<span data-ttu-id="47be5-103">通过[Mike Wasson](https://github.com/MikeWasson)</span><span class="sxs-lookup"><span data-stu-id="47be5-103">by [Mike Wasson](https://github.com/MikeWasson)</span></span>

<span data-ttu-id="47be5-104">Web API 2.1 引入了对 BSON 支持。</span><span class="sxs-lookup"><span data-stu-id="47be5-104">Web API 2.1 introduces support for BSON.</span></span> <span data-ttu-id="47be5-105">本主题演示如何在你的 Web API 控制器 （服务器端） 和.NET 客户端应用程序中使用 BSON。</span><span class="sxs-lookup"><span data-stu-id="47be5-105">This topic shows how to use BSON in your Web API controller (server side) and in a .NET client app.</span></span>

## <a name="what-is-bson"></a><span data-ttu-id="47be5-106">什么是 BSON？</span><span class="sxs-lookup"><span data-stu-id="47be5-106">What is BSON?</span></span>

<span data-ttu-id="47be5-107">[BSON](http://bsonspec.org/)是一种二进制序列化格式。</span><span class="sxs-lookup"><span data-stu-id="47be5-107">[BSON](http://bsonspec.org/) is a binary serialization format.</span></span> <span data-ttu-id="47be5-108">"BSON"代表"二进制 JSON"，但 BSON 和 JSON 序列化方式差别很大。</span><span class="sxs-lookup"><span data-stu-id="47be5-108">"BSON" stands for "Binary JSON", but BSON and JSON are serialized very differently.</span></span> <span data-ttu-id="47be5-109">BSON 是"JSON 类似的"，因为对象的名称-值对的形式类似于 JSON 形式表示。</span><span class="sxs-lookup"><span data-stu-id="47be5-109">BSON is "JSON-like", because objects are represented as name-value pairs, similar to JSON.</span></span> <span data-ttu-id="47be5-110">JSON，与数值数据类型存储字节，不是字符串</span><span class="sxs-lookup"><span data-stu-id="47be5-110">Unlike JSON, numeric data types are stored as bytes, not strings</span></span>

<span data-ttu-id="47be5-111">BSON 的设计目标是轻量，若要扫描，以及快速来编码/解码。</span><span class="sxs-lookup"><span data-stu-id="47be5-111">BSON was designed to be lightweight, easy to scan, and fast to encode/decode.</span></span>

- <span data-ttu-id="47be5-112">BSON 相当的大小为 JSON。</span><span class="sxs-lookup"><span data-stu-id="47be5-112">BSON is comparable in size to JSON.</span></span> <span data-ttu-id="47be5-113">具体取决于数据，BSON 负载不能小于或大于 JSON 负载。</span><span class="sxs-lookup"><span data-stu-id="47be5-113">Depending on the data, a BSON payload may be smaller or larger than a JSON payload.</span></span> <span data-ttu-id="47be5-114">用于序列化二进制数据，图像文件，如 BSON 小于 JSON，因为的二进制数据不是 base64 编码。</span><span class="sxs-lookup"><span data-stu-id="47be5-114">For serializing binary data, such as an image file, BSON is smaller than JSON, because the binary data does is not base64-encoded.</span></span>
- <span data-ttu-id="47be5-115">BSON 文档很容易扫描的原因是元素前缀与长度字段，因此分析器可以跳过，元素不解码它们。</span><span class="sxs-lookup"><span data-stu-id="47be5-115">BSON documents are easy to scan because elements are prefixed with a length field, so a parser can skip elements without decoding them.</span></span>
- <span data-ttu-id="47be5-116">编码和解码会高效，因为数值数据类型存储为数字，不是字符串。</span><span class="sxs-lookup"><span data-stu-id="47be5-116">Encoding and decoding are efficient, because numeric data types are stored as numbers, not strings.</span></span>

<span data-ttu-id="47be5-117">本机客户端，如.NET 客户端应用程序，可以从替代基于文本的格式，如 JSON 或 XML 中使用 BSON 获益。</span><span class="sxs-lookup"><span data-stu-id="47be5-117">Native clients, such as .NET client apps, can benefit from using BSON in place of text-based formats such as JSON or XML.</span></span> <span data-ttu-id="47be5-118">对于浏览器客户端，你将可能想要继续使用 JSON，因为 JavaScript 直接转换 JSON 负载。</span><span class="sxs-lookup"><span data-stu-id="47be5-118">For browser clients, you will probably want to stick with JSON, because JavaScript can directly convert the JSON payload.</span></span>

<span data-ttu-id="47be5-119">幸运的是，Web API 使用[内容协商](content-negotiation.md)，因此你的 API 可以支持这两种格式，并让客户端选择。</span><span class="sxs-lookup"><span data-stu-id="47be5-119">Fortunately, Web API uses [content negotiation](content-negotiation.md), so your API can support both formats and let the client choose.</span></span>

## <a name="enabling-bson-on-the-server"></a><span data-ttu-id="47be5-120">在服务器上启用 BSON</span><span class="sxs-lookup"><span data-stu-id="47be5-120">Enabling BSON on the Server</span></span>

<span data-ttu-id="47be5-121">在 Web API 配置中，添加**BsonMediaTypeFormatter**到格式化程序集合。</span><span class="sxs-lookup"><span data-stu-id="47be5-121">In your Web API configuration, add the **BsonMediaTypeFormatter** to the formatters collection.</span></span>

[!code-csharp[Main](bson-support-in-web-api-21/samples/sample1.cs)]

<span data-ttu-id="47be5-122">现在如果客户端请求"应用程序/bson"，Web API 将使用 BSON 格式化程序。</span><span class="sxs-lookup"><span data-stu-id="47be5-122">Now if the client requests "application/bson", Web API will use the BSON formatter.</span></span>

<span data-ttu-id="47be5-123">若要将 BSON 与其他媒体类型相关联，请将它们添加到 SupportedMediaTypes 集合中。</span><span class="sxs-lookup"><span data-stu-id="47be5-123">To associate BSON with other media types, add them to the SupportedMediaTypes collection.</span></span> <span data-ttu-id="47be5-124">下面的代码添加到支持的媒体类型"application/vnd.contoso":</span><span class="sxs-lookup"><span data-stu-id="47be5-124">The following code adds "application/vnd.contoso" to the supported media types:</span></span>

[!code-csharp[Main](bson-support-in-web-api-21/samples/sample2.cs)]

## <a name="example-http-session"></a><span data-ttu-id="47be5-125">示例 HTTP 会话</span><span class="sxs-lookup"><span data-stu-id="47be5-125">Example HTTP Session</span></span>

<span data-ttu-id="47be5-126">对于此示例中，我们将使用以下模型类加上一个简单的 Web API 控制器：</span><span class="sxs-lookup"><span data-stu-id="47be5-126">For this example, we'll use the following model class plus a simple Web API controller:</span></span>

[!code-csharp[Main](bson-support-in-web-api-21/samples/sample3.cs)]

<span data-ttu-id="47be5-127">客户端可能发送以下 HTTP 请求：</span><span class="sxs-lookup"><span data-stu-id="47be5-127">A client might send the following HTTP request:</span></span>

[!code-console[Main](bson-support-in-web-api-21/samples/sample4.cmd)]

<span data-ttu-id="47be5-128">下面是响应：</span><span class="sxs-lookup"><span data-stu-id="47be5-128">Here is the response:</span></span>

[!code-console[Main](bson-support-in-web-api-21/samples/sample5.cmd)]

<span data-ttu-id="47be5-129">此处我已替换为二进制数据， &quot;。&quot;字符。</span><span class="sxs-lookup"><span data-stu-id="47be5-129">Here I've replaced the binary data with &quot;.&quot; characters.</span></span> <span data-ttu-id="47be5-130">下面的屏幕快照所示 Fiddler 原始的十六进制值。</span><span class="sxs-lookup"><span data-stu-id="47be5-130">The following screen shot from Fiddler shows the raw hex values.</span></span>

[![](bson-support-in-web-api-21/_static/image2.png)](bson-support-in-web-api-21/_static/image1.png)

## <a name="using-bson-with-httpclient"></a><span data-ttu-id="47be5-131">使用 HttpClient BSON</span><span class="sxs-lookup"><span data-stu-id="47be5-131">Using BSON with HttpClient</span></span>

<span data-ttu-id="47be5-132">.NET 客户端应用程序可以使用与 BSON 格式化程序**HttpClient**。</span><span class="sxs-lookup"><span data-stu-id="47be5-132">.NET clients apps can use the BSON formatter with **HttpClient**.</span></span> <span data-ttu-id="47be5-133">有关详细信息**HttpClient**，请参阅[调用 Web API 从.NET 客户端](../advanced/calling-a-web-api-from-a-net-client.md)。</span><span class="sxs-lookup"><span data-stu-id="47be5-133">For more information about **HttpClient**, see [Calling a Web API From a .NET Client](../advanced/calling-a-web-api-from-a-net-client.md).</span></span>

<span data-ttu-id="47be5-134">下面的代码将发送 GET 请求，用于接受 BSON，并随后反序列化 BSON 负载在响应中。</span><span class="sxs-lookup"><span data-stu-id="47be5-134">The following code sends a GET request that accepts BSON, and then deserializes the BSON payload in the response.</span></span>

[!code-csharp[Main](bson-support-in-web-api-21/samples/sample6.cs)]

<span data-ttu-id="47be5-135">若要从服务器请求 BSON，设置到"应用程序/bson"Accept 标头：</span><span class="sxs-lookup"><span data-stu-id="47be5-135">To request BSON from the server, set the Accept header to "application/bson":</span></span>

[!code-csharp[Main](bson-support-in-web-api-21/samples/sample7.cs)]

<span data-ttu-id="47be5-136">若要反序列化响应正文，使用**BsonMediaTypeFormatter**。</span><span class="sxs-lookup"><span data-stu-id="47be5-136">To deserialize the response body, use the **BsonMediaTypeFormatter**.</span></span> <span data-ttu-id="47be5-137">此格式化程序不在默认格式化程序集合，因此你必须指定它时读取响应正文：</span><span class="sxs-lookup"><span data-stu-id="47be5-137">This formatter is not in the default formatters collection, so you have to specify it when you read the response body:</span></span>

[!code-csharp[Main](bson-support-in-web-api-21/samples/sample8.cs)]

<span data-ttu-id="47be5-138">下一个示例演示如何发送包含 BSON 的 POST 请求。</span><span class="sxs-lookup"><span data-stu-id="47be5-138">The next example shows how to send a POST request that contains BSON.</span></span>

[!code-csharp[Main](bson-support-in-web-api-21/samples/sample9.cs)]

<span data-ttu-id="47be5-139">大部分此代码是前面的示例相同。</span><span class="sxs-lookup"><span data-stu-id="47be5-139">Much of this code is the same as the previous example.</span></span> <span data-ttu-id="47be5-140">但在**PostAsync**方法，指定**BsonMediaTypeFormatter**作为格式化程序：</span><span class="sxs-lookup"><span data-stu-id="47be5-140">But in the **PostAsync** method, specify **BsonMediaTypeFormatter** as the formatter:</span></span>

[!code-csharp[Main](bson-support-in-web-api-21/samples/sample10.cs)]

## <a name="serializing-top-level-primitive-types"></a><span data-ttu-id="47be5-141">序列化的顶级基元类型</span><span class="sxs-lookup"><span data-stu-id="47be5-141">Serializing Top-Level Primitive Types</span></span>

<span data-ttu-id="47be5-142">每个 BSON 文档是键/值对的列表。BSON 规范未定义的序列化单个的原始值，如整数或字符串的语法。</span><span class="sxs-lookup"><span data-stu-id="47be5-142">Every BSON document is a list of key/value pairs.The BSON specification does not define a syntax for serializing a single raw value, such as an integer or string.</span></span>

<span data-ttu-id="47be5-143">若要解决此限制， **BsonMediaTypeFormatter**基元类型将其视为一种特殊情况。</span><span class="sxs-lookup"><span data-stu-id="47be5-143">To work around this limitation, the **BsonMediaTypeFormatter** treats primitive types as a special case.</span></span> <span data-ttu-id="47be5-144">在序列化之前, 它将值转换为键/值对具有键"Value"。</span><span class="sxs-lookup"><span data-stu-id="47be5-144">Before serializing, it converts the value into a key/value pair with the key "Value".</span></span> <span data-ttu-id="47be5-145">例如，假设你的 API 控制器返回一个整数：</span><span class="sxs-lookup"><span data-stu-id="47be5-145">For example, suppose your API controller returns an integer:</span></span>

[!code-csharp[Main](bson-support-in-web-api-21/samples/sample11.cs)]

<span data-ttu-id="47be5-146">在序列化之前, BSON 格式化程序将此转换为以下的键/值对：</span><span class="sxs-lookup"><span data-stu-id="47be5-146">Before serializing, the BSON formatter converts this to the following key/value pair:</span></span>

[!code-json[Main](bson-support-in-web-api-21/samples/sample12.json)]

<span data-ttu-id="47be5-147">当反序列化时，格式化程序会将数据转换回原始值。</span><span class="sxs-lookup"><span data-stu-id="47be5-147">When you deserialize, the formatter converts the data back to the original value.</span></span> <span data-ttu-id="47be5-148">但是，使用不同的 BSON 分析器的客户端将需要处理这种情况下，如果你的 web API 返回原始值。</span><span class="sxs-lookup"><span data-stu-id="47be5-148">However, clients using a different BSON parser will need to handle this case, if your web API returns raw values.</span></span> <span data-ttu-id="47be5-149">一般情况下，应考虑返回结构化的数据，而不是原始值。</span><span class="sxs-lookup"><span data-stu-id="47be5-149">In general, you should consider returning structured data, rather than raw values.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="47be5-150">其他资源</span><span class="sxs-lookup"><span data-stu-id="47be5-150">Additional Resources</span></span>

[<span data-ttu-id="47be5-151">Web API BSON 示例</span><span class="sxs-lookup"><span data-stu-id="47be5-151">Web API BSON Sample</span></span>](https://aspnet.codeplex.com/SourceControl/latest#Samples/WebApi/BSONSample/)

[<span data-ttu-id="47be5-152">媒体格式化程序</span><span class="sxs-lookup"><span data-stu-id="47be5-152">Media Formatters</span></span>](media-formatters.md)