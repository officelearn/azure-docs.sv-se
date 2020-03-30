---
title: Förstå och lösa WebHCat-fel på HDInsight - Azure
description: Läs om vanliga fel som returneras av WebHCat på HDInsight och hur du löser dem.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/01/2020
ms.openlocfilehash: 011ef4f192bbae12be7d2464d5b0526f584821a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638858"
---
# <a name="understand-and-resolve-errors-received-from-webhcat-on-hdinsight"></a>Förstå och lösa fel som tas emot från WebHCat på HDInsight

Lär dig mer om fel som tas emot när du använder WebHCat med HDInsight och hur du löser dem. WebHCat används internt av klientverktyg som Azure PowerShell och Data Lake Tools för Visual Studio.

## <a name="what-is-webhcat"></a>Vad är WebHCat

[WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) är ett REST API för [HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog), en tabell och lagringshanteringslager för Apache Hadoop. WebHCat är aktiverat som standard i HDInsight-kluster och används av olika verktyg för att skicka jobb, få jobbstatus och så vidare, utan att logga in på klustret.

## <a name="modifying-configuration"></a>Ändra konfiguration

Flera av felen i det här dokumentet uppstår eftersom ett konfigurerat maximum har överskridits. När upplösningssteget nämner att du kan ändra ett värde använder du Apache Ambari (webb- eller REST API) för att ändra värdet. Mer information finns i [Hantera HDInsight med Apache Ambari](hdinsight-hadoop-manage-ambari.md)

### <a name="default-configuration"></a>Standardkonfiguration

Om följande standardvärden överskrids kan det försämra WebHCat-prestanda eller orsaka fel:

| Inställning | Vad läget gör | Standardvärde |
| --- | --- | --- |
| [yarn.scheduler.capacity.maximum-applications][maximum-applications] |Det maximala antalet jobb som kan vara aktiva samtidigt (väntande eller körs) |10 000 |
| [templeton.exec.max-procs][max-procs] |Det maximala antalet begäranden som kan delges samtidigt |20 |
| [mapreduce.jobhistory.max-age-ms][max-age-ms] |Antalet dagar som jobbhistoriken behålls |7 dagar |

## <a name="too-many-requests"></a>För många förfrågningar

**HTTP-statuskod**: 429

| Orsak | Lösning |
| --- | --- |
| Du har överskridit de maximala samtidiga begäranden som betjänas av WebHCat per minut (standard 20) |Minska arbetsbelastningen för att säkerställa att du inte skickar in mer än det maximala antalet samtidiga `templeton.exec.max-procs`begäranden eller ökar gränsen för samtidig begäran genom att ändra . Mer information finns i [Ändra konfiguration](#modifying-configuration) |

## <a name="server-unavailable"></a>Servern är inte tillgänglig

**HTTP-statuskod**: 503

| Orsak | Lösning |
| --- | --- |
| Den här statuskoden inträffar vanligtvis under redundans mellan den primära och sekundära HeadNode för klustret |Vänta två minuter och försök sedan igen |

## <a name="bad-request-content-could-not-find-job"></a>Fel begärandeinnehåll: Det gick inte att hitta jobb

**HTTP-statuskod**: 400

| Orsak | Lösning |
| --- | --- |
| Jobbdetaljer har städats upp av jobbhistorikrengöringsaren |Standardkvarhållningsperioden för jobbhistorik är 7 dagar. Standardkvarhållningsperioden kan ändras `mapreduce.jobhistory.max-age-ms`genom att ändra . Mer information finns i [Ändra konfiguration](#modifying-configuration) |
| Jobbet har dödats på grund av en redundans |Försök med jobbering igen i upp till två minuter |
| Ett ogiltigt jobb-ID användes |Kontrollera om jobb-ID:et är korrekt |

## <a name="bad-gateway"></a>Felaktig gateway

**HTTP-statuskod**: 502

| Orsak | Lösning |
| --- | --- |
| Intern skräpinsamling sker i WebHCat-processen |Vänta tills skräpinsamlingen är klar eller starta om WebHCat-tjänsten |
| Time out väntar på ett svar från ResourceManager-tjänsten. Det här felet kan uppstå när antalet aktiva program går till det konfigurerade maxvärdet (standard 10 000) |Vänta tills jobb körs för att slutföra eller öka `yarn.scheduler.capacity.maximum-applications`den samtidiga jobbgränsen genom att ändra . Mer information finns i avsnittet [Ändra konfiguration.](#modifying-configuration) |
| Försöker hämta alla jobb via GET `Fields` [/jobs-anropet](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference+Jobs) medan den är inställd på`*` |Hämta inte *alla* jobbuppgifter. Använd `jobid` i stället för att hämta information för jobb som bara är större än vissa jobb-ID. Eller använd inte`Fields` |
| WebHCat-tjänsten är nere under HeadNode-redundans |Vänta i två minuter och försök igen |
| Det finns mer än 500 väntande jobb som skickas via WebHCat |Vänta tills för närvarande väntande jobb har slutförts innan fler jobb skickas |

[maximum-applications]: https://docs.cloudera.com/HDPDocuments/HDP2/HDP-2.1.3/bk_system-admin-guide/content/setting_application_limits.html
[max-procs]: https://cwiki.apache.org/confluence/display/Hive/WebHCat+Configure#WebHCatConfigure-WebHCatConfiguration
[max-age-ms]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.6.0/ds_Hadoop/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml
