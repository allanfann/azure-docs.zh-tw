---
title: 在 Linux 上建立 Java Web 應用程式 - Azure App Service
description: 在本快速入門中，您將在短短幾分鐘內在 Linux 上的 Azure App Service 中部署第一個 Java Hello World。
keywords: Azure, App Service, Web 應用程式, Linux, Java, Maven, 快速入門
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: quickstart
ms.date: 03/27/2019
ms.author: msangapu
ms.custom: mvc
ms.openlocfilehash: 7c6d5034335a455d24b1f22919b672e2ead2810d
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2019
ms.locfileid: "65510846"
---
# <a name="quickstart-create-a-java-app-in-app-service-on-linux"></a>快速入門：在 Linux 上的 App Service 中建立 Java 應用程式

[Linux 上的 App Service](app-service-linux-intro.md) 使用 Linux 作業系統提供可高度擴充、自我修復的 Web 主機服務。 本快速入門說明如何使用 [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) 搭配[適用於 Azure App Service 的 Maven 外掛程式](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin)，部署 Java Web 封存 (WAR) 檔案。
> [!NOTE]
>
> 使用 IntelliJ 和 Eclipse 等熱門 IDE 也可以執行相同的動作。 請參閱 [Azure Toolkit for IntelliJ 快速入門](/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app)或 [Azure Toolkit for Eclipse 快速入門](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app)中的類似文件。
>
![在 Azure 中執行的範例應用程式](media/quickstart-java/java-hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>建立 Java 應用程式

在 Cloud Shell 提示字元中執行下列 Maven 命令，以建立名為 `helloworld` 的新應用程式：

```bash
mvn archetype:generate -DgroupId=example.demo -DartifactId=helloworld -DarchetypeArtifactId=maven-archetype-webapp
```

## <a name="configure-the-maven-plugin"></a>設定 Maven 外掛程式

若要從 Maven 進行部署，請在 Cloud Shell 中使用程式碼編輯器，開啟 `helloworld` 目錄中的 `pom.xml` 專案檔案。 

```bash
code pom.xml
```

然後在 `pom.xml` 檔案的 `<build>` 元素內新增下列外掛程式定義。

```xml
<plugins>
    <!--*************************************************-->
    <!-- Deploy to Tomcat in App Service Linux           -->
    <!--*************************************************-->
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.5.4</version>
        <configuration>
            <!-- Specify v2 schema -->
            <schemaVersion>v2</schemaVersion>
            <!-- App information -->
            <subscriptionId>${SUBSCRIPTION_ID}</subscriptionId>
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appName>${WEBAPP_NAME}</appName>
            <region>${REGION}</region>
   
            <!-- Java Runtime Stack for App on Linux-->
            <runtime>
                <os>linux</os>
                <javaVersion>jre8</javaVersion>
                <webContainer>tomcat 8.5</webContainer>
            </runtime> 
            <deployment>
                <resources>
                    <resource>
                        <directory>${project.basedir}/target</directory>
                        <includes>
                            <include>*.war</include>
                        </includes>
                    </resource>
                </resources>
            </deployment>
        </configuration>
    </plugin>
</plugins>
```    


> [!NOTE] 
> 在本文中，我們只會使用封裝在 WAR 檔案中的 JAVA 應用程式。 此外掛程式也支援 JAR Web 應用程式，請瀏覽[將 Java SE JAR 檔案部署至 Linux 上的 App Service](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)，並加以試用。


更新外掛程式組態中的下列預留位置：

| Placeholder | 說明 |
| ----------- | ----------- |
| `SUBSCRIPTION_ID` | 您要作為應用程式部署目的地的唯一訂用帳戶識別碼。 使用 `az account show` 命令，可以從 Cloud Shell 或 CLI 取得預設訂用帳戶的 ID。 如需所有可用的訂用帳戶，請使用 `az account list` 命令。|
| `RESOURCEGROUP_NAME` | 容納新建立應用程式的新資源群組名稱。 將應用程式的所有資源放在群組中，藉此同時管理。 例如，刪除資源群組會刪除所有與應用程式相關聯的資源。 使用唯一的新資源群組名稱來更新此值，例如 TestResources。 您在下一節中會使用此資源群組名稱來清除所有的 Azure 資源。 |
| `WEBAPP_NAME` | 應用程式名稱會成為應用程式在部署至 Azure 時的部分主機名稱 (WEBAPP_NAME.azurewebsites.net)。 將此值更新為新 App Service 應用程式的唯一名稱 (例如 contoso)，它將會主控您的 Java 應用程式。 |
| `REGION` | 託管應用程式的 Azure 區域，例如 `westus2`。 您可以使用 `az account list-locations` 命令，從 Cloud Shell 或 CLI 取得區域清單。 |

## <a name="deploy-the-app"></a>部署應用程式

使用下列命令將您的 Java 應用程式部署至 Azure：

```bash
mvn package azure-webapp:deploy
```

完成部署後，在網頁瀏覽器中使用下列 URL，瀏覽至已部署的應用程式，例如 `http://<webapp>.azurewebsites.net`。 

![在 Azure 中執行的範例應用程式](media/quickstart-java/java-hello-world-in-browser-curl.png)

**恭喜！** 您已將第一個 Java 應用程式部署至 Linux 上的 App Service。

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [教學課程：搭配 PostgreSQL 的 Java Enterprise 應用程式](tutorial-java-enterprise-postgresql-app.md)

> [!div class="nextstepaction"]
> [設定 Java 應用程式](configure-custom-container.md)

> [!div class="nextstepaction"]
> [使用 Jenkins 的 CI/CD](/azure/jenkins/deploy-jenkins-app-service-plugin)

> [!div class="nextstepaction"]
> [適用於 Java 開發人員的其他 Azure 資源](/java/azure/)
