---
title: hbase hbck returnerar inkonsekvenser i Azure HDInsight
description: hbase hbck returnerar inkonsekvenser i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/08/2019
ms.openlocfilehash: fa02ac0dfe229f3e82d1c1c62d83ca06a81efca6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887333"
---
# <a name="scenario-hbase-hbck-command-returns-inconsistencies-in-azure-hdinsight"></a>Scenario: `hbase hbck` kommandot returnerar inkonsekvenser i Azure HDInsight

I den här artikeln beskrivs felsökningssteg och möjliga lösningar för problem när du interagerar med Azure HDInsight-kluster.

## <a name="issue-region-is-not-in-hbasemeta"></a>Problem: Regionen är inte i`hbase:meta`

Region xxx på HDFS, men `hbase:meta` inte listad i eller distribuerad på någon regionserver.

### <a name="cause"></a>Orsak

Varierar.

### <a name="resolution"></a>Lösning

1. Åtgärda metatabellen genom att köra:

    ```
    hbase hbck -ignorePreCheckPermission –fixMeta
    ```

1. Tilldela regioner till RegionServers genom att köra:

    ```
    hbase hbck -ignorePreCheckPermission –fixAssignment
    ```
---

## <a name="issue-region-is-offline"></a>Problem: Regionen är offline

Region xxx har inte distribuerats på någon RegionServer. Det innebär att `hbase:meta`regionen är i , men offline.

### <a name="cause"></a>Orsak

Varierar.

### <a name="resolution"></a>Lösning

Ta regioner online genom att köra:

```
hbase hbck -ignorePreCheckPermission –fixAssignment
```

---

## <a name="issue-regions-have-the-same-startend-keys"></a>Problem: Regioner har samma start-/slutnycklar

### <a name="cause"></a>Orsak

Varierar.

### <a name="resolution"></a>Lösning

Sammanfoga de överlappande områdena manuellt. Gå till tabellen för HBase HMaster Web UI, välj tabelllänken som har problemet. Du kommer att se startnyckeln/slutnyckeln för varje region som tillhör tabellen. Slå sedan samman dessa överlappande regioner. Gör `merge_region 'xxxxxxxx','yyyyyyy', true`i HBase-skalet . Ett exempel:

```
RegionA, startkey:001, endkey:010,

RegionB, startkey:001, endkey:080,

RegionC, startkey:010, endkey:080.
```

I det här fallet måste du slå samman RegionA och RegionC och få RegionD med samma nyckelintervall som RegionB och sedan slå samman RegionB och RegionD. xxxxxxx och yyyyyy är hashsträngen i slutet av varje regionnamn. Var försiktig här för att inte slå samman två diskontinuerliga regioner. Efter varje sammanslagning, som sammanfoga A och C, startar HBase en komprimering på RegionD. Vänta tills komprimeringen är klar innan du gör en ny sammanslagning med RegionD. Du hittar kompakteringsstatusen på den regionserversidan i HBase HMaster UI.

---

## <a name="issue-cant-load-regioninfo"></a>Problem: Det går inte att läsa in`.regioninfo`

Det går `.regioninfo` inte `/hbase/data/default/tablex/regiony`att läsa in för region .

### <a name="cause"></a>Orsak

Detta beror troligen på att region partiell borttagning när RegionServer kraschar eller vm startar om. Azure Storage är för närvarande ett platt blob-filsystem och vissa filåtgärder är inte atomära.

### <a name="resolution"></a>Lösning

Rensa de återstående filerna och mapparna manuellt:

1. Kör `hdfs dfs -ls /hbase/data/default/tablex/regiony` för att kontrollera vilka mappar/filer som fortfarande finns under den.

1. Kör `hdfs dfs -rmr /hbase/data/default/tablex/regiony/filez` för att ta bort alla underordnade filer/mappar

1. Kör `hdfs dfs -rmr /hbase/data/default/tablex/regiony` för att ta bort regionmappen.

---

## <a name="next-steps"></a>Nästa steg

Om du inte såg problemet eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure Community Support](https://azure.microsoft.com/support/community/).

* Anslut [@AzureSupport](https://twitter.com/azuresupport) med – det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen. Ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en supportbegäran från [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **Stöd** i menyraden eller öppna **supporthubben Hjälp +.** Mer detaljerad information finns i [Så här skapar du en Azure-supportbegäran](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Åtkomst till prenumerationshantering och faktureringssupport ingår i din Microsoft Azure-prenumeration och teknisk support tillhandahålls via en av [Azure-supportplanerna](https://azure.microsoft.com/support/plans/).
