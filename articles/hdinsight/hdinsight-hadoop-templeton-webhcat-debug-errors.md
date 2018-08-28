---
title: Förstå och lösa fel med WebHCat på HDInsight - Azure
description: Lär dig hur på cirka vanliga fel som returnerats av WebHCat på HDInsight och hur du löser dem.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: jasonh
ms.openlocfilehash: 3c4ce93a12d0ecb86552e9f73d83f623e71385a0
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43103413"
---
# <a name="understand-and-resolve-errors-received-from-webhcat-on-hdinsight"></a>Förstå och lösa fel togs emot från WebHCat på HDInsight

Läs mer om felmeddelanden när du använder WebHCat med HDInsight och hur du löser dem. WebHCat används internt av klientsidan verktyg som Azure PowerShell och Data Lake Tools för Visual Studio.

## <a name="what-is-webhcat"></a>Vad är WebHCat

[WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) är ett REST-API för [HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog), tabell- och storage lagringshanteringslager för Hadoop. WebHCat är aktiverat som standard på HDInsight-kluster och används av olika verktyg och skicka jobb, hämta jobbstatus och så vidare utan att logga in i klustret.

## <a name="modifying-configuration"></a>Ändra konfigurationen

> [!IMPORTANT]
> Flera av de fel som anges i det här dokumentet beror på att en konfigurerade maxantalet har överskridits. När steget upplösning nämner att du kan ändra ett värde, måste du använda något av följande för att utföra ändringen:

* För **Windows** kluster: använda en skriptåtgärd för att konfigurera värdet när klustret skapas. Mer information finns i [utveckla skriptåtgärder](hdinsight-hadoop-script-actions.md).

* För **Linux** kluster: Använd Ambari (webb- eller REST API) för att ändra värdet. Mer information finns i [hantera HDInsight med Ambari](hdinsight-hadoop-manage-ambari.md)

> [!IMPORTANT]
> Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

### <a name="default-configuration"></a>Standardkonfiguration

Om följande standardvärden överskrids, kan som försämra WebHCat prestanda eller orsaka fel:

| Inställning | Vad läget gör | Standardvärde |
| --- | --- | --- |
| [yarn.Scheduler.Capacity.maximum-program][maximum-applications] |Det maximala antalet jobb som kan vara aktiva samtidigt (väntande eller körs) |10 000 |
| [templeton.Exec.Max-procs][max-procs] |Det maximala antalet förfrågningar som hanteras samtidigt |20 |
| [mapreduce.jobhistory.Max ålder ms][max-age-ms] |Hur många dagar som jobbhistorik bevaras |7 dagar |

## <a name="too-many-requests"></a>För många förfrågningar

**HTTP-statuskod**: 429

| Orsak | Lösning |
| --- | --- |
| Du har överskridit de maximalt antal samtidiga förfrågningar som hanteras av WebHCat per minut (standard 20) |Minska arbetsbelastningen och se till att du inte skickar mer än det maximala antalet samtidiga begäranden eller öka gränsen för samtidiga förfrågningar genom att ändra `templeton.exec.max-procs`. Mer information finns i [ändra konfiguration](#modifying-configuration) |

## <a name="server-unavailable"></a>Servern är inte tillgänglig

**HTTP-statuskod**: 503

| Orsak | Lösning |
| --- | --- |
| Den här statuskoden sker vanligtvis under en redundansväxling mellan primära och sekundära huvudnoden för klustret |Vänta två minuter och försök igen |

## <a name="bad-request-content-could-not-find-job"></a>Felaktig begäran innehåll: Det gick inte att hitta jobbet

**HTTP-statuskod**: 400

| Orsak | Lösning |
| --- | --- |
| Information om återställningsjobb har rensats av jobbhistoriken ett rengöringsband |Standardkvarhållningsperioden för jobbets historik är 7 dagar. Loggperioden kan ändras genom att ändra `mapreduce.jobhistory.max-age-ms`. Mer information finns i [ändra konfiguration](#modifying-configuration) |
| Jobbet har avslutats på grund av en redundansväxling |Försök jobböverföring i upp till två minuter |
| Ett ogiltigt jobb-id användes |Kontrollera om jobb-id är korrekt |

## <a name="bad-gateway"></a>Felaktig gateway

**HTTP-statuskod**: 502

| Orsak | Lösning |
| --- | --- |
| Intern skräpinsamling inträffar i processen WebHCat |Vänta tills skräpinsamling slutförts eller starta om tjänsten WebHCat |
| Timeout för väntan på svar från ResourceManager-tjänsten. Det här felet kan inträffa när antalet aktiva program blir det konfigurerade maxantalet (standard 10 000) |Vänta tills pågående jobb för att slutföra eller öka gränsen för antal samtidiga jobb genom att ändra `yarn.scheduler.capacity.maximum-applications`. Mer information finns i den [ändra configuration](#modifying-configuration) avsnittet. |
| Försök att hämta alla jobb via den [GET /jobs](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference+Jobs) anrop när `Fields` är inställd på `*` |Inte hämtar *alla* Jobbdetaljer. I stället använda `jobid` att hämta information för jobb som bara är större än vissa jobb-id. Eller Använd inte `Fields` |
| WebHCat-tjänsten har stoppats under huvudnoden redundans |Vänta två minuter och försök igen |
| Det finns fler än 500 väntande jobb som skickas via WebHCat |Vänta tills väntar jobben har slutförts innan du skickar in fler jobb |

[maximum-applications]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.3/bk_system-admin-guide/content/setting_application_limits.html
[max-procs]: https://cwiki.apache.org/confluence/display/Hive/WebHCat+Configure#WebHCatConfigure-WebHCatConfiguration
[max-age-ms]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.6.0/ds_Hadoop/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml
