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
Azure 서비스를 사용하여 모든 기능을 갖춘 서버를 사용하지 않는 응용 프로그램을 만들었습니다.

## <a name="clean-up-resources"></a>리소스 정리

이 응용 프로그램의 작업이 완료되면 다음 명령을 사용하여 자습서 중에 만들어진 모든 리소스를 삭제할 수 있습니다.

```azurecli
az group delete --name first-serverless-app
```

메시지가 표시되면 `y`를 입력합니다.  

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.
> [!div class="checklist"]
> * 정적 웹 사이트 및 업로드된 이미지를 호스트하도록 Azure Blob Storage를 구성합니다.
> * Azure Functions를 사용하여 Azure Blob Storage에 이미지를 업로드합니다.
> * Azure Functions를 사용하여 이미지의 크기를 조정합니다.
> * Azure Cosmos DB에서 이미지 메타데이터를 저장합니다.
> * Cognitive Services Vision API를 사용하여 이미지 캡션을 자동 생성합니다.
> * 사용자를 인증하여 웹앱을 보호하도록 Azure Active Directory를 사용할 수 있습니다.

함수에 추가 서비스를 연결하는 방법을 알아보려면 Logic Apps 자습서를 진행합니다. 

> [!div class="nextstepaction"]
> [Logic Apps를 사용하는 함수](https://docs.microsoft.com/azure/azure-functions/functions-twitter-email)