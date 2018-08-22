---
title: 포함 파일
description: 포함 파일
services: functions
author: tdykstra
manager: jeconnoc
ms.service: multiple
ms.topic: include
ms.date: 06/25/2018
ms.author: tdykstra
ms.custom: include file
ms.openlocfilehash: 1c4aaf7afd9fbc54dd34c2cca13a8b74e947c16a
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079479"
---
<span data-ttu-id="00edb-103">Azure 서비스를 사용하여 모든 기능을 갖춘 서버를 사용하지 않는 응용 프로그램을 만들었습니다.</span><span class="sxs-lookup"><span data-stu-id="00edb-103">You've successfully created a full-featured, serverless application using Azure services.</span></span>

## <a name="clean-up-resources"></a><span data-ttu-id="00edb-104">리소스 정리</span><span class="sxs-lookup"><span data-stu-id="00edb-104">Clean up resources</span></span>

<span data-ttu-id="00edb-105">이 응용 프로그램의 작업이 완료되면 다음 명령을 사용하여 자습서 중에 만들어진 모든 리소스를 삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="00edb-105">When you are done working with this application, you can use the following command to delete all resources created during the tutorial:</span></span>

```azurecli
az group delete --name first-serverless-app
```

<span data-ttu-id="00edb-106">메시지가 표시되면 `y`를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="00edb-106">Type `y` when prompted.</span></span>  

## <a name="next-steps"></a><span data-ttu-id="00edb-107">다음 단계</span><span class="sxs-lookup"><span data-stu-id="00edb-107">Next steps</span></span>

<span data-ttu-id="00edb-108">이 자습서에서는 다음 방법에 대해 알아보았습니다.</span><span class="sxs-lookup"><span data-stu-id="00edb-108">In this tutorial, you learned how to:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="00edb-109">정적 웹 사이트 및 업로드된 이미지를 호스트하도록 Azure Blob Storage를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="00edb-109">Configure Azure Blob storage to host a static website and uploaded images.</span></span>
> * <span data-ttu-id="00edb-110">Azure Functions를 사용하여 Azure Blob Storage에 이미지를 업로드합니다.</span><span class="sxs-lookup"><span data-stu-id="00edb-110">Upload images to Azure Blob storage using Azure Functions.</span></span>
> * <span data-ttu-id="00edb-111">Azure Functions를 사용하여 이미지의 크기를 조정합니다.</span><span class="sxs-lookup"><span data-stu-id="00edb-111">Resize images using Azure Functions.</span></span>
> * <span data-ttu-id="00edb-112">Azure Cosmos DB에서 이미지 메타데이터를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="00edb-112">Store image metadata in Azure Cosmos DB.</span></span>
> * <span data-ttu-id="00edb-113">Cognitive Services Vision API를 사용하여 이미지 캡션을 자동 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="00edb-113">Use Cognitive Services Vision API to auto-generate image captions.</span></span>
> * <span data-ttu-id="00edb-114">사용자를 인증하여 웹앱을 보호하도록 Azure Active Directory를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="00edb-114">Use Azure Active Directory to secure the web app by authenticating users.</span></span>

<span data-ttu-id="00edb-115">함수에 추가 서비스를 연결하는 방법을 알아보려면 Logic Apps 자습서를 진행합니다.</span><span class="sxs-lookup"><span data-stu-id="00edb-115">To learn how to connect even more services to Functions, continue to the Logic Apps tutorial.</span></span> 

> [!div class="nextstepaction"]
> [<span data-ttu-id="00edb-116">Logic Apps를 사용하는 함수</span><span class="sxs-lookup"><span data-stu-id="00edb-116">Functions with Logic Apps</span></span>](https://docs.microsoft.com/azure/azure-functions/functions-twitter-email)