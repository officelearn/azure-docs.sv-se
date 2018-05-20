---
title: Förstå och lösa WebHCat fel på HDInsight - Azure | Microsoft Docs
description: Lär dig hur på cirka vanliga fel som returneras av WebHCat i HDInsight och hur du löser dem.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 1b3d94b1-207d-4550-aece-21dc45485549
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: larryfr
ms.openlocfilehash: 6370bbcda2312f907f442799e6183dd6d1a9dd50
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="understand-and-resolve-errors-received-from-webhcat-on-hdinsight"></a>Förstå och åtgärda fel togs emot från WebHCat på HDInsight

Läs mer om felmeddelanden när du använder WebHCat med HDInsight och hur du löser dem. WebHCat används internt av klientsidan verktyg som Azure PowerShell och Data Lake-verktyg för Visual Studio.

## <a name="what-is-webhcat"></a>Vad är WebHCat

[WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) är en REST-API för [HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog)en tabell och lagring lagringshanteringslager för Hadoop. WebHCat är aktiverat som standard i HDInsight-kluster och används av olika verktyg för att skicka jobb, hämta jobbstatus osv utan att logga in på klustret.

## <a name="modifying-configuration"></a>Ändra konfigurationen

> [!IMPORTANT]
> Flera av de fel som anges i det här dokumentet inträffa konfigurerade maximalt har överskridits. När steget upplösning nämns att du kan ändra ett värde, måste du använda något av följande för att utföra ändringen:

* För **Windows** kluster: Använd en skriptåtgärd för att konfigurera värdet när klustret skapas. Mer information finns i [utveckla skriptåtgärder](hdinsight-hadoop-script-actions.md).

* För **Linux** kluster: Använd Ambari (web eller REST API) för att ändra värdet. Mer information finns i [hantera HDInsight med Ambari](hdinsight-hadoop-manage-ambari.md)

> [!IMPORTANT]
> Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

### <a name="default-configuration"></a>Standardkonfigurationen

Om följande standardvärden överskrids kan försämra WebHCat prestanda eller orsaka fel:

| Inställning | Vad läget gör | Standardvärde |
| --- | --- | --- |
| [yarn.Scheduler.Capacity.maximum-program][maximum-applications] |Det maximala antalet jobb som kan vara aktiva samtidigt (väntande eller körs) |10 000 |
| [templeton.Exec.Max procs][max-procs] |Det maximala antalet förfrågningar som hanteras samtidigt |20 |
| [mapreduce.jobhistory.Max-ålder-ms][max-age-ms] |Antalet dagar som jobbhistorik bevaras |7 dagar |

## <a name="too-many-requests"></a>För många förfrågningar

**HTTP-statuskod**: 429

| Orsak | Lösning |
| --- | --- |
| Du har överskridit de maximala samtidiga begäranden som betjänats med WebHCat per minut (standard är 20) |Minska din arbetsbelastning så att du inte skickar mer än det maximala antalet samtidiga begäranden eller öka antal samtidiga begäranden genom att ändra `templeton.exec.max-procs`. Mer information finns i [ändra konfiguration](#modifying-configuration) |

## <a name="server-unavailable"></a>Servern är inte tillgänglig

**HTTP-statuskod**: 503

| Orsak | Lösning |
| --- | --- |
| Statuskoden genereras vanligtvis under växling mellan den primära och sekundära HeadNode för klustret |Vänta två minuter och försök igen |

## <a name="bad-request-content-could-not-find-job"></a>Felaktig begäran innehåll: Det gick inte att hitta jobbet

**HTTP-statuskod**: 400

| Orsak | Lösning |
| --- | --- |
| Jobbinformation har rensats av jobbhistoriken rengöringsband |Loggperioden för jobbhistorik är 7 dagar. Loggperioden kan ändras genom att ändra `mapreduce.jobhistory.max-age-ms`. Mer information finns i [ändra konfiguration](#modifying-configuration) |
| Jobbet har avslutats på grund av en växling vid fel |Försök jobbet i upp till två minuter |
| Ett ogiltigt jobb-id har använts |Kontrollera om jobb-id är korrekt |

## <a name="bad-gateway"></a>Ogiltig gateway

**HTTP-statuskod**: 502

| Orsak | Lösning |
| --- | --- |
| Internt skräpinsamling sker i WebHCat-processen |Vänta tills skräpinsamling avsluta eller starta om tjänsten WebHCat |
| Tidsgränsen nåddes för väntar på svar från ResourceManager-tjänsten. Det här felet kan inträffa om antalet aktiva program går den konfigurerade maximalt (standard 10 000-tal) |Vänta tills pågående jobb för att slutföra eller öka gränsen för antal samtidiga jobb genom att ändra `yarn.scheduler.capacity.maximum-applications`. Mer information finns i [ändra configuration](#modifying-configuration) avsnitt. |
| Försök att hämta alla jobb via den [GET /jobs](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference+Jobs) anrop när `Fields` har angetts till `*` |Inte hämtar *alla* Jobbdetaljer. I stället använda `jobid` att hämta information om jobb som endast är större än vissa jobb-id. Eller Använd inte `Fields` |
| WebHCat-tjänsten har stoppats under HeadNode växling vid fel |Vänta i två minuter och försök igen |
| Det finns mer än 500 väntande jobb skicka via WebHCat |Vänta tills väntar jobben har slutförts innan du skickar flera jobb |

[maximum-applications]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.3/bk_system-admin-guide/content/setting_application_limits.html
[max-procs]: https://hive.apache.org/javadocs/hcat-r0.5.0/configuration.html
[max-age-ms]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.6.0/ds_Hadoop/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml
