---
title: Förstå inställningarna för automatisk skalning i Azure Monitor
description: Detaljer för inställningarna för automatisk skalning och hur de fungerar. Gäller för virtuella datorer, molntjänster, Web Apps
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/18/2017
ms.author: ancav
ms.component: autoscale
ms.openlocfilehash: 2347d82b8c2f5a08b944577e5b06cde3b68617b3
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2018
ms.locfileid: "53385014"
---
# <a name="understand-autoscale-settings"></a>Förstå inställningarna för automatisk skalning
Inställningarna för automatisk skalning säkerställer att du har rätt mängd resurser som körs för att hantera varierande belastning av ditt program. Du kan konfigurera inställningarna för automatisk skalning kan utlösas baserat på mått som indikerar belastning eller prestanda eller utlösta vid ett schemalagt datum och tid. Den här artikeln tar en närmare titt på uppbyggnad av en autoskalningsinställning. Artikeln börjar med schema- och egenskaperna för en inställning och sedan går igenom de olika profiltyperna som kan konfigureras. Slutligen beskrivs hur funktionen för automatisk skalning i Azure utvärderar vilken profil som ska köras vid en given tidpunkt.

## <a name="autoscale-setting-schema"></a>Schema för inställning av automatisk skalning
För att visa automatisk skalning inställningen schemat kan används följande inställning för automatisk skalning. Det är viktigt att Observera att den här autoskalningsinställningen har:
- En profil. 
- Två mått regler i den här profilen: en för att skala ut och en för att skala in.
  - Skala ut regeln utlöses när den virtuella datorns skalningsuppsättning genomsnittliga procentandelen CPU mått är större än 85 procent under de senaste 10 minuterna.
  - Skala in regeln utlöses när den virtuella datorns skalningsuppsättning genomsnittet är mindre än 60 procent under den senaste minuten.

> [!NOTE]
> En inställning kan ha flera profiler. Mer information finns i den [profiler](#autoscale-profiles) avsnittet. En profil kan också ha flera regler för skalbarhet och skala in regler som definierats. Hur de utvärderas finns i den [utvärdering](#autoscale-evaluation) avsnittet.

```JSON
{
  "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/autoscalesettings/setting1",
  "name": "setting1",
  "type": "Microsoft.Insights/autoscaleSettings",
  "location": "East US",
  "properties": {
    "enabled": true,
    "targetResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
    "profiles": [
      {
        "name": "mainProfile",
        "capacity": {
          "minimum": "1",
          "maximum": "4",
          "default": "1"
        },
        "rules": [
          {
            "metricTrigger": {
              "metricName": "Percentage CPU",
              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
              "timeGrain": "PT1M",
              "statistic": "Average",
              "timeWindow": "PT10M",
              "timeAggregation": "Average",
              "operator": "GreaterThan",
              "threshold": 85
            },
            "scaleAction": {
              "direction": "Increase",
              "type": "ChangeCount",
              "value": "1",
              "cooldown": "PT5M"
            }
          },
    {
            "metricTrigger": {
              "metricName": "Percentage CPU",
              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
              "timeGrain": "PT1M",
              "statistic": "Average",
              "timeWindow": "PT10M",
              "timeAggregation": "Average",
              "operator": "LessThan",
              "threshold": 60
            },
            "scaleAction": {
              "direction": "Decrease",
              "type": "ChangeCount",
              "value": "1",
              "cooldown": "PT5M"
            }
          }
        ]
      }
    ]
  }
}
```

| Section | Elementnamn | Beskrivning |
| --- | --- | --- |
| Inställning | ID | Den autoskalningsinställning resurs-ID. Inställningarna för automatisk skalning är en Azure Resource Manager-resurs. |
| Inställning | namn | Autoskalningsinställningens namn. |
| Inställning | location | Platsen för autoskalningsinställningen. Den här platsen kan skilja sig från platsen för den resurs som skalas. |
| properties | targetResourceUri | Resurs-ID för den resurs som skalas. Du kan bara ha en autoskalningsinställning per resurs. |
| properties | Profiler | En autoskalningsinställning består av en eller flera profiler. Varje gång automatisk skalning motorn körs, kör en profil. |
| profil | namn | Namnet på profilen. Du kan välja vilket namn som hjälper dig att identifiera profilen. |
| profil | Capacity.maximum | Den maximala kapaciteten som tillåts. Det innebär att automatisk skalning, när du kör den här profilen inte skala din resurs ovanför det här talet. |
| profil | Capacity.minimum | Den minsta kapaciteten som tillåts. Det innebär att automatisk skalning, när du kör den här profilen inte skala din resurs under det här värdet. |
| profil | Capacity.default | Om det inte går att läsa Resursmått (i det här fallet CPU av ”vmss1”) och den aktuella kapaciteten är under standard, skalas automatisk skalning ut till standardinställningen. Detta är att säkerställa tillgängligheten för resursen. Om den aktuella kapaciteten är högre än standardkapaciteten, skala inte automatisk skalning i. |
| profil | regler | Automatisk skalning skalar automatiskt mellan de högsta och lägsta kapaciteterna, med hjälp av regler i profilen. Du kan ha flera regler i en profil. Det finns vanligtvis två regler: en för att avgöra om att skala ut och den andra sätt att avgöra om att skala i. |
| regel | metricTrigger | Definierar måttvillkor för regeln. |
| metricTrigger | MetricName | Namnet på måttet. |
| metricTrigger |  metricResourceUri | Resurs-ID för den resurs som genererar måttet. I de flesta fall är det samma som den resurs som skalas. I vissa fall kan det vara olika. Du kan exempelvis skala en skalningsuppsättning för virtuell dator baserat på antalet meddelanden i en lagringskö. |
| metricTrigger | timeGrain | Metrisk sampling varaktighet. Till exempel **TimeGrain = ”PT1M”** innebär att mått som ska aggregeras varje minut med hjälp av sammansättningsmetoden som anges i elementet statistik. |
| metricTrigger | statistik | Sammansättningsmetoden inom timeGrain-perioden. Till exempel **statistik = ”Average”** och **timeGrain = ”PT1M”** innebär att mått som ska aggregeras varje minut genom att ta medelvärdet. Den här egenskapen avgör hur måttet samplas. |
| metricTrigger | timeWindow | Hur lång tid att gå tillbaka för mått. Till exempel **timeWindow = ”PT10M”** innebär att varje gång automatisk skalning körs, frågar mått för de senaste 10 minuterna. Tidsperioden att låta måtten normaliseras, och undvika reaktioner med övergående spikar. |
| metricTrigger | timeAggregation | Sammansättningsmetoden som används för att sammansätta de samplade måtten. Till exempel **TimeAggregation = ”Average”** bör sammasätta de samplade måtten genom att ta medelvärdet. I föregående fall tar tio 1 minut exemplen och genomsnittlig dem. |
| regel | scaleAction | Åtgärd att vidta när metricTrigger regelns utlöses. |
| scaleAction | riktning | ”Öka” för att skala ut eller ”minska” att skala in.|
| scaleAction | värde | Hur mycket att öka eller minska kapaciteten för resursen. |
| scaleAction | nedkylningstiden | Hur lång tid att vänta efter en skalningsåtgärd innan du skalar igen. Till exempel om **nedkylningstiden = ”PT10M”**, automatisk skalning inte försöker skala igen för en annan 10 minuter. Nedkylningstiden är att låta måtten stabiliseras efter tillägg och borttagning av instanser. |

## <a name="autoscale-profiles"></a>Profiler för automatisk skalning

Det finns tre typer av profiler för automatisk skalning:

- **Vanliga profil:** Den vanligaste profilen. Om du inte behöver skala dina resurser baserat på dagen i veckan eller på en viss dag, kan du använda en vanlig profil. Den här profilen kan sedan konfigureras med mått regler som anger när du vill skala ut och när du skalar i. Du bör bara ha en vanlig profil som definierats.

    Exempelprofil som används tidigare i den här artikeln är ett exempel på en vanlig profil. Observera att det är också möjligt att ställa in en profil för att skala till ett antal statiska instanser för din resurs.

- **Fast datum profil:** Den här profilen är specialfall. Anta exempelvis att du har en viktig händelse som följer 26 December 2017 (Stillahavstid). Vill du lägsta och högsta kapacitet för din resurs för att skilja på den dagen, men fortfarande skalning på samma mått. I det här fallet bör du lägga till en fast datum profil din inställning listan över profiler. Profilen har konfigurerats för att bara körs dag för händelsen. Automatisk skalning använder vanliga profilen för en dag.

    ``` JSON
    "profiles": [{
    "name": " regularProfile",
    "capacity": {
    ...
    },
    "rules": [{
    ...
    },
    {
    ...
    }]
    },
    {
    "name": "eventProfile",
    "capacity": {
    ...
    },
    "rules": [{
    ...
    }, {
    ...
    }],
    "fixedDate": {
        "timeZone": "Pacific Standard Time",
               "start": "2017-12-26T00:00:00",
               "end": "2017-12-26T23:59:00"
    }}
    ]
    ```
    
- **Upprepning profil:** Den här typen av profil kan du se till att den här profilen används alltid på en viss dag i veckan. Upprepning profiler kan bara ha en starttid. De körs tills nästa återkommande profilen eller fast datum profilen är inställd att starta. En autoskalningsinställning med endast en upprepning profilen kör som-profil, även om det är en vanlig profil som definierats i samma inställning. I följande två exempel visas hur den här profilen används:

    **Exempel 1: Veckodagar och helger**
    
    Anta att helger, du vill att din maxkapacitet ska vara 4. På vardagar, eftersom du förväntar dig högre belastning du din maximal kapacitet är 10. Dina inställningar skulle i det här fallet innehåller två upprepning profiler, en för att köras på helger och den andra på vardagar.
    Inställningen ser ut så här:

    ``` JSON
    "profiles": [
    {
    "name": "weekdayProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }],
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday"
            ],
            "hours": [
                0
            ],
            "minutes": [
                0
            ]
        }
    }}
    },
    {
    "name": "weekendProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }]
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Saturday"
            ],
            "hours": [
                0
            ],
            "minutes": [
                0
            ]
        }
    }
    }]
    ```

    Föregående inställningen visar att varje upprepning-profil har ett schema. Det här schemat avgör när profilen startar körs. Profilen stoppas när det är dags att köra en annan profil.

    I föregående inställning, till exempel anges ”weekdayProfile” till börjar med måndag kl. 12:00. Det innebär att den här profilen börjar köras på måndag kl. 12:00. Det fortsätter tills lördag kl. 12:00, när ”weekendProfile” kommer att börja köra.

    **Exempel 2: kontorstid**
    
    Vi antar att du vill ha ett tröskelvärde för mått under kontorstid (9.00 till 5:00) och en för alla andra tider. Inställningen skulle se ut så här:
    
    ``` JSON
    "profiles": [
    {
    "name": "businessHoursProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }],
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday", “Tuesday”, “Wednesday”, “Thursday”, “Friday”
            ],
            "hours": [
                9
            ],
            "minutes": [
                0
            ]
        }
    }
    },
    {
    "name": "nonBusinessHoursProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }]
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday", “Tuesday”, “Wednesday”, “Thursday”, “Friday”
            ],
            "hours": [
                17
            ],
            "minutes": [
                0
            ]
        }
    }
    }]
    ```
    
    Föregående inställningen visar att ”businessHoursProfile” börjar som körs på måndag kl. 9:00 och fortsätter att 17:00:00. Det är då ”nonBusinessHoursProfile” börjar köras. ”NonBusinessHoursProfile” som körs fram till den 9:00:00 tisdag och sedan ”businessHoursProfile” tar igen. Detta upprepas tills fredag 17:00:00. I det här läget körs ”nonBusinessHoursProfile” hela vägen till måndag kl. 9:00.
    
> [!Note]
> Användargränssnittet för automatisk skalning i Azure-portalen tillämpar sluttid för upprepning profiler och börjar att köra den autoskalningsinställning standardprofil mellan upprepning profiler.
    
## <a name="autoscale-evaluation"></a>Automatisk skalning utvärdering
Med hänsyn till att inställningarna för automatisk skalning kan ha flera profiler, och varje profil kan ha flera mått regler, är det viktigt att förstå hur en autoskalningsinställning utvärderas. Varje gång automatisk skalning jobbet körs börjar den genom att välja den profil som är tillämpligt. Sedan automatisk skalning utvärderar minst och högsta värden och några mått regler i profilen och avgör om det krävs en skalningsåtgärd.

### <a name="which-profile-will-autoscale-pick"></a>Vilken profil kommer autoskalning välja?

Automatisk skalning använder följande sekvens för att välja profilen:
1. Kontaktinformationen först letas för alla fast datum-profil som är konfigurerad för att köras nu. Om det finns, körs det automatisk skalning. Om det finns flera profiler för fast datum som ska köras, väljer den första mallen för automatisk skalning.
2. Om det finns inga profiler för fast datum, tittar automatisk skalning på upprepningen profiler. Om en profil för upprepning hittas, kör den.
3. Om det finns ingen fast datum eller återkommande profiler, körs automatisk skalning regelbundna profilen.

### <a name="how-does-autoscale-evaluate-multiple-rules"></a>Hur utvärdera flera regler av automatisk skalning?

När autoskalning avgör vilken profil som ska köras, det utvärderar alla skalbar regler i profilen (detta är regler med **riktning = ”öka”**).

Om en eller flera regler för skalbar utlöses Autoskala beräknar den nya kapacitet som bestäms av den **scaleAction** för var och en av dessa regler. Sedan skalas den upp till maxvärdet för de kapaciteterna för att garantera tjänstens tillgänglighet.

Exempelvis kan vi antar att det är en VM-skalningsuppsättningen med en aktuell kapacitet på 10. Det finns två skalbara regler: en som ökar kapaciteten med 10 procent och en som ökar kapaciteten med 3 antal. Den första regeln skulle resultera i en ny kapacitet på 11 och den andra regeln skulle resultera i en kapacitet på 13. Om du vill för att garantera tjänstens tillgänglighet, väljer automatisk skalning åtgärden resulterar i maximal kapacitet, så den andra regeln väljs.

Om inga regler för skalbar utlöses Autoskala utvärderar alla skala in regler (regler med **riktning = ”minska”**). Automatisk skalning tar bara en inskalningsåtgärd om alla regler skala in utlöses.

Automatisk skalning beräknar den nya kapacitet som bestäms av den **scaleAction** för var och en av dessa regler. Sedan väljer den skalningsåtgärd som resulterar i det maximala antalet på de kapaciteterna för att garantera tjänstens tillgänglighet.

Exempelvis kan vi antar att det är en VM-skalningsuppsättningen med en aktuell kapacitet på 10. Det finns två skala in regler: en som minskar kapaciteten med 50 procent och en som minskar kapaciteten med 3 antal. Den första regeln skulle resultera i en ny kapacitet på 5 och den andra regeln skulle resultera i en kapacitet på 7. Om du vill för att garantera tjänstens tillgänglighet, väljer automatisk skalning åtgärden resulterar i maximal kapacitet, så den andra regeln väljs.

## <a name="next-steps"></a>Nästa steg
Läs mer om automatisk skalning genom att referera till följande:

* [Översikt över automatisk skalning](../azure-monitor/platform/autoscale-overview.md)
* [Azure Monitor autoscale vanliga mått](../azure-monitor/platform/autoscale-common-metrics.md)
* [Metodtips för automatisk skalning i Azure Monitor](../azure-monitor/platform/autoscale-best-practices.md)
* [Använda automatisk skalning åtgärder för att skicka e-post och webhook varningsmeddelanden](../azure-monitor/platform/autoscale-webhook-email.md)
* [REST-API för automatisk skalning](https://msdn.microsoft.com/library/dn931953.aspx)
