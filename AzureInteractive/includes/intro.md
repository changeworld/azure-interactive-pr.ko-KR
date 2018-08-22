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
이 자습서에서는 HTML 기반 사용자 인터페이스를 표시하는 간단한 웹 응용 프로그램을 배포합니다. 서버를 사용하지 않는 백 엔드를 사용하면 응용 프로그램에서 이미지를 업로드하고 자동으로 설명하는 캡션을 가져올 수 있습니다.

![웹앱 실행](media/functions-first-serverless-web-app/0-app-screenshot-finished.png)

다음 다이어그램에서는 응용 프로그램에서 사용하는 Azure 서비스를 보여줍니다.

1. Blob Storage는 정적 웹 콘텐츠(HTML, CSS, JS)를 제공하고 이미지를 저장합니다.
2. Azure Functions는 이미지 업로드, 크기 조정 및 메타데이터 저장을 관리합니다.
3. Cosmos DB는 이미지 메타데이터를 저장합니다.
4. Logic Apps는 Computer Vision API에서 이미지 캡션을 가져옵니다.
5. Azure Active Directory는 사용자 인증을 관리합니다.

![솔루션 아키텍처 다이어그램](media/functions-first-serverless-web-app/0-architecture.jpg)

이 자습서에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * 정적 웹 사이트 및 업로드된 이미지를 호스트하도록 Azure Blob Storage를 구성합니다.
> * Azure Functions를 사용하여 Azure Blob Storage에 이미지를 업로드합니다.
> * Azure Functions를 사용하여 이미지의 크기를 조정합니다.
> * Azure Cosmos DB에서 이미지 메타데이터를 저장합니다.
> * Cognitive Services Vision API를 사용하여 이미지 캡션을 자동 생성합니다.
> * 사용자를 인증하여 웹앱을 보호하도록 Azure Active Directory를 사용할 수 있습니다.