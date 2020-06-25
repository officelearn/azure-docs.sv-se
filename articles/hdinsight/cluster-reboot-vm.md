---
title: Starta om virtuella datorer för Azure HDInsight-kluster
description: Lär dig hur du startar om virtuella datorer som inte svarar för HDInsight-kluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/22/2020
ms.openlocfilehash: 46efccad60c06c73145bbf30c119f6a47cb856d5
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2020
ms.locfileid: "85323458"
---
# <a name="reboot-vms-for-hdinsight-cluster"></a>Starta om virtuella datorer för HDInsight-kluster

HDInsight-kluster innehåller grupper med virtuella datorer som klusternoder. För tids krävande kluster kan de här noderna sluta fungera av olika orsaker. I den här artikeln beskrivs hur du startar om virtuella datorer som inte svarar i ett HDInsight-kluster.

## <a name="when-to-reboot"></a>När du ska starta om

Om du startar om virtuella datorer i klustret får du avbrott i noden och startar om tjänster på noden. När noden startas om kan klustret skadas, och jobben kan bli långsamma eller Miss lyckas. Om du försöker starta om den aktiva Head-noden kommer alla jobb som körs att avlivas och du kan inte skicka jobb till klustret förrän tjänsterna är igång igen. Du bör överväga att bara starta om virtuella datorer när det behövs. Här följer några rikt linjer för när du ska överväga att starta om virtuella datorer.

- Det går inte att SSHa till noden, men den svarar på pingar.
- Arbetsnoden är avstängd utan pulsslag i Ambari-ANVÄNDARGRÄNSSNITTET.
- Den temporära disken är full på noden.
- Process tabellen på den virtuella datorn har många poster där processen har slutförts, men den visas med "avslutat tillstånd"

## <a name="use-rest-api-to-reboot-vms"></a>Använd REST API för att starta om virtuella datorer

Omstart av nod stöds bara via REST API för närvarande. Du kan använda funktionen **testa IT** i API-dokumentet för att skicka begär anden till HDInsight. Det finns två steg som krävs för att använda åtgärden starta om noden: lista noder och starta om noder.

1. Lista noder. Du kan hämta listan över klusternoder från REST API eller i Ambari. Mer information finns i [HDInsight List hosts REST API-åtgärd](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines/listhosts)

    ```
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/listHosts?api-version=2018-06-01-preview
    ```

2. Starta om värdar. När du har hämtat namnen på de noder som du vill starta om, använder du starta om värdar REST API för att starta om noderna. Mer information finns på [värdarna för att starta om värd REST API åtgärden](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines/restarthosts)

    ```
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/restartHosts?api-version=2018-06-01-preview
    ```

De faktiska namnen på noderna som du vill starta om anges i en JSON-matris i begär ande texten.

```json
[
  "gateway1",
  "gateway3"
]
```

## <a name="next-steps"></a>Nästa steg

* [Virtuella HDInsight-datorer REST API](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines)
* [HDInsight-REST API](https://docs.microsoft.com/rest/api/hdinsight/)