---
title: Samla in Azure-resursloggar i Log Analytics-arbetsytan
description: Lär dig hur du streamar Azure-resursloggar till en Log Analytics-arbetsyta i Azure Monitor.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 36bd464624118b7671a3879bcc1d34114bba9ce3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248598"
---
# <a name="collect-azure-platform-logs-in-log-analytics-workspace-in-azure-monitor"></a>Samla in Azure-plattformsloggar i Log Analytics-arbetsytan i Azure Monitor
[Plattformsloggar](platform-logs-overview.md) i Azure, inklusive Azure Activity-logg och resursloggar, tillhandahåller detaljerad diagnostik- och granskningsinformation för Azure-resurser och den Azure-plattform de är beroende av. I den här artikeln beskrivs hur du samlar in resursloggar på en Log Analytics-arbetsyta som gör att du kan analysera den med andra övervakningsdata som samlats in i Azure Monitor Logs med hjälp av kraftfulla loggfrågor och även utnyttja andra Azure Monitor-funktioner som aviseringar och Visualiseringar. 


## <a name="what-you-can-do-with-platform-logs-in-a-workspace"></a>Vad du kan göra med plattformsloggar på en arbetsyta
Genom att samla in plattformsloggar på en Log Analytics-arbetsyta kan du analysera loggarna för alla dina Azure-resurser tillsammans och dra nytta av alla funktioner som är tillgängliga för [Azure Monitor Logs](data-platform-logs.md) som innehåller följande:

* **Loggfrågor** - Skapa [loggfrågor](../log-query/log-query-overview.md) med ett kraftfullt frågespråk för att snabbt analysera och få insikter i dina diagnostikdata och analysera dem med data som samlats in från andra källor i Azure Monitor.
* **Avisering** – Få proaktiva meddelanden om kritiska villkor och mönster som identifieras i dina resursloggar med hjälp av [loggaviseringar i Azure Monitor](alerts-log.md).
* **Visualiseringar** - Fäst resultatet av en loggfråga på en Azure-instrumentpanel eller inkludera den i en arbetsbok som en del av en interaktiv rapport.

## <a name="prerequisites"></a>Krav
Du måste [skapa en ny arbetsyta](../learn/quick-create-workspace.md) om du inte redan har en. Arbetsytan behöver inte vara i samma prenumeration som resursen som skickar loggar så länge som användaren som konfigurerar inställningen har lämplig RBAC-åtkomst till båda prenumerationerna.

## <a name="create-a-diagnostic-setting"></a>Skapa en diagnostikinställning
Skicka plattformsloggar till en Log Analytics-arbetsyta och andra mål genom att skapa en diagnostikinställning för en Azure-resurs. Mer information finns [i Skapa diagnostikinställning för att samla in loggar och mått i Azure.](diagnostic-settings.md)


## <a name="activity-log-collection"></a>Insamling av aktivitetslogg
Du kan skicka aktivitetsloggen från en enskild prenumeration till upp till fem Log Analytics-arbetsytor. Resursloggdata som samlas in på en Log Analytics-arbetsyta lagras i tabellen **AzureActivity.** 

## <a name="resource-log-collection-mode"></a>Insamlingsläge för resurslogg
Resursloggdata som samlas in på en Log Analytics-arbetsyta lagras i tabeller enligt beskrivningen i [Struktur för Azure Monitor-loggar](../log-query/logs-structure.md). De tabeller som används av resursloggar beror på vilken typ av samling resursen använder:

- Azure diagnostik - Alla data som skrivs är till _AzureDiagnostics_ tabellen.
- Resursspecifik - Data skrivs till enskilda tabeller för varje kategori av resursen.

### <a name="azure-diagnostics-mode"></a>Azure Diagnostics-läge 
I det här läget samlas alla data från alla [diagnostikinställningar](diagnostic-settings.md) in i tabellen _AzureDiagnostics._ Det här är den äldre metoden som används idag av de flesta Azure-tjänster.

Eftersom flera resurstyper skickar data till samma tabell är dess schema superuppsättningen för scheman för alla olika datatyper som samlas in.

Tänk på följande exempel där diagnostikinställningar samlas in på samma arbetsyta för följande datatyper:

- Granskningsloggar för tjänst 1 (med ett schema bestående av kolumnerna A, B och C)  
- Felloggar för tjänst 1 (med ett schema som består av kolumnerna D, E och F)  
- Granskningsloggar för tjänst 2 (med ett schema bestående av kolumnerna G, H och I)  

Tabellen AzureDiagnostics ser ut så här:  

| ResourceProvider    | Kategori     | A  | B  | C  | D  | E  | F  | G  | H  | I  |
| -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
| Microsoft.Service1 | AuditLogs    | x1 (på andra) | y1 (y1) | Z1 |    |    |    |    |    |    |
| Microsoft.Service1 | ErrorLogs    |    |    |    | Q1 | w1 (på andra) | E1 (på andra) sätt |    |    |    |
| Microsoft.Service2 | AuditLogs    |    |    |    |    |    |    | j1 (på) | k1 (på andra) | l1 (1) |
| Microsoft.Service1 | ErrorLogs    |    |    |    | q2 (på andra) | w2 (på andra) | e2 (på andra) sätt |    |    |    |
| Microsoft.Service2 | AuditLogs    |    |    |    |    |    |    | j3 (på andra) | k3 (K3) | l3 (på andra) |
| Microsoft.Service1 | AuditLogs    | x5 (på ett sätt) | y5 (y5) | z5 (på ett sätt) |    |    |    |    |    |    |
| ... |

### <a name="resource-specific"></a>Resursspecifik
I det här läget skapas enskilda tabeller på den valda arbetsytan för varje kategori som valts i diagnostikinställningen. Den här metoden rekommenderas eftersom det gör det mycket enklare att arbeta med data i loggfrågor, ger bättre upptäckbarhet av scheman och deras struktur, förbättrar prestanda för både inmatningsfördröjning och frågetider och möjligheten att bevilja RBAC-rättigheter på en särskild tabell. Alla Azure-tjänster migrerar så småningom till resursspecifikt läge. 

Exemplet ovan skulle resultera i att tre tabeller skapas:
 
- Tabell *Service1AuditLogs* enligt följande:

    | Resursprovider | Kategori | A | B | C |
    | -- | -- | -- | -- | -- |
    | Service1 | AuditLogs | x1 (på andra) | y1 (y1) | Z1 |
    | Service1 | AuditLogs | x5 (på ett sätt) | y5 (y5) | z5 (på ett sätt) |
    | ... |

- Tabell *Service1ErrorLogs* enligt följande:  

    | Resursprovider | Kategori | D | E | F |
    | -- | -- | -- | -- | -- | 
    | Service1 | ErrorLogs |  Q1 | w1 (på andra) | E1 (på andra) sätt |
    | Service1 | ErrorLogs |  q2 (på andra) | w2 (på andra) | e2 (på andra) sätt |
    | ... |

- Tabell *Service2AuditLogs* enligt följande:  

    | Resursprovider | Kategori | G | H | I |
    | -- | -- | -- | -- | -- |
    | Service2 | AuditLogs | j1 (på) | k1 (på andra) | l1 (1)|
    | Service2 | AuditLogs | j3 (på andra) | k3 (K3) | l3 (på andra)|
    | ... |



### <a name="select-the-collection-mode"></a>Välj insamlingsläge
De flesta Azure-resurser skriver data till arbetsytan i antingen **Azure Diagnostic** eller **Resource-Specific mode** utan att ge dig ett val. Mer information om vilket läge den använder finns i [dokumentationen för varje tjänst.](diagnostic-logs-schema.md) Alla Azure-tjänster kommer så småningom att använda resursspecifikt läge. Som en del av den här övergången kan du välja ett läge i diagnostikinställningen med vissa resurser. Du bör ange resursspecifikt läge för alla nya diagnostikinställningar eftersom det gör data enklare att hantera och kan hjälpa dig att undvika komplexa migreringar vid ett senare tillfälle.
  
   ![Funktionslägesväljare för diagnostikinställningar](media/resource-logs-collect-workspace/diagnostic-settings-mode-selector.png)




> [!NOTE]
> För närvarande kan **Azure-diagnostik** och **resursspecifikt** läge endast väljas när du konfigurerar diagnostikinställningen i Azure-portalen. Om du konfigurerar inställningen med CLI, PowerShell eller Rest API, kommer den som standard att **Azure-diagnostik**.

Du kan ändra en befintlig diagnostikinställning till resursspecifikt läge. I det här fallet finns data som redan har samlats in kvar i tabellen _AzureDiagnostics_ tills den tas bort enligt lagringsinställningen för arbetsytan. Nya data kommer att samlas in i den särskilda tabellen. Använd [unionsoperatorn](https://docs.microsoft.com/azure/kusto/query/unionoperator) för att fråga data över båda tabellerna.

Fortsätt att titta på [Azure Updates-bloggen](https://azure.microsoft.com/updates/) för meddelanden om Azure-tjänster som stöder resursspecifikt läge.

### <a name="column-limit-in-azurediagnostics"></a>Kolumngräns i AzureDiagnostics
Det finns en egenskapsgräns på 500 för alla tabeller i Azure Monitor Logs. När den här gränsen har nåtts kommer alla rader som innehåller data med någon egenskap utanför de första 500 att tas bort vid inmatningstiden. *AzureDiagnostics-tabellen* är i synnerhet känslig för den här gränsen eftersom den innehåller egenskaper för alla Azure-tjänster som skriver till den.

Om du samlar in resursloggar från flera tjänster kan _AzureDiagnostics överskrida_ den här gränsen och data kommer att missas. Tills alla Azure-tjänster stöder resursspecifikt läge bör du konfigurera resurser för att skriva till flera arbetsytor för att minska möjligheten att nå kolumngränsen på 500.

### <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory, på grund av en mycket detaljerad uppsättning loggar, är en tjänst som är känd för att skriva ett stort antal kolumner och potentiellt orsaka _AzureDiagnostics_ att överskrida sin gräns. För alla diagnostikinställningar som konfigurerats innan det resursspecifika läget aktiverades skapas en ny kolumn för varje unikt namngiven användarparameter mot alla aktiviteter. Fler kolumner skapas på grund av den utförliga karaktären av aktivitetsindata och utdata.
 
Du bör migrera loggarna för att använda det resursspecifika läget så snart som möjligt. Om du inte kan göra det omedelbart är ett tillfälligt alternativ att isolera Azure Data Factory-loggar till sin egen arbetsyta för att minimera risken för att dessa loggar påverkar andra loggtyper som samlas in i dina arbetsytor.


## <a name="next-steps"></a>Nästa steg

* [Läs mer om resursloggar](platform-logs-overview.md).
* [Skapa diagnostikinställning för att samla in loggar och mått i Azure](diagnostic-settings.md).
