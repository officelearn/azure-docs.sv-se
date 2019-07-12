---
title: Stream, Azure-diagnostikloggar till Log Analytics-arbetsyta i Azure Monitor
description: Lär dig mer om att strömma Azure diagnostikloggar till Log Analytics-arbetsytan i Azure Monitor.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: e8e6276a38f06b5c6ebb24c89f3733b9fd7220f7
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612831"
---
# <a name="stream-azure-diagnostic-logs-to-log-analytics-workspace-in-azure-monitor"></a>Stream, Azure-diagnostikloggar till Log Analytics-arbetsyta i Azure Monitor

**[Azure diagnostikloggar](diagnostic-logs-overview.md)**  kan strömmas i nära realtid till en Log Analytics-arbetsyta i Azure Monitor med portalen, PowerShell-cmdletar eller Azure CLI.

## <a name="what-you-can-do-with-diagnostics-logs-in-a-log-analytics-workspace"></a>Vad du kan göra med diagnostik loggar i en Log Analytics-arbetsyta

Azure Monitor innehåller ett flexibelt log frågor och analys-verktyg som hjälper dig att få insyn i obearbetad data som genereras från Azure-resurser. Vissa funktioner är följande:

* **Loggfråga** -skrivning avancerade frågor över dina loggdata, korrelera loggar från olika källor och skapa diagram som kan fästas på instrumentpanelen i Azure.
* **Aviseringar** – identifiera när en eller flera händelser som matchar en viss fråga och få ett meddelande med en e-post eller webhook-anrop med hjälp av Azure Monitor-aviseringar.
* **Avancerade analyser** – Använd maskininlärning och mönstret matchande algoritmer för att identifiera eventuella problem som visas med dina loggar.

## <a name="enable-streaming-of-diagnostic-logs-to-log-analytics-workspace"></a>Kunna strömma diagnostikloggar till Log Analytics-arbetsyta

Du kan aktivera strömning av diagnostikloggar programmässigt, via portalen, eller med hjälp av den [Azure Monitor REST API: er](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings). Oavsett hur du skapar en diagnostikinställning i som du anger en Log Analytics-arbetsyta och loggkategorier och mått som du vill skicka i till arbetsytan. En diagnostik **loggkategori** är en typ av logg som en resurs kan ge.

Log Analytics-arbetsytan behöver inte finnas i samma prenumeration som resursen loggarna så länge som den användare som konfigurerar inställningen har lämplig RBAC-åtkomst till båda prenumerationerna.

> [!NOTE]
> Det går för närvarande inte att skicka flerdimensionella mätvärden via diagnostikinställningar. Mått med dimensioner exporteras som tillplattade endimensionella mått som aggregeras över dimensionsvärden.
>
> *Till exempel*: Måttet för inkommande meddelanden i en händelsehubb kan utforskas och visas för varje enskild kö. När måttet exporteras via diagnostikinställningar visas det dock som alla inkommande meddelanden i alla köer i händelsehubben.
>
>

## <a name="stream-diagnostic-logs-using-the-portal"></a>Stream diagnostikloggar med hjälp av portalen
1. I portalen, gå till Azure Monitor och klicka på **diagnostikinställningar** i den **inställningar** menyn.


2. Du kan också filtrera listan efter resursgruppen eller resursen. Klicka sedan på den resurs som du vill ange en diagnostikinställning.

3. Om inga inställningar finns på resursen har du valt, uppmanas du för att skapa en inställning. Klicka på ”Slå på diagnostik”.

   ![Lägg till diagnostikinställning - inga befintliga inställningar](media/diagnostic-logs-stream-log-store/diagnostic-settings-none.png)

   Om det finns befintliga inställningarna på resursen, visas en lista över inställningar som redan har konfigurerats på den här resursen. Klicka på ”Lägg till diagnostikinställning”.

   ![Lägg till diagnostikinställning - befintliga inställningar](media/diagnostic-logs-stream-log-store/diagnostic-settings-multiple.png)

3. Ge din ställa in ett namn och markera kryssrutan för **skicka till Log Analytics**, Välj en Log Analytics-arbetsyta.

   ![Lägg till diagnostikinställning - befintliga inställningar](media/diagnostic-logs-stream-log-store/diagnostic-settings-configure.png)

4. Klicka på **Spara**.

Den nya inställningen visas i din lista över inställningar för den här resursen efter en liten stund och diagnostiska loggar strömmas till arbetsytan när nya händelsedata genereras. Det kan vara upp till 15 minuter mellan när en händelse genereras och när den visas i Log Analytics.

### <a name="via-powershell-cmdlets"></a>Via PowerShell-cmdletar

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Strömning den [Azure PowerShell-Cmdlets](../../azure-monitor/platform/powershell-quickstart-samples.md), du kan använda den `Set-AzDiagnosticSetting` cmdlet med följande parametrar:

```powershell
Set-AzDiagnosticSetting -ResourceId [your resource ID] -WorkspaceID [resource ID of the Log Analytics workspace] -Categories [list of log categories] -Enabled $true
```

Observera att egenskapen workspaceID tar fullständiga Azure-resurs-ID för arbetsytan kan inte arbetsytan ID-/ nyckel visas i Log Analytics-portalen.

### <a name="via-azure-cli"></a>Via Azure CLI

Strömning den [Azure CLI](../../azure-monitor/platform/cli-samples.md), du kan använda den [az monitor diagnostic-settings skapa](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) kommando.

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --workspace <log analytics name or object ID> \
    --resource <target resource object ID> \
    --resource-group <log analytics workspace resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true
    }
    ]'
```

Du kan lägga till ytterligare kategorier diagnostikloggen genom att lägga till ordlistor JSON-matris som skickas som den `--logs` parametern.

Den `--resource-group` argumentet är bara krävs om `--workspace` är inte ett objekt-ID.

## <a name="how-do-i-query-the-data-from-a-log-analytics-workspace"></a>Hur jag för att fråga efter data från en Log Analytics-arbetsyta?

Du kan fråga diagnostikloggar som en del av Log Management-lösningen under tabellen AzureDiagnostics i bladet loggar i Azure Monitor-portalen. Det finns även [flera övervakningslösningar för Azure-resurser](../../azure-monitor/insights/solutions.md) du kan installera för att få omedelbar insyn i loggdata som skickas till Azure Monitor.

### <a name="examples"></a>Exempel

```Kusto
// Resources that collect diagnostic logs into this Log Analytics workspace, using Diagnostic Settings
AzureDiagnostics
| distinct _ResourceId
```
```Kusto
// Resource providers collecting diagnostic logs into this Log Analytics worksapce, with log volume per category
AzureDiagnostics
| summarize count() by ResourceProvider, Category
```
```Kusto
// Resource types collecting diagnostic logs into this Log Analytics workspace, with number of resources onboarded
AzureDiagnostics
| summarize ResourcesOnboarded=dcount(_ResourceId) by ResourceType
```
```Kusto
// Operations logged by specific resource provider, in this example - KeyVault
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.KEYVAULT"
| distinct OperationName
```

## <a name="azure-diagnostics-vs-resource-specific"></a>Azure Diagnostics mot Resursspecifika  
När ett mål för Log Analytics har aktiverats för Azure Diagnostics-konfiguration, finns det två olika sätt som data kommer att visas i din arbetsyta:  
- **Azure-diagnostik** – detta är den äldre metoden som används idag av flesta Azure-tjänster. I det här läget kan alla data från alla Diagnostikinställning pekar på en viss arbetsyta kommer få i den _AzureDiagnostics_ tabell. 
<br><br>Eftersom många resurser skickar data till samma tabell (_AzureDiagnostics_), schemat för den här tabellen är en uppsättning scheman för alla de olika datatyper som samlas in. Till exempel om du har skapat diagnostikinställningar för insamling av följande datatyper, som alla skickas till samma arbetsyta:
    - Granska loggar på resurs-1 (med ett schema som består av kolumnerna A, B och C)  
    - Felloggar resurs 2 (med ett schema som består av kolumnerna D, E och F)  
    - Data flödesloggar Resource 3 (med ett schema som består av kolumnerna G, H och jag)  

    Tabellen AzureDiagnostics ut enligt följande, med lite exempeldata:  

    | ResourceProvider | Category | A | B | C | D | E | F | G | H | I |
    | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
    | Microsoft.Resource1 | AuditLogs | x1 | y1 | z1 |
    | Microsoft.Resource2 | Felvillkoren | | | | q1 | W1 | e1 |
    | Microsoft.Resource3 | DataFlowLogs | | | | | | | j1 | k1 | l1|
    | Microsoft.Resource2 | Felvillkoren | | | | q2 | w2 | e2 |
    | Microsoft.Resource3 | DataFlowLogs | | | | | | | j3 | k3 | l3|
    | Microsoft.Resource1 | AuditLogs | x5 | y5 | z5 |
    | ... |

- **Resurs-specifika** – i det här läget kan enskilda tabeller i den valda arbetsytan skapas per varje kategori som valts i konfiguration av diagnostikinställningar. Den här nyare metoden gör det enklare att hitta exakt du vill hitta via explicit inkapsling av problem: en tabell för varje kategori. Dessutom ger den förmåner till stöd för dynamisk typer. Du kan redan finns i det här läget för väljer Azure-resurstyper, till exempel [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics) eller [Intune](https://docs.microsoft.com/intune/review-logs-using-azure-monitor) loggar. Slutligen planerar vi för varje typ av post att migrera till Resursspecifika-läge. 

    Detta skulle resultera i tre tabeller som skapas i exemplet ovan: 
    - Tabellen _AuditLogs_ på följande sätt:

        | ResourceProvider | Category | A | B | C |
        | -- | -- | -- | -- | -- |
        | Microsoft.Resource1 | AuditLogs | x1 | y1 | z1 |
        | Microsoft.Resource1 | AuditLogs | x5 | y5 | z5 |
        | ... |

    - Tabellen _felvillkoren_ på följande sätt:  

        | ResourceProvider | Category | D | E | F |
        | -- | -- | -- | -- | -- | 
        | Microsoft.Resource2 | Felvillkoren | q1 | W1 | e1 |
        | Microsoft.Resource2 | Felvillkoren | q2 | w2 | e2 |
        | ... |

    - Tabellen _DataFlowLogs_ på följande sätt:  

        | ResourceProvider | Category | G | H | I |
        | -- | -- | -- | -- | -- | 
        | Microsoft.Resource3 | DataFlowLogs | j1 | k1 | l1|
        | Microsoft.Resource3 | DataFlowLogs | j3 | k3 | l3|
        | ... |

    Andra fördelar med att använda läget Resursspecifika omfattar förbättrad prestanda för både datainmatningssvarstid och frågetider, bättre för att göra scheman och deras struktur, möjligheten att ge RBAC-behörighet på en viss tabell och mycket mer.

### <a name="selecting-azure-diagnostic-vs-resource-specific-mode"></a>Att välja Azure vs Resursspecifika diagnostikläge
För de flesta Azure-resurser behöver du inte välja om du vill använda Azure-diagnostik eller resurs-specifika läget. data flödar automatiskt via den metod som resursen har valt för att använda. Se dokumentationen från den resurs som du har aktiverat för att skicka data till Log Analytics information där läge används. 

Enligt informationen i föregående avsnitt, men det är slutligen målet med Azure Monitor har alla tjänster i Azure-användning Resursspecifika-läge. Att underlätta övergången och se till att inga data förloras som en del av det, vissa Azure-tjänster när Kom igång med Log Analytics får du ett urval av läge:  
   ![Diagnostiska inställningar läge väljare](media/diagnostic-logs-stream-log-store/diagnostic-settings-mode-selector.png)

Vi **starkt** rekommenderar att för att undvika potentiellt svårt migreringar ned vägen någon nyligen skapade diagnostikinställningar Använd resurs Centric läge.  

För befintliga diagnostikinställningar när det väl aktiverats av en viss Azure-resurs, kommer du att kunna retroaktivt växla från Azure-diagnostik till Resursspecifika-läge. Dina tidigare inmatade data fortsätter att vara tillgängliga i den _AzureDiagnostics_ tabellen förrän den åldrarna ut som konfigurerats i din kvarhållningsinställning på arbetsytan, men nya data skickas till tabellen dedikerad. Det innebär att för alla frågor som måste omfatta både de gamla data och nya (tills gamla data åldras fullt), ett [union](https://docs.microsoft.com/azure/kusto/query/unionoperator) operator i dina frågor som krävs för att kombinera de två datauppsättningarna.

Ta en titt nyheter om nya Azure Services stödjande loggar i resurs-specifika läge på den [Azure uppdaterar](https://azure.microsoft.com/updates/) blogg!

### <a name="known-limitation-column-limit-in-azurediagnostics"></a>Känd begränsning: kolumngräns i AzureDiagnostics
Det finns en explicit gräns för alla angivna Azure logganalys-tabellen som inte har fler än 500 kolumner. När detta uppnåtts kommer att tas bort alla rader som innehåller data med valfri kolumn utanför de första 500 vid inmatning tidpunkt. Tabellen AzureDiagnostics är särskilt sårbara påverkas om du vill att den här gränsen. Detta sker vanligtvis antingen eftersom en stor mängd datakällor som ska skickas till samma arbetsyta, eller flera utförlig datakällor som skickas till samma arbetsyta. 

#### <a name="azure-data-factory"></a>Azure Data Factory  
Azure Data Factory, är på grund av en mycket detaljerad uppsättning med loggar, en resurs som du känner till särskilt påverkas av den här gränsen. I synnerhet var läge för alla diagnostikinställningar konfigurerades innan resurs-specifika aktiverat eller uttryckligen välja att använda Resursspecifika-läge för omvänd kompatibilitet:  
- *Användarparametrar som definierats mot alla aktiviteter i din pipeline*: det blir en ny kolumn som skapats för varje unikt med namnet user-parameter mot alla aktiviteter. 
- *Aktivitetens indata och utdata*: dessa variera aktivitet till aktiviteten och generera ett stort antal kolumner på grund av deras utförlig natur. 
 
Som med bredare lösning förslagen nedan, rekommenderar vi att du migrerar dina loggar för att använda Resursspecifika-läge så snart som möjligt. Om det inte går att göra det direkt kan är ett mellanliggande alternativ att isolera ADF loggar i deras egen arbetsyta för att minimera risken för de här loggarna som påverkar andra loggtyper som samlas in i dina arbetsytor. 
 
#### <a name="workarounds"></a>Lösningar
Kortsiktigt tills alla Azure-tjänster är aktiverade i Resursspecifika-läge för tjänsterna du inte ännu stöda Resursspecifika-läge, rekommenderar vi att du separera utförlig-datatyper som publicerats av dessa tjänster i separata arbetsytor att minska den möjligheten att nått gränsen.  
 
Långsiktiga Azure Diagnostics blir går mot alla Azure-tjänster som stöder Resursspecifika-läge. Vi rekommenderar övergången till det här läget så snart som möjligt för att minska potentialen hos som påverkas av den här begränsningen på 500 kolumn.  


## <a name="next-steps"></a>Nästa steg

* [Läs mer om Azure-diagnostikloggar](diagnostic-logs-overview.md)

