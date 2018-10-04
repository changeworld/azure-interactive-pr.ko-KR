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
ms.openlocfilehash: f51b864cab14273c1e88dd85d22400e0e76ef770
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2018
ms.locfileid: "47460004"
---
이 시점에서 응용 프로그램은 이미지를 업로드하고 볼 수 있는 기능 갤러리입니다. 이 모듈에서는 Microsoft Cognitive Services에서 Computer Vision API를 사용하여 업로드된 이미지에 대한 캡션을 생성하고 Cosmos DB에서 이미지 메타데이터를 포함한 캡션을 저장하는 방법을 알아봅니다.

## <a name="create-a-computer-vision-account"></a>Computer Vision 계정 만들기

Microsoft Cognitive Services는 개발자들이 더욱 지능적인 응용 프로그램을 만들 수 있도록 제공되는 서비스의 컬렉션입니다. Computer Vision API는 고급 알고리즘을 사용하여 이미지를 처리하고 각 이미지에 대한 정보를 반환하는 서버를 사용하지 않는 서비스입니다. 최대 매월 5000개의 API 호출을 제공하는 무료 계층이 있습니다.

1. Cloud Shell에 로그인했는지 확인합니다. 그렇지 않은 경우 **포커스 모드로 전환**을 선택하여 Cloud Shell 창을 엽니다. 

1. 리소스 그룹에서 고유한 이름을 가진 **ComputerVision** 종류의 새 Cognitive Services 계정을 만듭니다. 무료 계층에서 **F0**를 SKU로 사용합니다. 기존 Computer Vision 계정이 있는 경우 표준 계정(S1)을 만들어야 하지만 일부 비용이 발생할 수 있습니다.

    ```azurecli
    az cognitiveservices account create -g first-serverless-app -n <computer vision account name> --kind ComputerVision --sku F0 -l westcentralus
    ```


## <a name="create-function-app-settings-for-computer-vision-url-and-key"></a>Computer Vision URL 및 키에 대한 함수 앱 설정 만들기

Computer Vision API를 호출하려면 URL 및 키가 필요합니다.

1. Computer Vision API 키 및 URL을 가져오고 bash 변수에 저장합니다.

    ```azurecli
    export COMP_VISION_KEY=$(az cognitiveservices account keys list -g first-serverless-app -n <computer vision account name> --query key1 --output tsv)
    ```
    ```azurecli
    export COMP_VISION_URL=$(az cognitiveservices account show -g first-serverless-app -n <computer vision account name> --query endpoint --output tsv)
    ```

1. 각각 함수 앱에서 **COMP_VISION_KEY** 및 **COMP_VISION_URL**이라는 앱 설정을 만듭니다.

    ```azurecli
    az functionapp config appsettings set -n <function app name> -g first-serverless-app --settings COMP_VISION_KEY=$COMP_VISION_KEY COMP_VISION_URL=$COMP_VISION_URL -o table
    ```


## <a name="call-computer-vision-api-from-resizeimage-function"></a>ResizeImage 함수에서 Computer Vision API 호출

다음 단계에서 각 업로드된 이미지를 설명하고 설명을 Cosmos DB에 저장하도록 Computer Vision API를 호출하는 **ResizeImage** 함수를 수정합니다.

1. Azure Portal에서 함수 앱을 엽니다.

1. **C#**

    1. (C#) 왼쪽 탐색 영역을 사용하여 **ResizeImage** 함수를 찾고 해당 코드 창을 엽니다.

    1. (C#) 코드를 [**/csharp/ResizeImage/run-module5.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run-module5.csx)의 콘텐츠로 바꿉니다. 이 코드는 `HttpClient`를 사용하여 Computer Vision API를 호출하고 결과를 Cosmos DB에 저장합니다.

1. **JavaScript**

    1. (JavaScript) Computer Vision API에 대한 HTTP 호출을 수행하기 위해 이 함수에는 npm의 `axios` 패키지가 필요합니다. npm 패키지를 설치하려면 왼쪽 탐색 창에서 함수 앱의 이름을 클릭하고, **플랫폼 기능**을 클릭합니다.

    1. (JavaScript) **콘솔**을 클릭하여 콘솔 창을 표시합니다.

    1. (JavaScript) 콘솔에서 `npm install axios` 명령을 실행합니다. 작업을 완료하는 데 일이 분 정도 걸릴 수 있습니다.

    1. (JavaScript) 왼쪽 탐색 창에서 함수 이름(**ResizeImage**)을 클릭하여 함수를 표시한 다음, 모든 **index.js**를 [**/javascript/ResizeImage/index-module5.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index-module5.js)의 콘텐츠로 바꿉니다.

1. 코드 창 아래에서 **로그**를 클릭하여 로그 패널을 확장합니다.

1. **저장**을 클릭합니다. 로그 패널을 확인하여 함수가 성공적으로 저장되고 오류가 없는지 확인합니다.


## <a name="test-the-application"></a>응용 프로그램 테스트

1. 브라우저에서 응용 프로그램을 엽니다. 이미지 파일을 선택하고 업로드합니다.

1. 몇 초 후에 새 이미지의 썸네일이 페이지에 표시됩니다. Computer Vision에 의해 생성된 설명을 보려면 이미지를 마우스로 가리킵니다.


## <a name="summary"></a>요약

이 모듈에서는 Microsoft Cognitive Services Computer Vision API를 사용하여 각 업로드된 이미지에 대한 캡션을 자동으로 생성하는 방법을 알아보았습니다. 다음으로, Azure App Service 인증을 사용하여 응용 프로그램에 인증을 추가하는 방법을 알아봅니다.
