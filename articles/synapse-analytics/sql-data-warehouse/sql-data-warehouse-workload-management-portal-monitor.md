---
title: Övervakning av portalen för arbetsbelastningshantering
description: Vägledning för övervakning av arbetsbelastningshanteringsportaler i Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: b1f21a996f7394def4d6b1e8bde9a5ccdf703dbb
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632415"
---
# <a name="azure-synapse-analytics--workload-management-portal-monitoring-preview"></a>Azure Synapse Analytics – Övervakning av arbetsbelastningshanteringsportalen (förhandsversion)

I den här artikeln beskrivs hur du övervakar resursanvändning och frågeaktivitet för [arbetsbelastningsgrupper.](sql-data-warehouse-workload-isolation.md#workload-groups)
Mer information om hur du konfigurerar Utforskaren för Azure Metrics finns i artikeln [Komma igång med Azure Metrics Explorer.](../../azure-monitor/platform/metrics-getting-started.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)  Mer information om hur du övervakar systemresursförbrukning finns i avsnittet [Resursanvändning](sql-data-warehouse-concept-resource-utilization-query-activity.md#resource-utilization) i Azure Synapse Analytics Monitoring.
Det finns två olika kategorier av arbetsbelastningsgruppmått som tillhandahålls för övervakning av arbetsbelastningshantering: resursallokering och frågeaktivitet.  Dessa mått kan delas och filtreras efter arbetsbelastningsgrupp.  Måtten kan delas och filtreras baserat på om de är systemdefinierade (resursklassarbetsbelastningsgrupper) eller användardefinierade (skapas av användare med SEMTAX FÖR [SKAPA ARBETSBELASTNINGSGRUPP).](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)

## <a name="workload-management-metric-definitions"></a>Måttdefinitioner för arbetsbelastningshantering

|Måttnamn                    |Beskrivning  |Sammansättningstyp |
|-------------------------------|-------------|-----------------|
|Effektiv takresurs procent | *Effektiv takresursprocent* är en hård gräns för procentandelen resurser som är tillgängliga för arbetsbelastningsgruppen, med beaktande *av effektiv minresursprocent* som allokerats för andra arbetsbelastningsgrupper. Måttet *Effektiv cap resursprocent* har `CAP_PERCENTAGE_RESOURCE` konfigurerats med hjälp av parametern i syntaxen [SKAPA ARBETSBELASTNINGSGRUPP.](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)  Det effektiva värdet beskrivs här.<br><br>Om till exempel `DataLoads` en arbetsbelastningsgrupp skapas med `CAP_PERCENTAGE_RESOURCE` = 100 och en annan arbetsbelastningsgrupp skapas med en effektiv minresursprocent på 25 %, är resursprocenten För *Effective cap resource percent* `DataLoads` arbetsbelastningen 75 %.<br><br>Den *effektiva cap-resursprocenten* bestämmer den övre gränsen för samtidighet (och därmed potentiellt dataflöde) som en arbetsbelastningsgrupp kan uppnå.  Om ytterligare dataflöde behövs utöver vad som för närvarande rapporteras av `CAP_PERCENTAGE_RESOURCE`måttet `MIN_PERCENTAGE_RESOURCE` Effektiv cap *resource percent,* antingen öka , minska , minska antalet andra arbetsbelastningsgrupper eller skala upp instansen för att lägga till fler resurser.  Minska `REQUEST_MIN_RESOURCE_GRANT_PERCENT` kan öka samtidighet, men kan inte öka det totala genomflödet.| Min, Medel, Max |
|Effektiv minresursprocent |*Effektiv min resursprocent* är den minsta procentandelen resurser som reserverats och isolerats för arbetsbelastningsgruppen med beaktande av minimum på servicenivå.  Måttet Effektiv min resursprocent har `MIN_PERCENTAGE_RESOURCE` konfigurerats med hjälp av parametern i syntaxen [SKAPA ARBETSBELASTNINGSGRUPP.](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)  Det effektiva värdet beskrivs [här](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest#effective-values).<br><br>Använd summaaggregeringstypen när det här måttet är ofiltrerat och osplitt för att övervaka den totala arbetsbelastningsisolering som konfigurerats på systemet.<br><br>Den *effektiva minresursprocenten* bestämmer den nedre gränsen för garanterad samtidighet (och därmed garanterat dataflöde) som en arbetsbelastningsgrupp kan uppnå.  Om ytterligare garanterade resurser behövs utöver vad som för närvarande rapporteras `MIN_PERCENTAGE_RESOURCE` av måttet Effektiv min *resursprocent,* öka parametern som konfigurerats för arbetsbelastningsgruppen.  Minska `REQUEST_MIN_RESOURCE_GRANT_PERCENT` kan öka samtidighet, men kan inte öka det totala genomflödet. |Min, Medel, Max|
|Aktiva frågor för arbetsbelastningsgrupp  |Det här måttet rapporterar aktiva frågor inom arbetsbelastningsgruppen.  Om du använder det här måttet ofiltrerat och ospliterat visas alla aktiva frågor som körs på systemet.|Summa         |
|Arbetsbelastningsgruppallokering efter max resursprocent |Det här måttet visar den procentuella fördelningen av resurser i förhållande till resursprocenten *för arbetsbelastningen.*  Det här måttet ger ett effektivt utnyttjande av arbetsbelastningsgruppen.<br><br>Överväg en `DataLoads` arbetsbelastningsgrupp med en *effektiv cap-resurs procent* på 75 % och en `REQUEST_MIN_RESOURCE_GRANT_PERCENT` konfigurerad med 25 %.  *Arbetsbelastningsgruppsallokeringen efter max resursprocentvärde* filtrerat till `DataLoads` skulle vara 33 % (25 % / 75 %) om en enskild fråga kördes i den här arbetsbelastningsgruppen.<br><br>Använd det här måttet för att identifiera en arbetsbelastningsgrupps användning.  Ett värde nära 100 % anger att alla resurser som är tillgängliga för arbetsbelastningsgruppen används.  Dessutom *köade arbetsbelastningsgruppen efter svar för* samma arbetsbelastningsgrupp som visar ett värde som är större än noll, vilket indikerar att arbetsbelastningsgruppen skulle använda ytterligare resurser om allokeras.  Omvänt, om det här måttet är konsekvent låg och *arbetsbelastningsgruppen aktiva frågor* är låg arbetsbelastningsgruppen inte utnyttjas.  Den här situationen är särskilt problematisk om *effektiv cap resurs procent* är större än noll eftersom det skulle tyda [underutnyttjade arbetsbelastning isolering](#underutilized-workload-isolation).|Min, Medel, Max |
|Arbetsbelastningsgruppallokering efter systemprocent | Det här måttet visar den procentuella fördelningen av resurser i förhållande till hela systemet.<br><br>Överväg en `DataLoads` arbetsbelastningsgrupp med en `REQUEST_MIN_RESOURCE_GRANT_PERCENT` konfigurerad på 25%.  *Arbetsbelastningsgruppallokering per systemprocentvärde* filtrerat till `DataLoads` skulle vara 25% (25% / 100%) om en enskild fråga kördes i den här arbetsbelastningsgruppen.|Min, Medel, Max |
|Timeout för arbetsbelastningsgruppfråga |Frågor för arbetsbelastningsgruppen som har time(out.  Frågetidsutgångar som rapporteras av det här måttet är bara när frågan har börjat köras (den innehåller inte väntetid på grund av låsning eller resursväntar).<br><br>Tidsgränsen för fråga `QUERY_EXECUTION_TIMEOUT_SEC` konfigureras med hjälp av parametern i syntaxen [SKAPA ARBETSBELASTNINGSGRUPP.](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)  Om du ökar värdet kan antalet tidsutgångar för frågor minskas.<br><br>Överväg `REQUEST_MIN_RESOURCE_GRANT_PERCENT` att öka parametern för arbetsbelastningsgruppen för att minska antalet timeout och allokera fler resurser per fråga.  Om du `REQUEST_MIN_RESOURCE_GRANT_PERCENT` ökar minskar samtidigheten för arbetsbelastningsgruppen. |Summa |
|Arbetsbelastningsgrupp köade frågor | Frågor för arbetsbelastningsgruppen som för närvarande står i kö väntar på att starta körningen.  Frågor kan vara kö eftersom de väntar på resurser eller lås.<br><br>Frågor kan vänta av många skäl.  Om systemet är överbelastat och samtidighetsbehovet är större än vad som är tillgängligt, köar frågor.<br><br>Överväg att lägga till mer `CAP_PERCENTAGE_RESOURCE` resurser till arbetsbelastningsgruppen genom att öka parametern i CREATE [WORKLOAD GROUP-satsen.](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)  Om `CAP_PERCENTAGE_RESOURCE` är större än måttet *Effektiv cap resource procent* påverkar den konfigurerade arbetsbelastningsisolering för andra arbetsbelastningsgrupper de resurser som allokerats till den här arbetsbelastningsgruppen.  Överväg `MIN_PERCENTAGE_RESOURCE` att sänka andra arbetsbelastningsgrupper eller skala upp instansen för att lägga till fler resurser. |Summa |

## <a name="monitoring-scenarios-and-actions"></a>Övervaka scenarier och åtgärder

Nedan finns en serie diagramkonfigurationer för att markera arbetsbelastningshanteringsmåttanvändning för felsökning tillsammans med associerade åtgärder för att lösa problemet.

### <a name="underutilized-workload-isolation"></a>Underutnyttjad arbetsbelastningsisolering

Tänk på följande arbetsbelastningsgrupp och klassifierarkonfiguration där en arbetsbelastningsgrupp med `wgPriority` namnet skapas och *TheCEO* `membername` mappas till den med hjälp av arbetsbelastningsklassificeraren. `wcCEOPriority`  Arbetsbelastningsgruppen `wgPriority` har 25 % arbetsbelastningsisolering konfigurerad för den (`MIN_PERCENTAGE_RESOURCE` = 25).  Varje fråga som skickas in av *THECEO* `REQUEST_MIN_RESOURCE_GRANT_PERCENT` ges 5% av systemresurser ( = 5).

```sql
CREATE WORKLOAD GROUP wgPriority
WITH ( MIN_PERCENTAGE_RESOURCE = 25
      ,CAP_PERCENTAGE_RESOURCE = 50
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5);

CREATE WORKLOAD CLASSIFIER wcCEOPriority
WITH ( WORKLOAD_GROUP = 'wgPriority'
      ,MEMBERNAME = 'TheCEO');
```

Nedanstående diagram är konfigurerat enligt följande:<br>
Mått 1: *Effektiv minresursprocent* (genomsnittlig `blue line`aggregering, )<br>
Mått 2: *Arbetsbelastningsgruppallokering efter systemprocent* (genomsnittlig aggregering, `purple line`)<br>
Filter: [Arbetsbelastningsgrupp] =`wgPriority`<br>
![underutnyttjade-wg.png](./media/sql-data-warehouse-workload-management-portal-monitor/underutilized-wg.png) Diagrammet visar att med 25% arbetsbelastning isolering, endast 10% används i genomsnitt.  I det här `MIN_PERCENTAGE_RESOURCE` fallet kan parametervärdet sänkas till mellan 10 eller 15 och tillåta andra arbetsbelastningar på systemet att förbruka resurserna.

### <a name="workload-group-bottleneck"></a>Flaskhals i arbetsbelastningsgruppen

Tänk på följande arbetsbelastningsgrupp och klassifierarkonfiguration där en arbetsbelastningsgrupp med `wgDataAnalyst` namnet skapas och *DataAnalyst* `membername` mappas till den med hjälp av arbetsbelastningsklassificeraren. `wcDataAnalyst`  Arbetsbelastningsgruppen `wgDataAnalyst` har 6 % arbetsbelastningsisolering konfigurerad för den (`MIN_PERCENTAGE_RESOURCE` `CAP_PERCENTAGE_RESOURCE` = 6) och en resursgräns på 9 % ( = 9).  Varje fråga som skickas av *DataAnalyst* får 3 % av systemresurserna (`REQUEST_MIN_RESOURCE_GRANT_PERCENT` = 3).

```sql
CREATE WORKLOAD GROUP wgDataAnalyst  
WITH ( MIN_PERCENTAGE_RESOURCE = 6
      ,CAP_PERCENTAGE_RESOURCE = 9
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 3);

CREATE WORKLOAD CLASSIFIER wcDataAnalyst
WITH ( WORKLOAD_GROUP = 'wgDataAnalyst'
      ,MEMBERNAME = 'DataAnalyst');
```

Nedanstående diagram är konfigurerat enligt följande:<br>
Mått 1: *Effektiv cap resurs procent* (Genomsnittlig aggregering, `blue line`)<br>
Mått 2: *Arbetsbelastningsgruppallokering efter max resursprocent* (genomsnittlig aggregering, `purple line`)<br>
Mått 3: *Arbetsbelastningsgrupp köade frågor* `turquoise line`(Summaaggregering, )<br>
Filter: [Arbetsbelastningsgrupp] =`wgDataAnalyst`<br>
![flaskhalsad wg](./media/sql-data-warehouse-workload-management-portal-monitor/bottle-necked-wg.png) Diagrammet visar att med ett 9% tak för resurser används arbetsbelastningsgruppen 90%+ (från *arbetsbelastningsgruppallokeringen med max resursprocentmått*).  Det finns en stadig kö av frågor som visas från *arbetsbelastningsgruppen köade frågor mått*.  I det här `CAP_PERCENTAGE_RESOURCE` fallet, öka till ett värde som är högre än 9% kommer att tillåta fler frågor att köra samtidigt.  Öka `CAP_PERCENTAGE_RESOURCE` förutsätter att det finns tillräckligt med resurser tillgängliga och inte isolerade av andra arbetsbelastningsgrupper.  Kontrollera att taket ökade genom att kontrollera *måttet Effektiv cap resource percent*.  Om mer dataflöde önskas bör `REQUEST_MIN_RESOURCE_GRANT_PERCENT` du också överväga att öka värdet som är större än 3.  Om `REQUEST_MIN_RESOURCE_GRANT_PERCENT` du ökar kan det göra att frågor kan köras snabbare.

## <a name="next-steps"></a>Nästa steg

- [Snabbstart: Konfigurera arbetsbelastningsisolering med T-SQL](quickstart-configure-workload-isolation-tsql.md)<br>
- [SKAPA ARBETSBELASTNINGSGRUPP (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)<br>
- [SKAPA ARBETSBELASTNINGSKLASSIFICERARE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest)<br>
- [Övervaka resursutnyttjande](sql-data-warehouse-concept-resource-utilization-query-activity.md)
