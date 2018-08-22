Azure Blob Storage는 정적 파일을 호스트하는 데 사용할 수 있는 저렴한 비용의 확장성이 매우 뛰어난 서비스입니다. 이 자습서에서는 이 기능을 사용하여 빌드하는 웹앱에 대한 정적 콘텐츠(예: HTML, JavaScript, CSS)를 제공합니다.

## <a name="create-a-storage-account"></a>Storage 계정 만들기

Storage 계정은 테이블, 큐, 파일, Blob(개체) 및 가상 머신 디스크를 저장하도록 허용하는 Azure 리소스입니다.

1. **포커스 모드로 전환** 단추를 선택하여 Cloud Shell(Bash)에 로그인합니다. 이 단추는 브라우저 창의 너비에 따라 페이지의 오른쪽 상단 또는 맨 아래에 위치합니다. 포커스 모드는 자습서에 표시된 명령을 쉽게 실행할 수 있도록 브라우저 창의 오른쪽에 있는 Cloud Shell 창에 연결합니다.

1. 용이한 관리를 위해 Azure에서 리소스 그룹은 관련 Azure 솔루션을 보유하는 컨테이너입니다. **first-serverless-app**이라는 새 리소스 그룹을 만듭니다.

    ```azurecli
    az group create -n first-serverless-app -l westcentralus
    ```

1. 이 자습서에 대한 정적 콘텐츠(HTML, CSS 및 JavaScript 파일)는 Blob Storage에서 호스트됩니다. Blob Storage를 사용하려면 Storage 계정이 필요합니다. 리소스 그룹에서 저장소 계정(범용 V2)을 만듭니다. `<storage account name>`을 고유한 이름으로 바꿉니다.

    ```azurecli
    az storage account create -n <storage account name> -g first-serverless-app --kind StorageV2 -l westcentralus --https-only true --sku Standard_LRS
    ```

1. [Azure Portal](https://portal.azure.com)의 맨 위에 있는 검색 표시줄을 사용하여 방금 만든 저장소 계정을 찾아 엽니다.

1. 왼쪽 탐색 영역에서 **정적 웹 사이트(미리 보기)** 를 선택하고 정적 웹 사이트 호스팅을 위한 컨테이너를 구성합니다.
    - **사용**을 선택하여 정적 웹 사이트를 사용하도록 설정합니다.
    - *index.html*을 인덱스 문서 이름으로 입력합니다. 필드에는 이미 회색 글꼴로 *index.html*이 있지만 예제 텍스트일 뿐입니다. 필드에 해당 값을 입력해야 합니다.
    - **저장**을 클릭합니다.
    
    ![정적 웹 사이트 설정 입력](media/functions-first-serverless-web-app/1-storage-static-website.png)

1. 자습서를 진행하면서 편리하게 복사할 수 있는 위치에 **기본 엔드포인트**를 저장합니다. 웹 응용 프로그램의 URL입니다.

## <a name="upload-the-web-application"></a>웹 응용 프로그램 업로드

1. 이 자습서에서 빌드한 응용 프로그램에 대한 원본 파일은 [GitHub 리포지토리](https://github.com/Azure-Samples/functions-first-serverless-web-application)에 위치합니다. Cloud Shell의 홈 디렉터리에서 이 리포지토리를 복제했는지 확인합니다.

    ```azurecli
    cd ~
    git clone https://github.com/Azure-Samples/functions-first-serverless-web-application
    ```

    리포지토리는 `/home/<username>/functions-first-serverless-web-application`에 복제됩니다.

1. 클라이언트 쪽 웹 응용 프로그램은 **www** 폴더에 위치하고 Vue.js JavaScript 프레임워크를 사용하여 빌드됩니다. 폴더로 변경하고 npm 명령을 실행하여 응용 프로그램의 종속성을 설치하고 응용 프로그램을 빌드합니다. 이러한 명령 중 마지막을 완료하려면 몇 분 정도 걸릴 수 있습니다.

    ```azurecli
    cd ~/functions-first-serverless-web-application/www
    npm install
    npm run generate
    ```

    응용 프로그램은 **dist** 폴더에서 생성됩니다.

1. 현재 디렉터리를 **dist**로 변경하고 응용 프로그램을 **$web** Blob 컨테이너로 업로드합니다.

    ```azurecli
    cd dist
    az storage blob upload-batch -s . -d \$web --account-name <storage account name>
    ```

1. 응용 프로그램을 보려면 웹 브라우저에서 Storage 정적 웹 사이트 기본 엔드포인트 URL을 엽니다.

    ![첫 번째 서버를 사용하지 않는 웹앱 홈페이지](media/functions-first-serverless-web-app/1-app-screenshot-new.png)


## <a name="summary"></a>요약

이 모듈에서 Storage 계정을 포함하는 **first-serverless-app**이라는 리소스 그룹을 만들었습니다. Storage 계정에서 **$web**이라는 Blob 컨테이너는 웹 응용 프로그램에 대한 정적 콘텐츠를 저장하고 콘텐츠를 공개적으로 사용할 수 있도록 합니다. 다음으로, 서버를 사용하지 않는 함수를 사용하여 이 웹 응용 프로그램에서 Blob Storage에 이미지를 업로드하는 방법을 알아봅니다.
