---
title: 포함 파일
description: 포함 파일
services: functions
author: tdykstra
manager: jeconnoc
ms.service: multiple
ms.topic: include
ms.date: 06/21/2018
ms.author: tdykstra
ms.custom: include file
ms.openlocfilehash: d651fd3d03f2678d625e60f9ab1e9f59e623964f
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079458"
---
<span data-ttu-id="f5ba3-103">이 자습서에서는 HTML 기반 사용자 인터페이스를 표시하는 간단한 웹 응용 프로그램을 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="f5ba3-103">In this tutorial, you deploy a simple web application that presents an HTML-based user interface.</span></span> <span data-ttu-id="f5ba3-104">서버를 사용하지 않는 백 엔드를 사용하면 응용 프로그램에서 이미지를 업로드하고 자동으로 설명하는 캡션을 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5ba3-104">A serverless backend enables the application to upload images and automatically get captions describing them.</span></span>

![웹앱 실행](media/functions-first-serverless-web-app/0-app-screenshot-finished.png)

<span data-ttu-id="f5ba3-106">다음 다이어그램에서는 응용 프로그램에서 사용하는 Azure 서비스를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="f5ba3-106">The following diagram shows the Azure services used by the application:</span></span>

1. <span data-ttu-id="f5ba3-107">Blob Storage는 정적 웹 콘텐츠(HTML, CSS, JS)를 제공하고 이미지를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="f5ba3-107">Blob Storage serves static web content (HTML, CSS, JS) and stores images.</span></span>
2. <span data-ttu-id="f5ba3-108">Azure Functions는 이미지 업로드, 크기 조정 및 메타데이터 저장을 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="f5ba3-108">Azure Functions manages image uploads, resizing, and metadata storage.</span></span>
3. <span data-ttu-id="f5ba3-109">Cosmos DB는 이미지 메타데이터를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="f5ba3-109">Cosmos DB stores image metadata.</span></span>
4. <span data-ttu-id="f5ba3-110">Logic Apps는 Computer Vision API에서 이미지 캡션을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="f5ba3-110">Logic Apps gets image captions from Computer Vision API.</span></span>
5. <span data-ttu-id="f5ba3-111">Azure Active Directory는 사용자 인증을 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="f5ba3-111">Azure Active Directory manages user authentication.</span></span>

![솔루션 아키텍처 다이어그램](media/functions-first-serverless-web-app/0-architecture.jpg)

<span data-ttu-id="f5ba3-113">이 자습서에서는 다음 방법에 대해 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="f5ba3-113">In this tutorial, you learn how to:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="f5ba3-114">정적 웹 사이트 및 업로드된 이미지를 호스트하도록 Azure Blob Storage를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="f5ba3-114">Configure Azure Blob storage to host a static website and uploaded images.</span></span>
> * <span data-ttu-id="f5ba3-115">Azure Functions를 사용하여 Azure Blob Storage에 이미지를 업로드합니다.</span><span class="sxs-lookup"><span data-stu-id="f5ba3-115">Upload images to Azure Blob storage using Azure Functions.</span></span>
> * <span data-ttu-id="f5ba3-116">Azure Functions를 사용하여 이미지의 크기를 조정합니다.</span><span class="sxs-lookup"><span data-stu-id="f5ba3-116">Resize images using Azure Functions.</span></span>
> * <span data-ttu-id="f5ba3-117">Azure Cosmos DB에서 이미지 메타데이터를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="f5ba3-117">Store image metadata in Azure Cosmos DB.</span></span>
> * <span data-ttu-id="f5ba3-118">Cognitive Services Vision API를 사용하여 이미지 캡션을 자동 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="f5ba3-118">Use Cognitive Services Vision API to auto-generate image captions.</span></span>
> * <span data-ttu-id="f5ba3-119">사용자를 인증하여 웹앱을 보호하도록 Azure Active Directory를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5ba3-119">Use Azure Active Directory to secure the web app by authenticating users.</span></span>