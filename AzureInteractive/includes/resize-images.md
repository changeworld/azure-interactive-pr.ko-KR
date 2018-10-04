---
title: 포함 파일
description: 포함 파일
services: functions
author: ggailey777
manager: jeconnoc
ms.service: multiple
ms.topic: include
ms.date: 06/21/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: d19a9d0e7e0347b38fc16f85fa440281be5347f2
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2018
ms.locfileid: "47460087"
---
<span data-ttu-id="4daee-103">이전 모듈에서는 서버를 사용하지 않는 함수가 웹 응용 프로그램에서 Blob Storage로 이미지의 안전한 업로드를 용이하게 할 수 있는 방법을 살펴보았습니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-103">In the previous module, you saw how a serverless function can facilitate the secure uploading of images to Blob storage from a web application.</span></span> <span data-ttu-id="4daee-104">이 모듈에서는 서버를 사용하지 않는 다른 함수를 만들어서 업로드된 이미지를 감시하고 썸네일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-104">In this module, you create another serverless function to watch for uploaded images and create thumbnails from them.</span></span>

## <a name="create-a-blob-storage-container-for-thumbnails"></a><span data-ttu-id="4daee-105">썸네일에 대한 Blob Storage 컨테이너 만들기</span><span class="sxs-lookup"><span data-stu-id="4daee-105">Create a blob storage container for thumbnails</span></span>

<span data-ttu-id="4daee-106">전체 크기 이미지는 **images**라는 컨테이너에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-106">The full size images are stored in a container named **images**.</span></span> <span data-ttu-id="4daee-107">다른 컨테이너가 해당 이미지의 썸네일을 저장해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-107">You need another container to store thumbnails of those images.</span></span>

1. <span data-ttu-id="4daee-108">Cloud Shell(bash)에 로그인했는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-108">Ensure you are still signed in to the Cloud Shell (bash).</span></span>  <span data-ttu-id="4daee-109">그렇지 않은 경우 **포커스 모드로 전환**을 선택하여 Cloud Shell 창을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-109">If not, select **Enter focus mode** to open a Cloud Shell window.</span></span> 

1. <span data-ttu-id="4daee-110">모든 Blob에 대한 공용 액세스 권한이 있는 저장소 계정에서 **thumbnails**라는 새 컨테이너를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-110">Create a new container named **thumbnails** in your storage account with public access to all blobs.</span></span>

    ```azurecli
    az storage container create -n thumbnails --account-name <storage account name> --public-access blob
    ```


## <a name="create-a-blob-triggered-serverless-function"></a><span data-ttu-id="4daee-111">Blob 트리거 서버를 사용하지 않는 함수 만들기</span><span class="sxs-lookup"><span data-stu-id="4daee-111">Create a blob-triggered serverless function</span></span>

<span data-ttu-id="4daee-112">트리거는 함수가 호출되는 방식을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-112">A trigger defines how a function is invoked.</span></span> <span data-ttu-id="4daee-113">다음에 만든 함수는 Blob 트리거를 사용합니다. Blob(이미지 파일)을 **images** 컨테이너에 업로드하면 함수가 자동으로 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-113">The function you create next uses a blob trigger: the function is automatically invoked when a blob (image file) is uploaded to the **images** container.</span></span> <span data-ttu-id="4daee-114">함수에는 하나의 트리거만 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-114">A function must have one trigger.</span></span> <span data-ttu-id="4daee-115">트리거는 관련 데이터가 있으며, 이 데이터는 일반적으로 함수를 트리거한 페이로드입니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-115">Triggers have associated data, which is usually the payload that triggered the function.</span></span>

<span data-ttu-id="4daee-116">바인딩은 함수가 Azure 또는 타사 서비스에서 데이터를 읽고 기록하는 방법을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-116">Bindings define how a function reads or writes data in Azure or third-party services.</span></span> <span data-ttu-id="4daee-117">이 함수는 트리거하는 이미지의 썸네일 버전을 만들고 *thumbnails* 컨테이너에서 썸네일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-117">This function creates a thumbnail version of the image that triggers it and saves the thumbnail in a *thumbnails* container.</span></span>

1. <span data-ttu-id="4daee-118">Azure Portal에서 함수 앱을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-118">Open your function app in the Azure Portal.</span></span>

1. <span data-ttu-id="4daee-119">함수 앱 창의 왼쪽 탐색에서 **함수** 위로 마우스를 가져가고, **+** 를 클릭하여 새로운 서버를 사용하지 않는 함수를 만들기 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-119">In the function app window's left hand navigation, hover over **Functions** and click **+** to start creating a new serverless function.</span></span> <span data-ttu-id="4daee-120">빠른 시작 페이지가 표시되면 **사용자 지정 함수**를 클릭하여 함수 템플릿 목록을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-120">If a quickstart page appears, click **Custom function** to see a list of function templates.</span></span>

1. <span data-ttu-id="4daee-121">**BlobTrigger** 템플릿을 찾아 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-121">Find the **BlobTrigger** template and select it.</span></span>

1. <span data-ttu-id="4daee-122">이러한 값을 사용하여 이미지를 업로드하는 경우 썸네일을 생성하는 함수를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-122">Use these values to create a function that creates thumbnails as images are uploaded.</span></span>

    | <span data-ttu-id="4daee-123">설정</span><span class="sxs-lookup"><span data-stu-id="4daee-123">Setting</span></span>      |  <span data-ttu-id="4daee-124">제안 값</span><span class="sxs-lookup"><span data-stu-id="4daee-124">Suggested value</span></span>   | <span data-ttu-id="4daee-125">설명</span><span class="sxs-lookup"><span data-stu-id="4daee-125">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="4daee-126">**언어**</span><span class="sxs-lookup"><span data-stu-id="4daee-126">**Language**</span></span> | <span data-ttu-id="4daee-127">C# 또는 JavaScript</span><span class="sxs-lookup"><span data-stu-id="4daee-127">C# or JavaScript</span></span> | <span data-ttu-id="4daee-128">기본 설정 언어를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-128">Choose your preferred language.</span></span> |
    | <span data-ttu-id="4daee-129">**함수 이름 지정**</span><span class="sxs-lookup"><span data-stu-id="4daee-129">**Name your function**</span></span> | <span data-ttu-id="4daee-130">ResizeImage</span><span class="sxs-lookup"><span data-stu-id="4daee-130">ResizeImage</span></span> | <span data-ttu-id="4daee-131">응용 프로그램이 함수를 검색할 수 있도록 표시된 대로 이 이름을 정확히 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-131">Type this name exactly as shown so the application can discover the function.</span></span> |
    | <span data-ttu-id="4daee-132">**Path**</span><span class="sxs-lookup"><span data-stu-id="4daee-132">**Path**</span></span> | <span data-ttu-id="4daee-133">images/{name}</span><span class="sxs-lookup"><span data-stu-id="4daee-133">images/{name}</span></span> | <span data-ttu-id="4daee-134">파일이 **images** 컨테이너에 표시되면 함수를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-134">Execute the function when a file appears in the **images** container.</span></span> |
    | <span data-ttu-id="4daee-135">**저장소 계정 정보**</span><span class="sxs-lookup"><span data-stu-id="4daee-135">**Storage account information**</span></span> | <span data-ttu-id="4daee-136">AZURE_STORAGE_CONNECTION_STRING</span><span class="sxs-lookup"><span data-stu-id="4daee-136">AZURE_STORAGE_CONNECTION_STRING</span></span> | <span data-ttu-id="4daee-137">연결 문자열을 사용하여 이전에 만든 환경 변수를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-137">Use the environment variable previously created with the connection string.</span></span> |

    ![새로운 함수에 대한 설정을 입력합니다.](media/functions-first-serverless-web-app/3-new-function.png)

1. <span data-ttu-id="4daee-139">**만들기**를 클릭하여 함수를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-139">Click **Create** to create the function.</span></span>

1. <span data-ttu-id="4daee-140">함수를 만들면 **통합**을 클릭하여 해당 트리거, 입력 및 출력 바인딩을 봅니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-140">When the function is created, click **Integrate** to view its trigger, input, and output bindings.</span></span>

1. <span data-ttu-id="4daee-141">**새 출력**을 클릭하여 새 출력 바인딩을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-141">Click **New output** to create a new output binding.</span></span>

    ![통합 탭에서 새 출력 선택](media/functions-first-serverless-web-app/3-new-output.jpg)

1. <span data-ttu-id="4daee-143">**Azure Blob Storage**를 선택하고 **선택**을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-143">Select **Azure Blob Storage** and click **Select**.</span></span> <span data-ttu-id="4daee-144">**선택** 단추를 보려면 아래로 스크롤해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-144">You may have to scroll down to see the **Select** button.</span></span>

    ![Azure Blob Storage 선택](media/functions-first-serverless-web-app/3-storage-output.jpg)

1. <span data-ttu-id="4daee-146">다음 값을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-146">Enter the following values.</span></span>

    | <span data-ttu-id="4daee-147">설정</span><span class="sxs-lookup"><span data-stu-id="4daee-147">Setting</span></span>      |  <span data-ttu-id="4daee-148">제안 값</span><span class="sxs-lookup"><span data-stu-id="4daee-148">Suggested value</span></span>   | <span data-ttu-id="4daee-149">설명</span><span class="sxs-lookup"><span data-stu-id="4daee-149">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="4daee-150">**Blob 매개 변수 이름**</span><span class="sxs-lookup"><span data-stu-id="4daee-150">**Blob parameter name**</span></span> | <span data-ttu-id="4daee-151">썸네일</span><span class="sxs-lookup"><span data-stu-id="4daee-151">thumbnail</span></span> | <span data-ttu-id="4daee-152">함수는 이 이름을 가진 매개 변수를 사용하여 썸네일을 작성합니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-152">The function uses the parameter with this name to write the thumbnail.</span></span> |
    | <span data-ttu-id="4daee-153">**함수 반환 값 사용**</span><span class="sxs-lookup"><span data-stu-id="4daee-153">**Use function return value**</span></span> | <span data-ttu-id="4daee-154">아니요</span><span class="sxs-lookup"><span data-stu-id="4daee-154">No</span></span> |  |
    | <span data-ttu-id="4daee-155">**Path**</span><span class="sxs-lookup"><span data-stu-id="4daee-155">**Path**</span></span> | <span data-ttu-id="4daee-156">thumbnails/{name}</span><span class="sxs-lookup"><span data-stu-id="4daee-156">thumbnails/{name}</span></span> | <span data-ttu-id="4daee-157">썸네일은 **thumbnails**라는 컨테이너에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-157">The thumbnails are written to a container named **thumbnails**.</span></span> |
    | <span data-ttu-id="4daee-158">**Storage 계정 연결**</span><span class="sxs-lookup"><span data-stu-id="4daee-158">**Storage account connection**</span></span> | <span data-ttu-id="4daee-159">AZURE_STORAGE_CONNECTION_STRING</span><span class="sxs-lookup"><span data-stu-id="4daee-159">AZURE_STORAGE_CONNECTION_STRING</span></span> | <span data-ttu-id="4daee-160">연결 문자열을 사용하여 이전에 만든 환경 변수를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-160">Use the environment variable previously created with the connection string.</span></span> |

    ![Blob 출력 바인딩에 대한 설정 입력](media/functions-first-serverless-web-app/3-blob-output.png)

1. <span data-ttu-id="4daee-162">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="4daee-162">**JavaScript**</span></span>

    1. <span data-ttu-id="4daee-163">(JavaScript) 창의 오른쪽 위 모서리에서 **고급 편집기**를 클릭하여 바인딩을 나타내는 JSON을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-163">(JavaScript) Click on **Advanced editor** in the top right corner of the window to reveal the JSON representing the bindings.</span></span>

    1. <span data-ttu-id="4daee-164">(JavaScript) `blobTrigger` 바인딩에서 `binary`의 값을 포함한 `dataType`이라는 속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-164">(JavaScript) In the `blobTrigger` binding, add a property named `dataType` with a value of `binary`.</span></span> <span data-ttu-id="4daee-165">이 Blob 콘텐츠를 이진 데이터로 함수에 전달하도록 바인딩을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-165">This configures the binding to pass the blob contents to the function as binary data.</span></span>

    ```json
    {
      "name": "myBlob",
      "type": "blobTrigger",
      "direction": "in",
      "path": "images/{name}",
      "connection": "AZURE_STORAGE_CONNECTION_STRING",
      "dataType": "binary"
    }
    ```

1. <span data-ttu-id="4daee-166">**저장**을 클릭하여 새 바인딩을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-166">Click **Save** to create the new binding.</span></span>

1. <span data-ttu-id="4daee-167">**C#**</span><span class="sxs-lookup"><span data-stu-id="4daee-167">**C#**</span></span>

    1. <span data-ttu-id="4daee-168">(C#) 왼쪽 탐색 창에서 **ResizeImage** 함수 이름을 선택하여 함수의 소스 코드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-168">(C#) Select the **ResizeImage** function name on the left navigation to open the function's source code.</span></span>

    1. <span data-ttu-id="4daee-169">(C#) 썸네일을 생성하기 위해 함수에는 **ImageResizer**라는 NuGet 패키지가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-169">(C#) The function requires a NuGet package called **ImageResizer** to generate the thumbnails.</span></span> <span data-ttu-id="4daee-170">**project.json** 파일을 사용하여 NuGet 패키지를 C# 함수에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-170">NuGet packages are added to C# functions using a **project.json** file.</span></span> <span data-ttu-id="4daee-171">파일을 만들려면 오른쪽에서 **파일 보기**를 클릭하여 함수를 구성하는 파일을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-171">To create the file, click **View Files** on the right to reveal the files that make up the function.</span></span>
    
    1. <span data-ttu-id="4daee-172">(C#) **추가**를 클릭하여 **project.json**이라는 새 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-172">(C#) Click **Add** to add a new file named **project.json**.</span></span>
    
    1. <span data-ttu-id="4daee-173">(C#) [**/csharp/ResizeImage/project.json**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/project.json)의 내용을 새로 생성된 파일에 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-173">(C#) Copy the contents of [**/csharp/ResizeImage/project.json**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/project.json) into the newly created file.</span></span> <span data-ttu-id="4daee-174">파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-174">Save the file.</span></span> <span data-ttu-id="4daee-175">파일이 업데이트될 때 패키지가 자동으로 복원됩니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-175">Packages are automatically restored when the file is updated.</span></span>
    
        ![ImageResizer를 포함한 project.json 파일](media/functions-first-serverless-web-app/3-project-json.png)
    
    1. <span data-ttu-id="4daee-177">(C#) **파일 보기** 아래에서 **run.csx**를 선택하고, 해당 콘텐츠를 [**/csharp/ResizeImage/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run.csx)의 콘텐츠로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-177">(C#) Select **run.csx** under **View Files** and replace its content with the content in [**/csharp/ResizeImage/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run.csx).</span></span>

1. <span data-ttu-id="4daee-178">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="4daee-178">**JavaScript**</span></span> 

    1. <span data-ttu-id="4daee-179">(JavaScript) 사진의 크기를 조정하기 위해 이 함수에는 npm의 `jimp` 패키지가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-179">(JavaScript) This function requires the `jimp` package from npm to resize the photo.</span></span> <span data-ttu-id="4daee-180">npm 패키지를 설치하려면 왼쪽 탐색 창에서 함수 앱의 이름을 클릭하고, **플랫폼 기능**을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-180">To install the npm package, click on the Function App's name on the left navigation and click **Platform features**.</span></span>

    1. <span data-ttu-id="4daee-181">(JavaScript) **콘솔**을 클릭하여 콘솔 창을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-181">(JavaScript) Click **Console** to reveal a console window.</span></span>

    1. <span data-ttu-id="4daee-182">(JavaScript) 콘솔에서 `npm install jimp` 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-182">(JavaScript) Run the command `npm install jimp` in the console.</span></span> <span data-ttu-id="4daee-183">작업을 완료하는 데 일이 분 정도 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-183">It may take a minute or two to complete the operation.</span></span>

    1. <span data-ttu-id="4daee-184">(JavaScript) 왼쪽 탐색 창에서 **ResizeImage** 함수 이름을 클릭하여 함수를 표시하고, 모든 **index.js**를 [**/javascript ResizeImage / index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index.js)의 콘텐츠로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-184">(JavaScript) Click on the **ResizeImage** function name in the left navigation to reveal the function, replace all of **index.js** with the content of [**/javascript/ResizeImage/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index.js).</span></span>

1. <span data-ttu-id="4daee-185">코드 창 아래에서 **로그**를 클릭하여 로그 패널을 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-185">Click **Logs** below the code window to expand the logs panel.</span></span>

1. <span data-ttu-id="4daee-186">**저장**을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-186">Click **Save**.</span></span> <span data-ttu-id="4daee-187">로그 패널을 확인하여 함수가 성공적으로 저장되고 오류가 없는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-187">Check the logs panel to ensure the function is successfully saved and there are no errors.</span></span>


## <a name="test-the-serverless-function"></a><span data-ttu-id="4daee-188">서버를 사용하지 않는 함수 테스트</span><span class="sxs-lookup"><span data-stu-id="4daee-188">Test the serverless function</span></span>

1. <span data-ttu-id="4daee-189">브라우저에서 응용 프로그램을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-189">Open the application in a browser.</span></span> <span data-ttu-id="4daee-190">이미지 파일을 선택하고 업로드합니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-190">Select an image file and upload it.</span></span> <span data-ttu-id="4daee-191">업로드가 완료되지만 아직 이미지를 표시하는 기능을 추가하지 않았으므로 앱은 업로드한 사진을 표시하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-191">The upload completes, but because we haven't added the ability to display images yet, the app doesn't show the uploaded photo.</span></span>

1. <span data-ttu-id="4daee-192">Cloud Shell에서 이미지를 **images** 컨테이너로 업로드했는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-192">In the Cloud Shell, confirm the image was uploaded to the **images** container.</span></span>

    ```azurecli
    az storage blob list --account-name <storage account name> -c images -o table
    ```

1. <span data-ttu-id="4daee-193">**thumbnails**라는 컨테이너에서 썸네일이 생성되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-193">Confirm the thumbnail was created in a container named **thumbnails**.</span></span>

    ```azurecli
    az storage blob list --account-name <storage account name> -c thumbnails -o table
    ```

1. <span data-ttu-id="4daee-194">썸네일 URL을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-194">Obtain the thumbnail's URL.</span></span>

    ```azurecli
    az storage blob url --account-name <storage account name> -c thumbnails -n <filename> --output tsv
    ```

    <span data-ttu-id="4daee-195">브라우저에서 URL을 열고 썸네일이 제대로 생성되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-195">Open the URL in a browser and confirm the thumbnail was properly created.</span></span>

1. <span data-ttu-id="4daee-196">다음 자습서로 넘어가기 전에 **images** 및 **thumbnails** 컨테이너에서 모든 파일을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-196">Before moving on to the next tutorial, delete all files in the **images** and **thumbnails** containers.</span></span>

    ```azurecli
    az storage blob delete-batch -s images --account-name <storage account name>
    ```
    ```azurecli
    az storage blob delete-batch -s thumbnails --account-name <storage account name>
    ```


## <a name="summary"></a><span data-ttu-id="4daee-197">요약</span><span class="sxs-lookup"><span data-stu-id="4daee-197">Summary</span></span>

<span data-ttu-id="4daee-198">이 모듈에서는 이미지를 Blob Storage 컨테이너에 업로드할 때마다 썸네일을 만드는 서버를 사용하지 않는 함수를 만들었습니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-198">In this module, you created a serverless function to create a thumbnail whenever an image is uploaded to a Blob storage container.</span></span> <span data-ttu-id="4daee-199">다음으로, Azure Cosmos DB를 사용하여 이미지 메타데이터를 저장하고 나열하는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="4daee-199">Next, you learn how to use Azure Cosmos DB to store and list image metadata.</span></span>
