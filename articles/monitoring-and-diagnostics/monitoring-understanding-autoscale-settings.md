---
title: "Så här fungerar Autoskala inställningar | Microsoft Docs"
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
ms.openlocfilehash: 79602cf053d834bf3d6dc6b4d5568637b179d5c7
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2018
---
# <a name="understand-autoscale-settings"></a>Förstå Autoskala inställningar
Autoskala inställningar kan du kontrollera att du har rätt antal resurser som körs för att hantera varierar belastningen på ditt program. Du kan konfigurera Autoskala inställningar ska utlösas baserat på mått som anger belastningen eller prestanda eller -utlösare på schemalagd dag och tid. Den här artikeln tar en närmare titt på en autoskalningsinställning uppbyggnad. Artikeln startar förstå schemat och egenskaperna för en inställning och sedan går igenom de olika profiltyper som kan konfigureras och slutligen beskrivs hur Autoskala utvärderar vilken profil som ska köras vid en given tidpunkt.

## <a name="autoscale-setting-schema"></a>Autoskala inställningen schema
Följande autoskalningsinställning används för att illustrera Autoskala inställningen schemat. Det är viktigt att Observera att den här Autoskala har:
- En profil 
- Den har två mått regler i den här profilen. en skalbar och en för skalan.
- Skalbar regeln utlöses när den virtuella datorns skaluppsättning genomsnittliga procentandelen CPU mått är större än 85% för de senaste 10 min.
- Skala i regeln utlöses när den virtuella datorns skaluppsättning genomsnittet är mindre än 60% för den senaste minuten.

> [!NOTE]
> En inställning kan ha flera profiler, hoppa till den [profiler](#autoscale-profiles) avsnittet om du vill veta mer.
> En profil kan också ha flera skalbar regler och skala i regler, hoppa till den [utvärdering avsnittet](#autoscale-evaluation) att se hur de utvärderas

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
| Profil | namn | Namnet på profilen som du kan välja vilket namn som hjälper dig att identifiera profilen. |
| Profil | Capacity.maximum | Den högsta tillåtna kapacitet. Det garanterar att Autoskala när du kör den här profilen skalas inte för din resurs ovanför det här numret. |
| Profil | Capacity.minimum | Den minsta kapaciteten som tillåts. Det garanterar att Autoskala när du kör den här profilen skalas inte för din resurs under det här värdet. |
| Profil | Capacity.default | Om det inte går att läsa resurs mått (i det här fallet processorn ”vmss1”) och den aktuella kapaciteten är standard-kapaciteten sedan att säkerställa tillgängligheten för resursen, Autoskala skala ut till standardinställningarna. Om den aktuella kapaciteten redan är högre än standardkapaciteten Autoskala kommer inte skala in. |
| Profil | regler | Autoskala skalas automatiskt mellan de högsta och lägsta kapaciteter med regler i profilen. Du kan ha flera regler i en profil. Grundläggande scenario är att ha två regler, en för att avgöra när du ska skalbar och den andra för att avgöra när du ska skala i. |
| regel | metricTrigger | Definierar mått villkoren i regeln. |
| metricTrigger | metricName | Namnet på måttet. |
| metricTrigger |  metricResourceUri | Resurs-ID för den resurs som genererar måttet. I de flesta fall är det samma som den resurs som skalas. I vissa fall kan det vara olika, till exempel kan du skala en skaluppsättning för virtuell dator baserat på antalet meddelanden i en kö för lagring. |
| metricTrigger | timeGrain | Mått provtagning varaktighet. Till exempel Tidskorn = ”PT1M” innebär att mätvärdena som ska aggregeras varje minut med hjälp av sammanställningsmetod som anges i ”statistik”. |
| metricTrigger | statistik | Sammanställningsmetod inom Tidskorn period. Exempelvis statistik = ”genomsnittliga” och Tidskorn = ”PT1M” innebär att mätvärdena som ska aggregeras varje 1 minut med medelvärdet. Den här egenskapen anger hur måttet samplas. |
| metricTrigger | timeWindow | Hur lång tid att gå tillbaka för mått. Till exempel värdet timeWindow = ”PT10M” innebär att varje gång Autoskala körs frågar mått för de senaste 10 minuterna. Tidsfönstret kan din mått anges och undviker att reagera på tillfälliga toppar. |
| metricTrigger | timeAggregation | Aggregeringen-metod som används för att aggregera provade mått. Till exempel TimeAggregation = ”genomsnittliga” bör aggregera provade mått genom att ta medelvärdet. Ta tio 1 minut exemplen i fallet ovan och genomsnittlig dem. |
| regel | scaleAction | Åtgärd att vidta när metricTrigger regelns utlöses. |
| scaleAction | riktning | ”Öka” om du vill skala ut, ”minska” om du vill skala i|
| scaleAction | värde | Hur mycket att öka eller minska resursens kapacitet |
| scaleAction | cooldown | Hur lång tid ska vänta efter en skalningsåtgärden innan skalning igen. Till exempel om cooldown = ”PT10M” och sedan efter en skalningsåtgärden sker Autoskala inte försöker skala igen för en annan 10 minuter. Cooldown är att tillåta mått att hålla efter tillägg eller borttagning av instanser. |

## <a name="autoscale-profiles"></a>Autoskalningsprofiler

Det finns tre typer av autoskalningsprofiler:

1. **Vanliga profil:** vanligaste profil. Om du inte behöver skala din resurs på olika sätt beroende på dag i veckan eller på en viss dag endast måste du ställa in en vanlig profil i din autoskalningsinställning. Den här profilen kan sedan konfigureras med mått regler som bestämmer när att skala ut och när skala in. Du bör bara ha en vanlig profil som har definierats.

    Exempelprofil som används tidigare i den här artikeln är ett exempel på en vanlig profil. Observera att det är också möjligt att ställa in en profil för att skala med en statisk instansantal för din resurs.

2. **Fast datum profil:** med vanlig profilen definierats anta att du har en viktig händelse kommande på 26 December 2017 (PST) och du vill minsta/högsta kapacitet för din resurs för att skilja den dagen, men fortfarande skala på samma mått . I det här fallet bör du lägga till en fast datum profil din inställning profiler lista. Profilen som är konfigurerad för att köras endast på händelsens dag. För varje dag utförs regelbundna profilen.

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
    
3. **Återkommande profil:** den här typen av profil kan du kontrollera att den här profilen används alltid på en viss dag i veckan. Återkommande profiler kan bara ha en starttid, därför de kör förrän nästa återkommande profilen eller fasta datum profil är inställd att starta. En autoskalningsinställning med endast en upprepning profilen kör som profil även om det är en vanlig profil som definierats i samma inställning. De två exemplen nedan visar användningen av den här profilen:

    **Exempel 1 - veckodag vs. Helger** anta att helger du vill din maxkapaciteten ska vara 4 men på vardagar, eftersom du förväntar dig fler belastningen du den maximala kapaciteten för att vara 10. I det här fallet innehåller inställningen två återkommande profiler, en för att köra på helger och den andra i veckodagar.
    Inställningen skulle se ut så här:

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

    Genom att titta på den föregående inställningen, märker du att varje upprepning profil har ett schema, schemat avgör när profilen startar körs. Profilen stoppar körs när det är dags att köra en annan profil.

    I den föregående inställningen, till exempel anges ”weekdayProfile” till börjar med måndag klockan 12, som innebär att den här profilen börjar med måndag som körs med 12.am. Den fortsätter att köra tills lördag 12a.m. när ”weekendProfile” har schemalagts att starta körning.

    **Exempel 2 - kontorstid** Låt oss ta ett annat exempel, kanske du vill ha mått tröskelvärde = ”x” under kontorstid, 21: 00 och 17: 00 och sedan från 05: 00. till 21: 00 Nästa dag, vill du mått tröskelvärdet vara ”y”.
    Inställningen skulle se ut så här:
    
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
    
    Genom att titta på den föregående inställningen påbörjas ”businessHoursProfile” måndag vid 21: 00 och ser körs förrän 05: 00. eftersom det är då ”nonBusinessHoursProfile” startar körs. ”NonBusinessHoursProfile” kör förrän 09 Tisdag och sedan ”businessHoursProfile” tar över. Detta upprepas tills fredag 17: 00, då ”nonBusinessHoursProfile” kör ända till måndag 21: 00 eftersom ”businessHoursProfile” inte startar köra till måndag 21: 00
    
> [!Note]
> Autoskala UX i Azure portal tvingar sluttider för upprepning profiler och påbörjas den autoskalningsinställning standardprofil between upprepning profiler.
    
## <a name="autoscale-evaluation"></a>Autoskala utvärdering
Anges att Autoskala inställningar kan ha flera autoskalningsprofiler och varje profil kan ha flera mått regler är det viktigt att förstå hur en autoskalningsinställning utvärderas. Varje gång Autoskala jobbkörningar börjar den genom att välja den profil som är tillämpligt, när du har valt profilen Autoskala utvärderar min, max värden och alla mått regler i profilen och avgör om det krävs en skalningsåtgärd.

### <a name="which-profile-will-autoscale-pick"></a>Vilken profil Autoskala hämtar?
- Autoskala letar först efter något fast datum-profil som är konfigurerad för att köras nu, om det finns Autoskala kör den. Om det finns flera fast datum profiler som ska köras, väljer Autoskala först.
- Om det finns inga profiler för fast datum, Autoskala tittar på upprepning profiler, om hittas, sedan den kör den.
- Om det finns ingen fast eller återkommande profiler och sedan Autoskala kör reguljära profilen.

### <a name="how-does-autoscale-evaluate-multiple-rules"></a>Hur utvärderar Autoskala flera regler?

När Autoskala avgör vilken profil som ska köra, startar det genom att utvärdera skalbar regeln i profilen (regler med riktning = ”öka”).
- Om en eller flera regler för skalbar utlöses, beräknar Autoskala ny kapacitet bestäms av scaleAction för var och en av dessa regler. Sedan den skala ut maximalt för de kapaciteterna så tjänsttillgänglighet.
- Exempel: om det finns en skaluppsättning för virtuell dator med en aktuell kapacitet på 10 och det finns två skalbara regler. en som ökar kapaciteten med 10% och en som ökar kapaciteten med 3. Den första regeln skulle resultera i en ny kapacitet på 11 och den andra regeln skulle resultera i en kapacitet på 13. För att säkerställa tjänsttillgänglighet väljer Autoskala den åtgärd som resulterar i maxkapaciteten, så den andra regeln är valt.

Om inga skalbar regler har utlösts Autoskala utvärderas alla skala i regler (regler med riktning = ”minska”). Autoskala tar bara en åtgärd för skala om alla regler i skala utlösts.
- Autoskala beräknar den nya kapacitet som bestäms av scaleAction för var och en av dessa regler. Därefter väljs skalningsåtgärd som resulterar i de kapaciteterna så tjänsttillgänglighet maximalt.
- Exempel: om det finns en skaluppsättning för virtuell dator med en aktuell kapacitet på 10 och det finns två skala i regler. en som minskar kapacitet med 50% och en som minskar kapacitet med 3. Den första regeln skulle resultera i en ny kapacitet på 5 och den andra regeln skulle resultera i en kapacitet på 7. För att säkerställa tjänsttillgänglighet väljer Autoskala den åtgärd som resulterar i maxkapaciteten, så den andra regeln är valt.

## <a name="next-steps"></a>Nästa steg
Om du vill veta finns mer om Autoskala i följande resurser:

* [Översikt över Autoskala](monitoring-overview-autoscale.md)
* [Azure övervakaren Autoskala vanliga mått](insights-autoscale-common-metrics.md)
* [Metodtips för Azure-Monitor Autoskala](insights-autoscale-best-practices.md)
* [Använda automatiska åtgärder för att skicka e-post och webhook aviseringar](insights-autoscale-to-webhook-email.md)
* [Autoskala REST API](https://msdn.microsoft.com/library/dn931953.aspx)
