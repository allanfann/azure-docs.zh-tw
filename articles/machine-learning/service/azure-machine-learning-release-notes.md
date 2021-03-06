---
title: 此版本有哪些新功能？
titleSuffix: Azure Machine Learning service
description: 了解 Azure Machine Learning 服務的最新更新，以及機器學習和資料準備 Python SDK。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: larryfr
author: Blackmist
ms.date: 05/14/2019
ms.custom: seodec18
ms.openlocfilehash: 892b9bc63f9f2d9abc7108587a7bf929473e4648
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2019
ms.locfileid: "65779440"
---
# <a name="azure-machine-learning-service-release-notes"></a>Azure Machine Learning 服務版本資訊

本文章會說明 Azure Machine Learning 服務的版本。  如需每個 SDK 的完整說明，請瀏覽參考文件以瞭解：
+ Azure Machine Learning [**主要適用於 Python 的 SDK**](https://aka.ms/aml-sdk)
+ Azure Machine Learning [**Data Prep SDK**](https://aka.ms/data-prep-sdk)

若要了解已知的 Bug 和因應措施，請參閱[已知問題的清單](resource-known-issues.md)。

## <a name="2019-05-14"></a>2019-05-14

### <a name="azure-machine-learning-sdk-for-python-v1039"></a>Azure Machine Learning SDK for Python v1.0.39
+ **變更**
  + 回合的組態 auto_prepare_environment 選項已被取代，自動準備成為預設值。

## <a name="2019-05-06"></a>2019-05-06

### <a name="azure-portal"></a>Azure 入口網站

在 Azure 入口網站中，您現在可以：
+ 建立及執行自動化的 ML 實驗 
+ 建立 Notebook VM 以試用範例 Jupyter 筆記本或您自己。
+ 包含自動化的機器學習服務、 視覺化介面和裝載 Notebook Vm 的機器學習服務工作區中全新的撰寫區段 （預覽）
    + 自動建立模型，使用自動化機器學習服務 
    + 使用拖曳和卸除視覺化介面，執行實驗
    + 建立 Notebook VM 瀏覽資料、 建立模型，並將服務部署。
+ 即時圖表和更新執行的報表中的計量，然後執行詳細資料頁面
+ 更新的檔案檢視器記錄檔、 輸出和執行詳細資料頁面中的快照集。
+ 全新和改進的報表建立體驗中的 [實驗] 索引標籤。 
+ 已新增的能夠從 Azure 機器學習服務工作區的 [概觀] 頁面下載的 config.json 檔案的詳細資訊。
+ 支援從 Azure Databricks 工作區的機器學習服務工作區建立 

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033"></a>Azure Machine Learning SDK for Python v1.0.33
+ **新功能**
  + _Workspace.create_方法現在接受 CPU 和 GPU 叢集的預設叢集設定。
  + 如果工作區建立失敗，則會清除相依的資源。
  + 預設 Azure Container Registry SKU 已切換為 basic。
  + 所需的 執行 或 映像建立時，會以延遲的方式，建立 azure Container Registry。
  + 支援環境的定型執行。

### <a name="notebook-virtual-machine"></a>Notebook 的虛擬機器 

使用 Notebook VM 作為安全、 符合企業需求的裝載環境的 Jupyter notebook 在其中設計程式的機器學習服務實驗，模型部署為 web 端點和執行所有支援 Azure 機器學習服務 SDK 使用 Python 的其他作業。 它提供了幾項功能：
+ [快速啟動 VM 的預先設定 notebook](quickstart-run-cloud-notebook.md) 具有最新版的 Azure 機器學習服務 SDK 和相關的封裝。
+ 存取是透過經實證的技術，例如 HTTPS、 Azure Active Directory 驗證和授權來保護。
+ 筆記本與您 Azure Machine Learning 工作區的 blob 儲存體帳戶中的程式碼的可靠的雲端儲存體。 您可以放心刪除您的 notebook VM，而不會遺失您的工作。
+ 預先安裝探索和實驗使用 Azure Machine Learning 服務功能的範例筆記本。
+ Azure Vm、 任何 VM 類型、 任何套件的任何驅動程式的完整自訂功能。 

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033-released"></a>Azure 機器學習服務 SDK for Python v1.0.33 發行。

+ Azure ML 硬體加速模型上[Fpga](concept-accelerate-with-fpgas.md)已正式推出。
  + 您現在可以[使用 azureml 加速度模型封裝](how-to-deploy-fpga-web-service.md)來：
    + 訓練支援的深度類神經網路 （ResNet 50、 ResNet 152、 DenseNet 121、 VGG-16 和 SSD VGG） 的加權
    + 使用轉送學習與支援的 DNN
    + 模型管理服務註冊模型，並將容器化模型
    + 將模型部署到 Azure VM 中使用 Azure Kubernetes Service (AKS) 叢集中 FPGA
  + 部署容器[Azure 資料方塊邊緣](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview)伺服器裝置
  + 評分與 gRPC 端點與此資料[範例](https://github.com/Azure-Samples/aml-hardware-accelerated-models)

### <a name="automated-machine-learning"></a>自動化 Machine Learning

+ 掃掠，以啟用動態新增 featurizers 效能最佳化的功能。 新的 featurizers： 工作內嵌項目，辨識項、 目標編碼、 文字目標版本編碼方式、 群集距離的權數
+ 處理有效的訓練/智慧型的 CV 分割內自動化 ML
+ 幾個記憶體最佳化的變化和執行階段效能改進
+ 模型說明中的效能改進
+ 本機執行的 ONNX 模型轉換
+ 已新增的 Subsampling 支援
+ 智慧型停止時定義不允出準則
+ 整體堆疊的項目

+ 時間序列預測
  + 新的預測趨勢預測函式   
  + 您現在可以使用輪流原點交叉驗證對時間序列資料
  + 新增用來設定的時間序列會延遲的新功能 
  + 加入以支援輪流視窗彙總功能的新功能
  + 新的假日偵測和 featurizer 國家/地區的程式碼中定義時嘗試設定

+ Azure Databricks
  + 啟用時間序列預測和模型 explainabilty/interpretability 功能
  + 您現在可以取消和繼續 （繼續） 自動化的 ML 實驗
  + 多核心處理新增的支援

### <a name="mlops"></a>MLOps
+ **本機部署和評分容器偵錯**<br/> 您現在可以部署在本機的 ML 模型，並快速反覆執行計分的檔案和相依性，以確保它們如預期般運作。

+ **Introduced InferenceConfig & Model.deploy()**<br/> 模型的部署現在支援使用 RunConfig 相同的項目指令碼中指定的來源資料夾。  此外，已簡化模型部署，以單一命令。

+ **追蹤的 Git 參考**<br/> 客戶一直要求基本的 Git 整合功能的一些時間，因為它可協助維護的端對端稽核記錄。 我們已實作追蹤跨 Azure ML 中的 Git 相關中繼資料 （儲存機制、 認可、 初始狀態） 的主要實體。 這項資訊會由 SDK 和 CLI 會自動收集。

+ **模型的程式碼剖析和驗證服務**<br/> 客戶經常抱怨的困難度，正確大小其推斷服務相關聯的計算。 與我們的模型分析服務，客戶可以提供的範例輸入，以及我們將會分析跨 16 不同的 CPU / 記憶體設定來判斷最佳的部署大小調整。

+ **推斷的自備您自己的基底映像**<br/> 另一個常見抱怨無法從測試移至推斷重新共用相依性。 與共用功能我們新基底映像，您可以立即重複使用測試基底映像、 相依性以及所有推斷。 這應該加速部署，並減少外部迴圈的內部間距。

+ **改善的 Swagger 結構描述產生體驗**<br/> 我們先前的 swagger 產生，方法是很容易出錯且無法自動化。 我們有新的內建方法，從任何 Python 函式，透過裝飾項目產生 swagger 結構描述。 我們有開放原始碼這段程式碼，我們的結構描述產生通訊協定不會連結至 Azure ML 平台。

+ **Azure ML CLI 是正式推出 (GA)**<br/> 現在可以使用單一 CLI 命令部署模型。 我們共同客戶的意見反應，沒有人會從 Jupyter notebook 的 ML 模型的部署。 [ **CLI 參考文件**](https://aka.ms/azmlcli)已更新。


## <a name="2019-04-22"></a>2019-04-22

Azure 機器學習服務 SDK for Python v1.0.30 發行。

[ `PipelineEndpoint` ](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline_endpoint.pipelineendpoint?view=azure-ml-py)介紹中加入新版本的發行管線的同時保有相同的端點。

## <a name="2019-04-17"></a>2019-04-17

### <a name="azure-machine-learning-data-prep-sdk-v112"></a>Azure Machine Learning 資料準備 SDK v1.1.2

注意：資料準備 Python SDK 將不會再安裝`numpy`和`pandas`封裝。 請參閱[更新的安裝指示](https://aka.ms/aml-data-prep-installation)。

+ **新功能**
  + 您現在可以使用樞紐轉換。
    + 操作說明指南：[Pivot notebook](https://aka.ms/aml-data-prep-pivot-nb)
  + 您現在可以在原生函式中使用規則運算式。
    + 範例：
      + `dflow.filter(dprep.RegEx('pattern').is_match(dflow['column_name']))`
      + `dflow.assert_value('column_name', dprep.RegEx('pattern').is_match(dprep.value))`
  + 您現在可以使用`to_upper` 並`to_lower` 運算式語言中的函式。
  + 您現在可以看到每個資料行資料設定檔中的唯一值數目。
  + 對於某些常用的讀取器的步驟，您現在可以傳入`infer_column_types`引數。 如果設定為`True`，資料準備會嘗試偵測並自動將資料行類型的轉換。
    + `inference_arguments` 現在已被取代。
  + 您現在可以呼叫`Dataflow.shape`。

+ **Bug 修正和增強功能**
  + `keep_columns` 現在接受一個額外的選擇性引數`validate_column_exists`，它會檢查的結果`keep_columns`會包含任何資料行。
  + 所有讀取器步驟 （從檔案讀取） 現在接受一個額外的選擇性引數`verify_exists`。
  + 讀取 pandas 資料框架，以及資料設定檔的提升的效能。
  + 修正的 bug，其中配量從資料流程的單一步驟失敗，單一索引。

## <a name="2019-04-15"></a>2019-04-15

### <a name="azure-portal"></a>Azure 入口網站
  + 您現在可以重新提交在現有的遠端計算叢集上執行的現有指令碼。 
  + 您現在可以使用新的參數執行發行的管線，在 [管線] 索引標籤上。 
  + 執行詳細資料現在支援新的快照集檔案檢視器。 當您送出特定的執行，您可以檢視快照集的目錄。 您也可以下載已提交給啟動執行的 notebook。
  + 您現在可以取消父執行從 Azure 入口網站。

## <a name="2019-04-08"></a>2019-04-08

### <a name="azure-machine-learning-sdk-for-python-v1023"></a>Azure Machine Learning SDK for Python v1.0.23

+ **新功能**
  + Azure 機器學習服務 SDK 現在支援 Python 3.7。
  + Azure Machine Learning DNN 估算現在提供內建的多個版本支援。 例如， `TensorFlow` 估算器現在接受`framework_version`參數，而使用者可以指定 '1.10' 或 '1.12' 的版本。 如需目前的 SDK 版本所支援的版本中，呼叫`get_supported_versions()`上所需的架構類別 (例如`TensorFlow.get_supported_versions()`)。
  如需最新的 SDK 版本所支援的版本，請參閱[DNN 估算器文件](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn?view=azure-ml-py)。

### <a name="azure-machine-learning-data-prep-sdk-v111"></a>Azure Machine Learning 資料準備 SDK v1.1.1

+ **新功能**
  + 您可以閱讀使用 read_ * 轉型的多個資料存放區/資料路徑/DataReference 來源。
  + 您可以執行下列作業來建立新的資料行的資料行： 部門、 樓層、 模數電源，長度。
  + 資料準備現在是 Azure ML 診斷套件的一部分，而且會依預設記錄診斷資訊。
    + 若要關閉這個功能，設定這個環境變數設為 true:DISABLE_DPREP_LOGGER

+ **Bug 修正和增強功能**
  + 改進程式碼文件常使用的類別和函式。
  + 無法讀取 Excel 檔案的 auto_read_file 中修正的 bug。
  + 已覆寫 read_pandas_dataframe 中的資料夾的選項。
  + 已新增的支援的 Fedora 27/28 和 Ubuntu 1804 dotnetcore2 相依性安裝，以及提升的效能。
  + 改善從 Azure Blob 的讀取效能。
  + 資料行類型偵測現在支援類型的資料行的長時間。
  + 修正的 bug，其中有些日期值顯示為時間戳記，而不是 Python datetime 物件。
  + 修正了其中一些類型的計數正顯示為雙精度浮點數，而不是整數。

  
## <a name="2019-03-25"></a>2019-03-25

### <a name="azure-machine-learning-sdk-for-python-v1021"></a>Azure Machine Learning SDK for Python v1.0.21

+ **新功能**
  + *Azureml.core.Run.create_children*方法可讓低度延遲建立多個子執行透過單一呼叫。

### <a name="azure-machine-learning-data-prep-sdk-v110"></a>Azure Machine Learning 資料準備 SDK 1.1.0 版

+ **重大變更**
  + 資料準備套件的概念已被取代，不再支援。 而不是保存在單一套件中的多個資料流程，您可以個別保存資料流程。
    + 操作說明指南：[開啟並儲存資料流程的 notebook](https://aka.ms/aml-data-prep-open-save-dataflows-nb)

+ **新功能**
  + 資料準備現可辨識的比對特定的語意型別，並據以分割資料行。 目前支援 STypes 包括： 傳送電子郵件位址 」、 「 地理的座標 （緯度與經度） 」、 「 IPv4 和 IPv6 位址 」、 「 美國電話號碼和 「 美國郵遞區號。
    + 操作說明指南：[語意類型 notebook](https://aka.ms/aml-data-prep-semantic-types-nb)
  + 資料準備現在支援從兩個數值資料行中產生結果的資料行的下列作業： 減、 乘、 分割，和模數。
  + 您可以呼叫`verify_has_data()`上檢查執行時，資料流程是否會產生記錄的資料流程。

+ **Bug 修正和增強功能**
  + 您現在可以指定要在長條圖中用於數值資料行設定檔的分類收納數目。
  + `read_pandas_dataframe`轉換現在需要 DataFrame，以具備字串或位元組型別資料行名稱。
  + 修正在`fill_nulls`轉換，其中值未正確填入如果資料行遺漏。

## <a name="2019-03-11"></a>2019-03-11

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>Azure Machine Learning SDK for Python v1.0.18

 + **變更**
   + Azureml tensorboard 封裝會取代 azureml-contrib-tensorboard。
   + 此版本中，您可以設定使用者帳戶在您的受控的計算叢集 (amlcompute)，建立它時。 這可以由傳入佈建設定的這些屬性。 您可以找到更多詳細資料，在[SDK 參考文件](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none-)。

### <a name="azure-machine-learning-data-prep-sdk-v1017"></a>Azure Machine Learning 資料準備 SDK v1.0.17

+ **新功能**
  + 現在支援加入兩個數值的資料行，來產生結果的資料行，使用運算式語言。

+ **Bug 修正和增強功能**
  + 改善的文件和 random_split 的參數檢查。
  
## <a name="2019-02-27"></a>2019-02-27

### <a name="azure-machine-learning-data-prep-sdk-v1016"></a>Azure Machine Learning 資料準備 SDK v1.0.16

+ **Bug 修复**
  + 修正服務主體驗證問題所造成的 API 變更。

## <a name="2019-02-25"></a>2019-02-25

### <a name="azure-machine-learning-sdk-for-python-v1017"></a>Azure Machine Learning SDK for Python v1.0.17

+ **新功能**

  + Azure Machine Learning 現在提供熱門 DNN 架構 Chainer 一流的支援。 使用[ `Chainer` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py)類別的使用者可以輕鬆地訓練並部署 Chainer 模型。
    + 了解如何[ChainerMN 以執行分散式的訓練](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/distributed-chainer/distributed-chainer.ipynb)
    + 了解如何[執行超參數微調與使用 HyperDrive Chainer](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)
  + Azure 機器學習服務管線新增資料存放區修改為基礎的管線執行的能力觸發程序。 管線[排程 notebook](https://aka.ms/pl-schedule)會更新，以展示此功能。

+ **Bug 修正和增強功能**
  + 我們新增了支援 Azure 機器學習服務管線將 source_directory_data_store 屬性設定為 「 所需的資料存放區 （例如 blob 儲存體） 上[RunConfigurations](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py)的值提供給[PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py)。 依預設步驟使用 Azure 檔案存放區的備份資料存放區，可能會遇到大量的步驟會同時執行時的節流問題。

### <a name="azure-portal"></a>Azure 入口網站

+ **新功能**
  + 新的拖曳和卸除資料表報表編輯器的體驗。 使用者可以將資料行從井拖曳到資料表區域會顯示資料表的預覽。 可以重新排列資料行。
  + 新的記錄檔檢視器
  + 執行、 計算、 模型、 映像，以及從 活動 索引標籤的 部署實驗的連結

### <a name="azure-machine-learning-data-prep-sdk-v1015"></a>Azure Machine Learning 資料準備 SDK v1.0.15

+ **新功能**
  + 資料準備目前支援從資料流程寫入檔案資料流。 也提供可操作檔案資料流名稱，以建立新的檔案名稱的功能。
    + 操作說明指南：[使用與檔案資料流 notebook](https://aka.ms/aml-data-prep-file-stream-nb)

+ **Bug 修正和增強功能**
  + 改善的 T-digest 對大型資料集的效能。
  + 資料準備現在支援從資料路徑的讀取資料。
  + 一種熱門編碼現在適用於布林值和數值資料行。
  + 其他其他的 bug 修正。

## <a name="2019-02-11"></a>2019-02-11

### <a name="azure-machine-learning-sdk-for-python-v1015"></a>適用於 Python 的 Azure Machine Learning SDK v1.0.15

+ **新功能**
  + Azure 機器學習服務管線新增 AzureBatchStep ([notebook](https://aka.ms/pl-azbatch))，HyperDriveStep (notebook)，並以時間為基礎來排程功能 ([notebook](https://aka.ms/pl-schedule))。
  +  DataTranferStep 已更新成可與 Azure SQL Server 和「適用於 PostgreSQL 的 Azure 資料庫」([otebook](https://aka.ms/pl-data-trans)) 搭配運作。

+ **變更**
  + 即將淘汰 `PublishedPipeline.get_published_pipeline` 而改用 `PublishedPipeline.get`。
  + 即將淘汰 `Schedule.get_schedule` 而改用 `Schedule.get`。

### <a name="azure-machine-learning-data-prep-sdk-v1012"></a>Azure Machine Learning 資料準備 SDK v1.0.12

+ **新功能**
  + 「資料準備」現在支援使用「資料存放區」從 Azure SQL 資料庫讀取資料。
 
+ **變更**
  + 改善記憶體效能的特定作業的大型資料。
  + `read_pandas_dataframe()` 現在會要求必須指定 `temp_folder`。
  + `ColumnProfile` 上的 `name` 屬性即將淘汰，請改用 `column_name`。

## <a name="2019-01-28"></a>2019-01-28

### <a name="azure-machine-learning-sdk-for-python-v1010"></a>Azure Machine Learning SDK for Python v1.0.10

+ **變更**： 
  + Azure ML SDK 不再具有 azure-cli 套件作為相依性。 明確的說，azure-cli-core 與 azure-cli-profile 相依性已從 azureml-core 移除。 以下是使用者影響的變更：
    + 如果您是執行 「 az 登入 」，然後使用 azureml sdk，SDK 會在一次執行瀏覽器或裝置程式碼記錄檔。 它不會使用由「az login」建立的任何認證狀態。
    + 對於 Azure CLI 驗證，例如使用「az login」，請使用 _azureml.core.authentication.AzureCliAuthentication_ 類別。 對於 Azure CLI 驗證，請在您已安裝 azureml-sdk 的 Python 環境中執行 _pip install azure-cli_。
    + 如果您正在使用服務主體執行「az login」以進行自動化，建議您使用 _azureml.core.authentication.ServicePrincipalAuthentication_ 類別，因為 azureml-sdk 不會使用 azure CLI 建立的認證狀態。 

+ **錯誤修正**：此版本主要包含次要錯誤修正

### <a name="azure-machine-learning-data-prep-sdk-v108"></a>Azure Machine Learning Data Prep SDK v1.0.8

+ **錯誤修正**
  + 改善效能的資料設定檔。
  + 已修正與錯誤報告相關的輕微錯誤。
  
### <a name="azure-portal-new-features"></a>Azure 入口網站：新功能
+ 全新的拖放圖表報告製作體驗。 使用者可以將資料行或屬性從 well 拖曳至圖表區域，在此區域系統將根據資料類型自動為使用者選取適當的圖表類型。 使用者可以將圖表類型變更為其他適用的類型，或新增其他屬性。

    支援的圖表類型：
    - 折線圖
    - 長條圖
    - 堆疊橫條圖
    - 盒狀圖
    - 散佈圖
    - 泡泡圖
+ 入口網站現在會動態地產生實驗報告。 當使用者將回合提交給實驗後，具有計量和圖表記錄的報告就會自動產生，以便您比較每個不同的回合。 

## <a name="2019-01-14"></a>2019-01-14

### <a name="azure-machine-learning-sdk-for-python-v108"></a>Azure Machine Learning SDK for Python v1.0.8

+ **錯誤修正**：此版本主要包含次要錯誤修正

### <a name="azure-machine-learning-data-prep-sdk-v107"></a>Azure Machine Learning Data Prep SDK v1.0.7

+ **新功能**
  + 資料存放區改善 (記載於[資料存放區操作指南](https://aka.ms/aml-data-prep-datastore-nb))
    + 已新增以相應增加方式從中讀取和寫入至 Azure 檔案共用和 ADLS 資料存放區的功能。
    + 使用資料存放區時，Data Prep 現在支援使用服務主體驗證，而非互動式驗證。
    + 已新增 wasb 和 wasbs url 的支援。

## <a name="2019-01-09"></a>2019-01-09

### <a name="azure-machine-learning-data-prep-sdk-v106"></a>Azure Machine Learning 資料準備 SDK v1.0.6

+ **錯誤修正**
  + 已修正與從 Spark 上公開可讀取的 Azure Blob 容器讀取相關的 Bug

## <a name="2018-12-20"></a>2018-12-20 

### <a name="azure-machine-learning-sdk-for-python-v106"></a>Azure Machine Learning SDK for Python v1.0.6
+ **錯誤修正**：此版本主要包含次要錯誤修正

### <a name="azure-machine-learning-data-prep-sdk-v104"></a>Azure Machine Learning Data Prep SDK v1.0.4

+ **新功能**
  + `to_bool` 函式現在允許將不相符的值轉換成 Error 值。 這是 `to_bool` 和 `set_column_types` 新的預設不相符行為，先前的預設行為會將不相符的值轉換為 False。
  + 在呼叫 `to_pandas_dataframe` 時，有新的選項可將數字資料行中的 null/遺失值解譯為 NaN。
  + 新增了功能，會檢查某些運算式的傳回類型，以確保類型一致性，並及早失敗。
  + 您現在可以呼叫 `parse_json`，以將資料行中的值剖析為 JSON 物件，並將其展開成多個資料行。

+ **錯誤修正**
  + 已修正錯誤，該錯誤會讓 Python 3.5.2 中的 `set_column_types` 損毀。
  + 已修正會在使用 AML 映像連線至資料存放區時當機的錯誤。

+ **更新**
  * 使用者入門教學課程、案例研究和操作指南的[範例 Notebook](https://aka.ms/aml-data-prep-notebooks)。

## <a name="2018-12-04-general-availability"></a>2018-12-04：正式運作

Azure Machine Learning 服務現已正式運作。

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning Compute
在此版本中，我們透過 [Azure Machine Learning Compute](how-to-set-up-training-targets.md#amlcompute) 來宣布新的受控計算體驗。 這個計算目標會取代適用於 Azure Machine Learning 的 Azure Batch AI 計算。 

這個計算目標：
+ 會用於模型定型和批次推斷
+ 是單一對多個節點的計算
+ 會為使用者執行叢集管理和作業排程
+ 會依預設自動調整
+ 同時支援 CPU 和 GPU 資源 
+ 可讓您使用低優先順序的 VM 來降低成本

您可以在 Python 中，使用 Azure 入口網站或 CLI 建立 Azure Machine Learning Compute。 它必須建立於您工作區所在的區域，而且無法附加至任何其他工作區。 這個計算目標會針對您的回合使用 Docker 容器，並封裝您的相依性，以便在您的所有節點上複寫相同的環境。

> [!Warning]
> 我們建議您建立新的工作區來使用 Azure Machine Learning Compute。 使用者嘗試從現有的工作區建立 Azure Machine Learning Compute 可能會看到錯誤，但機率很低。 您工作區中現有的計算應能持續運作而不會受到影響。

### <a name="azure-machine-learning-sdk-for-python-v102"></a>Azure Machine Learning SDK for Python v1.0.2
+ **重大變更**
  + 在此版本中，我們會移除從 Azure Machine Learning 中建立 VM 的支援。 您仍然可以附加現有的雲端 VM 或遠端內部部署伺服器。 
  + 我們也會移除對 BatchAI 的支援，這些功能現在全都應透過 Azure Machine Learning Compute 來支援。

+ <bpt id="p1">**</bpt>New<ept id="p1">**</ept>
  + 針對機器學習管線：
    + [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimator_step.estimatorstep?view=azure-ml-py) \(英文\)
    + [HyperDriveStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyper_drive_step.hyperdrivestep?view=azure-ml-py) \(英文\)
    + [MpiStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.mpi_step.mpistep?view=azure-ml-py) \(英文\)


+ **已更新**
  + 針對機器學習管線：
    + [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py) \(英文\) 現在接受 runconfig
    + [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) \(英文\) 現在可從 SQL 資料來源複製或複製到其中
    + 排程 SDK 中的功能，以建立和更新執行已發佈管線的排程

<!--+ **Bugs fixed**-->

### <a name="azure-machine-learning-data-prep-sdk-v052"></a>Azure Machine Learning 資料準備 SDK v0.5.2
+ **重大變更** 
  * `SummaryFunction.N` 已重新命名為 `SummaryFunction.Count`。
  
+ **錯誤修正**
  * 讀取自和寫入至遠端回合上的資料存放區時，請使用最新 AML 執行權杖 以前，如果在 Python 中更新 AML 執行權杖，將不會使用已更新的 AML 執行權杖來更新資料準備執行階段。
  * 其他更清楚的錯誤訊息
  * 當 Spark 使用 `Kryo` 序列化時，to_spark_dataframe() 將不再損毀
  * 值計數偵測器現在可以顯示 1000 個以上的唯一值
  * 如果原始的資料流程沒有名稱，隨機分割就不再失敗  

+ **詳細資訊**
  * [Azure Machine Learning 資料準備 SDK](https://aka.ms/data-prep-sdk)

### <a name="docs-and-notebooks"></a>文件和 Notebook
+ ML 管線
  + 適用於開始使用管線、批次範圍和樣式移轉範例之全新和更新的 Notebook： https://aka.ms/aml-pipeline-notebooks
  + 了解如何[建立您的第一個管線](how-to-create-your-first-pipeline.md)
  + 了解如何[使用管線執行批次預測](how-to-run-batch-predictions.md)
+ Azure Machine Learning 計算目標
  + [範例 Notebook](https://aka.ms/aml-notebooks) 現在已更新為使用新的受控計算。
  + [了解這個計算](how-to-set-up-training-targets.md#amlcompute)。

### <a name="azure-portal-new-features"></a>Azure 入口網站：新功能
+ 在入口網站中建立和管理 [Azure Machine Learning Compute](how-to-set-up-training-targets.md#amlcompute) 類型。
+ 監視 Azure Machine Learning Compute 的配額使用量和[要求配額](how-to-manage-quotas.md)。
+ 即時檢視 Azure Machine Learning Compute 叢集狀態。
+ 已新增對於建立 Azure Machine Learning Compute 和 Azure Kubernetes Service 的虛擬網路支援。
+ 使用現有的參數，重新執行您已發佈的管線。
+ 新的[自動化機器學習圖表](how-to-track-experiments.md#auto)，適用於分類模型 (升力、增益、校正、具備模型說明能力的特徵重要性圖表) 和迴歸模型 (殘差，以及具備模型說明能力的特徵重要性圖表)。 
+ 您可以在 Azure 入口網站中檢視管線。




## <a name="2018-11-20"></a>2018-11-20

### <a name="azure-machine-learning-sdk-for-python-v0180"></a>Azure Machine Learning SDK for Python v0.1.80

+ **重大變更** 
  * azureml.train.widgets 命名空間已移至 azureml.widgets。
  * azureml.core.compute.AmlCompute 取代了下列類別 - azureml.core.compute.BatchAICompute 和 azureml.core.compute.DSVMCompute。 第二個類別將會在後續版本中移除。 AmlCompute 類別現在有更簡易的定義，只需 vm_size 和 max_nodes 即可，並且會在提交作業時自動將叢集規模調整為 0 到 max_nodes。 我們已對[範例 Notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/training) 更新此資訊，這應該能提供使用範例給您參考。 希望您喜歡此簡化功能和後續版本中的許多有趣功能！

### <a name="azure-machine-learning-data-prep-sdk-v051"></a>Azure Machine Learning Data Prep SDK v0.5.1 

若要深入了解 Data Prep SDK，請閱讀[參考文件](https://aka.ms/data-prep-sdk)。
+ **新功能**
   * 建立新的 DataPrep CLI 來執行 DataPrep 套件，並檢視資料集或資料流程的資料設定檔
   * 重新設計 SetColumnType API 以改善可用性
   * 將 smart_read_file 重新命名為 auto_read_file
   * 資料設定檔現在包含偏度和峰度
   * 可透過分層取樣來取樣
   * 可從包含 CSV 檔案的 zip 檔案讀取
   * 可使用隨機劃分來分割資料列取向的資料集 (例如，測試-訓練集)
   * 可以藉由呼叫 `.dtypes` 從資料流程或資料設定檔取得所有資料行的資料類型
   * 可以藉由呼叫 `.row_count` 從資料流程或資料設定檔取得資料列計數

+ **錯誤修正**
   * 修正長時間的雙重轉換 
   * 修正新增任何資料行之後的判斷提示 
   * 修正 FuzzyGrouping 在某些情況下無法偵測到群組的問題
   * 修正排序函式以遵循多欄排列順序
   * 將 and/or 運算式修正為類似於 `pandas` 處理它們的方式
   * 修正從 dbfs 路徑讀取的方式
   * 讓錯誤訊息變得更容易了解 
   * 現在使用 AML 權杖讀取遠端計算目標時不會再失敗
   * Linux DSVM 現在不會再有失敗狀況
   * 現在字串述詞中有非字串值時不會再發生損毀
   * 現在能在資料流程發生正常的失敗時，處理判斷提示錯誤
   * 現在支援 Azure Databricks 上掛接 dbutils 的儲存位置

## <a name="2018-11-05"></a>2018-11-05

### <a name="azure-portal"></a>Azure 入口網站 
Azure Machine Learning 服務的 Azure 入口網站含有下列更新：
  * 用於已發佈管線的新 [管線] 索引標籤。
  * 已增加附加現有的 HDInsight 叢集作為計算目標的支援。

### <a name="azure-machine-learning-sdk-for-python-v0174"></a>Azure Machine Learning SDK for Python v0.1.74

+ **重大變更** 
  * *Workspace.compute_targets、datastores、experiments、images、models 以及 *webservices* 是屬性而非方法。 例如，將 Workspace.compute_targets() 取代為 Workspace.compute_targets。
  * *Run.get_context* 取代 *Run.get_submitted_run*。 第二個方法將會在後續版本中移除。
  * *PipelineData* 類別現在預期將 datastore 物件而不是 datastore_name 當作參數。 同樣地，*管線* 接受 default_datastore，而不是 default_datastore_name。

+ **新功能**
  * Azure Machine Learning 管線[範例 Notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline/pipeline-mpi-batch-prediction.ipynb) 現在使用 MPI 步驟。
  * Jupyter Notebooks 的 RunDetails 小工具已更新以顯示管線的視覺效果。

### <a name="azure-machine-learning-data-prep-sdk-v040"></a>Azure Machine Learning Data Prep SDK v0.4.0 
 
+ **新功能**
  * 類型計數已新增至資料設定檔 
  * 值計數與長條圖現在可以使用
  * 資料設定檔中有更多百分位數
  * 中間值可以在 Summarize 中使用
  * 現在支援 Python 3.7
  * 當您將包含資料存放區的資料流程儲存至 DataPrep 套件時，資料存放區資訊將保存為 DataPrep 套件的一部分
  * 現在支援寫入至資料存放區 
        
+ **已修正的 Bug**
  * 64 位元不帶正負號的整數溢位現在會在 Linux 上正確處理
  * 已在 smart_read 中修正純文字檔案不正確的文字標籤
  * 字串資料行類型現在會出現在 [計量] 檢視中
  * 類型計數現在已修正，可顯示對應至單一 FieldType，而不是個別項目的 ValueKinds
  * 當路徑以字串方式提供時，Write_to_csv 不會再失敗
  * 使用 Replace 時，將 “find” 留空將不會再失敗 

## <a name="2018-10-12"></a>2018-10-12

### <a name="azure-machine-learning-sdk-for-python-v0168"></a>Azure Machine Learning SDK for Python v0.1.68

+ **新功能**
  * 建立新工作區時的多個租用戶支援。

+ **已修正的 BUG**
  * 在部署 Web 服務時，您不再需要釘選 pynacl 程式庫版本。

### <a name="azure-machine-learning-data-prep-sdk-v030"></a>Azure Machine Learning 資料準備 SDK v0.3.0

+ **新功能**
  * 已新增 transform_partition_with_file(script_path) 方法，可讓使用者傳入要執行的 Python 檔案路徑

## <a name="2018-10-01"></a>2018-10-01

### <a name="azure-machine-learning-sdk-for-python-v0165"></a>Azure Machine Learning SDK for Python v0.1.65
[版本 0.1.65](https://pypi.org/project/azureml-sdk/0.1.65) 包含新功能、更多文件、錯誤修正和更多 [Notebook 範例](https://aka.ms/aml-notebooks)。

若要了解已知的 Bug 和因應措施，請參閱[已知問題的清單](resource-known-issues.md)。

+ **重大變更**
  * Workspace.experiments、Workspace.models、Workspace.compute_targets、Workspace.images、Workspace.web_services 會傳回字典，先前傳回清單。 請參閱 [azureml.core.Workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) API 文件。

  * 自動化 Machine Learning 已將正規化均方誤差從主要計量中移除。

+ **HyperDrive**
  * 各種貝氏的 HyperDrive bug 修正，以及取得計量呼叫的效能提升。 
  * Tensorflow 從 1.9 升級至 1.10 
  * 針對冷啟動的 Docker 映像最佳化。 
  * 作業現在會報告正確的狀態，即使是以非 0 的錯誤碼結束。 
  * SDK 中的 RunConfig 屬性驗證。 
  * HyperDrive 執行物件支援類似於一般執行的取消作業：不需要傳遞任何參數。 
  * 改善小工具以維護分散式執行和 HyperDrive 執行的下拉式清單值狀態。 
  * 已修正參數伺服器的 TensorBoard 和其他記錄檔支援。 
  * 服務端上的 Intel(R) MPI 支援。 
  * 修正 BatchAI 驗證期間散發執行修正的參數微調錯誤。 
  * 內容管理員現在會識別主要執行個體。 

+ **Azure 入口網站體驗**
  * 執行詳細資料中支援 log_table() 和 log_row()。 
  * 自動建立有 1、2 或 3 個數值資料行和選擇性類別資料行的資料表和資料列圖形。

+ **自動化 Machine Learning**
  * 改良錯誤處理和文件 
  * 已修正 run 屬性擷取的效能問題。 
  * 已修正繼續執行的問題。 
  * 已修正 Esembling 反覆項目問題。
  * 已修正 MAC OS 上的訓練懸置。
  * 對自訂驗證案例中的巨集平均 PR/ROC 曲線縮小取樣。
  * 已移除額外的索引邏輯。
  * 已從 get_output API 中移除篩選條件。

+ **管線**
  * 已新增 Pipeline.publish() 方法來直接發佈管線，而不需要先啟動執行。   
  * 已新增 PipelineRun.get_pipeline_runs() 方法來擷取從所發佈管線產生的管線執行。

+ **Project Brainwave**
  * 已更新 FPGA 上可用的新 AI 模型支援。

### <a name="azure-machine-learning-data-prep-sdk-v020"></a>Azure Machine Learning 資料準備 SDK v0.2.0
[版本 0.2.0](https://pypi.org/project/azureml-dataprep/0.2.0/) 包含下列功能和錯誤修正：

+ **新功能**
  * One-hot 編碼的支援
  * 分量轉換的支援
   
+ **已修正的 Bug：**
  * 適用於任何 Tornado 版本，不必降級 Tornado 版本
  * 值計數代表所有值，不只是前三個值

## <a name="2018-09-public-preview-refresh"></a>2018-09 (公開預覽刷新版)

Azure Machine Learning 的全新重新整理版本：深入了解此版本： https://azure.microsoft.com/blog/what-s-new-in-azure-machine-learning-service/


## <a name="next-steps"></a>後續步驟

閱讀 [Azure Machine Learning 服務](../service/overview-what-is-azure-ml.md)概觀。
