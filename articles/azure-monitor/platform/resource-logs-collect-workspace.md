---
title: Samla in Azures resurs loggar i Log Analytics arbets yta
description: Lär dig hur du direktuppspelar Azures resurs loggar till en Log Analytics arbets yta i Azure Monitor.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 36bd464624118b7671a3879bcc1d34114bba9ce3
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78391050"
---
# <a name="collect-azure-platform-logs-in-log-analytics-workspace-in-azure-monitor"></a>Samla in Azure-plattforms loggar i Log Analytics arbets yta i Azure Monitor
[Plattforms loggar](platform-logs-overview.md) i Azure, inklusive Azure aktivitets logg och resurs loggar, ger detaljerad diagnostik och gransknings information för Azure-resurser och Azure-plattformen som de är beroende av. I den här artikeln beskrivs hur du samlar in resurs loggar på en Log Analytics arbets yta som gör att du kan analysera den med andra övervaknings data som samlas in i Azure Monitor loggar med kraftfulla logg frågor och även för att utnyttja andra Azure Monitor funktioner, till exempel aviseringar och visualiseringar. 


## <a name="what-you-can-do-with-platform-logs-in-a-workspace"></a>Vad du kan göra med plattforms loggar i en arbets yta
Genom att samla in plattforms loggar på en Log Analytics arbets yta kan du analysera loggarna för alla dina Azure-resurser tillsammans och dra nytta av alla funktioner som är tillgängliga för [Azure Monitor loggar](data-platform-logs.md) som innehåller följande:

* **Logg frågor** – skapa [logg frågor](../log-query/log-query-overview.md) med ett kraftfullt frågespråk för att snabbt analysera och få insikter om dina diagnostikdata och analysera dem med data som samlas in från andra källor i Azure Monitor.
* **Avisering** – få proaktiva meddelanden om kritiska villkor och mönster som identifieras i resurs loggarna med [logg aviseringar i Azure Monitor](alerts-log.md).
* **Visualiseringar** – fäst resultatet av en logg fråga på en Azure-instrumentpanel eller ta med den i en arbets bok som en del av en interaktiv rapport.

## <a name="prerequisites"></a>Förutsättningar
Du måste [skapa en ny arbets yta](../learn/quick-create-workspace.md) om du inte redan har en. Arbets ytan behöver inte finnas i samma prenumeration som resursen som skickar loggar så länge som den användare som konfigurerar inställningen har lämplig RBAC-åtkomst till båda prenumerationerna.

## <a name="create-a-diagnostic-setting"></a>Skapa en diagnostisk inställning
Skicka plattforms loggar till en Log Analytics arbets yta och andra destinationer genom att skapa en diagnostisk inställning för en Azure-resurs. Mer information finns i [skapa diagnostisk inställning för insamling av loggar och mått i Azure](diagnostic-settings.md) .


## <a name="activity-log-collection"></a>Aktivitets logg samling
Du kan skicka aktivitets loggen från en enskild prenumeration till upp till fem Log Analytics arbets ytor. Resurs logg data som samlas in i en Log Analytics-arbetsyta lagras i tabellen **AzureActivity** . 

## <a name="resource-log-collection-mode"></a>Läge för resurs logg insamling
Resurs logg data som samlas in i en Log Analytics-arbetsyta lagras i tabeller enligt beskrivningen i [strukturen i Azure Monitor loggar](../log-query/logs-structure.md). Tabellerna som används av resurs loggar beror på vilken typ av samling resursen använder:

- Azure Diagnostics – alla data som skrivs är till _AzureDiagnostics_ -tabellen.
- Resurs specifika data skrivs till en enskild tabell för varje resurs kategori.

### <a name="azure-diagnostics-mode"></a>Azure-diagnostik läge 
I det här läget samlas alla data från alla [diagnostiska inställningar](diagnostic-settings.md) i _AzureDiagnostics_ -tabellen. Detta är den äldre metoden som används idag av de flesta Azure-tjänster.

Eftersom flera resurs typer skickar data till samma tabell, är schemats supermängd till schemana för alla olika data typer som samlas in.

Tänk på följande exempel där diagnostikinställningar samlas in i samma arbets yta för följande data typer:

- Gransknings loggar för tjänst 1 (med ett schema som består av kolumnerna A, B och C)  
- Fel loggarna för tjänsten 1 (med ett schema bestående av kolumnerna D, E och F)  
- Gransknings loggar för tjänst 2 (med ett schema som består av kolumnerna G, H och I)  

AzureDiagnostics-tabellen ser ut så här:  

| ResourceProvider    | Kategori     | A  | B  | C  | D  | E  | F  | Projektredovisnings  | H  | I  |
| -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
| Microsoft. Service1 | AuditLogs    | x1 | Y1 | z1 |    |    |    |    |    |    |
| Microsoft. Service1 | ErrorLogs    |    |    |    | q1 | W1 | e1 |    |    |    |
| Microsoft. service2 | AuditLogs    |    |    |    |    |    |    | j1 | K1 | L1 |
| Microsoft. Service1 | ErrorLogs    |    |    |    | q2 | w2 | e2 |    |    |    |
| Microsoft. service2 | AuditLogs    |    |    |    |    |    |    | j3 | k3 | l3 |
| Microsoft. Service1 | AuditLogs    | x5 | y5 | z5 |    |    |    |    |    |    |
| ... |

### <a name="resource-specific"></a>Resurs-/regionsspecifika
I det här läget skapas enskilda tabeller i den valda arbets ytan för varje kategori som väljs i den diagnostiska inställningen. Den här metoden rekommenderas eftersom det gör det mycket enklare att arbeta med data i logg frågor, vilket ger bättre identifiering av scheman och deras struktur, förbättrar prestandan för både svars tid och fråge tider och möjligheten att bevilja RBAC-rättigheter på en speciell tabell. Alla Azure-tjänster kommer slutligen att migreras till det resursbaserade läget. 

Exemplet ovan skulle resultera i att tre tabeller skapas:
 
- Tabell *Service1AuditLogs* enligt följande:

    | Resurs leverantör | Kategori | A | B | C |
    | -- | -- | -- | -- | -- |
    | Service1 | AuditLogs | x1 | Y1 | z1 |
    | Service1 | AuditLogs | x5 | y5 | z5 |
    | ... |

- Tabell *Service1ErrorLogs* enligt följande:  

    | Resurs leverantör | Kategori | D | E | F |
    | -- | -- | -- | -- | -- | 
    | Service1 | ErrorLogs |  q1 | W1 | e1 |
    | Service1 | ErrorLogs |  q2 | w2 | e2 |
    | ... |

- Tabell *Service2AuditLogs* enligt följande:  

    | Resurs leverantör | Kategori | Projektredovisnings | H | I |
    | -- | -- | -- | -- | -- |
    | Service2 | AuditLogs | j1 | K1 | L1|
    | Service2 | AuditLogs | j3 | k3 | l3|
    | ... |



### <a name="select-the-collection-mode"></a>Välj samlings läge
De flesta Azure-resurser kommer att skriva data till arbets ytan i **Azure-diagnostik** eller **resurs särskilt läge** utan att du behöver välja något. I [dokumentationen för varje tjänst](diagnostic-logs-schema.md) finns information om vilket läge den använder. Alla Azure-tjänster kommer att använda resurs-/regionsspecifika läge. Som en del av den här över gången kan vissa resurser välja ett läge i den diagnostiska inställningen. Du bör ange ett resurs alternativ för alla nya diagnostikinställningar eftersom det gör det enklare att hantera data och kan hjälpa dig att undvika komplexa migreringar vid ett senare tillfälle.
  
   ![Läges väljare för diagnostiska inställningar](media/resource-logs-collect-workspace/diagnostic-settings-mode-selector.png)




> [!NOTE]
> För närvarande går det bara att välja **Azure Diagnostics** och **resurs särskilt** läge när du konfigurerar den diagnostiska inställningen i Azure Portal. Om du konfigurerar inställningen med CLI-, PowerShell-eller REST-API är det standardinställningen för **Azure-diagnostik**.

Du kan ändra en befintlig diagnostisk inställning till ett resurs speciellt läge. I det här fallet finns data som redan har samlats in kvar i _AzureDiagnostics_ -tabellen tills den tas bort enligt inställningen för kvarhållning för arbets ytan. Nya data samlas in i den dedikerade tabellen. Använd [union](https://docs.microsoft.com/azure/kusto/query/unionoperator) -operatorn för att fråga efter data i båda tabellerna.

Fortsätt att titta på [Azures uppdaterings](https://azure.microsoft.com/updates/) blogg om du vill veta mer om Azure-tjänster som stöder resurs-/regionsspecifika läge.

### <a name="column-limit-in-azurediagnostics"></a>Kolumn begränsning i AzureDiagnostics
Det finns en gräns på 500-egenskapen för alla tabeller i Azure Monitor loggar. När den här gränsen har uppnåtts raderas alla rader som innehåller data med en egenskap utanför de första 500 vid inmatnings tiden. *AzureDiagnostics* -tabellen är särskilt känslig för den här gränsen eftersom den innehåller egenskaper för alla Azure-tjänster som skriver till den.

Om du samlar in resurs loggar från flera tjänster kan _AzureDiagnostics_ överskrida den här gränsen och data kommer att saknas. Tills alla Azure-tjänster har stöd för det resursbaserade läget bör du konfigurera resurser för att skriva till flera arbets ytor för att minska risken för att nå 500-kolumn gränsen.

### <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory, på grund av en mycket detaljerad uppsättning loggar, är en tjänst som är känd för att skriva ett stort antal kolumner och eventuellt orsaka att _AzureDiagnostics_ överskrider gränsen. För alla diagnostikinställningar som kon figurer ATS innan det resurs-specifika läget har Aktiver ATS kommer det att finnas en ny kolumn som skapats för varje unik, namngiven användar parameter mot valfri aktivitet. Fler kolumner kommer att skapas på grund av den utförliga typen av aktivitets indata och utdata.
 
Du bör migrera loggarna för att använda det resursbaserade läget så snart som möjligt. Om du inte kan göra det direkt är det ett interimistiskt alternativ att isolera Azure Data Factory loggar i sin egen arbets yta för att minimera risken för att dessa loggar påverkar andra logg typer som samlas in i dina arbets ytor.


## <a name="next-steps"></a>Nästa steg

* [Läs mer om resurs loggar](platform-logs-overview.md).
* [Skapa en diagnostisk inställning för insamling av loggar och mått i Azure](diagnostic-settings.md).
