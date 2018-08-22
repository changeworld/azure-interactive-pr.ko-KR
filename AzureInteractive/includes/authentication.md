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
ms.openlocfilehash: d1f9a07ce3d3b096b498e48b5c4f68c3454b2b37
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079491"
---
<span data-ttu-id="d19e3-103">Azure App Service 인증을 사용하면 Azure 함수 앱에서 턴키 인증을 지원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d19e3-103">Azure App Service Authentication enables turn-key authentication support in an Azure Function app.</span></span> <span data-ttu-id="d19e3-104">Facebook, Twitter, Microsoft 계정, Google 및 Azure Active Directory와 원활하게 통합합니다.</span><span class="sxs-lookup"><span data-stu-id="d19e3-104">It integrates seamlessly with Facebook, Twitter, Microsoft Account, Google, and Azure Active Directory.</span></span> <span data-ttu-id="d19e3-105">App Service 인증을 추가하여 웹앱의 백 엔드 API를 보호합니다.</span><span class="sxs-lookup"><span data-stu-id="d19e3-105">You'll add App Service Authentication to protect the backend APIs of your web app.</span></span>

## <a name="enable-app-service-authentication"></a><span data-ttu-id="d19e3-106">App Service 인증을 사용하도록 설정</span><span class="sxs-lookup"><span data-stu-id="d19e3-106">Enable App Service Authentication</span></span>

1. <span data-ttu-id="d19e3-107">Azure Portal에서 함수 앱을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="d19e3-107">Open the function app in the Azure Portal.</span></span>

1. <span data-ttu-id="d19e3-108">**플랫폼 기능** 아래에서 **인증/권한 부여**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d19e3-108">Under **Platform features**, select **Authentication/Authorization**.</span></span>

    ![인증/권한 부여 선택](media/functions-first-serverless-web-app/6-authorization.jpg)

1. <span data-ttu-id="d19e3-110">다음 값을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d19e3-110">Select the following values:</span></span>
    
    | <span data-ttu-id="d19e3-111">설정</span><span class="sxs-lookup"><span data-stu-id="d19e3-111">Setting</span></span>      |  <span data-ttu-id="d19e3-112">제안 값</span><span class="sxs-lookup"><span data-stu-id="d19e3-112">Suggested value</span></span>   | <span data-ttu-id="d19e3-113">설명</span><span class="sxs-lookup"><span data-stu-id="d19e3-113">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="d19e3-114">**App Service 인증**</span><span class="sxs-lookup"><span data-stu-id="d19e3-114">**App Service Authentication**</span></span> | <span data-ttu-id="d19e3-115">다른</span><span class="sxs-lookup"><span data-stu-id="d19e3-115">On</span></span> | <span data-ttu-id="d19e3-116">인증을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d19e3-116">Enable authentication.</span></span> |
    | <span data-ttu-id="d19e3-117">**요청이 인증되지 않은 경우 작업**</span><span class="sxs-lookup"><span data-stu-id="d19e3-117">**Action when request is not authenticated**</span></span> | <span data-ttu-id="d19e3-118">Azure Active Directory로 로그인</span><span class="sxs-lookup"><span data-stu-id="d19e3-118">Log in with Azure Active Directory</span></span> | <span data-ttu-id="d19e3-119">구성된 인증 방법(아래)을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d19e3-119">Select a configured authentication method (below).</span></span> |
    | <span data-ttu-id="d19e3-120">**인증 공급자**</span><span class="sxs-lookup"><span data-stu-id="d19e3-120">**Authentication Providers**</span></span> | <span data-ttu-id="d19e3-121">아래 참조</span><span class="sxs-lookup"><span data-stu-id="d19e3-121">See below</span></span> | <span data-ttu-id="d19e3-122">아래 참조</span><span class="sxs-lookup"><span data-stu-id="d19e3-122">See below</span></span> |
    | <span data-ttu-id="d19e3-123">**토큰 저장소**</span><span class="sxs-lookup"><span data-stu-id="d19e3-123">**Token store**</span></span> | <span data-ttu-id="d19e3-124">다른</span><span class="sxs-lookup"><span data-stu-id="d19e3-124">On</span></span> | <span data-ttu-id="d19e3-125">App Service가 토큰을 저장하고 관리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d19e3-125">Allow App Service to store and manage tokens.</span></span> |
    | <span data-ttu-id="d19e3-126">**허용되는 외부 리디렉션 URL**</span><span class="sxs-lookup"><span data-stu-id="d19e3-126">**Allowed external redirect URLs**</span></span> | <span data-ttu-id="d19e3-127">응용 프로그램의 URL(예: https://firstserverlessweb.z4.web.core.windows.net/)</span><span class="sxs-lookup"><span data-stu-id="d19e3-127">The URL of your application, for example: https://firstserverlessweb.z4.web.core.windows.net/</span></span> | <span data-ttu-id="d19e3-128">사용자가 인증된 후에 App Service가 리디렉션할 수 있는 URL입니다.</span><span class="sxs-lookup"><span data-stu-id="d19e3-128">URL(s) that App Service is allowed to redirect to after a user is authenticated.</span></span> |

1. <span data-ttu-id="d19e3-129">**Azure Active Directory**를 선택하여 **Azure Active Directory 설정**을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="d19e3-129">Select **Azure Active Directory** to reveal **Azure Active Directory Settings**.</span></span>

    1. <span data-ttu-id="d19e3-130">**Express**를 **관리 모드**로 선택하고 다음 정보를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="d19e3-130">Select **Express** as the **Management Mode** and fill in the following information.</span></span>
    
        | <span data-ttu-id="d19e3-131">설정</span><span class="sxs-lookup"><span data-stu-id="d19e3-131">Setting</span></span>      |  <span data-ttu-id="d19e3-132">제안 값</span><span class="sxs-lookup"><span data-stu-id="d19e3-132">Suggested value</span></span>   | <span data-ttu-id="d19e3-133">설명</span><span class="sxs-lookup"><span data-stu-id="d19e3-133">Description</span></span>                                        |
        | --- | --- | ---|
        | <span data-ttu-id="d19e3-134">**관리 모드**</span><span class="sxs-lookup"><span data-stu-id="d19e3-134">**Management mode**</span></span> | <span data-ttu-id="d19e3-135">Express, 새 AD 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="d19e3-135">Express, Create new AD app</span></span> | <span data-ttu-id="d19e3-136">서비스 주체 및 Azure Active Directory 인증을 자동으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d19e3-136">Automatically set up a service principal and Azure Active Directory authentication.</span></span> |
        | <span data-ttu-id="d19e3-137">**앱 만들기**</span><span class="sxs-lookup"><span data-stu-id="d19e3-137">**Create app**</span></span> | <span data-ttu-id="d19e3-138">my-serverless-webapp</span><span class="sxs-lookup"><span data-stu-id="d19e3-138">my-serverless-webapp</span></span> | <span data-ttu-id="d19e3-139">고유한 응용 프로그램 이름을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="d19e3-139">Enter a unique application name.</span></span> |
    
    1. <span data-ttu-id="d19e3-140">**확인**을 클릭하여 Azure Active Directory 설정을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="d19e3-140">Click **OK** to save the Azure Active Directory settings.</span></span>

    ![인증/권한 부여 및 Azure Active Directory 설정](media/functions-first-serverless-web-app/6-create-aad.png)

1. <span data-ttu-id="d19e3-142">**저장**을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="d19e3-142">Click **Save**.</span></span>


## <a name="modify-the-web-app-to-enable-authentication"></a><span data-ttu-id="d19e3-143">인증을 사용하도록 웹앱 수정</span><span class="sxs-lookup"><span data-stu-id="d19e3-143">Modify the web app to enable authentication</span></span>

1. <span data-ttu-id="d19e3-144">Cloud Shell에서 현재 디렉터리가 **www/dist** 폴더인지를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="d19e3-144">In the Cloud Shell, ensure that the current directory is the **www/dist** folder.</span></span>

    ```azurecli
    cd ~/functions-first-serverless-web-application/www/dist
    ```

1. <span data-ttu-id="d19e3-145">함수 앱에서 인증을 사용하려면 다음 코드 줄을 **settings.js**에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d19e3-145">To enable authentication in your function app, append the following line of code to **settings.js**.</span></span>

    `window.authEnabled = true`

    <span data-ttu-id="d19e3-146">다음 명령을 사용하거나 VIM과 같은 명령줄 편집기를 사용하여 결과를 변경하고 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="d19e3-146">Make the change and view the result by using the following commands or by using a command-line editor like VIM.</span></span>

    ```azurecli
    echo "window.authEnabled = true" >> settings.js
    ```

    <span data-ttu-id="d19e3-147">파일의 변경 내용을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="d19e3-147">Confirm the change was made to the file.</span></span>

    ```azurecli
    cat settings.js
    ```

1. <span data-ttu-id="d19e3-148">Blob Storage에 파일을 업로드합니다.</span><span class="sxs-lookup"><span data-stu-id="d19e3-148">Upload the file to Blob storage.</span></span>

    ```azurecli
    az storage blob upload -c \$web --account-name <storage account name> -f settings.js -n settings.js
    ```


## <a name="test-the-application"></a><span data-ttu-id="d19e3-149">응용 프로그램 테스트</span><span class="sxs-lookup"><span data-stu-id="d19e3-149">Test the application</span></span>

1. <span data-ttu-id="d19e3-150">브라우저에서 응용 프로그램을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="d19e3-150">Open the application in a browser.</span></span> <span data-ttu-id="d19e3-151">**로그인**을 클릭하여 로그인합니다.</span><span class="sxs-lookup"><span data-stu-id="d19e3-151">Click **Log in** and log in.</span></span>

1. <span data-ttu-id="d19e3-152">이미지 파일을 선택하고 업로드합니다.</span><span class="sxs-lookup"><span data-stu-id="d19e3-152">Select an image file and upload it.</span></span>

    ![로그인 페이지](media/functions-first-serverless-web-app/6-aad-auth.png)
    

## <a name="summary"></a><span data-ttu-id="d19e3-154">요약</span><span class="sxs-lookup"><span data-stu-id="d19e3-154">Summary</span></span>

<span data-ttu-id="d19e3-155">이 모듈에서는 Azure App Service 인증을 사용하여 응용 프로그램에 인증을 추가하는 방법을 알아보았습니다.</span><span class="sxs-lookup"><span data-stu-id="d19e3-155">In this module, you learned how to add authentication to the applcation using Azure App Service Authentication.</span></span>
