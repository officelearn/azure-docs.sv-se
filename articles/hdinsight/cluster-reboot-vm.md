---
title: Starta om virtuella datorer för Azure HDInsight-kluster
description: Lär dig hur du startar om virtuella datorer som inte svarar för HDInsight-kluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/22/2020
ms.openlocfilehash: c0f0bd9eb423b3de6a602647dff93fd9fce6e13e
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2020
ms.locfileid: "86077022"
---
# <a name="reboot-vms-for-hdinsight-cluster"></a>Starta om virtuella datorer för HDInsight-kluster

HDInsight-kluster innehåller grupper med virtuella datorer som klusternoder. För tids krävande kluster kan de här noderna sluta fungera av olika orsaker. I den här artikeln beskrivs hur du startar om virtuella datorer som inte svarar i ett HDInsight-kluster.

## <a name="when-to-reboot"></a>När du ska starta om

> [!WARNING]  
> Om du startar om virtuella datorer i klustret får du avbrott i noden och startar om tjänster på noden. 

När noden startas om kan klustret skadas, och jobben kan bli långsamma eller Miss lyckas. Om du försöker starta om den aktiva Head-noden kommer alla jobb som körs att avlivas och du kan inte skicka jobb till klustret förrän tjänsterna är igång igen. Du bör överväga att bara starta om virtuella datorer när det behövs. Här följer några rikt linjer för när du ska överväga att starta om virtuella datorer.

- Det går inte att SSHa till noden, men den svarar på pingar.
- Arbetsnoden är avstängd utan pulsslag i Ambari-ANVÄNDARGRÄNSSNITTET.
- Den temporära disken är full på noden.
- Process tabellen på den virtuella datorn har många poster där processen har slutförts, men den visas med "avslutat tillstånd".

> [!WARNING]  
> Du bör vara mer försiktig när du startar om virtuella datorer för **HBase** och **Kafka** clustes, eftersom det kan leda till förlust av data.

## <a name="use-powershell-to-reboot-vms"></a>Använd PowerShell för att starta om virtuella datorer

Det finns två steg som krävs för att använda åtgärden starta om noden: lista noder och starta om noder.

1. Lista noder. Du kan hämta listan över klusternoder via [Get-AzHDInsightHost](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsighthost). 

  ```
  Get-AzHDInsightHost -ClusterName myclustername
  ```

2. Starta om värdar. När du har hämtat namnen på de noder som du vill starta om startar du om noderna med hjälp av [restart-AzHDInsightHost](https://docs.microsoft.com/powershell/module/az.hdinsight/restart-azhdinsighthost).

  ```
  Restart-AzHDInsightHost -ClusterName myclustername -Name wn0-myclus, wn1-myclus
  ```

## <a name="use-rest-api-to-reboot-vms"></a>Använd REST API för att starta om virtuella datorer

Du kan använda funktionen **testa IT** i API-dokumentet för att skicka begär anden till HDInsight. Det finns två steg som krävs för att använda åtgärden starta om noden: lista noder och starta om noder.

1. Lista noder. Du kan hämta listan över klusternoder från REST API eller i Ambari. Mer information finns i [HDInsight List hosts REST API operation](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines/listhosts).

    ```
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/listHosts?api-version=2018-06-01-preview
    ```

2. Starta om värdar. När du har hämtat namnen på de noder som du vill starta om, använder du starta om noder REST API för att starta om noderna. Nodnamnet följer mönstret **"NodeType (ned/HN/ZK/GW)" + "x" + "första 6 tecknen i kluster namnet"**. Mer information finns på [värdarna för att starta om värd REST API åtgärden](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines/restarthosts).

    ```
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/restartHosts?api-version=2018-06-01-preview
    ```

De faktiska namnen på noderna som du vill starta om anges i en JSON-matris i begär ande texten.

```json
[
  "wn0-abcdef",
  "zk1-abcdef"
]
```

## <a name="next-steps"></a>Nästa steg

* [Starta om AzHDInsightHost](https://docs.microsoft.com/powershell/module/az.hdinsight/restart-azhdinsighthost)
* [Virtuella HDInsight-datorer REST API](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines)
* [HDInsight-REST API](https://docs.microsoft.com/rest/api/hdinsight/)
