---
title: Förstå inställningar för automatisk skalning i Azure Monitor
description: En detaljerad uppdelning av inställningar för automatisk skalning och hur de fungerar. Gäller virtuella datorer, molntjänster, webbappar
ms.topic: conceptual
ms.date: 12/18/2017
ms.subservice: autoscale
ms.openlocfilehash: 9a2b94208de7ce490a0e7acfbb71175b4a7c846e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75364313"
---
# <a name="understand-autoscale-settings"></a>Förstå inställningarna för automatisk skalning
Inställningar för automatisk skalning säkerställer att du har rätt mängd resurser som körs för att hantera den fluktuerande belastningen på ditt program. Du kan konfigurera inställningar för automatisk skalning som ska utlösas baserat på mått som anger belastning eller prestanda, eller utlösas vid ett schemalagt datum och en schemalagd tidpunkt. Den här artikeln tar en detaljerad titt på anatomin i en automatisk skalningsinställning. Artikeln börjar med schemat och egenskaperna för en inställning och går sedan igenom de olika profiltyper som kan konfigureras. Slutligen beskrivs i artikeln hur funktionen Automatisk skalning i Azure utvärderar vilken profil som ska köras vid en viss tidpunkt.

## <a name="autoscale-setting-schema"></a>Inställningsschema för automatisk skalning
För att illustrera inställningsschemat för automatisk skalning används följande inställning för automatisk skalning. Det är viktigt att notera att den här inställningen för automatisk skalning har:
- En profil. 
- Två måttregler i den här profilen: en för utskalning och en för skala in.
  - Skalningsregeln utlöses när skaluppsättningen för den virtuella datorns skala är större än 85 procent under de senaste 10 minuterna.
  - Skalningsregeln utlöses när den virtuella datorns skaluppsättnings genomsnitt är mindre än 60 procent för den senaste minuten.

> [!NOTE]
> En inställning kan ha flera profiler. Mer information finns i [avsnittet profiler.](#autoscale-profiles) En profil kan också ha flera skalningsregler och skalningsregler definierade. Mer information om hur de [evaluation](#autoscale-evaluation) utvärderas finns i utvärderingsavsnittet.

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
| Inställning | ID | Resurs-ID för automatisk skalning. Inställningar för automatisk skalning är en Azure Resource Manager-resurs. |
| Inställning | namn | Inställningsnamnet För automatisk skalning. |
| Inställning | location | Platsen för inställningen Automatisk skalning. Den här platsen kan skilja sig från platsen för den resurs som skalas. |
| properties | targetResourceUri | Resurs-ID för den resurs som skalas. Du kan bara ha en inställning för automatisk skalning per resurs. |
| properties | Profiler | En automatisk skalningsinställning består av en eller flera profiler. Varje gång motorn för automatisk skalning körs en profil. |
| profil | namn | Namnet på profilen. Du kan välja vilket namn som helst som hjälper dig att identifiera profilen. |
| profil | Kapacitet.maximal | Den maximala tillåtna kapaciteten. Det säkerställer att Automatisk skalning, när du kör den här profilen, inte skala din resurs över detta nummer. |
| profil | Kapacitet.minimum | Minsta tillåtna kapacitet. Det säkerställer att Automatisk skalning, när du kör den här profilen, inte skala din resurs under detta nummer. |
| profil | Capacity.default | Om det finns ett problem med att läsa resursmåttet (i det här fallet processorn för "vmss1" och den aktuella kapaciteten är lägre än standardvärdet skalas automatisk skalning ut till standardvärdet. Detta för att säkerställa resursens tillgänglighet. Om den aktuella kapaciteten redan är högre än standardkapaciteten skalas inte automatisk skalning in. |
| profil | regler | Automatisk skalning skalas automatiskt mellan maximal och minimal kapacitet, med hjälp av reglerna i profilen. Du kan ha flera regler i en profil. Vanligtvis finns det två regler: en för att avgöra när du ska skala ut och den andra för att avgöra när du ska skala in. |
| Regel | metricTrigger | Definierar regelns måttvillkor. |
| metricTrigger | metricName | Namnet på måttet. |
| metricTrigger |  metricResourceUri | Resurs-ID för den resurs som avger måttet. I de flesta fall är det samma som resursen som skalas. I vissa fall kan det vara annorlunda. Du kan till exempel skala en skala för den virtuella datorn baserat på antalet meddelanden i en lagringskö. |
| metricTrigger | tidGrain | Den metriska samplingstiden. **TimeGrain = "PT1M"** innebär till exempel att måtten ska aggregeras var 1 minut med hjälp av den aggregeringsmetod som anges i statistikelementet. |
| metricTrigger | Statistik | Aggregeringsmetoden inom tidenGrainperiod. Statistik **= "Medel"** och **timeGrain = "PT1M"** innebär till exempel att måtten ska aggregeras var 1 minut genom att ta medelvärdet. Den här egenskapen avgör hur måttet samplas. |
| metricTrigger | tidWindow | Hur lång tid det tar att se tillbaka efter mått. Till exempel **timeWindow = "PT10M"** innebär att varje gång Automatisk skalning körs, frågar den mått för de senaste 10 minuterna. Tidsfönstret gör att dina mått kan normaliseras och undviker att reagera på tillfälliga toppar. |
| metricTrigger | timeAggregation | Aggregeringsmetoden som används för att sammanställa de samplade måtten. **Tidsaggregering = "Medel"** bör till exempel aggregera de samplade måtten genom att ta medelvärdet. I föregående fall, ta tio 1-minuters prover, och genomsnitt dem. |
| Regel | scaleAction | Åtgärden som ska vidtas när mettrigger av regeln utlöses. |
| scaleAction | riktning | "Öka" för att skala ut, eller "Minska" för att skala in.|
| scaleAction | värde | Hur mycket för att öka eller minska resursens kapacitet. |
| scaleAction | cooldown | Hur lång tid det ska ta att vänta efter en skalning innan du skalar igen. Om du till exempel **använder cooldown = "PT10M"** försöker automatisk skalning inte skalas igen i ytterligare 10 minuter. Den cooldown är att låta måtten stabiliseras efter tillägg eller borttagning av instanser. |

## <a name="autoscale-profiles"></a>Profiler för automatisk skalning

Det finns tre typer av profiler för automatisk skalning:

- **Regelbunden profil:** Den vanligaste profilen. Om du inte behöver skala resursen baserat på veckodagen eller en viss dag kan du använda en vanlig profil. Den här profilen kan sedan konfigureras med måttregler som bestämmer när du ska skala ut och när du ska skala in. Du bör bara ha en vanlig profil definierad.

    Exempelprofilen som används tidigare i den här artikeln är ett exempel på en vanlig profil. Observera att det också är möjligt att ange en profil för att skala till ett statiskt instansantal för din resurs.

- **Profil för fast datum:** Denna profil är för speciella fall. Anta till exempel att du har en viktig händelse som kommer upp den 26 december 2017 (PST). Du vill att resursens lägsta och maximala kapacitet ska vara annorlunda den dagen, men ändå skalas på samma mått. I det här fallet bör du lägga till en fast datumprofil i inställningens lista över profiler. Profilen är konfigurerad för att köras endast på händelsens dag. För alla andra dagar använder Automatisk skalning den vanliga profilen.

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
    
- **Profil för återkommande:** Med den här typen av profil kan du se till att den här profilen alltid används en viss veckodag. Återkommande profiler har bara en starttid. De körs tills nästa återkommande profil eller fast datumprofil är inställd på att starta. En automatisk skalningsinställning med endast en återkommande profil kör den profilen, även om det finns en vanlig profil definierad i samma inställning. Följande två exempel illustrerar hur den här profilen används:

    **Exempel 1: Vardagar kontra helger**
    
    Låt oss säga att på helgerna vill du att din maximala kapacitet ska vara 4. På vardagar, eftersom du förväntar dig mer belastning, vill du att din maximala kapacitet ska vara 10. I det här fallet skulle inställningen innehålla två återkommande profiler, en att köra på helger och den andra på vardagar.
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

    Inställningen föregående visar att varje återkommande profil har ett schema. Det här schemat avgör när profilen börjar köras. Profilen stannar när det är dags att köra en annan profil.

    I föregående inställning är till exempel "weekdayProfile" inställt på att starta på måndag klockan 12:00. Det innebär att profilen börjar köras på måndag klockan 12:00. Det fortsätter fram till lördag kl 12:00, när "weekendProfile" är planerad att börja köras.

    **Exempel 2: Öppettider**
    
    Anta att du vill ha ett måtttröskel under kontorstid (9:00 till 17:00) och en annan för alla andra tider. Inställningen skulle se ut så här:
    
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
    
    Den föregående inställningen visar att "businessHoursProfile" börjar köras på måndag klockan 9:00 och fortsätter till 17:00. Det är då "nonBusinessHoursProfile" börjar köras. Den "nonBusinessHoursProfile" löper fram till 09:00 tisdag, och sedan "businessHoursProfile" tar över igen. Detta upprepas fram till fredag kl 17:00. Vid den tidpunkten, "nonBusinessHoursProfile" körs hela vägen till måndag kl 9:00.
    
> [!Note]
> Användargränssnittet för automatisk skalning i Azure-portalen framtvingar sluttider för återkommande profiler och börjar köra inställningen Automatisk skalningsinställningens standardprofil mellan återkommande profiler.
    
## <a name="autoscale-evaluation"></a>Utvärdering av automatisk skalning
Med tanke på att inställningar för automatisk skalning kan ha flera profiler och varje profil kan ha flera måttregler, är det viktigt att förstå hur en automatisk skalningsinställning utvärderas. Varje gång jobbet Automatisk skalning körs börjar det med att välja den profil som gäller. Sedan utvärderar Automatisk skalning minimi- och maximivärdena och eventuella måttregler i profilen och avgör om en skalningsåtgärd är nödvändig.

### <a name="which-profile-will-autoscale-pick"></a>Vilken profil ska autoskala välja?

Automatisk skalning använder följande sekvens för att välja profilen:
1. Den letar först efter en fast datumprofil som är konfigurerad för att köras nu. Om så är det körs den automatiskt. Om det finns flera fasta datumprofiler som ska köras väljer Automatisk skalning den första.
2. Om det inte finns några fasta datumprofiler tittar Automatisk skalning på återkommande profiler. Om en återkommande profil hittas körs den.
3. Om det inte finns några fasta datum- eller återkommande profiler körs den vanliga profilen automatiskt.

### <a name="how-does-autoscale-evaluate-multiple-rules"></a>Hur utvärderar Automatisk skalning flera regler?

När Automatisk skalning har fastställt vilken profil som ska köras utvärderas alla utskalningsregler i profilen (det här är regler med **riktning = "Ökning"**).

Om en eller flera utskalningsregler utlöses beräknar Automatisk skalning den nya kapacitet som bestäms av **skalanÅtgärd** för var och en av dessa regler. Sedan skalas den ut till den maximala kapaciteten för att säkerställa tjänstens tillgänglighet.

Anta till exempel att det finns en skalauppsättning för virtuella datorer med en aktuell kapacitet på 10. Det finns två utskalningsregler: en som ökar kapaciteten med 10 procent och en som ökar kapaciteten med 3 räknas. Den första regeln skulle resultera i en ny kapacitet på 11, och den andra regeln skulle resultera i en kapacitet på 13. För att säkerställa tjänstens tillgänglighet väljer automatisk skalning den åtgärd som resulterar i maximal kapacitet, så den andra regeln väljs.

Om inga utskalningsregler utlöses utvärderar Automatisk skalning alla regler för inskalning (regler med **riktning = "Minskning").** Automatisk skalning kräver bara en skalningsåtgärd om alla skalningsregler utlöses.

Automatisk skalning beräknar den nya kapaciteten som bestäms av **skalanÅtgärd** av var och en av dessa regler. Sedan väljer den skala åtgärd som resulterar i maximalt av dessa kapaciteter för att säkerställa tjänstens tillgänglighet.

Anta till exempel att det finns en skalauppsättning för virtuella datorer med en aktuell kapacitet på 10. Det finns två skalningsregler: en som minskar kapaciteten med 50 procent och en som minskar kapaciteten med 3 antal. Den första regeln skulle resultera i en ny kapacitet på 5, och den andra regeln skulle resultera i en kapacitet på 7. För att säkerställa tjänstens tillgänglighet väljer automatisk skalning den åtgärd som resulterar i maximal kapacitet, så den andra regeln väljs.

## <a name="next-steps"></a>Nästa steg
Läs mer om Automatisk skalning genom att läsa följande:

* [Översikt över automatisk skalning](../../azure-monitor/platform/autoscale-overview.md)
* [Azure Monitor automatisk skalning vanliga mått](../../azure-monitor/platform/autoscale-common-metrics.md)
* [Metodtips för autoskalning i Azure Monitor](../../azure-monitor/platform/autoscale-best-practices.md)
* [Använda åtgärder för automatisk skalning för att skicka aviseringar om e-post och webhook](../../azure-monitor/platform/autoscale-webhook-email.md)
* [REST-API för automatisk skalning](https://msdn.microsoft.com/library/dn931953.aspx)

