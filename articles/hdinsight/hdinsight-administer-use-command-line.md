---
title: Hantera Apache Hadoop-kluster med Azure klassiska CLI - Azure HDInsight
description: Lär dig hur du använder den klassiska Azure CLI för att hantera Apache Hadoop-kluster i Azure HDInsight.
services: hdinsight
ms.reviewer: jasonh
author: tylerfox
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: tyfox
ms.openlocfilehash: 4de4674d8a4c2b573df12648739971e460531636
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2018
ms.locfileid: "52495092"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-using-the-azure-classic-cli"></a>Hantera Apache Hadoop-kluster i HDInsight med hjälp av den klassiska Azure-CLI
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Lär dig hur du använder den [klassiska Azure-CLI](../cli-install-nodejs.md) att hantera [Apache Hadoop](https://hadoop.apache.org/) kluster i Azure HDInsight. Klassisk CLI är implementerat i Node.js. Det kan användas på alla plattformar som har stöd för Node.js, inklusive Windows, Mac- och Linux.

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

## <a name="prerequisites"></a>Förutsättningar
Innan du påbörjar den här artikeln måste du ha:

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Azure klassiskt CLI** – Se [installera och konfigurera den klassiska Azure-CLI](../cli-install-nodejs.md) för information om installation och konfiguration.
* **Ansluta till Azure**, med följande kommando:

    ```cli
    azure login
    ```
  
    Mer information om autentisering med ett arbets- eller skolkonto konto finns i [Anslut till en Azure-prenumeration från den klassiska Azure-CLI](/cli/azure/authenticate-azure-cli).
* **Växla till läget Azure Resource Manager**, med följande kommando:
  
    ```cli
    azure config mode arm
    ```

Om du vill ha hjälp med att använda den **-h** växla.  Exempel:

```cli
azure hdinsight cluster create -h
```

## <a name="create-clusters-with-the-cli"></a>Skapa kluster med CLI
Se [Skapa kluster i HDInsight med hjälp av den klassiska Azure-CLI](hdinsight-hadoop-create-linux-clusters-azure-cli.md).

## <a name="list-and-show-cluster-details"></a>Visa information om kluster
Använd följande kommandon för att visa information om kluster:

```cli
azure hdinsight cluster list
azure hdinsight cluster show <Cluster Name>
```

![Kommandoradsverktyget vy över klusterlista][image-cli-clusterlisting]

## <a name="delete-clusters"></a>Ta bort kluster
Använd följande kommando för att ta bort ett kluster:

```cli
azure hdinsight cluster delete <Cluster Name>
```

Du kan också ta bort ett kluster genom att ta bort resursgruppen som innehåller klustret. Observera att detta tar bort alla resurser i gruppen, inklusive standardkontot för lagring.

```cli
azure group delete <Resource Group Name>
```

## <a name="scale-clusters"></a>Skala kluster
Så här ändrar storleken för Apache Hadoop-kluster:

```cli
azure hdinsight cluster resize [options] <clusterName> <Target Instance Count>
```


## <a name="enabledisable-http-access-for-a-cluster"></a>Aktivera/inaktivera HTTP-åtkomst för ett kluster

```cli
azure hdinsight cluster enable-http-access [options] <Cluster Name> <userName> <password>
azure hdinsight cluster disable-http-access [options] <Cluster Name>
```

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig hur du utför olika HDInsight-kluster administrativa uppgifter. Mer information finns i följande artiklar:

* [Administrera HDInsight med hjälp av Azure-portalen][hdinsight-admin-portal]
* [Administrera HDInsight med hjälp av Azure PowerShell][hdinsight-admin-powershell]
* [Kom igång med Azure HDInsight][hdinsight-get-started]
* [Hur du använder den klassiska Azure-CLI][azure-command-line-tools]

[azure-command-line-tools]: ../cli-install-nodejs.md
[azure-create-storageaccount]:../storage/common/storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md

[image-cli-account-download-import]: ./media/hdinsight-administer-use-command-line/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-administer-use-command-line/command-line-list-of-clusters.png "Lista och visa kluster"
