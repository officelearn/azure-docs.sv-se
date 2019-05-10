---
title: Skala automatiskt Azure HDInsight-kluster (förhandsversion)
description: Använda HDInsight Autoscale-funktionen för att automatiskt skala kluster
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: hrasheed
ms.openlocfilehash: f8803a498e62958a5488f2ac8830137c37533e54
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65413705"
---
# <a name="automatically-scale-azure-hdinsight-clusters-preview"></a>Skala automatiskt Azure HDInsight-kluster (förhandsversion)

Azure HDInsight funktionen för automatisk skalning skalas automatiskt antalet arbetarnoder i ett kluster upp och ned. Andra typer av noder i klustret kan inte skalas för närvarande.  När du skapar ett nytt HDInsight-kluster, kan du ange ett lägsta och högsta antal arbetsnoder. Automatisk skalning övervakar resurskraven för analytics-belastningen sedan och skalar antalet arbetarnoder upp eller ned. Det finns ingen extra kostnad för den här funktionen.

## <a name="cluster-compatibility"></a>Kluster-kompatibilitet

> [!Important]
> Funktionen för automatisk skalning fungerar endast för kluster som skapats efter allmän tillgänglighet av funktionen i maj 2019. Det fungerar inte för befintliga kluster.

I följande tabell beskrivs klustertyper och versioner som är kompatibla med funktionen för automatisk skalning.

| Version | Spark | Hive | LLAP | Hbase | Kafka | Storm | ML |
|---|---|---|---|---|---|---|---|
| HDInsight 3.6 utan ESP | Ja | Ja | Nej | Nej | Nej | Nej | Nej |
| HDInsight 4.0 utan ESP | Ja | Ja | Nej | Nej | Nej | Nej | Nej |
| HDInsight 3.6 med ESP | Ja | Ja | Nej | Nej | Nej | Nej | Nej |
| HDInsight 3.6 med ESP | Ja | Ja | Nej | Nej | Nej | Nej | Nej |

## <a name="how-it-works"></a>Hur det fungerar

Du kan välja load-baserade skala eller schemabaserade skala för ditt HDInsight-kluster. Load-baserade skalning ändrar antalet noder i klustret, inom ett intervall som du anger för att säkerställa optimal CPU-användning och minimera körs kostnaden.

Schemabaserad skalning ändras antalet noder i klustret, baserat på villkor som börjar gälla vid specifika tidpunkter. Dessa villkor skala kluster till ett önskat antal noder.

### <a name="metrics-monitoring"></a>Övervaka mått

Automatisk skalning kontinuerligt övervakar klustret och samlar in följande mått:

* **Totalt antal väntande CPU**: Det totala antalet kärnor som krävs för att starta körning av alla väntande behållare.
* **Totalt antal väntande minne**: Den totala mängden minne (i MB) krävs för att starta körning av alla väntande behållare.
* **Totalt antal kostnadsfria CPU**: Summan av alla oanvända kärnor på aktiva arbetsnoderna.
* **Totalt ledigt minne**: Summan av oanvänt minne (i MB) på de aktiva arbetsnoderna.
* **Använt minne per nod**: Belastningen på en underordnad nod. En underordnad nod där 10 GB minne används, anses under högre belastning än en worker med 2 GB minne som används.
* **Antal program huvudservrar per nod**: Antal program Master (AM) behållare som körs på en underordnad nod. En underordnad nod som är värd för två AM-behållare, anses vara viktigare än en underordnad nod som är värd för noll AM-behållare.

Mått som ovan kontrolleras var 60: e sekund. Skala automatiskt kan skala upp och skala ned beslut baserat på de här måtten.

### <a name="load-based-cluster-scale-up"></a>Load-baserade klusteruppskalningen

När följande villkor har identifierats, utfärdar automatisk skalning en upp-begäran:

* Totalt antal väntande CPU är större än totalt ledigt CPU under mer än 3 minuter.
* Totalt antal väntande minne är större än totala lediga minnet under mer än 3 minuter.

Tjänsten HDInsight beräknar hur många nya arbetsnoderna behövs för att uppfylla de aktuella CPU och minne och skickar en upp-begäran för att lägga till det begärda antalet noder.

### <a name="load-based-cluster-scale-down"></a>Load-baserat kluster skala ned

När följande villkor har identifierats, utfärdar automatisk skalning en begäran om skala ned:

* Totalt antal väntande CPU är mindre än totalt ledigt CPU i mer än 10 minuter.
* Totalt antal väntande minne är mindre än totala lediga minnet i mer än 10 minuter.

Automatisk skalning skickar baserat på antalet AM behållare per nod och aktuella CPU och minne, en begäran att ta bort ett visst antal noder. Tjänsten identifierar vilka noder som lämpar sig för borttagning av baserat på aktuella jobbkörningen. Skala ned åtgärden först decommissions noderna och tar bort dem från klustret.

## <a name="get-started"></a>Kom igång

### <a name="create-a-cluster-with-load-based-autoscaling"></a>Skapa ett kluster med load-baserad automatisk skalning

Utför följande steg om du vill aktivera funktionen för automatisk skalning med load-baserade skalning som en del av normal klusterskapningsprocessen:

1. Välj **anpassad (storlek, inställningar, appar)** snarare än **Snabbregistrering**.
1. På **anpassade** steg 5 (**klusterstorlek**), kontrollera den **Worker noden Autoskala** kryssrutan.
1. Välj alternativet **Load-baserade** under **Autoskala typ**.
1. Ange önskade värden för följande egenskaper:  

    * Inledande **många av arbetsnoder**.  
    * **Minsta** antalet arbetsnoder.  
    * **Maximal** antalet arbetsnoder.  

    ![Aktivera alternativet för worker node load-baserad automatisk skalning](./media/hdinsight-autoscale-clusters/usingAutoscale.png)

Det inledande antalet arbetarnoder måste ligga mellan lägsta och högsta, inklusive. Det här värdet anger den ursprungliga storleken för klustret när den skapas. Det minsta antalet arbetarnoder måste vara större än noll.

### <a name="create-a-cluster-with-schedule-based-autoscaling"></a>Skapa ett kluster med schemabaserad automatisk skalning

Utför följande steg om du vill aktivera funktionen för automatisk skalning med schemabaserade skalning som en del av normal klusterskapningsprocessen:

1. Välj **anpassad (storlek, inställningar, appar)** snarare än **Snabbregistrering**.
1. På **anpassade** steg 5 (**klusterstorlek**), kontrollera den **Worker noden Autoskala** kryssrutan.
1. Ange den **många av arbetsnoder**, som styr gränsen för skalning av klustret.
1. Välj alternativet **schemabaserade** under **Autoskala typ**.
1. Klicka på **konfigurera** att öppna den **autoskalningskonfigurationen** fönster.
1. Välj din tidszon och klicka sedan på **+ Lägg till villkor**
1. Välj dagarna i veckan som nytt villkor som ska tillämpas på.
1. Redigera den tid som villkoret ska ta effekt och antalet noder som klustret ska skalas till.
1. Lägga till fler villkor om det behövs.

    ![Aktivera alternativet för worker noden schemabaserad automatisk skalning](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-schedule-creation.png)

Antalet noder måste vara mellan 1 och antalet arbetsnoder som du har angett innan du lägger till villkor.

### <a name="final-creation-steps"></a>Skapa en sista steg

För både load- och schemabaserad skalning, väljer du typ av virtuell dator för arbetarnoder genom att klicka på **Worker nodstorlek** och **gå nodstorlek**. När du har valt VM-typ för varje nodtyp kan du se det uppskattade kostnaden området för hela klustret. Justera VM-typer som passar din budget.

![Aktivera alternativet för worker noden schemabaserad automatisk skalning](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-node-size-selection.png)

Din prenumeration har en kapacitet kvot för varje region. Det totala antalet kärnor i din huvudnoder som kombineras med det maximala antalet arbetsnoder får inte överskrida kvoten för kapacitet. Den här kvoten är dock en mjuk gräns. Du kan alltid skapa ett supportärende för att få ökad enkelt.

> [!Note]  
> Om du överskrider den totala grundkvoten får du ett felmeddelande om ”maximalt antal nod överskrider de tillgängliga kärnorna i den här regionen, Välj en annan region eller kontakta supporten för att öka kvoten”.

Mer information om HDInsight-kluster med Azure portal finns i [skapa Linux-baserade kluster i HDInsight med Azure portal](hdinsight-hadoop-create-linux-clusters-portal.md).  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>Skapa ett kluster med en Resource Manager-mall

#### <a name="load-based-autoscaling"></a>Load-baserad automatisk skalning

Du kan skapa ett HDInsight-kluster med load-baserad automatisk skalning en Azure Resource Manager-mall, genom att lägga till en `autoscale` noden till den `computeProfile`  >  `workernode` avsnittet med egenskaper `minInstanceCount` och `maxInstanceCount` som visas i json-kodfragmentet nedan.

```json
{
  "name": "workernode",
  "targetInstanceCount": 4,
  "autoscale": {
      "capacity": {
          "minInstanceCount": 2,
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

För mer information om hur du skapar kluster med Resource Manager-mallar, se [skapa Apache Hadoop-kluster i HDInsight med hjälp av Resource Manager-mallar](hdinsight-hadoop-create-linux-clusters-arm-templates.md).  

#### <a name="schedule-based-autoscaling"></a>Schemabaserad automatisk skalning

Du kan skapa ett HDInsight-kluster med schemabaserad automatisk skalning en Azure Resource Manager-mall, genom att lägga till en `autoscale` noden till den `computeProfile`  >  `workernode` avsnittet. Den `autoscale` noden innehåller ett `recurrence` som har en `timezone` och `schedule` som beskriver när ändringen kommer att äga rum.

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

### <a name="enable-and-disable-autoscale-for-a-running-cluster"></a>Aktivera och inaktivera automatisk skalning för ett kluster som körs

Om du vill aktivera automatisk skalning på ett aktivt kluster, Välj **klusterstorlek** under **inställningar**. Klicka sedan på **aktivera autoskalning**. Välj typ av automatisk skalning som du vill och ange alternativ för att skala baserat på belastning eller schema. Klicka slutligen på **spara**.

![Aktivera alternativet för worker noden schemabaserad automatisk skalning](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-enable-running-cluster.png)

## <a name="monitoring"></a>Övervakning

### <a name="cluster-status"></a>Klusterstatus

Med hjälp av klusterstatus i Azure-portalen kan du övervaka aktiviteter för automatisk skalning.

![Aktivera alternativet för worker node load-baserad automatisk skalning](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-cluster-status.png)

Alla statusmeddelanden för kluster som du kan se beskrivs i listan nedan.

| Klusterstatus | Förklaring |
|---|---|
| Körs | Klustret fungerar normalt. Alla tidigare Autoskala aktiviteterna har slutförts. |
| Uppdaterar  | Autoskalningskonfigurationen klustret uppdateras.  |
| HdInsight-konfiguration  | Ett kluster skala upp eller skala ned åtgärden pågår.  |
| Uppdateringsfel  | Problem påträffades under konfigurationsuppdateringen för automatisk skalning i HDInsight. Kunder kan välja att antingen ett nytt eller inaktivera automatisk skalning.  |
| Fel  | Det är något fel med klustret och kan inte använda den. Ta bort det här klustret och skapa en ny.  |

Om du vill visa det aktuella antalet noder i klustret, går du till den **klusterstorlek** diagram på den **översikt** för ditt kluster, eller klicka på **klusterstorlek** under  **Inställningar för**.

### <a name="operation-history"></a>Åtgärden historik

Du kan visa historiken för klustret skala upp och skala ned som en del av klustermått. Du kan också ange alla åtgärder för skalning över den senaste dagen, vecka eller andra tidsperiod.

Välj **mått** under **övervakning**. Klicka sedan på **Lägg till mått** och **nummer för aktiva medarbetare** från den **mått** listrutan. Klicka på knappen i det övre högra hörnet att ändra tidsintervallet.

![Aktivera alternativet för worker noden schemabaserad automatisk skalning](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-chart-metric.png)


## <a name="next-steps"></a>Nästa steg

* Läs mer om bästa praxis för att skala kluster manuellt i [skalning Metodtips](hdinsight-scaling-best-practices.md)
