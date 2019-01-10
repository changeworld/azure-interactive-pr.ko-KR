---
title: 포함 파일
description: 포함 파일
services: functions
author: ggailey777
manager: jeconnoc
ms.service: multiple
ms.topic: include
ms.date: 10/12/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 3779c2e130afa7ee8d5879f30a924e258b7a41e9
ms.sourcegitcommit: fdb43556b8dcf67cb39c18e532b5fab7ac53eaee
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2018
ms.locfileid: "49315979"
---
빌드 중인 응용 프로그램은 사진 갤러리입니다. API를 호출하는 클라이언트 쪽 JavaScript를 사용하여 이미지를 업로드하고 표시합니다. 이 모듈에서 이미지를 업로드하려면 시간이 제한된 URL을 생성하는 서버를 사용하지 않는 함수를 사용하여 API를 만듭니다. 웹 응용 프로그램은 [Blob Storage REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api)를 사용하여 Blob Storage에 이미지를 업로드하기 위해 생성된 URL을 사용합니다.

## <a name="create-a-blob-storage-container-for-images"></a>이미지에 대한 Blob Storage 컨테이너 만들기

이미지를 업로드하고 호스트하기 위해 응용 프로그램에는 별도 저장소 컨테이너가 필요합니다.

1. Cloud Shell(bash)에 로그인했는지 확인합니다. 그렇지 않은 경우 **포커스 모드로 전환**을 선택하여 Cloud Shell 창을 엽니다.

1.  모든 Blob에 대한 공용 액세스 권한이 있는 저장소 계정에서 **images**라는 새 컨테이너를 만듭니다.

    ```azurecli
    az storage container create -n images --account-name <storage account name> --public-access blob
    ```

## <a name="create-an-azure-function-app"></a>Azure Function 앱 만들기

Azure Functions는 서버를 사용하지 않는 함수를 실행하기 위한 서비스입니다. HTTP 요청과 같은 이벤트에 의해 또는 저장소 컨테이너에서 Blob을 만들 때 서버를 사용하지 않는 함수를 트리거(호출)할 수 있습니다.

Azure 함수 앱은 하나 이상의 서버를 사용하지 않는 함수에 대한 컨테이너입니다.

**first-serverless-app**이라는 이전에 만든 리소스 그룹에서 고유한 이름의 새 Azure 함수 앱을 만듭니다. 함수 앱에는 Storage 계정이 필요합니다. 이 자습서에서는 기존 Storage 계정을 사용합니다.

```azurecli
az functionapp create -n <function app name> -g first-serverless-app -s <storage account name> -c westcentralus
```

## <a name="configure-the-function-app"></a>함수 앱 구성

이 자습서의 함수 앱에는 Functions 런타임의 버전 1.x가 필요합니다. `FUNCTIONS_EXTENSION_VERSION` 응용 프로그램 설정을 `~1`로 설정하면 함수 앱이 최신 1.x 버전에 고정됩니다. [az functionapp config appsettings set](https://docs.microsoft.com/cli/azure/functionapp/config/appsettings#set) 명령을 사용하여 응용 프로그램 설정을 설정합니다.

다음 Azure CLI 명령에서 `<app_name>은 함수 앱의 이름입니다.

```azurecli
az functionapp config appsettings set --name <function app name> -g first-serverless-app --settings FUNCTIONS_EXTENSION_VERSION=~1
```

## <a name="create-an-http-triggered-serverless-function"></a>HTTP 트리거 서버를 사용하지 않는 함수 만들기

사진 갤러리 웹 응용 프로그램을 통해 서버를 사용하지 않는 함수에 HTTP를 요청하여 Blob Storage에 이미지를 안전하게 업로드하기 위해 시간이 제한된 URL을 생성할 수 있습니다. 이 함수는 HTTP 요청에 의해 트리거되고 Azure Storage SDK를 사용하여 보안 URL을 생성하고 반환합니다.

1. 함수 앱을 만든 후에 검색 상자를 사용하여 Azure Portal에서 검색하고 클릭하여 엽니다.

    ![함수 앱 열기](media/functions-first-serverless-web-app/2-search-function-app.png)

1. 함수 앱 창의 왼쪽 탐색에서 **함수** 위로 마우스를 가져가고, **+** 를 클릭하여 새로운 서버를 사용하지 않는 함수를 만들기 시작합니다.

    ![새 함수 만들기](media/functions-first-serverless-web-app/2-new-function.png)

1. **사용자 지정 함수**를 클릭하여 함수 템플릿 목록을 확인합니다.

1. **HttpTrigger** 템플릿을 찾고 사용할 언어를 클릭합니다(C# 또는 JavaScript).

1. 이러한 값을 사용하여 Blob 업로드 URL을 생성하는 함수를 만듭니다.

    | 설정      |  제안 값   | 설명                                        |
    | --- | --- | ---|
    | **언어** | C# 또는 JavaScript | 사용할 언어를 선택합니다. |
    | **함수 이름 지정** | GetUploadUrl | 응용 프로그램이 함수를 검색할 수 있도록 표시된 대로 이 이름을 정확히 입력합니다. |
    | **권한 부여 수준** | 익명 | 함수가 공개적으로 액세스될 수 있습니다. |

    ![새 HTTP 트리거 함수에 대한 설정을 입력합니다.](media/functions-first-serverless-web-app/2-new-function-httptrigger.png)

1. **만들기**를 클릭하여 함수를 만듭니다.

1. **C#** 

    1. 함수의 소스 코드가 표시되면 모든 **run.csx**를 [**csharp/GetUploadUrl/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetUploadUrl/run.csx)의 콘텐츠로 바꿉니다.

1. **JavaScript** 

    1. (JavaScript) 보안 URL을 빌드하는 데 필요한 SAS(공유 액세스 서명) 토큰을 생성하기 위해 이 함수에는 npm의 `azure-storage` 패키지가 필요합니다. npm 패키지를 설치하려면 왼쪽 탐색 창에서 함수 앱의 이름을 클릭하고, **플랫폼 기능**을 클릭합니다.

    1. (JavaScript) **콘솔**을 클릭하여 콘솔 창을 표시합니다.

        ![콘솔 창을 엽니다.](media/functions-first-serverless-web-app/2-open-console.jpg)

    1. (JavaScript) `cd d:\home\site\wwwroot` 명령을 실행하여 현재 디렉터리가 **d:\home\site\wwwroot**인지 확인합니다.

    1. (JavaScript) `npm init -y` 명령을 실행하여 빈 **package.json** 파일을 만듭니다.

    1. (JavaScript) 콘솔에서 `npm install --save azure-storage` 명령을 실행하여 패키지를 설치하고 **package.json**에 저장합니다. 작업을 완료하는 데 일이 분 정도 걸릴 수 있습니다.

    1. (JavaScript) 왼쪽 탐색 창에서 함수 이름(**GetUploadUrl**)을 클릭하여 함수를 표시하고, 모든 **index.js**를 [**javascript/GetUploadUrl/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetUploadUrl/index.js)의 콘텐츠로 바꿉니다.
    
        ![업데이트 후 index.js](media/functions-first-serverless-web-app/2-paste-js.jpg)

1. 코드 창 아래에서 **로그**를 클릭하여 로그 패널을 확장합니다.

1. **저장**을 클릭합니다. 로그 패널을 확인하여 함수가 성공적으로 컴파일되었는지 확인합니다.

함수는 Blob Storage에 파일을 업로드하는 데 사용되는 SAS(공유 액세스 서명) URL이라는 항목을 생성합니다. SAS URL은 단기간 동안 유효하고 단일 파일만을 업로드하도록 허용합니다. [공유 액세스 서명을 사용하는 방법](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)에 대한 자세한 정보는 Blob Storage 설명서를 참조하세요.


## <a name="add-an-environment-variable-for-the-storage-connection-string"></a>저장소 연결 문자열에 대한 환경 변수 추가

SAS URL을 생성할 수 있도록 방금 만든 함수에는 Storage 계정에 대한 연결 문자열이 필요합니다. 함수의 본문에서 연결 문자열을 하드코딩하는 대신 응용 프로그램 설정으로 저장할 수 있습니다. 함수 앱의 모든 함수에서 환경 변수로 응용 프로그램 설정에 액세스할 수 있습니다.

1. Cloud Shell에서 Storage 계정 연결 문자열을 쿼리하고 **STORAGE_CONNECTION_STRING**이라는 bash 변수에 저장합니다.

    ```azurecli
    export STORAGE_CONNECTION_STRING=$(az storage account show-connection-string -n <storage account name> -g first-serverless-app --query "connectionString" --output tsv)
    ```

    변수를 성공적으로 설정되었는지 확인합니다.

    ```azurecli
    echo $STORAGE_CONNECTION_STRING
    ```

1. 이전 단계에서 저장된 값을 사용하여 **AZURE_STORAGE_CONNECTION_STRING**이라는 새 응용 프로그램 설정을 만듭니다.

    ```azurecli
    az functionapp config appsettings set -n <function app name> -g first-serverless-app --settings AZURE_STORAGE_CONNECTION_STRING=$STORAGE_CONNECTION_STRING -o table
    ```

    명령의 출력에 올바른 값을 가진 새 응용 프로그램 설정이 포함되는지 확인합니다.


## <a name="test-the-serverless-function"></a>서버를 사용하지 않는 함수 테스트

함수를 만들고 편집하는 것 외에도 Azure Portal에서는 함수를 테스트하기 위한 기본 제공 도구를 제공합니다.

1. HTTP 서버를 사용하지 않는 함수를 테스트하려면 코드 창의 오른쪽에서 **테스트** 탭을 클릭하고 테스트 패널을 확장합니다.

1. **Http 메서드**를 **GET**으로 변경합니다.

1. **쿼리** 아래에서 *매개 변수 추가*\*를 클릭하고 다음 매개 변수를 추가합니다.

    | 이름      |  값   | 
    | --- | --- |
    | filename | image1.jpg |

1. 테스트 패널에서 **실행**을 클릭하여 함수에 HTTP 요청을 보냅니다.

1. 함수는 출력에서 업로드 URL을 반환합니다. 함수 실행이 로그 패널에 표시됩니다.

    ![함수를 보여주는 로그가 성공적으로 실행되었습니다.](media/functions-first-serverless-web-app/2-test-function.png)


## <a name="configure-cors-in-the-function-app"></a>함수 앱에서 CORS 구성

앱의 프런트 엔드가 Blob Storage에서 호스트되기 때문에 Azure 함수 앱과 도메인 이름이 다릅니다. 클라이언트 쪽 JavaScript에서 성공적으로 만든 함수를 호출하려면 함수 앱은 CORS(교차 원본 리소스 공유)에 대해 구성되어야 합니다.

1. 함수 앱 창의 왼쪽 탐색 모음에서 함수 앱의 이름을 클릭합니다.

1. **플랫폼 기능**을 클릭하여 고급 기능 목록을 확인합니다.

1. **API** 아래에서 **CORS**를 클릭합니다.

    ![CORS 선택](media/functions-first-serverless-web-app/2-open-cors.jpg)

1. 이전 모듈의 응용 프로그램 URL에 대해 허용 원본을 추가하여 후행 **/**(예: `https://firstserverlessweb.z4.web.core.windows.net`)를 생략합니다.

    ![서버를 사용하지 않는 웹앱 URL이 추가되었음을 보여주는 CORS 설정](media/functions-first-serverless-web-app/2-add-cors.png)

1. **저장**을 클릭합니다.

1. C#

   1. (C#) `GetUploadUrl` 함수로 다시 이동한 다음, **통합** 탭을 선택합니다.

   1. (C#) 선택한 HTTP 메서드 아래에서 **OPTIONS**를 선택합니다.

      **GET**, **POST** 및 **OPTIONS**을 모두 선택해야 합니다. CORS에서는 C# 함수에 기본적으로 선택되지 않은 OPTIONS 메서드를 사용합니다.  

   1. (C#) **저장**을 클릭합니다.

1. 포털에서 함수 앱으로 이동하고, **개요** 탭을 선택한 다음, **다시 시작**을 클릭하여 CORS에 대한 변경 내용이 적용되도록 합니다.

## <a name="configure-cors-in-the-storage-account"></a>Storage 계정에서 CORS 구성

앱이 파일을 업로드하기 위해 Blob Storage에 대한 클라이언트 쪽 JavaScript를 호출하기 때문에 CORS에 대한 Storage 계정을 구성해야 합니다.

1. 다음 명령을 실행하여 모든 원본이 Storage 계정에 파일을 업로드하도록 허용합니다.

    ```azurecli
    az storage cors add --methods OPTIONS PUT --origins '*' --exposed-headers '*' --allowed-headers '*' --services b --account-name <storage account name>
    ```


## <a name="modify-the-web-app-to-upload-images"></a>이미지를 업로드하도록 웹앱 수정

웹앱은 **settings.js**라는 파일에서 설정을 검색합니다. 다음 단계에서는 Cloud Shell을 사용하여 파일을 만든 다음, `window.apiBaseUrl`을 함수 앱의 URL로 설정하고, `window.blobBaseUrl`을 Azure Blob Storage 엔드포인트의 URL로 설정합니다.

1. Cloud Shell에서 현재 디렉터리가 **www/dist** 폴더인지를 확인합니다.

    ```azurecli
    cd ~/functions-first-serverless-web-application/www/dist
    ```

1. 함수 앱의 URL을 쿼리하고 **FUNCTION_APP_URL**이라는 bash 변수에 저장합니다.

    ```azurecli
    export FUNCTION_APP_URL="https://"$(az functionapp show -n <function app name> -g first-serverless-app --query "defaultHostName" --output tsv)
    ```

    변수가 올바르게 설정되었는지 확인합니다.

    ```azurecli
    echo $FUNCTION_APP_URL
    ```

1. 함수 앱에 대한 API 호출의 기본 URI를 설정하려면 **settings.js**를 만들고, 다음과 같은 함수 앱 URL을 추가합니다.

    `window.apiBaseUrl = 'https://fnapp@lab.GlobalLabInstanceId.azurewebsites.net'`

    다음 명령을 실행하거나 VIM과 같은 명령줄 편집기를 사용하여 변경할 수 있습니다.

    ```azurecli
    echo "window.apiBaseUrl = '$FUNCTION_APP_URL'" > settings.js
    ```

    파일을 성공적으로 작성했는지 확인합니다.

    ```azurecli
    cat settings.js
    ```

1. 기본 Blob Storage 기본 URL을 쿼리하고 **BLOB_BASE_URL**이라는 bash 변수에 저장합니다.

    ```azurecli
    export BLOB_BASE_URL=$(az storage account show -n <storage account name> -g first-serverless-app --query primaryEndpoints.blob -o tsv | sed 's/\/$//')
    ```

    변수가 올바르게 설정되었는지 확인합니다.

    ```azurecli
    echo $BLOB_BASE_URL
    ```

1. 함수 앱에 대한 API 호출의 기본 URI를 설정하려면 다음 코드 줄과 같은 저장소 URL을 **settings.js**에 추가합니다.

    `window.blobBaseUrl = 'https://mystorage.blob.core.windows.net'`

    다음 명령을 실행하거나 VIM과 같은 명령줄 편집기를 사용하여 변경할 수 있습니다.

    ```azurecli
    echo "window.blobBaseUrl = '$BLOB_BASE_URL'" >> settings.js
    ```

    이제 파일이 성공적으로 작성되고 2개의 줄을 포함하는지 확인합니다.

    ```azurecli
    cat settings.js
    ```

1. Blob Storage에 파일을 업로드합니다.

    ```azurecli
    az storage blob upload -c \$web --account-name <storage account name> -f settings.js -n settings.js
    ```


## <a name="test-the-web-application"></a>웹 응용 프로그램 테스트

이 시점에서 갤러리 응용 프로그램은 이미지를 Blob Storage에 업로드할 수 있지만 아직 이미지를 표시할 수 없습니다. 이후 모듈에서 만들 수 있으므로 아직 존재하지 않는 `GetImages` 함수를 호출하려고 합니다. 해당 호출에 실패하고 웹 페이지가 "분석 중..." 상태에서 중단된 것으로 표시되지만 선택한 이미지는 성공적으로 업로드됩니다.

Azure Portal에서 **images** 컨테이너의 콘텐츠를 확인하여 이미지가 성공적으로 업로드되었는지 확인할 수 있습니다.

1. 브라우저 창에서 응용 프로그램을 찾습니다. 이미지 파일을 선택하고 업로드합니다. 업로드가 완료되지만 아직 이미지를 표시하는 기능을 추가하지 않았으므로 앱은 업로드한 사진을 표시하지 않습니다. (웹 페이지가 "이미지 분석 중..."에서 중단된 것으로 표시됩니다. 나중에 수정합니다.)

1. Cloud Shell에서 이미지를 **images** 컨테이너로 업로드했는지 확인합니다.

    ```azurecli
    az storage blob list --account-name <storage account name> -c images -o table
    ```

1. 다음 자습서로 넘어가기 전에 **images** 컨테이너에서 모든 파일을 삭제합니다.

    ```azurecli
    az storage blob delete-batch -s images --account-name <storage account name>
    ```


## <a name="summary"></a>요약

이 모듈에서는 Azure 함수 앱을 만들고 서버를 사용하지 않는 함수를 사용하여 웹 응용 프로그램이 Blob Storage에 이미지를 업로드하도록 하는 방법을 알아보았습니다. 다음으로, Blob 트리거 서버를 사용하지 않는 함수를 사용하여 업로드된 이미지에 대한 썸네일을 만드는 방법을 알아봅니다.
