<span data-ttu-id="90226-101">Azure Blob Storage는 정적 파일을 호스트하는 데 사용할 수 있는 저렴한 비용의 확장성이 매우 뛰어난 서비스입니다.</span><span class="sxs-lookup"><span data-stu-id="90226-101">Azure Blob storage is a low-cost and massively scalable service that can be used to host static files.</span></span> <span data-ttu-id="90226-102">이 자습서에서는 이 기능을 사용하여 빌드하는 웹앱에 대한 정적 콘텐츠(예: HTML, JavaScript, CSS)를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="90226-102">For this tutorial, you use it to serve static content (for example, HTML, JavaScript, CSS) for the web app that you build.</span></span>

## <a name="create-a-storage-account"></a><span data-ttu-id="90226-103">Storage 계정 만들기</span><span class="sxs-lookup"><span data-stu-id="90226-103">Create a Storage account</span></span>

<span data-ttu-id="90226-104">Storage 계정은 테이블, 큐, 파일, Blob(개체) 및 가상 머신 디스크를 저장하도록 허용하는 Azure 리소스입니다.</span><span class="sxs-lookup"><span data-stu-id="90226-104">A Storage account is an Azure resource that allows you to store tables, queues, files, blobs (objects), and virtual machine disks.</span></span>

1. <span data-ttu-id="90226-105">**포커스 모드로 전환** 단추를 선택하여 Cloud Shell(Bash)에 로그인합니다.</span><span class="sxs-lookup"><span data-stu-id="90226-105">Log in to the Cloud Shell (Bash), by selecting the **Enter focus mode** button.</span></span> <span data-ttu-id="90226-106">이 단추는 브라우저 창의 너비에 따라 페이지의 오른쪽 상단 또는 맨 아래에 위치합니다.</span><span class="sxs-lookup"><span data-stu-id="90226-106">This button is at the top right or the bottom of the page, depending on how wide your browser window is.</span></span> <span data-ttu-id="90226-107">포커스 모드는 자습서에 표시된 명령을 쉽게 실행할 수 있도록 브라우저 창의 오른쪽에 있는 Cloud Shell 창에 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="90226-107">Focus mode docks a Cloud Shell window on the right side of your browser window, so you can easily execute commands that are shown in the tutorial.</span></span>

1. <span data-ttu-id="90226-108">용이한 관리를 위해 Azure에서 리소스 그룹은 관련 Azure 솔루션을 보유하는 컨테이너입니다.</span><span class="sxs-lookup"><span data-stu-id="90226-108">In Azure, a Resource Group is a container that holds related Azure resources for ease of management.</span></span> <span data-ttu-id="90226-109">**first-serverless-app**이라는 새 리소스 그룹을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="90226-109">Create a new resource group named **first-serverless-app**.</span></span>

    ```azurecli
    az group create -n first-serverless-app -l westcentralus
    ```

1. <span data-ttu-id="90226-110">이 자습서에 대한 정적 콘텐츠(HTML, CSS 및 JavaScript 파일)는 Blob Storage에서 호스트됩니다.</span><span class="sxs-lookup"><span data-stu-id="90226-110">The static content (HTML, CSS, and JavaScript files) for this tutorial is hosted in Blob Storage.</span></span> <span data-ttu-id="90226-111">Blob Storage를 사용하려면 Storage 계정이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="90226-111">Blob Storage requires a Storage account.</span></span> <span data-ttu-id="90226-112">리소스 그룹에서 저장소 계정(범용 V2)을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="90226-112">Create a Storage account (general purpose V2) in the resource group.</span></span> <span data-ttu-id="90226-113">`<storage account name>`을 고유한 이름으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="90226-113">Replace `<storage account name>` with a unique name.</span></span>

    ```azurecli
    az storage account create -n <storage account name> -g first-serverless-app --kind StorageV2 -l westcentralus --https-only true --sku Standard_LRS
    ```

1. <span data-ttu-id="90226-114">[Azure Portal](https://portal.azure.com)의 맨 위에 있는 검색 표시줄을 사용하여 방금 만든 저장소 계정을 찾아 엽니다.</span><span class="sxs-lookup"><span data-stu-id="90226-114">Using the Search bar at the top of the [Azure portal](https://portal.azure.com), find the storage account you just created and open it.</span></span>

1. <span data-ttu-id="90226-115">왼쪽 탐색 영역에서 **정적 웹 사이트(미리 보기)** 를 선택하고 정적 웹 사이트 호스팅을 위한 컨테이너를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="90226-115">On the left navigation, select **Static website (preview)** to configure a container for static website hosting.</span></span>
    - <span data-ttu-id="90226-116">**사용**을 선택하여 정적 웹 사이트를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="90226-116">Select **Enabled** to enable static website.</span></span>
    - <span data-ttu-id="90226-117">*index.html*을 인덱스 문서 이름으로 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="90226-117">Enter *index.html* as the index document name.</span></span> <span data-ttu-id="90226-118">필드에는 이미 회색 글꼴로 *index.html*이 있지만 예제 텍스트일 뿐입니다. 필드에 해당 값을 입력해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="90226-118">The field already has *index.html* in a gray font but this is only example text; you still have to enter that value in the field.</span></span>
    - <span data-ttu-id="90226-119">**저장**을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="90226-119">Click **Save**.</span></span>
    
    ![정적 웹 사이트 설정 입력](media/functions-first-serverless-web-app/1-storage-static-website.png)

1. <span data-ttu-id="90226-121">자습서를 진행하면서 편리하게 복사할 수 있는 위치에 **기본 엔드포인트**를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="90226-121">Save the **Primary Endpoint** somewhere you can conveniently copy it from while working through the tutorial.</span></span> <span data-ttu-id="90226-122">웹 응용 프로그램의 URL입니다.</span><span class="sxs-lookup"><span data-stu-id="90226-122">This is the URL of your web application.</span></span>

## <a name="upload-the-web-application"></a><span data-ttu-id="90226-123">웹 응용 프로그램 업로드</span><span class="sxs-lookup"><span data-stu-id="90226-123">Upload the web application</span></span>

1. <span data-ttu-id="90226-124">이 자습서에서 빌드한 응용 프로그램에 대한 원본 파일은 [GitHub 리포지토리](https://github.com/Azure-Samples/functions-first-serverless-web-application)에 위치합니다.</span><span class="sxs-lookup"><span data-stu-id="90226-124">The source files for the application that you build in this tutorial are located in a [GitHub repository](https://github.com/Azure-Samples/functions-first-serverless-web-application).</span></span> <span data-ttu-id="90226-125">Cloud Shell의 홈 디렉터리에서 이 리포지토리를 복제했는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="90226-125">Make sure that you are in your home directory in Cloud Shell and clone this repository.</span></span>

    ```azurecli
    cd ~
    git clone https://github.com/Azure-Samples/functions-first-serverless-web-application
    ```

    <span data-ttu-id="90226-126">리포지토리는 `/home/<username>/functions-first-serverless-web-application`에 복제됩니다.</span><span class="sxs-lookup"><span data-stu-id="90226-126">The repository is cloned to `/home/<username>/functions-first-serverless-web-application`.</span></span>

1. <span data-ttu-id="90226-127">클라이언트 쪽 웹 응용 프로그램은 **www** 폴더에 위치하고 Vue.js JavaScript 프레임워크를 사용하여 빌드됩니다.</span><span class="sxs-lookup"><span data-stu-id="90226-127">The client-side web application is located in the **www** folder and is built using the Vue.js JavaScript framework.</span></span> <span data-ttu-id="90226-128">폴더로 변경하고 npm 명령을 실행하여 응용 프로그램의 종속성을 설치하고 응용 프로그램을 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="90226-128">Change into the folder and run npm commands to install the application's dependencies and build the application.</span></span> <span data-ttu-id="90226-129">이러한 명령 중 마지막을 완료하려면 몇 분 정도 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="90226-129">The last of these commands might take several minutes to complete.</span></span>

    ```azurecli
    cd ~/functions-first-serverless-web-application/www
    npm install
    npm run generate
    ```

    <span data-ttu-id="90226-130">응용 프로그램은 **dist** 폴더에서 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="90226-130">The application is generated in the **dist** folder.</span></span>

1. <span data-ttu-id="90226-131">현재 디렉터리를 **dist**로 변경하고 응용 프로그램을 **$web** Blob 컨테이너로 업로드합니다.</span><span class="sxs-lookup"><span data-stu-id="90226-131">Change the current directory to **dist** and upload the application to the **$web** Blob container.</span></span>

    ```azurecli
    cd dist
    az storage blob upload-batch -s . -d \$web --account-name <storage account name>
    ```

1. <span data-ttu-id="90226-132">응용 프로그램을 보려면 웹 브라우저에서 Storage 정적 웹 사이트 기본 엔드포인트 URL을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="90226-132">Open the Storage static websites primary endpoint URL in a web browser to view the application.</span></span>

    ![첫 번째 서버를 사용하지 않는 웹앱 홈페이지](media/functions-first-serverless-web-app/1-app-screenshot-new.png)


## <a name="summary"></a><span data-ttu-id="90226-134">요약</span><span class="sxs-lookup"><span data-stu-id="90226-134">Summary</span></span>

<span data-ttu-id="90226-135">이 모듈에서 Storage 계정을 포함하는 **first-serverless-app**이라는 리소스 그룹을 만들었습니다.</span><span class="sxs-lookup"><span data-stu-id="90226-135">In this module, you created a resource group named **first-serverless-app** containing a Storage account.</span></span> <span data-ttu-id="90226-136">Storage 계정에서 **$web**이라는 Blob 컨테이너는 웹 응용 프로그램에 대한 정적 콘텐츠를 저장하고 콘텐츠를 공개적으로 사용할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="90226-136">A blob container named **$web** in the Storage account stores the static content for your web application and makes the content available publicly.</span></span> <span data-ttu-id="90226-137">다음으로, 서버를 사용하지 않는 함수를 사용하여 이 웹 응용 프로그램에서 Blob Storage에 이미지를 업로드하는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="90226-137">Next, you learn how to use a serverless function to upload images to Blob storage from this web application.</span></span>
