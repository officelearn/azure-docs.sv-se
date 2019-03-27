---
title: Hantera Log Analytics-arbetsytor i Azure Monitor | Microsoft Docs
description: Du kan hantera Log Analytics-arbetsytor i Azure Monitor med en rad olika administrativa uppgifter på användare, konton, arbetsytor och Azure-konton.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: d0e5162d-584b-428c-8e8b-4dcaa746e783
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: magoedte
ms.openlocfilehash: 6990bed4065183ecabb502ea90b5ddf26db563b4
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2019
ms.locfileid: "58500193"
---
# <a name="manage-log-data-and-workspaces-in-azure-monitor"></a>Hantera loggdata och arbetsytor i Azure Monitor
Azure Monitor-butiker logga data över en Log Analytics-arbetsyta som är i grunden en behållare som innehåller data och konfigurationsinformation. För att hantera åtkomst för att logga data måste utföra du olika administrativa uppgifter relaterade till arbetsytor. Du eller andra medlemmar i din organisation kan använda flera arbetsytor för att hantera olika uppsättningar av data som samlas in från alla eller delar av din IT-infrastruktur.

Den här artikeln förklarar hur du hanterar åtkomst till loggar och administrera arbetsytor som de. 

## <a name="create-a-workspace"></a>Skapa en arbetsyta
Om du vill skapa en Log Analytics-arbetsytan måste du:

1. Ha en Azure-prenumeration.
2. Välja ett arbetsytenamn.
3. Associera arbetsytan med en av dina prenumerationer och resursgrupper.
4. Välja en geografisk plats.

Se följande artiklar för information om hur du skapar en arbetsyta:

- [Skapa en Log Analytics-arbetsyta i Azure portal](../learn/quick-create-workspace.md)
- [Skapa en Log Analytics-arbetsyta med Azure CLI 2.0](../learn/quick-create-workspace-cli.md)
- [Skapa en Log Analytics-arbetsyta med Azure PowerShell](../learn/quick-create-workspace-posh.md)

## <a name="determine-the-number-of-workspaces-you-need"></a>Bestämma antalet arbetsytor du behöver
En Log Analytics-arbetsyta är en Azure-resurs och är en behållare där data samlas in, aggregeras, analyseras och presenteras i Azure Monitor. Du kan ha flera arbetsytor per Azure-prenumeration och du kan ha åtkomst till fler än en arbetsyta, med möjlighet att enkelt söka i dem. Det här avsnittet beskriver när det kan vara praktiskt att skapa fler än en arbetsyta.

Tillhandahåller en Log Analytics-arbetsyta:

* En geografisk plats för lagring av data.
* Dataisolering olika användare behörighet att arbetsytan-centric hämtas. Inte relevant när du arbetar i resurs-centric läge.
* För konfigurationen av inställningarna som [prisnivån](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#changing-pricing-tier), [kvarhållning](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period) och [data tak som skall](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#daily-cap).
* Avgifter som rör datainmatning och kvarhållning görs på arbetsyteresursen.

Från förbrukning synsätt rekommenderar vi att du skapar så få arbetsytor som möjligt. Det gör administration och fråga upplevelse för enklare och snabbare. Men baserat på föregående egenskaper kan du behöva skapa flera arbetsytor om:

* Du är ett globalt företag och du behöver logga data som lagras i vissa områden för datasuveränitet eller kompatibilitetsskäl i data.
* Du använder Azure och du vill undvika kostnader för överföring av utgående data genom att ha en arbetsyta i samma region som de Azure-resurser som den hanterar.
* Du är leverantör av hanterade tjänster och behöver Log Analytics-data för varje kund du hanterar isolerade från andra kunders data.
* Du hanterar flera kunder och vill att varje kund / avdelning / affärsgrupp ska se sina egna data, men inte data från andra, och det finns inga företagets behov för en konsoliderad mellan kund / avdelning / affärsgrupp vy ”.

När du använder Windows-agenter för att samla in data måste du [konfigurera varje agent så att den rapporterar till en eller flera arbetsytor](../../azure-monitor/platform/agent-windows.md).

Om du använder System Center Operations Manager kan varje hanteringsgrupp för Operations Manager endast anslutas till en arbetsyta. Du kan installera Microsoft Monitoring Agent på datorer som hanteras av Operations Manager och låta agenten rapporten till både Operations Manager och en annan Log Analytics-arbetsyta.

När arbetsytan arkitekturen har definierats kan du ska framtvinga den här principen på Azure-resurser med [Azure Policy](../../governance/policy/overview.md). Detta kan ge en inbyggd definition som automatiskt gäller för alla Azure-resurser. Du kan till exempel ange en princip för att se till att alla dina Azure-resurser i en viss region skickas alla sina diagnostikloggar till en viss arbetsyta.

## <a name="view-workspace-details"></a>Visa information om arbetsytan
När du analyserar data i Log Analytics-arbetsytan från den **Azure Monitor** menyn i Azure-portalen, som du skapar och hanterar arbetsytor i den **Log Analytics-arbetsytor** menyn.
 

1. Logga in på den [Azure-portalen](https://portal.azure.com) och klicka på **alla tjänster**. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Log Analytics** arbetsytor.  

    ![Azure Portal](media/manage-access/azure-portal-01.png)  

3. Välj din arbetsyta i listan.

4. Sidan för arbetsytan visar information om arbetsytan, komma igång, konfiguration och länkar till ytterligare information.  

    ![Information om arbetsytan](./media/manage-access/workspace-overview-page.png)  


## <a name="workspace-permissions-and-scope"></a>Behörigheter för arbetsytan och omfång
De data som en användare har tillgång till bestäms av flera faktorer som listas i följande tabell. Alla beskrivs i avsnitten nedan.

| Faktor | Beskrivning |
|:---|:---|
| [Åtkomstläge](#access-modes) | Metoden som användaren använder för att får åtkomst till arbetsytan.  Definierar vilka data som är tillgängliga och kontroll åtkomstläge som används. |
| [Åtkomstläge för kontroll](#access-control-mode) | Inställningen på den arbetsyta som definierar om behörigheter tillämpas på arbetsytan- eller resursen. |
| [Behörigheter](#manage-accounts-and-users) | Behörigheter som tillämpas på enskilda eller grupper av användare för arbetsytan eller resurs. Definierar vilka data som användaren har åtkomst till. |
| [Tabellen nivå RBAC](#table-level-rbac) | Valfritt detaljerade behörigheter som gäller för alla användare oavsett deras åtkomst eller åtkomstkontroll läge. Definierar vilka datatyper som en användare kan komma åt. |



## <a name="access-modes"></a>Åtkomstlägen
Den _åtkomstläge_ avser hur en användare ansluter till en Log Analytics-arbetsyta och definierar omfattningen av data som de kan komma åt. 

**Arbetsytan-centric**: En användare kan visa alla loggar från arbetsytan som de har behörighet att i det här läget. Frågorna i det här läget är begränsade till alla data i alla tabeller i arbetsytan. Det här är åtkomstläge som används när loggar som nås med arbetsytan som omfång, t.ex när du väljer **loggar** från den **Azure Monitor** menyn i Azure-portalen.

**Resurs-centric**: När du öppnar arbetsytan för en viss resurs, till exempel när du väljer **loggar** från en resurs-menyn i Azure-portalen kan du visa loggar för endast den resursen i alla tabeller som du har åtkomst till. Frågorna i det här läget är begränsade till endast de data som är kopplade till den här resursen. Det här läget kan även detaljerad rollbaserad åtkomstkontroll (RBAC). 

> [!NOTE]
> Loggarna är tillgängliga för resurs-centric frågor endast om de är korrekt kopplade till önskad resurs. Följande resurser har för närvarande begränsningar: 
> - Datorer utanför Azure
> - Service Fabric
> - Application Insights
> - Containrar
> - Anpassade loggar som skapats av HTTP-API för datainsamling
>
> Du kan testa om loggarna är korrekt kopplade till resursen genom att köra en fråga och kontrollera posterna är du intresserad av. Om rätt resurs-ID finns i den [_ResourceId](log-standard-properties.md#_resourceid) egenskap och sedan data är tillgängliga för resurs-centric frågor.

### <a name="comparing-access-modes"></a>Jämföra åtkomstlägen

I följande tabell sammanfattas Åtkomstlägen:

| | Workspace-centric | Resurs-centric |
|:---|:---|:---|
| Vem varje modell riktar sig till? | Central administration. Administratörer måste du konfigurera insamling av data och användare som behöver åtkomst till en mängd olika resurser. För närvarande krävs av användare som har åtkomst till loggar för resurser utanför Azure. | Programmet team. Administratörer för Azure-resurser som övervakas. |
| Det kräver en användare för att visa loggar? | Behörigheter för arbetsytan. Se **behörigheter för arbetsytan** i [hantera konton och användare](#manage-accounts-and-users). | Läsbehörighet till resursen. Se **resursbehörighet** i [hantera konton och användare](#manage-accounts-and-users). Behörigheter kan vara ärvd (till exempel från den aktuella resursgruppen) eller direkt tilldelad till resursen. Behörighet att loggarna för resursen tilldelas automatiskt. |
| Vad är omfånget för behörigheter? | Arbetsyta. Användare med åtkomst till arbetsytan kan fråga efter alla loggar på arbetsytan från tabeller som de har behörighet till. Se [tabell åtkomstkontroll](#table-level-rbac) | Azure-resurs. Användare kan fråga loggar för resurser som de har åtkomst till från en arbetsyta men det går inte att fråga loggar för andra resurser. |
| Hur kan användare åtkomst till loggar? | Starta **loggar** från **Azure Monitor** menyn eller **Log Analytics-arbetsytor**. | Starta **loggar** från menyn för Azure-resursen. |


## <a name="access-control-mode"></a>Åtkomstkontrolläge
Den _kontroll åtkomstläge_ är en inställning på varje arbetsytor som definierar hur behörigheter avgörs för den arbetsytan.

**Kräver behörigheter för arbetsytan**:  Den här kontrolläge tillåter inte detaljerade RBAC. De måste beviljas behörigheter till arbetsytan eller till specifika tabeller för en användare att få åtkomst till arbetsytan. 

Om en användare har åtkomst till arbetsytan i arbetsytan-centric läge, kommer de har åtkomst till alla data några tabeller som de har beviljats åtkomst till. Om en användare har åtkomst till arbetsytan i resurs-centric läge, kommer de har åtkomst till endast data för den resursen i alla tabeller som de har beviljats åtkomst till.

Det här är standardinställningen för alla arbetsytor som skapats före mars 2019.

**Använd behörigheter för resursen eller arbetsytan**: Den här kontrollen-läge låter detaljerade RBAC. Användare beviljas åtkomst till endast de data som är kopplade till resurser som de kan visa via Azure-behörigheter, resurser som de har `read` behörighet. 

När en användare har åtkomst till arbetsytan i arbetsytan-centric läge, tillämpas behörigheter för arbetsytan. När en användare har åtkomst till arbetsytan i resurs-centric läge, endast resursbehörighet att verifieras och behörigheter för arbetsytan kommer att ignoreras. Aktivera RBAC för en användare genom att ta bort dem från behörigheter för arbetsytan och låta sina resursbehörighet ska identifieras.

Det här är standardinställningen för alla arbetsytor som skapats efter mars 2019.

> [!NOTE]
> Om en användare har bara resursbehörighet till arbetsytan kan de bara kommer att kunna komma åt en arbetsyta med hjälp av [resurs-centric läge](#access-modes).


### <a name="define-access-control-mode-in-azure-portal"></a>Definiera åtkomstläge för åtkomstkontroll i Azure-portalen
Du kan visa den aktuella arbetsyteläge åtkomstkontroll på de **översikt** för arbetsytan i den **Log Analytics-arbetsyta** menyn.

![Visa arbetsytan åtkomstläge kontroll](media/manage-access/view-access-control-mode.png)

Du kan ändra den här inställningen på den **egenskaper** för arbetsytan. Ändrar den här inställningen inaktiveras om du inte har behörighet att konfigurera arbetsytan.

![Ändra arbetsytan åtkomstläge](media/manage-access/change-access-control-mode.png)

### <a name="define-access-control-mode-in-powershell"></a>Definiera kontroll åtkomstläge i PowerShell

Använd följande kommando för att undersöka åtkomstläge för kontroll för alla arbetsytor i prenumerationen:

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {$_.Name + ": " + $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions} 
```

Använd följande skript för att ange åtkomstkontroll läget för en viss arbetsyta:

```powershell
$WSName = "my-workspace"
$Workspace = Get-AzResource -Name $WSName -ExpandProperties
if ($Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null) 
    { $Workspace.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else 
    { $Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $Workspace.ResourceId -Properties $Workspace.Properties -Force
```

Använd följande skript för att ställa in åtkomstläge för kontroll för alla arbetsytor i prenumerationen

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {
if ($_.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null) 
    { $_.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else 
    { $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $_.ResourceId -Properties $_.Properties -Force
```

### <a name="define-access-mode-in-resource-manager-template"></a>Definiera åtkomstläge i Resource Manager-mall
Om du vill konfigurera åtkomstläge i en Azure Resource Manager-mall, ange den **enableLogAccessUsingOnlyResourcePermissions** funktionen flaggan arbetsytan till en av följande värden.

- **false**: Ange arbetsytan till arbetsytan-centric behörigheter. Det här är standardinställningen om inte flaggan.
- **true**: Ange arbetsytan till resurs-centric behörigheter.


## <a name="manage-accounts-and-users"></a>Hantera konton och användare
Behörigheter till arbetsytan som tillämpas på en viss användare definieras av deras åtkomstläge och [kontroll åtkomstläge](#access-control-mode) i arbetsytan. **Behörigheter för arbetsytan** tillämpas när en användare har åtkomst till alla arbetsytan med **arbetsyta-centric** i [arbetsyta-centric läge](#access-modes). **Resursbehörighet** tillämpas när en användare ansluter till en arbetsyta med **använder resursen eller arbetsytan behörigheter** [kontroll åtkomstläge](#access-control-mode) med [resurs-centric läge ](#access-modes).

### <a name="workspace-permissions"></a>Behörigheter för arbetsytan
Varje arbetsyta kan ha flera associerade konton, och varje konto kan ha åtkomst till flera arbetsytor. Åtkomst hanteras [Azure rollbaserad åtkomst](../../role-based-access-control/role-assignments-portal.md). 


Följande aktiviteter kräver även Azure-behörigheter:

| Åtgärd                                                          | Azure-behörigheter krävs | Anteckningar |
|-----------------------------------------------------------------|--------------------------|-------|
| Att lägga till och ta bort övervakningslösningar                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | Behörigheterna måste beviljas på resursgrupp- eller prenumerationsnivå. |
| Ändra prisnivån                                       | `Microsoft.OperationalInsights/workspaces/*/write` | |
| Visa data i lösningspanelerna *säkerhetskopiering* och *Site Recovery* | Administratör/medadministratör | Åtkomst till resurser som distribueras med den klassiska distributionsmodellen |
| Skapa en arbetsyta i Azure Portal                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||


#### <a name="manage-access-to-log-analytics-workspace-using-azure-permissions"></a>Hantera åtkomst till Log Analytics-arbetsyta med hjälp av Azure-behörigheter 
Om du vill bevilja åtkomst till Log Analytics-arbetsytan med Azure-behörigheter följer du stegen i [Använda rolltilldelningar för att hantera åtkomsten till dina Azure-prenumerationsresurser](../../role-based-access-control/role-assignments-portal.md).

Azure har två inbyggda användarroller för Log Analytics-arbetsytor:
- Log Analytics Reader
- Log Analytics Contributor

Medlemmar av *Log Analytics Reader*-rollen kan:
- Visa och söka i alla övervakningsdata 
- Visa övervakningsinställningar, även konfiguration av Azure-diagnostik för alla Azure-resurser.

Läsarroll för Log Analytics innehåller följande Azure åtgärder:

| Typ    | Behörighet | Beskrivning |
| ------- | ---------- | ----------- |
| Åtgärd | `*/read`   | Möjlighet att visa alla Azure-resurser och resurskonfigurationen. Detta omfattar visning av: <br> Status för tillägg för virtuell dator <br> Konfiguration av Azure-diagnostik för resurser <br> Alla egenskaper och inställningar för alla resurser |
| Åtgärd | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | Kan utföra Log Search v2-sökfrågor |
| Åtgärd | `Microsoft.OperationalInsights/workspaces/search/action` | Kan utföra Log Search v1-sökfrågor |
| Åtgärd | `Microsoft.Support/*` | Kan öppna supportärenden |
|Ingen åtgärd | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | Förhindrar läsning av arbetsytenyckeln nyckel som krävs för att använda datasamlings-API och installera agenter. Detta hindrar användaren från att lägga till nya resurser till arbetsytan |


Medlemmar av *Log Analytics Contributor*-rollen kan:
- Läsa alla övervakningsdata som Log Analytics Reader  
- Skapa och konfigurera Automation -konton  
- Lägga till och ta bort hanteringslösningar    
    > [!NOTE] 
    > För att kunna genomföra de sista två åtgärderna, måste den här behörigheten beviljas resource group eller på prenumerationsnivån.  

- Läsa lagringskontonycklar   
- Konfigurera loggsamlingar från Azure Storage  
- Redigera övervakningsinställningar för Azure-resurser, bland annat
  - Lägga till tillägg för virtuell dator i virtuella datorer
  - Konfigurera Azure-diagnostik på alla Azure-resurser

> [!NOTE] 
> Du kan använda möjligheten att lägga till ett virtuellt datortillägg i en virtuell dator för att få fullständig kontroll över datorn.

Rollen Log Analytics Contributor innehåller följande Azure åtgärder:

| Behörighet | Beskrivning |
| ---------- | ----------- |
| `*/read`     | Kan visa alla resurser och resurskonfigurationer. Detta omfattar visning av: <br> Status för tillägg för virtuell dator <br> Konfiguration av Azure-diagnostik för resurser <br> Alla egenskaper och inställningar för alla resurser |
| `Microsoft.Automation/automationAccounts/*` | Kan skapa och konfigurera Azure Automation-konton, inklusive lägga till och redigera runbookflöden |
| `Microsoft.ClassicCompute/virtualMachines/extensions/*` <br> `Microsoft.Compute/virtualMachines/extensions/*` | Lägga till, uppdatera och ta bort virtuella datortillägg, även Microsoft Monitoring Agent-tillägget och OMS Agent for Linux-tillägget |
| `Microsoft.ClassicStorage/storageAccounts/listKeys/action` <br> `Microsoft.Storage/storageAccounts/listKeys/action` | Visa lagringskontonyckeln. Krävs för att ställa in Log Analytics för läsning av loggar från Azure-lagringskonton |
| `Microsoft.Insights/alertRules/*` | Lägga till, uppdatera och ta bort aviseringsregler |
| `Microsoft.Insights/diagnosticSettings/*` | Lägga till, uppdatera och ta bort diagnostikinställningar på Azure-resurser |
| `Microsoft.OperationalInsights/*` | Lägga till, uppdatera och ta bort konfigurationer för Log Analytics-arbetsytor |
| `Microsoft.OperationsManagement/*` | Lägga till och ta bort hanteringslösningar |
| `Microsoft.Resources/deployments/*` | Skapa och ta bort distributioner. Krävs för att lägga till och ta bort lösningar, arbetsytor och Automation-konton |
| `Microsoft.Resources/subscriptions/resourcegroups/deployments/*` | Skapa och ta bort distributioner. Krävs för att lägga till och ta bort lösningar, arbetsytor och Automation-konton |

För att kunna lägga till och ta bort användare i en användarroll måste du ha behörigheten `Microsoft.Authorization/*/Delete` och `Microsoft.Authorization/*/Write`.

Använd de här rollerna för att ge användare åtkomst med olika omfång:
- Prenumeration: åtkomst till alla arbetsytor i prenumerationen
- Resursgrupp: åtkomst till alla arbetsytor i resursgruppen
- Resurs: endast åtkomst till en angiven arbetsyta

Du bör utföra tilldelningar på resursnivå (arbetsyta) för att garantera korrekta åtkomstkontroll.  Använd [anpassade roller](../../role-based-access-control/custom-roles.md) för att skapa roller med specifik behörighet.

### <a name="resource-permissions"></a>Resursbehörigheter 
När användarna fråga loggar från en arbetsyta med hjälp av resource-centric åtkomst, har de följande behörigheter för resursen:

| Behörighet | Beskrivning |
| ---------- | ----------- |
| `Microsoft.Insights/logs/<tableName>/read`<br><br>Exempel:<br>`Microsoft.Insights/logs/*/read`<br>`Microsoft.Insights/logs/Heartbeat/read` | Möjligheten att visa alla loggdata för resursen.  |


Den här behörigheten beviljas vanligtvis från en roll som innehåller  _\*/läsa eller_ _\*_ behörigheter till exempel inbyggt [läsare](../../role-based-access-control/built-in-roles.md#reader) och [ Deltagare](../../role-based-access-control/built-in-roles.md#contributor) roller. Observera att anpassade roller som innehåller specifika åtgärder eller dedikerade inbyggda roller inte kanske innehåller den här behörigheten.

Se [definiera per tabell åtkomstkontroll](#table-level-rbac) nedan om du vill skapa olika åtkomstkontroll för olika tabeller.


## <a name="table-level-rbac"></a>Tabellen nivå RBAC
**Tabellen nivå RBAC** kan du tillhandahålla mer detaljerad kontroll till data i Log Analytics-arbetsytan utöver behörigheterna som helst. Den här kontrollen kan du definiera specifika datatyper som är bara tillgängliga för en specifik uppsättning användare.

Du implementerar tabell åtkomstkontroll med [Azure anpassade roller](../../role-based-access-control/custom-roles.md) antingen bevilja eller neka åtkomst till specifika [tabeller](../log-query/log-query-overview.md#how-azure-monitor-log-data-is-organized) på arbetsytan. Dessa roller tillämpas på arbetsytor med antingen arbetsytan-centric eller resurs-centric [åtkomst kontroll lägen](#access-control-mode) oavsett användarens [åtkomstläge](#access-modes).

Skapa en [anpassad roll](../../role-based-access-control/custom-roles.md) med följande åtgärder för att definiera åtkomst till tabellen åtkomstkontroll.

- Om du vill bevilja åtkomst till en tabell, lägger till den i den **åtgärder** delen av rolldefinitionen.
- Om du vill neka åtkomst till en tabell, lägger till den i den **NotActions** delen av rolldefinitionen.
- Använd * att ange alla tabeller.

Till exempel vill skapa en roll med åtkomst till den _pulsslag_ och _AzureActivity_ tabeller, skapa en anpassad roll med hjälp av följande åtgärder:

```
"Actions":  [
              "Microsoft.OperationalInsights/workspaces/query/Heartbeat/read",
              "Microsoft.OperationalInsights/workspaces/query/AzureActivity/read"
  ],
```

Skapa en roll med åtkomst till endast _SecurityBaseline_ och inga andra tabeller, skapa en anpassad roll med hjälp av följande åtgärder:

```
    "Actions":  [
        "Microsoft.OperationalInsights/workspaces/query/*/read"
    ],
    "NotActions":  [
        "Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read"
    ],
```

### <a name="custom-logs"></a>Anpassade loggar
 Anpassade loggar skapas av datakällor, till exempel anpassade loggar och HTTP Data Collector API. Det enklaste sättet att identifiera vilken typ av logg är genom att markera de tabeller som visas under [anpassade loggar i loggen schemat](../log-query/get-started-portal.md#understand-the-schema).

 Du kan inte bevilja eller neka åtkomst till enskilda anpassade loggar, men du kan bevilja eller neka åtkomst till alla anpassade loggar. Om du vill skapa en roll med åtkomst till alla anpassade loggar, skapar du en anpassad roll med hjälp av följande åtgärder:

```
    "Actions":  [
        "Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read"
    ],
```

### <a name="considerations"></a>Överväganden

- Om en användare beviljas globala läsbehörighet med de läsare eller deltagare standardroller som innehåller den  _\*/läsa_ åtgärd, åsidosätts per tabell access control och ge dem åtkomst till alla loggdata.
- Om en användare beviljas åtkomst per tabell men inga andra behörigheter, skulle de att kunna komma åt loggdata från API: et men inte från Azure-portalen. Använda Log Analytics Reader som dess grundläggande roll för att ge åtkomst till Azure-portalen.
- In prenumerationens administratörer har åtkomst till alla datatyper av, oavsett eventuella andra behörighetsinställningar.
- Arbetsytan ägare behandlas som andra användare för åtkomstkontroll per tabell.
- Du bör tilldela roller till säkerhetsgrupper i stället för enskilda användare för att minska antalet tilldelningar. Detta kommer också hur du använder befintliga verktyg för gruppen för att konfigurera och verifiera åtkomst.




## <a name="next-steps"></a>Nästa steg
* Se [översikt över Log Analytics-agenten](../../azure-monitor/platform/log-analytics-agent.md) samla in data från datorer i ditt datacenter eller andra moln.
* Om du vill konfigurera datainsamling från virtuella Azure-datorer läser du [Samla in data om Azure Virtual Machines](../../azure-monitor/learn/quick-collect-azurevm.md).  

