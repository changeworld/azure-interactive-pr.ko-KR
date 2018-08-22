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
ms.openlocfilehash: 2202cdebe77668972372983a0e802d00edabf6dd
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079467"
---
<span data-ttu-id="dbb2e-103">Azure Cosmos DB는 서버를 사용하지 않는 전 세계에 배포된 Microsoft의 멀티모델 데이터베이스입니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-103">Azure Cosmos DB is Microsoft's serverless, globally distributed, multi-model database.</span></span> <span data-ttu-id="dbb2e-104">이 모듈에서는 Azure Functions를 사용하여 Cosmos DB에서 JSON 문서로 이미지 메타데이터를 저장하고 검색하는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-104">In this module, you learn how to use Azure Functions to store and retrieve image metadata as JSON documents in Cosmos DB.</span></span>

## <a name="create-a-cosmos-db-account-database-and-collection"></a><span data-ttu-id="dbb2e-105">Cosmos DB 계정, 데이터베이스 및 컬렉션 만들기</span><span class="sxs-lookup"><span data-stu-id="dbb2e-105">Create a Cosmos DB account, database, and collection</span></span>

<span data-ttu-id="dbb2e-106">Cosmos DB 계정은 Cosmos DB 데이터베이스를 포함하는 Azure 리소스입니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-106">A Cosmos DB account is an Azure resource that contains Cosmos DB databases.</span></span>

1. <span data-ttu-id="dbb2e-107">Cloud Shell에 로그인했는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-107">Ensure you are still signed into the Cloud Shell.</span></span>  <span data-ttu-id="dbb2e-108">그렇지 않은 경우 **포커스 모드로 전환**을 선택하여 Cloud Shell 창을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-108">If not, select **Enter focus mode** to open a Cloud Shell window.</span></span> 

1. <span data-ttu-id="dbb2e-109">이 자습서의 다른 리소스와 동일한 리소스 그룹에서 고유한 이름을 사용하여 Cosmos DB 계정을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-109">Create a Cosmos DB account with a unique name in the same resource group as the other resources in this tutorial.</span></span>

    ```azurecli
    az cosmosdb create -g first-serverless-app -n <cosmos db account name>
    ```

1. <span data-ttu-id="dbb2e-110">Cosmos DB 계정을 만든 후에 계정에서 **imagesdb**라는 새 데이터베이스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-110">After the Cosmos DB account is created, create a new database named **imagesdb** in the account.</span></span>

    ```azurecli
    az cosmosdb database create -g first-serverless-app -n <cosmos db account name> --db-name imagesdb
    ```

1. <span data-ttu-id="dbb2e-111">데이터베이스를 만든 후에 데이터베이스에서 400RU(요청 단위)의 처리량을 가진 **images**라는 새 컬렉션을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-111">After the database is created, create a new collection named **images** in the database with a throughput of 400 request units (RUs).</span></span>

    ```azurecli
    az cosmosdb collection create -g first-serverless-app -n <cosmos db account name> --db-name imagesdb --collection-name images --throughput 400
    ```


## <a name="save-a-document-to-cosmos-db-when-a-thumbnail-is-created"></a><span data-ttu-id="dbb2e-112">썸네일를 만들 때 Cosmos DB에 문서를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-112">Save a document to Cosmos DB when a thumbnail is created</span></span>

<span data-ttu-id="dbb2e-113">Cosmos DB 출력 바인딩을 통해 Azure Functions의 Cosmos DB 컬렉션에서 문서를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-113">The Cosmos DB output binding lets you create documents in a Cosmos DB collection from Azure Functions.</span></span> <span data-ttu-id="dbb2e-114">다음 단계에서는 **ResizeImage** 함수에서 Cosmos DB 출력 바인딩을 구성하고, 저장할 문서(개체)를 반환하도록 함수를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-114">In the following steps, you configure a Cosmos DB output binding in the **ResizeImage** function and modify the function to return a document (object) to be saved.</span></span>

1. <span data-ttu-id="dbb2e-115">Azure Portal에서 함수 앱을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-115">Open the function app in the Azure Portal.</span></span>

1. <span data-ttu-id="dbb2e-116">왼쪽 탐색 영역에서 **ResizeImage** 함수를 확장한 다음, **통합**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-116">In the left hand navigation, expand the **ResizeImage** function, and then select **Integrate**.</span></span>

1. <span data-ttu-id="dbb2e-117">**출력**에서 **새 출력**을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-117">Under **Outputs**, click **New Output**.</span></span>

1. <span data-ttu-id="dbb2e-118">**Azure Cosmos DB** 항목을 찾아 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-118">Find the **Azure Cosmos DB** item and select it.</span></span> <span data-ttu-id="dbb2e-119">그런 다음 **선택**을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-119">Then click **Select**.</span></span>

    ![새 출력 선택](media/functions-first-serverless-web-app/4-new-output.jpg)

1. <span data-ttu-id="dbb2e-121">다음 값으로 **Azure Cosmos DB 출력** 아래에 있는 필드를 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-121">Fill out the fields under **Azure Cosmos DB output** with the following values.</span></span>

    | <span data-ttu-id="dbb2e-122">설정</span><span class="sxs-lookup"><span data-stu-id="dbb2e-122">Setting</span></span>      |  <span data-ttu-id="dbb2e-123">제안 값</span><span class="sxs-lookup"><span data-stu-id="dbb2e-123">Suggested value</span></span>   | <span data-ttu-id="dbb2e-124">설명</span><span class="sxs-lookup"><span data-stu-id="dbb2e-124">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="dbb2e-125">**문서 매개 변수 이름**</span><span class="sxs-lookup"><span data-stu-id="dbb2e-125">**Document parameter name**</span></span> | <span data-ttu-id="dbb2e-126">**함수 반환 값 사용**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-126">Select **Use function return value**</span></span> | <span data-ttu-id="dbb2e-127">텍스트 상자의 값은 자동으로 **$return**으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-127">The value of the textbox is automatically set to **$return**.</span></span> |
    | <span data-ttu-id="dbb2e-128">**데이터베이스 이름**</span><span class="sxs-lookup"><span data-stu-id="dbb2e-128">**Database name**</span></span> | <span data-ttu-id="dbb2e-129">imagesdb</span><span class="sxs-lookup"><span data-stu-id="dbb2e-129">imagesdb</span></span> | <span data-ttu-id="dbb2e-130">사용자가 만든 데이터베이스의 이름을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-130">Use the name of the database that you created.</span></span> |
    | <span data-ttu-id="dbb2e-131">**컬렉션 이름**</span><span class="sxs-lookup"><span data-stu-id="dbb2e-131">**Collection name**</span></span> | <span data-ttu-id="dbb2e-132">images</span><span class="sxs-lookup"><span data-stu-id="dbb2e-132">images</span></span> | <span data-ttu-id="dbb2e-133">사용자가 만든 컬렉션의 이름을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-133">Use the name of the collection that you created.</span></span> |

1. <span data-ttu-id="dbb2e-134">**Azure Cosmos DB 계정 연결** 옆에 있는 **새로 만들기**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-134">Next to **Azure Cosmos DB account connection**, click **new**.</span></span> <span data-ttu-id="dbb2e-135">이전에 만든 Cosmos DB 계정을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-135">Select the Cosmos DB account you previously created.</span></span>

    ![Azure Cosmos DB 출력 바인딩에 대한 설정 입력](media/functions-first-serverless-web-app/4-cosmos-db-output.png)

1. <span data-ttu-id="dbb2e-137">**저장**을 클릭하여 Cosmos DB 출력 바인딩을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-137">Click **Save** to create the Cosmos DB output binding.</span></span>

1. <span data-ttu-id="dbb2e-138">왼쪽에서 **ResizeImage** 함수 이름을 클릭하여 함수를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-138">Click on the **ResizeImage** function name on the left to open the function.</span></span>

1. <span data-ttu-id="dbb2e-139">**C#**</span><span class="sxs-lookup"><span data-stu-id="dbb2e-139">**C#**</span></span>

    1. <span data-ttu-id="dbb2e-140">(C#) 함수의 반환 형식을 **void**에서 **object**로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-140">(C#) Change the return type of the function from **void** to **object**.</span></span>

    1. <span data-ttu-id="dbb2e-141">(C#) 함수 끝에서 다음 코드 블록을 추가하여 저장할 문서를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-141">(C#) At the end of the function, add the following code block to return the document to be saved:</span></span>
    
        ```csharp
        return new {
            id = name,
            imgPath = "/images/" + name,
            thumbnailPath = "/thumbnails/" + name
        };
        ```
    
        ![수정 후 ResizeImages 함수의 run.csx](media/functions-first-serverless-web-app/4-update-function.png)

1. <span data-ttu-id="dbb2e-143">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="dbb2e-143">**JavaScript**</span></span>

    1. <span data-ttu-id="dbb2e-144">(JavaScript) `else` 절에서 `context.done()` 문을 변경하여 Cosmos DB에 저장할 문서를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-144">(JavaScript) Change the `context.done()` statement in the `else` clause to return the document to be saved to Cosmos DB.</span></span>

    ```javascript
    if (error) {
        context.done(error);
    } else {
        context.bindings.thumbnail = stream;
        context.done(null, {
            id: context.bindingData.name,
            imgPath: "/images/" + context.bindingData.name,
            thumbnailPath: "/thumbnails/" + context.bindingData.name
        });
    }
    ```

1. <span data-ttu-id="dbb2e-145">코드 창 아래에서 **로그**를 클릭하여 로그 패널을 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-145">Click **Logs** below the code window to expand the logs panel.</span></span>

1. <span data-ttu-id="dbb2e-146">**저장**을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-146">Click **Save**.</span></span> <span data-ttu-id="dbb2e-147">로그 패널을 확인하여 함수가 성공적으로 저장되고 오류가 없는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-147">Check the logs panel to ensure the function is successfully saved and there are no errors.</span></span>


## <a name="create-a-function-to-list-images-from-cosmos-db"></a><span data-ttu-id="dbb2e-148">Cosmos DB에서 이미지를 나열하는 함수 만들기</span><span class="sxs-lookup"><span data-stu-id="dbb2e-148">Create a function to list images from Cosmos DB</span></span>

<span data-ttu-id="dbb2e-149">Cosmos DB에서 이미지 메타데이터를 검색하기 위해 웹 응용 프로그램에는 API가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-149">The web application requires an API to retrieve image metadata from Cosmos DB.</span></span> <span data-ttu-id="dbb2e-150">다음과 같은 단계에서</span><span class="sxs-lookup"><span data-stu-id="dbb2e-150">In the following steps.</span></span> <span data-ttu-id="dbb2e-151">Cosmos DB 입력 바인딩을 사용하는 HTTP 트리거 함수를 만들어서 데이터베이스 컬렉션을 쿼리합니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-151">uou create an HTTP triggered function that uses a Cosmos DB input binding to query the database collection.</span></span>

1. <span data-ttu-id="dbb2e-152">함수 앱에서 왼쪽에 있는 **함수**를 마우스로 가리키고 **+** 를 클릭하여 새 함수를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-152">In your function app, hover over **Functions** on the left and click **+** to create a new function.</span></span>

1. <span data-ttu-id="dbb2e-153">**HttpTrigger** 템플릿을 찾아 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-153">Find the **HttpTrigger** template and select it.</span></span>

1. <span data-ttu-id="dbb2e-154">이러한 값을 사용하여 get 이미지 URL을 생성하는 함수를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-154">Use these values to create a function that generates a get images URL.</span></span>

    | <span data-ttu-id="dbb2e-155">설정</span><span class="sxs-lookup"><span data-stu-id="dbb2e-155">Setting</span></span>      |  <span data-ttu-id="dbb2e-156">제안 값</span><span class="sxs-lookup"><span data-stu-id="dbb2e-156">Suggested value</span></span>   | <span data-ttu-id="dbb2e-157">설명</span><span class="sxs-lookup"><span data-stu-id="dbb2e-157">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="dbb2e-158">**함수 이름 지정**</span><span class="sxs-lookup"><span data-stu-id="dbb2e-158">**Name your function**</span></span> | <span data-ttu-id="dbb2e-159">GetImages</span><span class="sxs-lookup"><span data-stu-id="dbb2e-159">GetImages</span></span> | <span data-ttu-id="dbb2e-160">응용 프로그램이 함수를 검색할 수 있도록 표시된 대로 이 이름을 정확히 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-160">Type this name exactly as shown so the application can discover the function.</span></span> |
    | <span data-ttu-id="dbb2e-161">**권한 부여 수준**</span><span class="sxs-lookup"><span data-stu-id="dbb2e-161">**Authorization level**</span></span> | <span data-ttu-id="dbb2e-162">익명</span><span class="sxs-lookup"><span data-stu-id="dbb2e-162">Anonymous</span></span> | <span data-ttu-id="dbb2e-163">함수가 공개적으로 액세스될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-163">Allow the function to be accessed publicly.</span></span> |

1. <span data-ttu-id="dbb2e-164">**만들기**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-164">Click **Create**.</span></span>

1. <span data-ttu-id="dbb2e-165">새 함수를 만들면 왼쪽 탐색 영역에 있는 함수의 이름에서 **통합**을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-165">When the new function is created, click **Integrate** under the function's name on the left navigation.</span></span>

1. <span data-ttu-id="dbb2e-166">**새 입력**을 클릭하고 **Azure Cosmos DB**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-166">Click **New Input** and select **Azure Cosmos DB**.</span></span> 

    ![새 입력 선택](media/functions-first-serverless-web-app/4-new-input.jpg)

1. <span data-ttu-id="dbb2e-168">**선택**을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-168">Click **Select**.</span></span>

1. <span data-ttu-id="dbb2e-169">다음 값을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-169">Fill out the following values:</span></span>

    | <span data-ttu-id="dbb2e-170">설정</span><span class="sxs-lookup"><span data-stu-id="dbb2e-170">Setting</span></span>      |  <span data-ttu-id="dbb2e-171">제안 값</span><span class="sxs-lookup"><span data-stu-id="dbb2e-171">Suggested value</span></span>   | <span data-ttu-id="dbb2e-172">설명</span><span class="sxs-lookup"><span data-stu-id="dbb2e-172">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="dbb2e-173">**문서 매개 변수 이름**</span><span class="sxs-lookup"><span data-stu-id="dbb2e-173">**Document parameter name**</span></span> | <span data-ttu-id="dbb2e-174">documents</span><span class="sxs-lookup"><span data-stu-id="dbb2e-174">documents</span></span> | <span data-ttu-id="dbb2e-175">함수에서 매개 변수 이름과 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-175">Matches parameter name in the function.</span></span> |
    | <span data-ttu-id="dbb2e-176">**데이터베이스 이름**</span><span class="sxs-lookup"><span data-stu-id="dbb2e-176">**Database name**</span></span> | <span data-ttu-id="dbb2e-177">imagesdb</span><span class="sxs-lookup"><span data-stu-id="dbb2e-177">imagesdb</span></span> |  |
    | <span data-ttu-id="dbb2e-178">**컬렉션 이름**</span><span class="sxs-lookup"><span data-stu-id="dbb2e-178">**Collection name**</span></span> | <span data-ttu-id="dbb2e-179">images</span><span class="sxs-lookup"><span data-stu-id="dbb2e-179">images</span></span> |  |
    | <span data-ttu-id="dbb2e-180">**SQL query**</span><span class="sxs-lookup"><span data-stu-id="dbb2e-180">**SQL query**</span></span> | <span data-ttu-id="dbb2e-181">select \* from c order by c._ts desc</span><span class="sxs-lookup"><span data-stu-id="dbb2e-181">select \* from c order by c._ts desc</span></span> | <span data-ttu-id="dbb2e-182">먼저 최신 문서를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-182">Get documents, latest documents first.</span></span> |
    | <span data-ttu-id="dbb2e-183">**Azure Cosmos DB 계정 연결**</span><span class="sxs-lookup"><span data-stu-id="dbb2e-183">**Azure Cosmos DB account connection**</span></span> | <span data-ttu-id="dbb2e-184">기존 연결 문자열 선택</span><span class="sxs-lookup"><span data-stu-id="dbb2e-184">Select existing connection string</span></span> |  |

1. <span data-ttu-id="dbb2e-185">**저장**을 클릭하여 입력 바인딩을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-185">Click **Save** to create the input binding.</span></span>

1. <span data-ttu-id="dbb2e-186">**C#**</span><span class="sxs-lookup"><span data-stu-id="dbb2e-186">**C#**</span></span>

    1. <span data-ttu-id="dbb2e-187">함수의 이름을 클릭하여 코드 창을 연 다음, 모든 **run.csx**를 [**/csharp/GetImages/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetImages/run.csx)의 콘텐츠로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-187">Click the function's name to open the code window, and then replace all of **run.csx** with the content in [**/csharp/GetImages/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetImages/run.csx).</span></span>

1. <span data-ttu-id="dbb2e-188">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="dbb2e-188">**JavaScript**</span></span>

    1. <span data-ttu-id="dbb2e-189">함수의 이름을 클릭하여 코드 창을 연 다음, 모든 **index.js**를 [**/javascript/GetImages/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetImages/index.js)의 콘텐츠로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-189">Click the function's name to open the code window, and then replace all of **index.js** with the content in [**/javascript/GetImages/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetImages/index.js).</span></span>

1. <span data-ttu-id="dbb2e-190">코드 창 아래에서 **로그**를 클릭하여 로그 패널을 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-190">Click **Logs** below the code window to expand the logs panel.</span></span>

1. <span data-ttu-id="dbb2e-191">**저장**을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-191">Click **Save**.</span></span> <span data-ttu-id="dbb2e-192">로그 패널을 확인하여 함수가 성공적으로 저장되고 오류가 없는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-192">Check the logs panel to ensure the function is successfully saved and there are no errors.</span></span>


## <a name="test-the-application"></a><span data-ttu-id="dbb2e-193">응용 프로그램 테스트</span><span class="sxs-lookup"><span data-stu-id="dbb2e-193">Test the application</span></span>

1. <span data-ttu-id="dbb2e-194">브라우저에서 응용 프로그램을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-194">Open the application in a browser.</span></span> <span data-ttu-id="dbb2e-195">이미지 파일을 선택하고 업로드합니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-195">Select an image file and upload it.</span></span>

1. <span data-ttu-id="dbb2e-196">몇 초 후에 새 이미지의 썸네일이 페이지에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-196">After a few seconds, the thumbnail of the new image appears on the page.</span></span>

1. <span data-ttu-id="dbb2e-197">Azure Portal에서 검색 상자를 사용하여 이름으로 Cosmos DB 계정을 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-197">In the Azure portal, use the Search box to search for your Cosmos DB account by name.</span></span> <span data-ttu-id="dbb2e-198">클릭하여 엽니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-198">Click it to open it.</span></span>

1. <span data-ttu-id="dbb2e-199">왼쪽에서 **데이터 탐색기**를 클릭하여 컬렉션 및 문서를 찾아봅니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-199">Click **Data Explorer** on the left to browse collections and documents.</span></span>

1. <span data-ttu-id="dbb2e-200">**imagesdb** 데이터베이스 아래에서 **images** 컬렉션을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-200">Under the **imagesdb** database, select the **images** collection.</span></span>

1. <span data-ttu-id="dbb2e-201">업로드된 이미지에 대한 문서가 생성되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-201">Confirm that a document was created for the uploaded image.</span></span>

    ![업로드된 이미지에 대한 문서를 표시하는 데이터 탐색기](media/functions-first-serverless-web-app/4-data-explorer.png)



## <a name="summary"></a><span data-ttu-id="dbb2e-203">요약</span><span class="sxs-lookup"><span data-stu-id="dbb2e-203">Summary</span></span>

<span data-ttu-id="dbb2e-204">이 모듈에서는 Cosmos DB 계정, 데이터베이스 및 컬렉션을 만드는 방법을 알아보았습니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-204">In this module, you learned how to create a Cosmos DB account, database, and collection.</span></span> <span data-ttu-id="dbb2e-205">Cosmos DB 바인딩을 사용하여 Cosmos DB 컬렉션에서 이미지 메타데이터를 저장하고 검색하는 방법을 알아보았습니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-205">You also learned how to use the Cosmos DB bindings to save and retrieve image metadata in the Cosmos DB collection.</span></span> <span data-ttu-id="dbb2e-206">다음으로, Microsoft Cognitive Services를 사용하여 각 업로드된 이미지에 대한 캡션을 자동으로 생성하는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="dbb2e-206">Next, you learn how to automatically generate a caption for each uploaded image using Microsoft Cognitive Services.</span></span>