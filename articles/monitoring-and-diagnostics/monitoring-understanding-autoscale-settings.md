---
title: "Så här fungerar Autoskala inställningar i Azure | Microsoft Docs"
description: "En detaljerad uppdelning av Autoskala inställningar och hur de fungerar."
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ce2930aa-fc41-4b81-b0cb-e7ea922467e1
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2017
ms.author: ancav
ms.openlocfilehash: 73c79ec4ee1beb5220e088421c78ffffd932eef1
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2018
---
# <a name="understand-autoscale-settings"></a>Förstå inställningarna för automatisk skalning
Autoskala inställningar hjälper att säkerställa att du har rätt antal resurser som körs för att hantera varierar belastningen på ditt program. Du kan konfigurera inställningar för Autoskala ska utlösas baserat på mått som anger belastningen eller prestanda eller utlösta på schemalagd dag och tid. Den här artikeln tar en närmare titt på en autoskalningsinställning uppbyggnad. Artikeln börjar med schemat och egenskaperna för en inställning och sedan går igenom de olika profiltyper som kan konfigureras. Slutligen beskrivs hur funktionen Autoskala i Azure utvärderar vilken profil som ska köras vid en given tidpunkt.

## <a name="autoscale-setting-schema"></a>Autoskala inställningen schema
Följande autoskalningsinställning används för att illustrera Autoskala inställningen schemat. Det är viktigt att Observera att den här Autoskala har:
- En profil. 
- Två mått regler i den här profilen: en för att skala ut och en för skalan i.
  - Skalbar regeln utlöses när den virtuella datorns skaluppsättning genomsnittliga procentandelen CPU mått är större än 85 procent under de senaste 10 minuterna.
  - Skala i regeln utlöses när den virtuella datorns skaluppsättning genomsnittet är mindre än 60 procent för den senaste minuten.

> [!NOTE]
> En inställning kan ha flera profiler. Mer information finns i [profiler](#autoscale-profiles) avsnitt. En profil kan också ha flera skalbar regler och skala i regler. Information om hur de utvärderas finns i [utvärdering](#autoscale-evaluation) avsnitt.

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
| Inställning | ID | Den autoskalningsinställning resurs-ID. Autoskala inställningar är en Azure Resource Manager-resurs. |
| Inställning | namn | Autoskalningsinställningens namn. |
| Inställning | location | Autoskalningsinställningen plats. Den här platsen kan skilja sig från platsen för den resurs som skalas. |
| properties | targetResourceUri | Resurs-ID för den resurs som skalas. Du kan bara ha en autoskalningsinställning per resurs. |
| properties | Profiler | En autoskalningsinställning består av en eller flera profiler. Varje gång Autoskala motorn körs, kör en profil. |
| Profil | namn | Namnet på profilen. Du kan välja vilket namn som hjälper dig att identifiera profilen. |
| Profil | Capacity.maximum | Den högsta tillåtna kapacitet. Det garanterar att Autoskala när du kör den här profilen inte skala din resurs ovanför det här numret. |
| Profil | Capacity.minimum | Den minsta kapaciteten som tillåts. Det garanterar att Autoskala när du kör den här profilen inte skala din resurs under det här värdet. |
| Profil | Capacity.default | Om det inte går att läsa resurs mått (i det här fallet Processorn ”vmss1”), och den aktuella kapaciteten är lägre än standardvärdet, skalas Autoskala ut till standardinställningarna. Detta är att säkerställa tillgängligheten för resursen. Om den aktuella kapaciteten redan är högre än standardkapaciteten, skala inte Autoskala i. |
| Profil | regler | Autoskala skalas automatiskt mellan de högsta och lägsta kapaciteterna med regler i profilen. Du kan ha flera regler i en profil. Det finns vanligtvis två regler: en för att avgöra när du ska skalas ut och den andra för att avgöra när du vill skala i. |
| regel | metricTrigger | Definierar mått villkoren i regeln. |
| metricTrigger | metricName | Namnet på måttet. |
| metricTrigger |  metricResourceUri | Resurs-ID för den resurs som genererar måttet. I de flesta fall är det samma som den resurs som skalas. I vissa fall kan det vara olika. Du kan exempelvis skala en skaluppsättning för virtuell dator baserat på antalet meddelanden i en kö för lagring. |
| metricTrigger | timeGrain | Mått provtagning varaktighet. Till exempel **Tidskorn = ”PT1M”** innebär att mätvärdena som ska aggregeras varje 1 minut med hjälp av metoden aggregering som anges i statistik-elementet. |
| metricTrigger | statistik | Sammanställningsmetod inom Tidskorn period. Till exempel **statistik = ”medel”** och **Tidskorn = ”PT1M”** innebär att mätvärdena som ska aggregeras varje 1 minut, genom att göra medelvärdet. Den här egenskapen anger hur måttet samplas. |
| metricTrigger | timeWindow | Hur lång tid att gå tillbaka för mått. Till exempel **värdet timeWindow = ”PT10M”** innebär att varje gång Autoskala körs frågar mått för de senaste 10 minuterna. Tidsfönstret kan din mått till normaliseras, och undviker att reagera på tillfälliga toppar. |
| metricTrigger | timeAggregation | Aggregeringen-metod som används för att aggregera provade mått. Till exempel **TimeAggregation = ”medel”** bör aggregera provade mått genom att ta medelvärdet. I föregående fall ta tio 1 minut prover och genomsnittlig dem. |
| regel | scaleAction | Åtgärd att vidta när metricTrigger regelns utlöses. |
| scaleAction | riktning | ”Öka” om du vill skala upp eller ”minska” till skalan i.|
| scaleAction | värde | Hur mycket att öka eller minska resursens kapacitet. |
| scaleAction | cooldown | Hur lång tid ska vänta efter en skalningsåtgärden innan skalning igen. Till exempel om **cooldown = ”PT10M”**, Autoskala försöker inte att skala igen för en annan 10 minuter. Cooldown är att tillåta mått att hålla efter tillägg eller borttagning av instanser. |

## <a name="autoscale-profiles"></a>Autoskalningsprofiler

Det finns tre typer av autoskalningsprofiler:

- **Vanliga profil:** vanligaste profilen. Du kan använda en vanlig profil om du inte behöver skala din resurs som baseras på dag i veckan eller på en viss dag. Den här profilen kan sedan konfigureras med mått regler som bestämmer när att skala upp och när skala. Du bör bara ha en vanlig profil som har definierats.

    Exempelprofil som används tidigare i den här artikeln är ett exempel på en vanlig profil. Observera att det är också möjligt att ställa in en profil för att skala med en statisk instansantal för din resurs.

- **Fast datum profil:** profilen är specialfall. Anta exempelvis att du har en viktig händelse kommande på 26 December 2017 (PST). Vill du lägsta och högsta kapacitet för din resurs för att skilja på den dagen, men fortfarande skala på samma mått. I det här fallet bör du lägga till en fast datum profil din inställning listan över profiler. Profilen som är konfigurerad för att köras endast på händelsens dag. För varje dag använder Autoskala reguljära profil.

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
    
- **Återkommande profil:** den här typen av profil kan du kontrollera att den här profilen används alltid på en viss dag i veckan. Återkommande profiler kan bara ha en starttid. De kör förrän nästa återkommande profilen eller fasta datum profil är inställd att starta. En autoskalningsinställning med endast en upprepning profil körs den här profilen, även om det är en vanlig profil som definierats i samma inställning. I följande två exempel visas hur den här profilen används:

    **Exempel 1: Veckodagar kontra helger**
    
    Anta att helger, du vill att din maxkapacitet ska vara 4. På vardagar, eftersom du förväntar dig fler belastning, vill du den maximala kapaciteten för att vara 10. I det här fallet innehåller inställningen två återkommande profiler, en för att köra på helger och den andra i veckodagar.
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

    Föregående inställningen visar att varje upprepning profil har ett schema. Det här schemat avgör när profilen startar körs. Profilen slutar när det är dags att köra en annan profil.

    I den föregående inställningen, till exempel anges ”weekdayProfile” till börjar med måndag klockan 12:00. Det innebär att den här profilen börjar köras på måndag klockan 12:00. Det fortsätter tills lördag klockan 12:00, när ”weekendProfile” är schemalagd att börja köras.

    **Exempel 2: kontorstid**
    
    Anta att du vill ha ett mått tröskelvärdet under kontorstid (9:00:00 till 5:00) och en för alla andra tider. Inställningen skulle se ut så här:
    
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
    
    Föregående inställningen visar att ”businessHoursProfile” börjar med måndag på 9:00:00 och 17:00:00 i fortsättningen. Det är då ”nonBusinessHoursProfile” börjar köras. ”NonBusinessHoursProfile” körs förrän 9:00:00 tisdag och sedan ”businessHoursProfile” tar över igen. Detta upprepas tills fredag på 17:00:00. ”NonBusinessHoursProfile” körs vid den punkten ända till måndag vid 9:00:00.
    
> [!Note]
> Autoskala användargränssnittet i Azure portal tvingar sluttider för upprepning profiler och börjar att köra den autoskalningsinställning standardprofil between upprepning profiler.
    
## <a name="autoscale-evaluation"></a>Autoskala utvärdering
Med hänsyn till att Autoskala inställningarna kan ha flera profiler och varje profil kan ha flera mått regler, är det viktigt att förstå hur en autoskalningsinställning utvärderas. Varje gång Autoskala jobbet körs börjar genom att välja den profil som är tillämpligt. Autoskala utvärderar lägsta och högsta värde och mått regler i profilen och sedan bestämmer om en skalningsåtgärd krävs.

### <a name="which-profile-will-autoscale-pick"></a>Vilken profil Autoskala hämtar?

Autoskala använder följande sekvens för att välja profilen:
1. Det verkar först för alla fasta datum-profil som är konfigurerad för att köras nu. Om det finns, körs det Autoskala. Om det finns flera fast datum profiler som ska köras, väljs Autoskala först.
2. Om det finns inga profiler för fast datum, kontrollerar Autoskala upprepning profiler. Om en profil för upprepning hittas, kör den.
3. Om det finns ingen fast datum eller återkommande profiler, kör Autoskala reguljära profilen.

### <a name="how-does-autoscale-evaluate-multiple-rules"></a>Hur utvärderar Autoskala flera regler?

När Autoskala avgör vilken profil som ska köras, utvärderas alla skalbar regler i profilen (detta är regler med **riktning = ”öka”**).

Om en eller flera regler för skalbar utlöses, Autoskala beräknar den nya kapacitet som bestäms av den **scaleAction** för var och en av dessa regler. Sedan skalas den ut maximalt för de kapaciteterna så tjänsttillgänglighet.

Till exempel anta att det är en virtuell dator skaluppsättning med en aktuell kapacitet på 10. Det finns två skalbara regler: en som ökar kapaciteten med 10 procent och en som ökar kapaciteten med 3 räknare. Den första regeln skulle resultera i en ny kapacitet på 11 och den andra regeln skulle resultera i en kapacitet på 13. För att säkerställa tjänsttillgänglighet väljer Autoskala den åtgärd som resulterar i maximal kapacitet, så den andra regeln är valt.

Om inga skalbar regler har utlösts Autoskala utvärderas alla skala i regler (regler med **riktning = ”minska”**). Autoskala tar bara en åtgärd för skala om alla regler i skala utlösts.

Autoskala beräknar den nya kapacitet som bestäms av den **scaleAction** för var och en av dessa regler. Därefter väljs skalningsåtgärd som resulterar i de kapaciteterna så tjänsttillgänglighet maximalt.

Till exempel anta att det är en virtuell dator skaluppsättning med en aktuell kapacitet på 10. Det finns två skala i regler: en som minskar kapacitet med 50 procent och en som minskar kapacitet med 3 räknare. Den första regeln skulle resultera i en ny kapacitet på 5 och den andra regeln skulle resultera i en kapacitet på 7. För att säkerställa tjänsttillgänglighet väljer Autoskala den åtgärd som resulterar i maximal kapacitet, så den andra regeln är valt.

## <a name="next-steps"></a>Nästa steg
Läs mer om autoskalning genom att referera till följande:

* [Översikt över Autoskala](monitoring-overview-autoscale.md)
* [Azure övervakaren Autoskala vanliga mått](insights-autoscale-common-metrics.md)
* [Metodtips för Azure-Monitor Autoskala](insights-autoscale-best-practices.md)
* [Använda automatiska åtgärder för att skicka e-post och webhook aviseringar](insights-autoscale-to-webhook-email.md)
* [Autoskala REST API](https://msdn.microsoft.com/library/dn931953.aspx)
