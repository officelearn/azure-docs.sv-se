---
title: "Hantera Hadoop-kluster med hjälp av Azure CLI - Azure HDInsight | Microsoft Docs"
description: "Lär dig hur du använder Azure-kommandoradsgränssnittet för att hantera Hadoop-kluster i Azure HDInsight. Azure CLI fungerar i Windows, Mac- och Linux."
services: hdinsight
editor: cgronlun
manager: jhubbard
author: mumian
tags: azure-portal
documentationcenter: 
ms.assetid: 4f26c79f-8540-44bd-a470-84722a9e4eca
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: jgao
ms.openlocfilehash: cc1b78e430ab46f367fafcc16e1240aa76a9ba64
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="manage-hadoop-clusters-in-hdinsight-using-the-azure-cli"></a>Hantera Hadoop-kluster i HDInsight med hjälp av Azure CLI
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Lär dig hur du använder den [Azure-kommandoradsgränssnittet](../cli-install-nodejs.md) hantera Hadoop-kluster i Azure HDInsight. Azure CLI är implementerat i Node.js. Det kan användas på alla plattformar som har stöd för Node.js, inklusive Windows, Mac- och Linux. För närvarande stöder inte HDInsight [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/overview).

Den här artikeln beskriver endast med hjälp av Azure CLI med HDInsight. En allmän vägledning om hur du använder Azure CLI finns [installera och konfigurera Azure CLI][azure-command-line-tools].

## <a name="prerequisites"></a>Krav
Innan du påbörjar den här artikeln måste du ha:

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Azure CLI** - Se [Installera och konfigurera Azure CLI](../cli-install-nodejs.md) för information om installation och konfiguration.
* **Ansluta till Azure**, med följande kommando:

    ```cli
    azure login
    ```
  
    Mer information om autentisering med ett arbets- eller skolkonto finns i [Anslut till en Azure-prenumeration från Azure CLI](../xplat-cli-connect.md).
* **Växla till läget Azure Resource Manager**, med följande kommando:
  
    ```cli
    azure config mode arm
    ```

För att få hjälp att använda den **-h** växla.  Exempel:

```cli
azure hdinsight cluster create -h
```

## <a name="create-clusters-with-the-cli"></a>Skapa kluster med CLI
Se [Skapa kluster i HDInsight med hjälp av Azure CLI](hdinsight-hadoop-create-linux-clusters-azure-cli.md).

## <a name="list-and-show-cluster-details"></a>Listan och visa information om kluster
Listan och visa information om kluster med hjälp av följande kommandon:

```cli
azure hdinsight cluster list
azure hdinsight cluster show <Cluster Name>
```

![Kommandoradsverktyget vy av klustret lista][image-cli-clusterlisting]

## <a name="delete-clusters"></a>Ta bort kluster
Använd följande kommando för att ta bort ett kluster:

```cli
azure hdinsight cluster delete <Cluster Name>
```

Du kan också ta bort ett kluster genom att ta bort resursgruppen som innehåller klustret. Observera detta tar bort alla resurser i gruppen inklusive standardkontot för lagring.

```cli
azure group delete <Resource Group Name>
```

## <a name="scale-clusters"></a>Skala kluster
Ändra storlek för Hadoop-kluster:

```cli
azure hdinsight cluster resize [options] <clusterName> <Target Instance Count>
```


## <a name="enabledisable-http-access-for-a-cluster"></a>Aktivera/inaktivera HTTP-åtkomst för ett kluster

```cli
azure hdinsight cluster enable-http-access [options] <Cluster Name> <userName> <password>
azure hdinsight cluster disable-http-access [options] <Cluster Name>
```

## <a name="enabledisable-rdp-access-for-a-cluster"></a>Aktivera/inaktivera RDP-åtkomst för ett kluster

```cli
azure hdinsight cluster enable-rdp-access [options] <Cluster Name> <rdpUserName> <rdpPassword> <rdpExpiryDate>
azure hdinsight cluster disable-rdp-access [options] <Cluster Name>
```

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig hur du utför olika HDInsight-kluster administrativa uppgifter. Mer information finns i följande artiklar:

* [Administrera HDInsight med hjälp av Azure-portalen][hdinsight-admin-portal]
* [Administrera HDInsight med hjälp av Azure PowerShell][hdinsight-admin-powershell]
* [Kom igång med Azure HDInsight][hdinsight-get-started]
* [Hur du använder Azure CLI][azure-command-line-tools]

[azure-command-line-tools]: ../cli-install-nodejs.md
[azure-create-storageaccount]:../storage/common/storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md

[image-cli-account-download-import]: ./media/hdinsight-administer-use-command-line/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-administer-use-command-line/command-line-list-of-clusters.png "Listan och visa kluster"
