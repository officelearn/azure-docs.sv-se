---
title: Hantera Log Analytics arbets ytor i Azure Monitor | Microsoft Docs
description: Du kan hantera Log Analytics arbets ytor i Azure Monitor med hjälp av olika administrativa uppgifter för användare, konton, arbets ytor och Azure-konton.
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
ms.date: 07/16/2019
ms.author: magoedte
ms.openlocfilehash: fbfbd8e26ab3e92f06194322be7ec2be2fb180fd
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/16/2019
ms.locfileid: "68254451"
---
# <a name="manage-log-data-and-workspaces-in-azure-monitor"></a>Hantera loggdata och arbets ytor i Azure Monitor
Azure Monitor lagrar loggdata i en Log Analytics arbets yta, vilket är i princip en behållare som innehåller data och konfigurations information. Om du vill hantera åtkomst till loggdata utför du olika administrativa uppgifter relaterade till arbets ytor. Du eller andra medlemmar i din organisation kan använda flera arbetsytor för att hantera olika uppsättningar av data som samlas in från alla eller delar av din IT-infrastruktur.

Den här artikeln beskriver hur du hanterar åtkomst till loggar och administrerar de arbets ytor som innehåller dem. 

## <a name="create-a-workspace"></a>Skapa en arbetsyta
Om du vill skapa en Log Analytics arbets yta måste du:

1. Ha en Azure-prenumeration.
2. Välja ett arbetsytenamn.
3. Associera arbetsytan med en av dina prenumerationer och resursgrupper.
4. Välja en geografisk plats.

I följande artiklar finns information om hur du skapar en arbets yta:

- [Skapa en Log Analytics arbets yta i Azure Portal](../learn/quick-create-workspace.md)
- [Skapa en Log Analytics arbets yta med Azure CLI 2,0](../learn/quick-create-workspace-cli.md)
- [Skapa en Log Analytics arbets yta med Azure PowerShell](../learn/quick-create-workspace-posh.md)

## <a name="determine-the-number-of-workspaces-you-need"></a>Bestämma antalet arbetsytor du behöver
En Log Analytics-arbetsyta är en Azure-resurs och är en behållare där data samlas in, aggregeras, analyseras och presenteras i Azure Monitor. Du kan ha flera arbets ytor per Azure-prenumeration och du kan få till gång till fler än en arbets yta, med möjlighet att enkelt fråga sig över dem. Det här avsnittet beskriver när det kan vara praktiskt att skapa fler än en arbetsyta.

En Log Analytics arbets yta innehåller:

* En geografisk plats för data lagring.
* Data isolering för att definiera olika användares åtkomst rättigheter i läget för den centrerade arbets ytan. Inte relevant när du arbetar i resurs-centrat läge.
* Omfattning för konfiguration av inställningar som [pris nivå](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#changing-pricing-tier), [kvarhållning](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period) och [data capping](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#daily-cap).
* Avgifter som rör data inmatning och kvarhållning görs i arbets ytans resurs.

Från förbrukning synsätt rekommenderar vi att du skapar så få arbetsytor som möjligt. Det gör administration och fråga upplevelse för enklare och snabbare. Men baserat på föregående egenskaper kan du behöva skapa flera arbetsytor om:

* Du är ett globalt företag och du behöver logga data som lagras i vissa regioner för data suveränitet eller efterföljandekrav.
* Du använder Azure och du vill undvika kostnader för överföring av utgående data genom att ha en arbetsyta i samma region som de Azure-resurser som den hanterar.
* Du är leverantör av hanterade tjänster och behöver Log Analytics-data för varje kund du hanterar isolerade från andra kunders data.
* Du hanterar flera kunder och du vill att varje kund/avdelning/affärs grupp ska se sina egna data, men inte data från andra, och det finns inget behov av affärs grupper mellan kund/avdelning/företag. ".

När du använder Windows-agenter för att samla in data måste du [konfigurera varje agent så att den rapporterar till en eller flera arbetsytor](../../azure-monitor/platform/agent-windows.md).

Om du använder System Center Operations Manager kan varje hanteringsgrupp för Operations Manager endast anslutas till en arbetsyta. Du kan installera Microsoft Monitoring Agent på datorer som hanteras av Operations Manager och låta agenten rapporten till både Operations Manager och en annan Log Analytics-arbetsyta.

När arbets ytans arkitektur har definierats bör du tillämpa den här principen på Azure-resurser med [Azure policy](../../governance/policy/overview.md). Detta kan ge en inbyggd definition som automatiskt tillämpas på alla Azure-resurser. Du kan till exempel ange en princip för att se till att alla dina Azure-resurser i en viss region har skickat alla diagnostikloggar till en viss arbets yta.

## <a name="view-workspace-details"></a>Visa information om arbets ytan
När du analyserar data i din Log Analytics-arbetsyta från **Azure Monitor** -menyn i Azure Portal skapar du och hanterar arbets ytor i **Log Analytics arbets ytans** meny.
 

1. Logga in på [Azure Portal](https://portal.azure.com) och klicka på **alla tjänster**. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Log Analytics** arbets ytor.  

    ![Azure Portal](media/manage-access/azure-portal-01.png)  

3. Välj din arbets yta i listan.

4. Sidan arbets yta visar information om arbets ytan, komma igång, konfiguration och länkar för ytterligare information.  

    ![Information om arbetsytan](./media/manage-access/workspace-overview-page.png)  


## <a name="workspace-permissions-and-scope"></a>Behörigheter och omfång för arbets ytan
De data som en användare har åtkomst till bestäms av flera faktorer som anges i följande tabell. Var och en beskrivs i avsnitten nedan.

| Faktor | Beskrivning |
|:---|:---|
| [Åtkomst läge](#access-modes) | Metod som användaren använder för att få åtkomst till arbets ytan.  Definierar omfattningen av tillgängliga data och åtkomst kontroll läge som används. |
| [Åtkomst kontrol läge](#access-control-mode) | Inställning på arbets ytan som definierar om behörigheter tillämpas på arbets ytan eller resurs nivån. |
| [Behörigheter](#manage-accounts-and-users) | Behörigheter som tillämpas på enskilda eller grupper av användare för arbets ytan eller resursen. Definierar vilka data som användaren kommer att ha åtkomst till. |
| [RBAC för tabell nivå](#table-level-rbac) | Valfria detaljerade behörigheter som gäller för alla användare oavsett åtkomst läge eller åtkomst kontrol läge. Definierar vilka data typer som en användare kan komma åt. |



## <a name="access-modes"></a>Åtkomst lägen
_Åtkomst Läget_ avser hur en användare kommer åt en Log Analytics-arbetsyta och definierar omfattningen av de data som de har åtkomst till. 

**Område – centrerad**: I det här läget kan en användare Visa alla loggar i arbets ytan som de har behörighet till. Frågor i det här läget är begränsade till alla data i alla tabeller i arbets ytan. Detta är det åtkomst läge som används när loggar nås med arbets ytan som omfång, till exempel när du väljer **loggar** på **Azure Monitor** -menyn i Azure Portal.

**Resurs – centrerad**: När du öppnar arbets ytan för en viss resurs, t. ex. När du väljer **loggar** från en resurs meny i Azure Portal, kan du bara visa loggar för den resursen i alla tabeller som du har åtkomst till. Frågor i det här läget är begränsade till data som är associerade med den resursen. Det här läget möjliggör också detaljerad rollbaserad åtkomst kontroll (RBAC). 

> [!NOTE]
> Loggar är tillgängliga för resursbaserade frågor endast om de var korrekt kopplade till den aktuella resursen. För närvarande har följande resurser begränsningar: 
> - Datorer utanför Azure
> - Service Fabric
> - Application Insights
> - Containrar
>
> Du kan testa om loggarna är korrekt kopplade till sin resurs genom att köra en fråga och granska de poster som du är intresse rad av. Om rätt resurs-ID finns i egenskapen [_ResourceId](log-standard-properties.md#_resourceid) är data tillgängliga för resursbaserade frågor.

### <a name="comparing-access-modes"></a>Jämför åtkomst lägen

I följande tabell sammanfattas åtkomst lägena:

| | Arbets yta – centrerad | Resurs-centrisk |
|:---|:---|:---|
| Vem är varje modell avsedd för? | Central administration. Administratörer som behöver konfigurera data insamling och användare som behöver åtkomst till en mängd olika resurser. Krävs också för användare som har åtkomst till loggar för resurser utanför Azure. | Program team. Administratörer av Azure-resurser som övervakas. |
| Vad kräver en användare att visa loggar? | Behörigheter till arbets ytan. Se **behörigheter för arbets ytan** i [Hantera konton och användare](#manage-accounts-and-users). | Läs åtkomst till resursen. Se **resurs behörigheter** i [Hantera konton och användare](#manage-accounts-and-users). Behörigheter kan ärvas (till exempel från resurs gruppen innehåller) eller tilldelas direkt till resursen. Behörighet till loggarna för resursen tilldelas automatiskt. |
| Vad är behörighets omfånget? | Platsen. Användare med åtkomst till arbets ytan kan fråga alla loggar på den arbets ytan från tabeller som de har behörighet till. Se [tabell åtkomst kontroll](#table-level-rbac) | Azure-resurs. Användaren kan söka efter resurser i loggar som de har åtkomst till från en arbets yta, men kan inte skicka frågor till loggar för andra resurser. |
| Hur kan användare få åtkomst till loggar? | Starta **loggar** från **Azure Monitor** -menyn eller **Log Analytics arbets ytor**. | Starta **loggar** från menyn för Azure-resursen. |


## <a name="access-control-mode"></a>Åtkomst kontrol läge
_Åtkomst kontrol läget_ är en inställning på varje arbets yta som definierar hur behörigheter bestäms för arbets ytan.

**Kräv behörigheter för arbets yta**:  Det här kontroll läget tillåter inte detaljerad RBAC. För att en användare ska kunna komma åt arbets ytan måste de beviljas behörigheter till arbets ytan eller till vissa tabeller. 

Om en användare har åtkomst till arbets ytan i det koncentriska läget för arbets yta, kommer de att ha åtkomst till alla tabeller som de har beviljats åtkomst till. Om en användare har åtkomst till arbets ytan i resurs-koncentriskt läge, har de bara åtkomst till data för resursen i alla tabeller som de har beviljats åtkomst till.

Detta är standardinställningen för alla arbets ytor som skapats före mars 2019.

**Använd resurs-eller arbets ytans behörigheter**: Det här kontroll läget möjliggör detaljerad RBAC. Användare beviljas åtkomst till data som är associerade med resurser som de kan visa via Azure-behörigheter, resurser som de har `read` behörighet till. 

När en användare ansluter till arbets ytan i det centrerade områdets läge för arbets yta kommer arbets ytans behörigheter att gälla. När en användare ansluter till arbets ytan i resurs-koncentriskt läge, verifieras bara resurs behörigheter och arbets ytans behörigheter kommer att ignoreras. Aktivera RBAC för en användare genom att ta bort dem från arbets ytans behörigheter och tillåta att deras resurs behörigheter identifieras.

Detta är standardinställningen för alla arbets ytor som skapats efter 2019 mars.

> [!NOTE]
> Om en användare bara har resurs behörigheter till arbets ytan, kommer de endast att kunna komma åt arbets ytan med [resurs-centrat läge](#access-modes).


### <a name="define-access-control-mode-in-azure-portal"></a>Definiera åtkomst kontrol läge i Azure Portal
Du kan visa den aktuella arbets ytans åtkomst kontrol läge på sidan **Översikt** för arbets ytan på menyn **Log Analytics arbets yta** .

![Visa åtkomst kontrol läge för arbets yta](media/manage-access/view-access-control-mode.png)

Du kan ändra den här inställningen på sidan **Egenskaper** för arbets ytan. Att ändra inställningen kommer att inaktive ras om du inte har behörighet att konfigurera arbets ytan.

![Ändra arbets ytans åtkomst läge](media/manage-access/change-access-control-mode.png)

### <a name="define-access-control-mode-in-powershell"></a>Definiera åtkomst kontrol läge i PowerShell

Använd följande kommando för att kontrol lera åtkomst kontrol läget för alla arbets ytor i prenumerationen:

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {$_.Name + ": " + $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions} 
```

Använd följande skript för att ställa in åtkomst kontrol läget för en angiven arbets yta:

```powershell
$WSName = "my-workspace"
$Workspace = Get-AzResource -Name $WSName -ExpandProperties
if ($Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null) 
    { $Workspace.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else 
    { $Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $Workspace.ResourceId -Properties $Workspace.Properties -Force
```

Använd följande skript för att ställa in åtkomst kontrol läget för alla arbets ytor i prenumerationen

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {
if ($_.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null) 
    { $_.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else 
    { $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $_.ResourceId -Properties $_.Properties -Force
```

### <a name="define-access-mode-in-resource-manager-template"></a>Definiera åtkomst läge i Resource Manager-mall
Om du vill konfigurera åtkomst Läget i en Azure Resource Manager mall ställer du in funktions flaggan **enableLogAccessUsingOnlyResourcePermissions** på arbets ytan till något av följande värden.

- **falskt**: Ange arbets ytans-centrerad behörighet. Detta är standardinställningen om flaggan inte har angetts.
- **Sant**: Ange arbets ytan till resurs-Centred permissions.


## <a name="manage-accounts-and-users"></a>Hantera konton och användare
Behörigheterna till arbets ytan som används för en viss användare definieras av deras åtkomst läge och [åtkomst kontrol läge](#access-control-mode) för arbets ytan. **Behörigheter för arbets ytan** tillämpas när en användare får åtkomst till en arbets yta med hjälp av **arbets ytan – centrerad** i läget för en [arbets yta](#access-modes). **Resurs behörigheter** tillämpas när en användare får åtkomst till en arbets yta med **resurs-eller arbets ytans behörigheter** [åtkomst kontrol läge](#access-control-mode) med hjälp av [resurs-Centrerat läge](#access-modes).

### <a name="workspace-permissions"></a>Behörigheter för arbets ytan
Varje arbetsyta kan ha flera associerade konton, och varje konto kan ha åtkomst till flera arbetsytor. Åtkomst hanteras [Azure rollbaserad åtkomst](../../role-based-access-control/role-assignments-portal.md). 


Följande aktiviteter kräver även Azure-behörigheter:

||Åtgärd |Azure-behörigheter krävs |Anteckningar |
|-------|-------------------------|------|
| Lägga till och ta bort övervaknings lösningar | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | Behörigheterna måste beviljas på resursgrupp- eller prenumerationsnivå. |
| Ändra prisnivån | `Microsoft.OperationalInsights/workspaces/*/write` | |
| Visa data i lösningspanelerna *säkerhetskopiering* och *Site Recovery* | Administratör/medadministratör | Åtkomst till resurser som distribueras med den klassiska distributionsmodellen |
| Skapa en arbetsyta i Azure Portal | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||
| Visa grundläggande egenskaper för arbets yta och ange bladet arbets yta i portalen | `Microsoft.OperationalInsights/workspaces/read` ||
| Fråga loggar med valfritt gränssnitt | `Microsoft.OperationalInsights/workspaces/query/read` ||
| Åtkomst till alla logg typer med hjälp av frågor | `Microsoft.OperationalInsights/workspaces/query/*/read` ||
| Åtkomst till en speciell logg tabell | `Microsoft.OperationalInsights/workspaces/query/<table_name>/read` ||
| Läs arbets ytans nycklar för att tillåta sändning av loggar till den här arbets ytan | `Microsoft.OperationalInsights/workspaces/sharedKeys/action` ||



#### <a name="manage-access-to-log-analytics-workspace-using-azure-permissions"></a>Hantera åtkomst till Log Analytics-arbetsyta med Azure-behörigheter 
Om du vill bevilja åtkomst till Log Analytics-arbetsytan med Azure-behörigheter följer du stegen i [Använda rolltilldelningar för att hantera åtkomsten till dina Azure-prenumerationsresurser](../../role-based-access-control/role-assignments-portal.md).

Azure har två inbyggda användar roller för Log Analytics arbets ytor:
- Log Analytics Reader
- Log Analytics Contributor

Medlemmar av *Log Analytics Reader*-rollen kan:
- Visa och söka i alla övervakningsdata 
- Visa övervakningsinställningar, även konfiguration av Azure-diagnostik för alla Azure-resurser.

Läsarroll för Log Analytics innehåller följande Azure åtgärder:

| Typ    | Behörighet | Beskrivning |
| ------- | ---------- | ----------- |
| Åtgärd | `*/read`   | Möjlighet att visa alla Azure-resurser och resurskonfigurationen. Detta omfattar visning av: <br> Status för tillägg för virtuell dator <br> Konfiguration av Azure-diagnostik för resurser <br> Alla egenskaper och inställningar för alla resurser. <br> För arbets ytor ger den fullständig obegränsad behörighet att läsa inställningarna för arbets ytan och köra frågor på data. Se fler detaljerade alternativ ovan. |
| Action | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | Föråldrad, du behöver inte tilldela dem till användare. s |
| Action | `Microsoft.OperationalInsights/workspaces/search/action` | Föråldrad, du behöver inte tilldela dem till användarna. |
| Action | `Microsoft.Support/*` | Kan öppna supportärenden |
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
| `*/read`     | Möjlighet att visa alla Azure-resurser och resurskonfigurationen. Detta omfattar visning av: <br> Status för tillägg för virtuell dator <br> Konfiguration av Azure-diagnostik för resurser <br> Alla egenskaper och inställningar för alla resurser. <br> För arbets ytor ger den fullständig obegränsad behörighet att läsa inställningarna för arbets ytan och köra frågor på data. Se fler detaljerade alternativ ovan. |
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

Du bör utföra tilldelningar på resurs nivå (arbets yta) för att säkerställa en korrekt åtkomst kontroll.  Använd [anpassade roller](../../role-based-access-control/custom-roles.md) för att skapa roller med specifik behörighet.

### <a name="resource-permissions"></a>Resurs behörigheter 
När användarna frågar efter loggar från en arbets yta med hjälp av resurs-Centred Access har de följande behörigheter för resursen:

| Behörighet | Beskrivning |
| ---------- | ----------- |
| `Microsoft.Insights/logs/<tableName>/read`<br><br>Exempel:<br>`Microsoft.Insights/logs/*/read`<br>`Microsoft.Insights/logs/Heartbeat/read` | Möjlighet att visa alla logg data för resursen.  |
| `Microsoft.Insights/diagnosticSettings/write ` | Möjlighet att konfigurera diagnostikinställningar för att tillåta konfiguration av loggar för den här resursen. |

Den här behörigheten beviljas vanligt vis _\*_ från en roll som innehåller  _\*/Read eller_ behörigheter som de inbyggda rollerna [läsare](../../role-based-access-control/built-in-roles.md#reader) och [deltagare](../../role-based-access-control/built-in-roles.md#contributor) . Observera att anpassade roller som innehåller särskilda åtgärder eller dedikerade inbyggda roller kanske inte innehåller den här behörigheten.

Se [definiera åtkomst kontroll per tabell](#table-level-rbac) nedan om du vill skapa en annan åtkomst kontroll för olika tabeller.


## <a name="table-level-rbac"></a>RBAC för tabell nivå
Med **RBAC** -tabellnivå kan du ge mer detaljerad kontroll över data i en Log Analytics arbets yta förutom de andra behörigheterna. Med den här kontrollen kan du definiera vissa data typer som endast är tillgängliga för en speciell uppsättning användare.

Du implementerar tabell åtkomst kontroll med [Azures anpassade roller](../../role-based-access-control/custom-roles.md) för att antingen bevilja eller neka åtkomst till vissa [tabeller](../log-query/logs-structure.md) i arbets ytan. De här rollerna tillämpas på arbets ytor med antingen icke-centrerade eller resursbaserade [åtkomst kontroll lägen](#access-control-mode) oavsett användarens [åtkomst läge](#access-modes).

Skapa en [anpassad roll](../../role-based-access-control/custom-roles.md) med följande åtgärder för att definiera åtkomst till tabell åtkomst kontroll.

- Om du vill bevilja åtkomst till en tabell inkluderar du den i avsnittet **åtgärder** i roll definitionen.
- Om du vill neka åtkomst till en tabell inkluderar du den i avsnittet **NotActions** i roll definitionen.
- Använd * för att ange alla tabeller.

Om du till exempel vill skapa en roll med åtkomst till _pulsslags_ -och _AzureActivity_ -tabellerna skapar du en anpassad roll med följande åtgärder:

```
"Actions":  [
              "Microsoft.OperationalInsights/workspaces/query/Heartbeat/read",
              "Microsoft.OperationalInsights/workspaces/query/AzureActivity/read"
  ],
```

Skapa en roll med enbart åtkomst till _SecurityBaseline_ och inga andra tabeller genom att skapa en anpassad roll med följande åtgärder:

```
    "Actions":  [
        "Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read"
    ],
    "NotActions":  [
        "Microsoft.OperationalInsights/workspaces/query/*/read"
    ],
```

### <a name="custom-logs"></a>Anpassade loggar
 Anpassade loggar skapas av data källor, till exempel anpassade loggar och API för HTTP-datainsamling. Det enklaste sättet att identifiera logg typen är genom att kontrol lera tabellerna som visas under [anpassade loggar i logg schemat](../log-query/get-started-portal.md#understand-the-schema).

 Du kan för närvarande inte bevilja eller neka åtkomst till enskilda anpassade loggar, men du kan bevilja eller neka åtkomst till alla anpassade loggar. Skapa en roll med åtkomst till alla anpassade loggar genom att skapa en anpassad roll med följande åtgärder:

```
    "Actions":  [
        "Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read"
    ],
```

### <a name="considerations"></a>Överväganden

- Om en användare beviljas global Läs behörighet med standard läsare eller deltagar roller som innehåller  _\*/Read_ -åtgärden, åsidosätter den åtkomst kontrollen per tabell och ger dem åtkomst till alla loggdata.
- Om en användare beviljas åtkomst per tabell men inga andra behörigheter, skulle de kunna komma åt loggdata från API: et, men inte från Azure Portal. Om du vill ge åtkomst till Azure Portal använder du Log Analytics Reader som bas roll.
- Administratörer för prenumerationen kommer att ha åtkomst till alla data typer, oavsett andra behörighets inställningar.
- Arbets ytans ägare behandlas som alla andra användare för åtkomst kontroll per tabell.
- Du bör tilldela roller till säkerhets grupper i stället för enskilda användare för att minska antalet tilldelningar. Detta hjälper dig också att använda befintliga grupp hanterings verktyg för att konfigurera och verifiera åtkomst.




## <a name="next-steps"></a>Nästa steg
* Se [översikt över Log Analytics-agenten](../../azure-monitor/platform/log-analytics-agent.md) samla in data från datorer i ditt datacenter eller andra moln.
* Om du vill konfigurera datainsamling från virtuella Azure-datorer läser du [Samla in data om Azure Virtual Machines](../../azure-monitor/learn/quick-collect-azurevm.md).  

