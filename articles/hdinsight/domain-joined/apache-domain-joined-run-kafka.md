---
title: 使用企業安全性套件在 HDInsight 中設定 Apache Kafka 原則 - Azure
description: 了解如何使用企業安全性套件在 Azure HDInsight 中設定 Kafka 的 Apache Ranger 原則。
ms.service: hdinsight
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: tutorial
ms.date: 01/14/2019
ms.openlocfilehash: 6434f7cae3c3fa402efad00b2f6bfb0bc405f9e3
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2019
ms.locfileid: "64730245"
---
# <a name="tutorial-configure-apache-kafka-policies-in-hdinsight-with-enterprise-security-package-preview"></a>教學課程：使用企業安全性套件在 HDInsight 中設定 Apache Kafka 原則 (預覽)

了解如何對企業安全性套件 (ESP) Apache Kafka 叢集設定 Apache Ranger 原則。 ESP 叢集連線到網域，讓使用者使用網域認證進行驗證。 在此教學課程中，您會建立兩個 Ranger 原則來限制 `sales*` 和 `marketingspend` 主題的存取權。

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 建立網域使用者
> * 建立 Ranger 原則
> * 在 Kafka 叢集中建立主題
> * 測試 Ranger 原則

## <a name="before-you-begin"></a>開始之前

* 如果您沒有 Azure 訂用帳戶，請建立[免費帳戶](https://azure.microsoft.com/free/)。

* 登入 [Azure 入口網站](https://portal.azure.com/)。

* 使用企業安全性套件建立 [HDInsight Kafka 叢集](apache-domain-joined-configure-using-azure-adds.md)。

## <a name="connect-to-apache-ranger-admin-ui"></a>連線到 Apache Ranger 系統管理 UI

1. 從瀏覽器中，使用 URL `https://<ClusterName>.azurehdinsight.net/Ranger/` 連線到 Ranger 管理員使用者介面。 請記得將 `<ClusterName>` 變更為 Kafka 叢集的名稱。

    > [!NOTE]  
    > Ranger 認證與 Hadoop 叢集認證並非相同。 若要避免瀏覽器使用快取的 Hadoop 認證，請使用新的 InPrivate 瀏覽器視窗連線至 Ranger 管理員 UI。

2. 使用您的 Azure Active Directory (AD) 管理員認證登入。 Azure AD 管理員認證與 HDInsight 叢集認證或 Linux HDInsight 節點 SSH 認證並非相同。

   ![Apache Ranger 管理員 UI](./media/apache-domain-joined-run-kafka/apache-ranger-admin-login.png)

## <a name="create-domain-users"></a>建立網域使用者

請參閱[使用企業安全性套件建立 HDInsight 叢集](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds)，以了解如何建立 **sales_user** 和 **marketing_user** 網域使用者。 在生產情節中，網域使用者來自 Active Directory 租用戶。

## <a name="create-ranger-policy"></a>建立 Ranger 原則

為 **sales_user** 和 **marketing_user** 建立 Ranger 原則。

1. 開啟 **Ranger 管理員 UI**。

2. 按一下 [Kafka] 底下的 [\<ClusterName>_kafka]。 可能會列出其中一個預先設定的原則。

3. 按一下 [新增原則]，並輸入下列值︰

   |**設定**  |**建議的值**  |
   |---------|---------|
   |原則名稱  |  hdi 銷售* 原則   |
   |話題   |  銷售* |
   |選取使用者  |  sales_user1 |
   |權限  | 發佈、取用、建立 |

   主題名稱可以包含下列萬用字元：

   * 「*」表示出現零次以上的字元。
   * 「?」表示單一字元。

   ![Apache Ranger 管理員 UI 建立原則](./media/apache-domain-joined-run-kafka/apache-ranger-admin-create-policy.png)   

   >[!NOTE]
   >如果 [選取使用者] 未自動填入網域使用者，請稍候 Ranger 與 Azure AD 同步處理。

4. 按一下 [新增] 以儲存規則。

5. 按一下 [新增原則]，然後輸入下列值︰

   |**設定**  |**建議的值**  |
   |---------|---------|
   |原則名稱  |  hdi 行銷原則   |
   |話題   |  marketingspend |
   |選取使用者  |  marketing_user1 |
   |權限  | 發佈、取用、建立 |

   ![Apache Ranger 管理員 UI 建立原則](./media/apache-domain-joined-run-kafka/apache-ranger-admin-create-policy-2.png)  

6. 按一下 [新增] 以儲存規則。

## <a name="create-topics-in-a-kafka-cluster-with-esp"></a>在 Kafka 叢集中使用 ESP 建立主題

建立兩個主題，`salesevents` 和 `marketingspend`：

1. 下列命令可用來開啟與叢集的 SSH 連線：

   ```bash
   ssh DOMAINADMIN@CLUSTERNAME-ssh.azurehdinsight.net
   ```

   將 `DOMAINADMIN` 取代為[叢集建立](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds#create-a-hdinsight-cluster-with-esp)期間設定的叢集管理使用者，並將 `CLUSTERNAME` 取代為您的叢集名稱。 出現提示時，請輸入管理使用者帳戶的密碼。 如需搭配 HDInsight 使用 `SSH` 的詳細資訊，請參閱[搭配 HDInsight 使用 SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)。

2. 下列命令可用來將叢集名稱儲存到變數，並安裝 JSON 剖析公用程式 `jq`。 出現提示時，請輸入 Kafka 叢集名稱。

   ```bash
   sudo apt -y install jq
   read -p 'Enter your Kafka cluster name:' CLUSTERNAME
   ```

3. 下列命令可用來取得 Kafka 訊息代理程式主機。 出現提示時，輸入叢集管理員帳戶的密碼。

   ```bash
   export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`; \
   ```

   > [!Note]  
   > 繼續操作之前，您可能需要設定開發環境 (如果您尚未設定)。 您將需要 Java JDK、Apache Maven 和具有 SCP 的 SSH 用戶端等元件。 如需詳細資訊，請參閱[設定指示](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer)。
   
1. 下載[已加入 Apache Kafka 網域的產生者/取用者範例](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer)。

1. 依照**建置並部署範例**底下的步驟 2 和 3 進行操作 (在[教學課程：使用 Apache Kafka Producer 和 Consumer API](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-producer-consumer-api#build-and-deploy-the-example) 中)

1. 執行下列命令：

   ```bash
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf kafka-producer-consumer.jar create salesevents $KAFKABROKERS
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf kafka-producer-consumer.jar create marketingspend $KAFKABROKERS
   ```

## <a name="test-the-ranger-policies"></a>測試 Ranger 原則

根據設定的 Ranger 原則，**sales_user** 可產生/取用主題 `salesevents`，但是無法產生/取用主題 `marketingspend`。 相反地，**marketing_user** 可產生/取用主題 `marketingspend`，而無法產生/取用主題 `salesevents`。

1. 對於叢集開啟新的 SSH 連線。 下列命令可用來以 **sales_user1** 身分登入：

   ```bash
   ssh sales_user1@CLUSTERNAME-ssh.azurehdinsight.net
   ```

2. 執行以下命令：

   ```bash
   export KAFKA_OPTS="-Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf"
   ```

3. 上一節中的訊息代理程式名稱可用來設定下列環境變數：

   ```bash
   export KAFKABROKERS=<brokerlist>:9092
   ```

   範例： `export KAFKABROKERS=wn0-khdicl.contoso.com:9092,wn1-khdicl.contoso.com:9092`

4. 依照**建置並部署範例**底下的步驟 3 進行操作 (在[教學課程：使用 Apache Kafka Producer 和 Consumer API](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-producer-consumer-api#build-and-deploy-the-example))，以確定 `kafka-producer-consumer.jar` 也可供 **sales_user**使用。

5. 藉由執行下列命令，確認 **sales_user1** 可產生至主題 `salesevents`：

   ```bash
   java -jar kafka-producer-consumer.jar producer salesevents $KAFKABROKERS
   ```

6. 下列命令可用來取用主題 `salesevents`：

   ```bash
   java -jar kafka-producer-consumer.jar consumer salesevents $KAFKABROKERS
   ```

   請確認您能夠讀取訊息。

7. 藉由在相同 ssh 視窗中執行下列命令，確認 **sales_user1** 無法產生至主題 `marketingspend`：

   ```bash
   java -jar kafka-producer-consumer.jar producer marketingspend $KAFKABROKERS
   ```

   發生授權錯誤，可予以忽略。

8. 請注意，無法從主題 `salesevents` 取用 **marketing_user1**。

   重複執行上述步驟 1-4，不過這次是以 **marketing_user1** 身分執行。

   下列命令可用來取用主題 `salesevents`：

   ```bash
   java -jar kafka-producer-consumer.jar consumer salesevents $KAFKABROKERS
   ```

   先前的訊息不會出現。

9. 從 Ranger UI 檢視稽核存取事件。

   ![Ranger UI 原則稽核](./media/apache-domain-joined-run-kafka/apache-ranger-admin-audit.png)

## <a name="next-steps"></a>後續步驟

* [將您自己的金鑰攜帶至 Apache Kafka](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-byok)
* [使用企業安全性套件維護 Apache Hadoop 安全性的簡介](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-introduction)
