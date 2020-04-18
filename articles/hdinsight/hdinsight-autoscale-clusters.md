---
title: Skala Azure HDInsight-kluster automatiskt
description: Använda azure HDInsight Autoscale-funktionen för att automatiskt rensa Kluster för Apache Hadoop-skala
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/07/2020
ms.openlocfilehash: 4f9b43b6f800bb47942ccc00fee0fac4536d2ec0
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2020
ms.locfileid: "81640583"
---
# <a name="automatically-scale-azure-hdinsight-clusters"></a>Skala Azure HDInsight-kluster automatiskt

> [!Important]
> Azure HDInsight Autoscale-funktionen släpptes för allmän tillgänglighet den 7 november 2019 för Spark- och Hadoop-kluster och inkluderade förbättringar som inte är tillgängliga i förhandsversionen av funktionen. Om du skapade ett Spark-kluster före den 7 november 2019 och vill använda funktionen Automatisk skalning i klustret är den rekommenderade sökvägen att skapa ett nytt kluster och aktivera Automatisk skalning i det nya klustret.
>
> Automatisk skalning för LLAP-kluster (Interactive Query) och HBase är fortfarande i förhandsgranskning. Automatisk skalning är endast tillgängligt på Spark-, Hadoop-, Interactive Query- och HBase-kluster.

Azure HDInsights kluster-funktion för automatisk skalning skalar automatiskt antalet arbetsnoder i ett kluster upp och ned. Andra typer av noder i klustret kan inte skalas för närvarande.  När ett nytt HDInsight-kluster skapas kan ett minsta och högsta antal arbetsnoder anges. Automatisk skalning övervakar sedan resurskraven för analysbelastningen och skalar antalet arbetsnoder uppåt eller nedåt. Det finns ingen extra kostnad för den här funktionen.

## <a name="cluster-compatibility"></a>Klusterkompatibilitet

I följande tabell beskrivs de klustertyper och versioner som är kompatibla med funktionen Automatisk skalning.

| Version | Spark | Hive | LLAP (PÅ ANDRA) | HBase | Kafka | Storm | ML |
|---|---|---|---|---|---|---|---|
| HDInsight 3,6 utan ESP | Ja | Ja | Ja | Ja* | Inga | Inga | Inga |
| HDInsight 4.0 utan ESP | Ja | Ja | Ja | Ja* | Inga | Inga | Inga |
| HDInsight 3.6 med ESP | Ja | Ja | Ja | Ja* | Inga | Inga | Inga |
| HDInsight 4.0 med ESP | Ja | Ja | Ja | Ja* | Inga | Inga | Inga |

\*HBase-kluster kan bara konfigureras för schemabaserad skalning, inte belastningsbaserad.

## <a name="how-it-works"></a>Hur det fungerar

Du kan välja belastningsbaserad skalning eller schemabaserad skalning för ditt HDInsight-kluster. Inläsningsbaserad skalning ändrar antalet noder i klustret, inom ett intervall som du anger, för att säkerställa optimal CPU-användning och minimera driftskostnaderna.

Schemabaserad skalning ändrar antalet noder i klustret baserat på villkor som börjar gälla vid specifika tidpunkter. Dessa villkor skalar klustret till ett avsett antal noder.

### <a name="metrics-monitoring"></a>Övervakning av mätvärden

Automatisk skalning övervakar klustret kontinuerligt och samlar in följande mått:

|Mått|Beskrivning|
|---|---|
|Totalt väntande processor|Det totala antalet kärnor som krävs för att starta körningen av alla väntande behållare.|
|Totalt väntande minne|Det totala minne (i MB) som krävs för att starta körningen av alla väntande behållare.|
|Totalt gratis CPU|Summan av alla oanvända kärnor på de aktiva arbetsnoderna.|
|Totalt ledigt minne|Summan av oanvänt minne (i MB) på de aktiva arbetsnoderna.|
|Använt minne per nod|Belastningen på en arbetsnod. En arbetsnod där 10 GB minne används, anses vara under mer belastning än en arbetare med 2 GB använt minne.|
|Antal programbakgrunder per nod|Antalet APPLICATION Master-behållare (AM) som körs på en arbetsnod. En arbetarnod som är värd för två AM-behållare anses viktigare än en arbetarnod som är värd för noll AM-behållare.|

Ovanstående mått kontrolleras var 60:e sekund. Automatisk skalning fattar beslut baserat på dessa mått.

### <a name="load-based-scale-conditions"></a>Belastningsbaserade skalningsförhållanden

När följande villkor upptäcks utfärdar automatisk skalning en skalningsbegäran:

|Skala upp|Skala nedåt|
|---|---|
|Totalt väntande CPU är större än total gratis CPU i mer än 3 minuter.|Totalt väntande CPU är mindre än totalt gratis CPU i mer än 10 minuter.|
|Totalt väntande minne är större än totalt ledigt minne i mer än 3 minuter.|Totalt väntande minne är mindre än totalt ledigt minne i mer än 10 minuter.|

För uppskalning utfärdar automatisk skalning en uppskalningsbegäran för att lägga till det antal noder som krävs. Uppskalningen baseras på hur många nya arbetsnoder som behövs för att uppfylla de aktuella cpu- och minneskraven.

För nedskalning utfärdar automatisk skalning en begäran om att ta bort ett visst antal noder. Nedskalningen baseras på antalet AM-behållare per nod. Och de nuvarande CPU- och minneskraven. Tjänsten identifierar också vilka noder som är kandidater för borttagning baserat på aktuell jobbkörning. Nedskalningsåtgärden inaktiverar först noderna och tar sedan bort dem från klustret.

## <a name="get-started"></a>Kom igång

### <a name="create-a-cluster-with-load-based-autoscaling"></a>Skapa ett kluster med belastningsbaserad automatisk skalning

Så här aktiverar du funktionen Automatisk skalning med belastningsbaserad skalning genom att utföra följande steg som en del av den normala processen för att skapa kluster:

1. Markera kryssrutan **Aktivera automatisk skalning** på fliken **Konfiguration + prissättning.**
1. Välj **Läsinbaserad** under **Typ För automatisk skalning**.
1. Ange de avsedda värdena för följande egenskaper:  

    * Initialt **antal noder** för **arbetarnod**.
    * **Min** antal arbetsnoder.
    * **Max** antal arbetsnoder.

    ![Aktivera inläsningsbaserad automatisk skalning av arbetsnod](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-autoscale.png)

Det ursprungliga antalet arbetsnoder måste minska mellan minimum och högsta, inklusive. Det här värdet definierar den ursprungliga storleken på klustret när det skapas. Det minsta antalet arbetsnoder bör anges till tre eller fler. Skalning av klustret till färre än tre noder kan leda till att det fastnar i felsäkert läge på grund av otillräcklig filreplikering.  Mer information finns [i Fastna i felsäkert läge](./hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode).

### <a name="create-a-cluster-with-schedule-based-autoscaling"></a>Skapa ett kluster med schemabaserad automatisk skalning

Så här aktiverar du funktionen Automatisk skalning med schemabaserad skalning genom att utföra följande steg som en del av den normala processen för att skapa kluster:

1. Kontrollera kryssrutan **Aktivera automatisk skalning** på fliken **Konfiguration + prissättning.**
1. Ange **noderna Antal** för **arbetarnoden**, som styr gränsen för att skala upp klustret.
1. Välj alternativet **Schemabaserad** under **Typ För automatisk skalning**.
1. Välj **Konfigurera** om du vill öppna **konfigurationsfönstret för automatisk skalning.**
1. Markera tidszonen och klicka sedan på **+ Lägg till villkor**
1. Välj de veckodagar som det nya villkoret ska gälla för.
1. Redigera den tid som villkoret ska börja gälla och antalet noder som klustret ska skalas till.
1. Lägg till fler villkor om det behövs.

    ![Aktivera schemabaserad skapande av arbetsnoder](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-schedule-creation.png)

Antalet noder måste vara mellan 3 och det maximala antalet arbetsnoder som du angav innan du lägger till villkor.

### <a name="final-creation-steps"></a>Steg för att skapa det slutliga

Välj den virtuella datorn-typen för arbetsnoder genom att välja en virtuell dator i listrutan under **Nodstorlek**. När du har valt den virtuella datorn-typen för varje nodtyp kan du se det uppskattade kostnadsintervallet för hela klustret. Justera vm-typerna så att de passar din budget.

![Aktivera nodsschemabaserad nodstorlek för arbetsnod](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-vmsize.png)

Din prenumeration har en kapacitetskvot för varje region. Det totala antalet kärnor i huvudet noder och den maximala arbetarnoder kan inte överstiga kapacitetskvoten. Denna kvot är dock en mjuk gräns. Du kan alltid skapa en supportbiljett för att få den ökad lätt.

> [!Note]  
> Om du överskrider den totala kärnkvotgränsen visas ett felmeddelande om att "den maximala noden har överskridit de tillgängliga kärnorna i den här regionen, välj en annan region eller kontakta supporten för att öka kvoten".

Mer information om hur du skapar HDInsight-kluster med Azure-portalen finns [i Skapa Linux-baserade kluster i HDInsight med Azure-portalen](hdinsight-hadoop-create-linux-clusters-portal.md).  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>Skapa ett kluster med en Resource Manager-mall

#### <a name="load-based-autoscaling"></a>Uppläsningsbaserad automatisk skalning

Du kan skapa ett HDInsight-kluster med inläsningsbaserad automatisk skalning `autoscale` av en `computeProfile`  >  `workernode` Azure Resource `minInstanceCount` `maxInstanceCount` Manager-mall genom att lägga till en nod i avsnittet med egenskaperna och som visas i json-kodavsnittet nedan.

```json
{
  "name": "workernode",
  "targetInstanceCount": 4,
  "autoscale": {
      "capacity": {
          "minInstanceCount": 3,
          "maxInstanceCount": 10
      }
  },
  "hardwareProfile": {
      "vmSize": "Standard_D13_V2"
  },
  "osProfile": {
      "linuxOperatingSystemProfile": {
          "username": "[parameters('sshUserName')]",
          "password": "[parameters('sshPassword')]"
      }
  },
  "virtualNetworkProfile": null,
  "scriptActions": []
}
```

#### <a name="schedule-based-autoscaling"></a>Schemabaserad automatisk skalning

Du kan skapa ett HDInsight-kluster med schemabaserad automatisk skalning av `autoscale` en Azure `computeProfile`  >  `workernode` Resource Manager-mall genom att lägga till en nod i avsnittet. Noden `autoscale` innehåller en `recurrence` som `timezone` har `schedule` en och som beskriver när ändringen ska äga rum.

```json
{
  "autoscale": {
    "recurrence": {
      "timeZone": "Pacific Standard Time",
      "schedule": [
        {
          "days": [
            "Monday",
            "Tuesday",
            "Wednesday",
            "Thursday",
            "Friday"
          ],
          "timeAndCapacity": {
            "time": "11:00",
            "minInstanceCount": 10,
            "maxInstanceCount": 10
          }
        },
      ]
    }
  },
  "name": "workernode",
  "targetInstanceCount": 4,
}
```

### <a name="enable-and-disable-autoscale-for-a-running-cluster"></a>Aktivera och inaktivera Automatisk skalning för ett kluster som körs

#### <a name="using-the-azure-portal"></a>Använda Azure Portal

Om du vill aktivera Automatisk skalning i ett kluster som körs väljer du **Klusterstorlek** under **Inställningar**. Välj sedan **Aktivera automatisk skalning**. Välj den typ av automatisk skalning som du vill använda och ange alternativen för belastningsbaserad eller schemabaserad skalning. Slutligen väljer du **Spara**.

![Aktivera arbetsnodens schemabaserade kluster som körs automatiskt](./media/hdinsight-autoscale-clusters/azure-portal-settings-autoscale.png)

#### <a name="using-the-rest-api"></a>Använda REST API

Om du vill aktivera eller inaktivera Automatisk skalning i ett kluster som körs med REST API gör du en POST-begäran till slutpunkten För automatisk skalning:

```
https://management.azure.com/subscriptions/{subscription Id}/resourceGroups/{resourceGroup Name}/providers/Microsoft.HDInsight/clusters/{CLUSTERNAME}/roles/workernode/autoscale?api-version=2018-06-01-preview
```

Använd lämpliga parametrar i begäran nyttolast. Den json nyttolast nedan kan användas för att aktivera Automatisk skalning. Använd nyttolasten `{autoscale: null}` för att inaktivera Automatisk skalning.

```json
{ autoscale: { capacity: { minInstanceCount: 3, maxInstanceCount: 2 } } }
```

Se föregående avsnitt om [hur du aktiverar belastningsbaserad automatisk skalning](#load-based-autoscaling) för en fullständig beskrivning av alla nyttolastparametrar.

## <a name="guidelines"></a>Riktlinjer

### <a name="choosing-load-based-or-schedule-based-scaling"></a>Välja belastningsbaserad eller schemabaserad skalning

Tänk på följande faktorer innan du fattar ett beslut om vilket läge du ska välja:

* Aktivera Automatisk skalning när klustret skapas.
* Det minsta antalet noder bör vara minst tre.
* Belastningsvarians: följer belastningen på klustret ett konsekvent mönster vid specifika tidpunkter, på specifika dagar. Om inte, är belastningsbaserad schemaläggning ett bättre alternativ.
* SLA-krav: Skalning av automatisk skalning är reaktiv i stället för prediktiv. Kommer det att finnas en tillräcklig fördröjning mellan när belastningen börjar öka och när klustret måste vara på sin målstorlek? Om det finns strikta SLA-krav och belastningen är ett fast känt mönster är "schemabaserad" ett bättre alternativ.

### <a name="consider-the-latency-of-scale-up-or-scale-down-operations"></a>Tänk på svarstiden för skala upp eller skala ner operationer

Det kan ta 10 till 20 minuter innan en skalningsåtgärd har slutförts. Planera för den här fördröjningen när du ställer in ett anpassat schema. Om du till exempel behöver klusterstorleken 20 vid 09:00, anger du schemautlösaren till en tidigare tid, till exempel 08:30 så att skalningsåtgärden har slutförts klockan 9:00.

### <a name="preparation-for-scaling-down"></a>Förberedelser för nedskalning

Under klusterskalningsprocessen inaktiverar Automatisk skalning noderna för att uppfylla målstorleken. Om aktiviteter körs på dessa noder väntar automatisk skalning tills aktiviteterna har slutförts. Eftersom varje arbetsnod också fungerar som en roll i HDFS flyttas temp-data till de återstående noderna. Så du bör se till att det finns tillräckligt med utrymme på de återstående noderna för att vara värd för alla temp-data.

De löpande jobben fortsätter. De väntande jobben väntar på schemaläggning med färre tillgängliga arbetsnoder.

### <a name="minimum-cluster-size"></a>Minsta klusterstorlek

Skala inte klustret till färre än tre noder. Skalning av klustret till färre än tre noder kan leda till att det fastnar i felsäkert läge på grund av otillräcklig filreplikering.  Mer information finns [i Fastna i felsäkert läge](./hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode).

## <a name="monitoring"></a>Övervakning

### <a name="cluster-status"></a>Klusterstatus

Klusterstatusen i Azure-portalen kan hjälpa dig att övervaka aktiviteter för automatisk skalning.

![Aktivera klusterstatus för arbetsnodinläsningsbaserad klusterstatus för automatisk skalning](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-cluster-status.png)

Alla klusterstatusmeddelanden som du kan se förklaras i listan nedan.

| Klusterstatus | Beskrivning |
|---|---|
| Körs | Klustret fungerar normalt. Alla tidigare aktiviteter för automatisk skalning har slutförts. |
| Uppdatera  | Konfigurationen för automatisk skalning av klustret uppdateras.  |
| HDInsight-konfiguration  | En klusterskala upp eller skala ned-åtgärd pågår.  |
| Uppdateringsfel  | HDInsight träffade problem under konfigurationsuppdateringen för automatisk skalning. Kunder kan välja att antingen försöka uppdatera igen eller inaktivera automatisk skalning.  |
| Fel  | Något är fel med klustret, och det är inte användbart. Ta bort det här klustret och skapa ett nytt.  |

Om du vill visa det aktuella antalet noder i klustret går du till **klusterstorleksdiagrammet** på **sidan Översikt** för klustret. Eller välj **Klusterstorlek** under **Inställningar**.

### <a name="operation-history"></a>Drifthistorik

Du kan visa klusterskalnings- och skalningshistoriken som en del av klustermåtten. Du kan också visa alla skalningsåtgärder under den senaste dagen, veckan eller annan tidsperiod.

Välj **Mått** under **Övervakning**. Välj sedan **Lägg till mått** och Antal aktiva **arbetare** i listrutan **Mått.** Välj knappen längst upp till höger om du vill ändra tidsintervallet.

![Aktivera mått för schemabaserad automatisk skalning av arbetarenod](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-chart-metric.png)

## <a name="next-steps"></a>Nästa steg

Läs om riktlinjer för skalning av kluster manuellt i [riktlinjer för skalning](hdinsight-scaling-best-practices.md)
