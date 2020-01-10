---
title: Skala Azure HDInsight-kluster automatiskt
description: Använd funktionen automatisk skalning i Azure HDInsight för att automatiskt Apache Hadoop skalnings kluster
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/22/2019
ms.openlocfilehash: 45804bd3e81e7363010979b7a6e028356b3a5080
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75780070"
---
# <a name="automatically-scale-azure-hdinsight-clusters"></a>Skala Azure HDInsight-kluster automatiskt

> [!Important]
> Funktionen för autoskalning fungerar bara för Spark-, Hive-, LLAP-och HBase-kluster som skapats efter den 8 maj 2019. 

Azure HDInsights kluster funktion för automatisk skalning skalar automatiskt antalet arbetsnoder i ett kluster upp och ned. Andra typer av noder i klustret kan inte skalas för närvarande.  När du skapar ett nytt HDInsight-kluster kan du ange ett minsta och högsta antal arbets noder. Autoskalning övervakar sedan resurs kraven för analys belastningen och skalar antalet arbetsnoder uppåt eller nedåt. Den här funktionen kostar inget extra.

## <a name="cluster-compatibility"></a>Kluster kompatibilitet

I följande tabell beskrivs de kluster typer och versioner som är kompatibla med funktionen för autoskalning.

| Version | Spark | Hive | LLAP | HBase | Kafka | Storm | ML |
|---|---|---|---|---|---|---|---|
| HDInsight 3,6 utan ESP | Ja | Ja | Ja | Ja* | Inga | Inga | Inga |
| HDInsight 4,0 utan ESP | Ja | Ja | Ja | Ja* | Inga | Inga | Inga |
| HDInsight 3,6 med ESP | Ja | Ja | Ja | Ja* | Inga | Inga | Inga |
| HDInsight 4,0 med ESP | Ja | Ja | Ja | Ja* | Inga | Inga | Inga |

\* HBase-kluster kan bara konfigureras för schemabaserade skalning, inte för inläsnings-baserade.

## <a name="how-it-works"></a>Så här fungerar det

Du kan välja inläsnings-baserad skalning eller schema-baserad skalning för ditt HDInsight-kluster. Vid belastnings utjämning ändras antalet noder i klustret, inom ett intervall som du anger, för att säkerställa optimal CPU-användning och minimera löpande kostnad.

Schemabaserade skalningar ändrar antalet noder i klustret baserat på villkor som börjar gälla vid vissa tidpunkter. Dessa villkor skalar klustret till ett önskat antal noder.

### <a name="metrics-monitoring"></a>Mått övervakning

Autoskalning övervakar kontinuerligt klustret och samlar in följande mått:

* **Total väntande processor**: det totala antalet kärnor som krävs för att starta körningen av alla väntande behållare.
* **Totalt väntande minne**: det totala minne (i MB) som krävs för att starta körningen av alla väntande behållare.
* **Total ledig processor**: summan av alla oanvända kärnor på aktiva arbetsnoder.
* **Totalt ledigt minne**: summan av oanvänt minne (i MB) på aktiva arbetsnoder.
* **Använt minne per nod**: belastningen på en arbets nod. En arbetsnoden där 10 GB minne används, anses vara under mer belastning än en arbets tagare med 2 GB använt minne.
* **Antal program huvud per nod**: antalet program huvud behållare som körs på en arbetsnoden. En arbetsnoden som är värd för två AM-behållare anses vara viktigare än en arbetsnoden som är värd för noll AM-behållare.

Ovanstående mått kontrol leras var 60: e sekund. Med autoskalning kan du skala upp och skala ned beslut baserat på dessa mått.

### <a name="load-based-cluster-scale-up"></a>Belastningsutjämnad kluster skalning

När följande villkor upptäcks kommer autoskalning att utfärda en skalnings förfrågan:

* Den totala väntande processorn är större än den totala kostnads fria processorn i mer än 3 minuter.
* Totalt minne som väntar är större än det totala lediga minnet i mer än 3 minuter.

HDInsight-tjänsten beräknar hur många nya arbetsnoder som behövs för att uppfylla de aktuella processor-och minnes kraven, och utfärdar sedan en skalnings förfrågan för att lägga till antalet noder som krävs.

### <a name="load-based-cluster-scale-down"></a>Belastnings Utjämnings kluster, skala ned

När följande villkor upptäcks, kommer autoskalning att utfärda en nedskalning-begäran:

* Den totala väntande processorn är mindre än den totala lediga processorn i mer än 10 minuter.
* Totalt minne som väntar är mindre än det totala lediga minnet i mer än 10 minuter.

Baserat på antalet AM-behållare per nod och de aktuella processor-och minnes kraven, utfärdar autoskalning en begäran om att ta bort ett visst antal noder. Tjänsten identifierar också vilka noder som ska tas bort, baserat på den aktuella jobb körningen. Åtgärden för att skala ned inaktiverar först noderna och tar sedan bort dem från klustret.

## <a name="get-started"></a>Kom i gång

### <a name="create-a-cluster-with-load-based-autoscaling"></a>Skapa ett kluster med load-based autoskalning

Om du vill använda autoskalning i ett kluster måste alternativet **Aktivera autoskalning** vara aktiverat när klustret skapas. 

Om du vill aktivera funktionen för automatisk skalning med belastningsutjämnad skalning utför du följande steg som en del av processen för att skapa ett vanligt kluster:

1. Markera kryss rutan **Aktivera autoskalning** på fliken **konfiguration + prissättning** .
1. Välj **load-based** under **typ av autoskalning**.
1. Ange önskade värden för följande egenskaper:  

    * Initialt **antal noder** för **arbetsnoden**.
    * **Minsta** antal arbetsnoder.
    * **Högsta** antal arbetsnoder.

    ![Aktivera inläsnings-baserad autoskalning för arbetsnoden](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-autoscale.png)

Det första antalet arbetsnoder måste ligga mellan minimum och maximum, inklusive. Det här värdet definierar den ursprungliga storleken på klustret när den skapas. Det minsta antalet arbetsnoder måste anges till tre eller fler. . Om du skalar klustret till färre än tre noder kan det bli fastnat i fel säkert läge på grund av otillräcklig filreplikering. Mer information finns i [få fastna i fel säkert läge]( https://docs.microsoft.com/ azure/hdinsight/hdinsight-scaling-best-practices#getting-stuck-in-safe-mode) .

### <a name="create-a-cluster-with-schedule-based-autoscaling"></a>Skapa ett kluster med hjälp av schemabaserade autoskalning

Om du vill aktivera funktionen för automatisk skalning med schemabaserade skalning utför du följande steg som en del av processen för att skapa ett vanligt kluster:

1. Markera kryss rutan **Aktivera autoskalning** på fliken **konfiguration + prissättning** .
1. Ange **antalet noder** för **arbetsnoden**, som styr gränsen för att skala upp klustret.
1. Välj alternativet **schema – baserat** på **typ av autoskalning**.
1. Klicka på **Konfigurera** för att öppna fönstret för **Automatisk skalnings konfiguration** .
1. Välj din tidszon och klicka sedan på **+ Lägg till villkor**
1. Välj vecko dagarna som det nya villkoret ska gälla för.
1. Redigera den tid då villkoret ska börja gälla och antalet noder som klustret ska skalas till.
1. Lägg till fler villkor om det behövs.

    ![Aktivera schema skapande för arbetsnoden](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-schedule-creation.png)

Antalet noder måste vara mellan 3 och det högsta antalet arbetsnoder som du angav innan du lägger till villkor.

### <a name="final-creation-steps"></a>Slutliga skapande steg

För både belastnings beroende och schemabaserade skalning väljer du VM-typen för arbetsnoder genom att välja en virtuell dator i list rutan under Node- **storlek**. När du har valt VM-typen för varje nodtyp kan du se det beräknade kostnads intervallet för hela klustret. Justera VM-typerna så att de passar din budget.

![Aktivera schemaläggning av noden för den automatiska skalnings storleken på arbetsnoden](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-vmsize.png)

Din prenumeration har en kapacitets kvot för varje region. Det totala antalet kärnor i head-noderna kombinerat med det maximala antalet arbetsnoder får inte överstiga kapacitets kvoten. Den här kvoten är dock en mjuk gräns. Du kan alltid skapa ett support ärende så att det blir lättare att öka.

> [!Note]  
> Om du överskrider den totala kvot gränsen visas ett fel meddelande om att den maximala noden har överskridit de tillgängliga kärnorna i den här regionen, Välj en annan region eller kontakta supporten för att öka kvoten.

Mer information om hur du skapar HDInsight-kluster med hjälp av Azure Portal finns i [skapa Linux-baserade kluster i HDInsight med hjälp av Azure Portal](hdinsight-hadoop-create-linux-clusters-portal.md).  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>Skapa ett kluster med en Resource Manager-mall

#### <a name="load-based-autoscaling"></a>Load-based autoskalning

Du kan skapa ett HDInsight-kluster med belastningsutjämnad autoskalning av en Azure Resource Manager mall genom att lägga till en `autoscale`-nod i `computeProfile` > `workernode`-avsnittet med egenskaperna `minInstanceCount` och `maxInstanceCount` som visas i JSON-kodfragmentet nedan.

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

Mer information om hur du skapar kluster med Resource Manager-mallar finns i [skapa Apache Hadoop kluster i HDInsight med hjälp av Resource Manager-mallar](hdinsight-hadoop-create-linux-clusters-arm-templates.md).  

#### <a name="schedule-based-autoscaling"></a>Schema-baserad autoskalning

Du kan skapa ett HDInsight-kluster med schemabaserade autoskalning av en Azure Resource Manager mall genom att lägga till en `autoscale`-nod i avsnittet `computeProfile` > `workernode`. `autoscale`-noden innehåller en `recurrence` som har en `timezone` och `schedule` som beskriver när ändringen ska äga rum.

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

### <a name="enable-and-disable-autoscale-for-a-running-cluster"></a>Aktivera och inaktivera autoskalning för ett kluster som körs

#### <a name="using-the-azure-portal"></a>Använda Azure Portal

Om du vill aktivera autoskalning på ett kluster som körs väljer du **kluster storlek** under **Inställningar**. Klicka sedan på **Aktivera autoskalning**. Välj den typ av autoskalning som du vill använda och ange alternativ för belastnings beroende eller schemabaserade skalningar. Klicka slutligen på **Spara**.

![Aktivera schemabaserade autoskalning för arbetsnoden och köra kluster](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-enable-running-cluster.png)

#### <a name="using-the-rest-api"></a>Använda REST API

Om du vill aktivera eller inaktivera autoskalning på ett kluster som körs med hjälp av REST API gör du en POST-begäran till slut punkten för autoskalning enligt kodfragmentet nedan:

```
https://management.azure.com/subscriptions/{subscription Id}/resourceGroups/{resourceGroup Name}/providers/Microsoft.HDInsight/clusters/{CLUSTERNAME}/roles/workernode/autoscale?api-version=2018-06-01-preview
```

Använd lämpliga parametrar i nytto lasten för begäran. JSON-nyttolasten nedan kan användas för att aktivera autoskalning. Använd nytto Last `{autoscale: null}` för att inaktivera autoskalning.

```json
{ autoscale: { capacity: { minInstanceCount: 3, maxInstanceCount: 2 } } }
```

Se föregående avsnitt om hur du [aktiverar belastningsutjämnad autoskalning](#load-based-autoscaling) för en fullständig beskrivning av alla nytto Last parametrar.

## <a name="best-practices"></a>Bästa metoder

### <a name="choosing-load-based-or-schedule-based-scaling"></a>Välja inload-baserad eller schema-baserad skalning

Tänk på följande faktorer innan du fattar ett beslut om vilket läge som ska väljas:

* Aktivera autoskalning när klustret skapas.
* Det minsta antalet noder bör vara minst tre.
* Inläsnings varians: belastningen på klustret följer ett konsekvent mönster vid vissa tidpunkter på vissa dagar. Om inte, är belastnings beroende schemaläggning ett bättre alternativ.
* SLA-krav: autoskalning av skalning är aktiv i stället för förutsägelse. Kommer det att finnas en tillräcklig fördröjning mellan när belastningen börjar öka och när klustret måste ha en mål storlek? Om det finns strikta SLA-krav och belastningen är ett fast känt mönster, är schema-based ett bättre alternativ.

### <a name="consider-the-latency-of-scale-up-or-scale-down-operations"></a>Beakta svars tiden för skalning upp-eller nedskalning

Det kan ta 10 till 20 minuter för en skalnings åtgärd att slutföras. När du ställer in ett anpassat schema bör du planera för den här fördröjningen. Om du till exempel behöver kluster storleken 20 kl. 9:00 kan du ange schema utlösaren till en tidigare tidpunkt, till exempel 8:30 AM, så att skalnings åtgärden slutförs med 9:00.

### <a name="preparation-for-scaling-down"></a>Förberedelse för skalning

Under processen för kluster skalning nedåt inaktiverar autoskalning noderna för att uppfylla mål storleken. Om det finns aktiviteter som körs på dessa noder, väntar autoskalning tills aktiviteterna har slutförts. Eftersom varje arbetsnoden också betjänar en roll i HDFS, flyttas temporära data till de återstående noderna. Därför bör du se till att det finns tillräckligt med utrymme på de återstående noderna som är värdar för alla temporära data.

De jobb som körs fortsätter att köras och avslutas. Väntande jobb väntar på att schemaläggas som normalt med färre tillgängliga arbetsnoder.

### <a name="minimum-cluster-size"></a>Minsta kluster storlek

Skala inte upp klustret till färre än tre noder. Om du skalar klustret till färre än tre noder kan det bli fastnat i fel säkert läge på grund av otillräcklig filreplikering. Mer information finns i [få fastna i fel säkert läge]( https://docs.microsoft.com/ azure/hdinsight/hdinsight-scaling-best-practices#getting-stuck-in-safe-mode) .

## <a name="monitoring"></a>Övervakning

### <a name="cluster-status"></a>Klusterstatus

Kluster statusen som visas i Azure Portal kan hjälpa dig att övervaka automatiska skalnings aktiviteter.

![Aktivera belastnings utjämning av arbetsnodens kluster status för automatisk skalning](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-cluster-status.png)

Alla de kluster status meddelanden som du kan se förklaras i listan nedan.

| Klusterstatus | Förklaring |
|---|---|
| Körs | Klustret fungerar normalt. Alla tidigare autoskalning-aktiviteter har slutförts. |
| Uppdatering  | Konfigurationen för automatisk skalning av klustret uppdateras.  |
| HDInsight-konfiguration  | En åtgärd för att skala upp eller skala upp i klustret pågår.  |
| Uppdaterings fel  | HDInsight påträffade problem under konfigurations uppdateringen för automatisk skalning. Kunder kan välja att antingen försöka uppdatera igen eller inaktivera autoskalning.  |
| Fel  | Något är fel med klustret och det kan inte användas. Ta bort det här klustret och skapa ett nytt.  |

Om du vill visa det aktuella antalet noder i klustret går du till diagrammet **kluster storlek** på sidan **Översikt** för klustret, eller så klickar du på **kluster storlek** under **Inställningar**.

### <a name="operation-history"></a>Åtgärds historik

Du kan visa klustrets skalnings-och skalnings historik som en del av kluster måtten. Du kan också Visa alla skalnings åtgärder under den senaste dagen, veckan eller en annan tids period.

Välj **mått** under **övervakning**. Klicka sedan på **Lägg till mått** och **Antal aktiva arbetare** i list rutan **mått** . Klicka på knappen i det övre högra hörnet för att ändra tidsintervallet.

![Aktivera schema baserat på arbetsnodens mått för autoskalning](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-chart-metric.png)

## <a name="next-steps"></a>Nästa steg

* Läs om metod tips för att skala kluster manuellt i [metod tips för skalning](hdinsight-scaling-best-practices.md)
