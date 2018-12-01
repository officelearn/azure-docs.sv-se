---
title: Översikt över Azure-aktivitetsloggen
description: Lär dig vad Azure-aktivitetsloggen är och hur du kan använda den för att förstå händelser som inträffar i din Azure-prenumeration.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: johnkem
ms.component: activitylog
ms.openlocfilehash: 22c9aeaa9f02d91ea90083fae69f28fa5984c565
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2018
ms.locfileid: "52680624"
---
# <a name="monitor-subscription-activity-with-the-azure-activity-log"></a>Övervaka aktivitet om prenumeration med Azure-aktivitetsloggen

Den **Azure-aktivitetsloggen** är en prenumerationslogg som ger insikt i händelser på prenumerationsnivå som har inträffat i Azure. Detta omfattar en mängd data från Azure Resource Manager driftdata uppdateringar på Service Health-händelser. Aktivitetsloggen kallades tidigare ”granskningsloggar” eller ”Driftloggar” eftersom administrativ kategori rapporter kontrollplanet händelser för dina prenumerationer. Med aktivitetsloggen kan du fastställa den ”vad, vem, och när” för alla skrivåtgärder (PUT, POST, ta bort) vidtas på resurserna i din prenumeration. Du kan också förstå statusen för åtgärden och andra relevanta egenskaper. Aktivitetsloggen inkluderar inte läsåtgärder (GET) eller åtgärder för resurser som använder klassiskt / ”RDFE”-modellen.

![Aktivitet loggar eller andra typer av loggar ](./media/monitoring-overview-activity-logs/Activity_Log_vs_other_logs_v5.png)

Bild 1: Aktivitetsloggar vs andra typer av loggar

Aktivitetsloggen skiljer sig från [diagnostikloggar](monitoring-overview-of-diagnostic-logs.md). Aktivitetsloggar ger information om åtgärder på en resurs från utsidan (”kontrollplanet”). Diagnostikloggar genereras av en resurs och ange information om hur den här resursen (”dataplanet”).

> [!WARNING]
> Azure-aktivitetsloggen är främst för aktiviteter som sker i Azure Resource Manager. Den spårar inte resurser med hjälp av klassisk/RDFE-modellen. Vissa typer av klassiska resurser har en proxy-resursprovider i Azure Resource Manager (till exempel Microsoft.ClassicCompute). Om du interagerar med en klassisk resurstyp via Azure Resource Manager med hjälp av dessa proxy resursprovidrar visas åtgärderna i aktivitetsloggen. Om du interagerar med en klassisk resurstyp utanför Azure Resource Manager-proxyservrar, registreras endast dina åtgärder i loggen igen. Åtgärden loggen kan sökas i ett separat avsnitt i portalen.
>
>

Du kan hämta händelser från din aktivitetslogg med Azure portal, CLI, PowerShell-cmdletar och REST-API för Azure Monitor.

> [!NOTE]
> [Nyare aviseringar](monitoring-overview-alerts.md) ger en förbättrad upplevelse när du skapar och hanterar aktivitet logga Varningsregler.  [Läs mer](alert-activity-log.md).


## <a name="categories-in-the-activity-log"></a>Kategorier i aktivitetsloggen
Aktivitetsloggen innehåller flera kategorier av data. Mer information om scheman av dessa kategorier [finns i den här artikeln](monitoring-activity-log-schema.md). Exempel på dessa är:
* **Administrativa** – den här kategorin innehåller en post för alla skapa, uppdatera och ta bort åtgärden åtgärder som utförs via Resource Manager. Exempel på typer av händelser som visas i den här kategorin är ”Skapa virtuell dator” och ”ta bort nätverkssäkerhetsgruppen” varje åtgärd som en användare eller program med hjälp av Resource Manager är utformat som en åtgärd på en viss resurstyp. Om åtgärdstypen är skriva, ta bort eller åtgärden, registreras poster i start- och lyckas eller misslyckas av åtgärden i den administrativa kategorin. Den administrativa kategorin omfattar även ändringar av rollbaserad åtkomstkontroll i en prenumeration.
* **Tjänstehälsa** – den här kategorin innehåller en post för alla service health incidenter som har inträffat i Azure. Ett exempel på typen av händelse som du ser i den här kategorin är ”SQL Azure i östra USA har drabbats av driftstopp”. Service health-händelser levereras i fem sorterna: åtgärd krävs, Assisted-återställning, Incident, underhåll, Information eller säkerhet, och visas bara om du har en resurs i den prenumeration som skulle påverkas av händelsen.
* **Resource Health** – den här kategorin innehåller en post för eventuella resource health-händelser som har inträffat för dina Azure-resurser. Ett exempel på typen av händelse som du ser i den här kategorin är ”virtuell dator hälsostatus ändrats till inte tillgänglig”. Resource health-händelser kan representera en av fyra health-status: tillgänglig, är inte tillgänglig, Degraderad och okänd. Dessutom kan resurshälsotillståndshändelser kategoriseras som användaren startat eller plattform initieras.
* **Aviseringen** – den här kategorin innehåller en post för alla Azure-aviseringar-aktiveringar. Ett exempel på typen av händelse som du ser i den här kategorin är ”processor på myVM har varit över 80 under de senaste 5 minuterna”. En mängd olika Azure-system har en datastyrd begrepp – du kan definiera en regel av något slag och få ett meddelande när villkoren matchar regeln. Varje gång en stöds Azure aviseringstyp ”aktiverar,' eller villkoren uppfylls för att generera ett meddelande, en post med aktiveringen skickas också till den här kategorin för aktivitetsloggen.
* **Automatisk skalning** – den här kategorin innehåller en post för alla händelser relaterade till driften av motorn för automatisk skalning baserat på alla inställningarna för automatisk skalning som du har definierat i din prenumeration. Ett exempel på typen av händelse som du ser i den här kategorin är ”autoskalning uppåt åtgärden misslyckades”. Med automatisk skalning kan du automatiskt skala ut eller skala antalet instanser i en resurstyp som stöds som är baserade på tid på dagen och/eller belastningen (mått) data med hjälp av en autoskalningsinställning. När villkoren uppfylls att skala upp eller ned, start- och lyckade eller misslyckade händelser registreras i den här kategorin.
* **Rekommendationen** – den här kategorin innehåller rekommendationshändelser från Azure Advisor.
* **Security** – den här kategorin innehåller en post för alla aviseringar som genereras av Azure Security Center. Ett exempel på typen av händelse som du ser i den här kategorin är ”misstänkt dubbelt filnamnstillägg fil körs”.
* **Principen** – den här kategorin innehåller inte några händelser; den är reserverad för framtida användning. 

## <a name="event-schema-per-category"></a>Händelseschema per kategori
[Se den här artikeln för att förstå Händelseschema för aktivitetslogg per kategori.](monitoring-activity-log-schema.md)

## <a name="what-you-can-do-with-the-activity-log"></a>Vad du kan göra med aktivitetsloggen
Här följer några av de saker som du kan göra med aktivitetsloggen:

![Azure-aktivitetslogg](./media/monitoring-overview-activity-logs/Activity_Log_Overview_v3.png)


* Fråga efter och visa den på den **Azure-portalen**.
* [Skapa en avisering i en händelse i aktivitetsloggen.](monitoring-activity-log-alerts.md)
* [Stream den till en **Händelsehubb** ](monitoring-stream-activity-logs-event-hubs.md) för inmatning av en tjänst från tredje part eller anpassade analyslösning till exempel Power BI.
* Analysera dem i Power BI med hjälp av den [ **Power BI-Innehållspaketet**](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).
* [Spara den till en **Lagringskonto** för arkivering eller manuell granskning](monitoring-archive-activity-log.md). Du kan ange kvarhållning tid (i dagar) med den **Loggprofil**.
* Fråga den via PowerShell-cmdleten, CLI eller REST API.

## <a name="query-the-activity-log-in-the-azure-portal"></a>Fråga i aktivitetsloggen i Azure portal
Du kan visa din aktivitetslogg på flera platser i Azure-portalen:
* Den **aktivitetsloggen** som du kan komma åt genom att söka efter aktivitetsloggen under **alla tjänster** i det vänstra navigeringsfönstret.
* **Övervaka** visas som standard i det vänstra navigeringsfönstret. Aktivitetsloggen är en del av Azure Monitor.
* De flesta **resurser**, till exempel bladet för en virtuell dator. Aktivitetsloggen är ett avsnitt om de flesta resursbladen och att klicka på den automatiskt filtrerar händelser till de som är relaterade till den specifika resursen.

Du kan filtrera din aktivitetslogg av de här fälten i Azure-portalen:
* TimeSpan - start- och tiden för händelser.
* Kategori – händelsekategori enligt beskrivningen ovan.
* Prenumeration – namn på en eller flera Azure-prenumeration.
* Resursgrupp – en eller flera resursgrupper inom dessa prenumerationer.
* Resursen (namn) - namnet på en viss resurs.
* Resurstyp - typen av resurs, till exempel Microsoft.Compute/virtualmachines.
* Åtgärdens namn – namnet på en Azure Resource Manager-åtgärd, till exempel Microsoft.SQL/servers/Write.
* Allvarlighetsgrad - allvarlighetsgraden för händelse (kritisk information, varning, fel,).
* Händelsen startades av - 'anroparen' eller användare som utförde åtgärden.
* Öppna sökning – det här är en öppen textruta för sökning som söker efter strängen i alla fält i alla händelser.

När du har definierat en uppsättning filter kan fästa du en fråga till instrumentpanelen i Azure att alltid hålla ett öga på specifika händelser.

För ännu mer kraft du kan klicka på den **loggar** ikon som visar aktivitetsloggdata i den [Log Analytics Activity Log Analytics-lösningen](../azure-monitor/platform/collect-activity-logs.md). Bladet aktivitetslogg erbjuder en grundläggande filter/Bläddra-miljö på loggar, men Log Analytics kan du pivotera, fråga och visualisera dina data på ett mer kraftfulla sätt.

## <a name="export-the-activity-log-with-a-log-profile"></a>Exportera aktivitetslogg till en Loggprofil
En **Loggprofil** styr hur din aktivitetslogg exporteras. Med en logg-profil kan konfigurera du:

* Där aktivitetsloggen ska skickas (Lagringskonto eller Event Hubs)
* Vilka kategorier (skriva, ta bort, åtgärd) ska skickas. *Betydelsen av ”kategori” i Loggprofiler och aktivitetslogghändelser är olika. I profilen för den Log representerar ”kategori” åtgärdstypen (skriva, ta bort, åtgärd). I en aktivitetslogghändelse representerar egenskapen ”kategori” källan eller typen av händelse (till exempel Administration, ServiceHealth, avisering och mer).*
* Vilka regioner (platser) ska exporteras. Se till att inkludera ”global”, eftersom många händelser i aktivitetsloggen är globala händelser.
* Hur länge aktivitetsloggen ska behållas i ett Lagringskonto.
    - En kvarhållning av noll dagar innebär loggar hålls alltid. I annat fall kan värdet vara valfritt antal dagar mellan 1 och 2147483647.
    - Om principerna för kvarhållning har angetts men lagra loggar i ett Lagringskonto är inaktiverad (till exempel om det bara Event Hubs eller Log Analytics-alternativen är markerade), påverkar principerna för kvarhållning inte.
    - Principer för kvarhållning är tillämpad per dag, så i slutet av en dag (UTC) loggar från den dag som är nu utöver kvarhållning principen tas bort. Till exempel om du har en bevarandeprincip för en dag skulle i början av dagen idag loggar från dag innan igår tas bort. Ta bort börjar vid midnatt UTC-tid, men Observera att det kan ta upp till 24 timmar innan loggarna som ska tas bort från ditt lagringskonto.

Du kan använda ett lagring eller event hub-namnområde som inte är i samma prenumeration som det genererar loggar. Den användare som konfigurerar inställningen måste ha lämplig RBAC-åtkomst till båda prenumerationerna.

> [!NOTE]
>  Du kan för närvarande inte arkivera data till ett lagringskonto som ligger bakom ett skyddat virtuellt nätverk.

> [!WARNING]
> Formatet för loggdata i lagringskontot ändras till JSON Lines den 1 november 2018. [Den här artikeln beskriver effekten av den här ändringen samt hur du uppdaterar dina verktyg för att hantera det nya formatet.](./monitor-diagnostic-logs-append-blobs.md) 
>
> 

De här inställningarna kan konfigureras via alternativet ”Export” i bladet aktivitetsloggen i portalen. De kan också konfigureras programmässigt [med hjälp av REST-API i Azure Monitor](https://msdn.microsoft.com/library/azure/dn931927.aspx), PowerShell-cmdletar eller CLI. En prenumeration kan bara ha en loggprofil.

### <a name="configure-log-profiles-using-the-azure-portal"></a>Konfigurera loggprofiler med Azure portal
Du kan strömma aktivitetsloggen till en Event Hub eller lagra dem i ett Lagringskonto med hjälp av alternativet ”Exportera till Event Hub” i Azure-portalen.

1. Gå till **aktivitetsloggen** med hjälp av menyn på vänster sida av portalen.

    ![Gå till aktivitetsloggen i portalen](./media/monitoring-overview-activity-logs/activity-logs-portal-navigate-v2.png)
2. Klicka på den **exportera till Event Hub** längst upp på bladet.

    ![Exportera-knappen i portalen](./media/monitoring-overview-activity-logs/activity-logs-portal-export-v2.png)
3. På bladet som visas, kan du välja:  
  * regioner som du vill exportera händelser
  * Storage-konto som du vill spara händelser
  * antal dagar som du vill behålla dessa händelser i lagring. En inställning på 0 dagar behåller loggarna alltid.
  * Service Bus Namespace där du vill ha en Händelsehubb skapas för direktuppspelning av dessa händelser.

     ![Exportera aktivitetslogg bladet](./media/monitoring-overview-activity-logs/activity-logs-portal-export-blade.png)
4. Klicka på **spara** dessa inställningar ska sparas. Inställningarna tillämpas omedelbart på din prenumeration.

### <a name="configure-log-profiles-using-the-azure-powershell-cmdlets"></a>Konfigurera loggprofiler med hjälp av Azure PowerShell-Cmdlets

#### <a name="get-existing-log-profile"></a>Hämta befintlig loggprofil

```
Get-AzureRmLogProfile
```

#### <a name="add-a-log-profile"></a>Lägg till en loggprofil

```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus -RetentionInDays 90 -Category Write,Delete,Action
```

| Egenskap  | Krävs | Beskrivning |
| --- | --- | --- |
| Namn |Ja |Namnet på din loggprofil. |
| StorageAccountId |Nej |Resurs-ID för det Lagringskonto som aktivitetsloggen ska sparas. |
| serviceBusRuleId |Nej |Service Bus regel-ID för Service Bus-namnområde som du vill ha händelsehubbar som skapats i. Är en sträng med det här formatet: `{service bus resource ID}/authorizationrules/{key name}`. |
| Plats |Ja |Kommaavgränsad lista över regioner som du vill samla in händelser i aktivitetsloggen. |
| RetentionInDays |Ja |Antal dagar för vilka händelser ska behållas, mellan 1 och 2147483647. Värdet noll lagrar loggarna på obestämd tid (alltid). |
| Kategori |Nej |Kommaavgränsad lista över kategorier som ska samlas in. Möjliga värden är skriva, ta bort och åtgärder. |

#### <a name="remove-a-log-profile"></a>Ta bort en loggprofil
```
Remove-AzureRmLogProfile -name my_log_profile
```

### <a name="configure-log-profiles-using-the-azure-cli"></a>Konfigurera loggprofiler med hjälp av Azure CLI

#### <a name="get-existing-log-profile"></a>Hämta befintlig loggprofil

```azurecli
az monitor log-profiles list
az monitor log-profiles show --name <profile name>
```

Den `name` egenskapen ska vara namnet på din loggprofil.

#### <a name="add-a-log-profile"></a>Lägg till en loggprofil

```azurecli
az monitor log-profiles create --name <profile name> \
    --locations <location1 location2 ...> \
    --location <location> \
    --categories <category1 category2 ...>
```

Fullständig dokumentation för att skapa en profil för övervakaren med CLI finns i den [CLI-kommandoreferens](/cli/azure/monitor/log-profiles#az-monitor-log-profiles-create)

#### <a name="remove-a-log-profile"></a>Ta bort en loggprofil

```azurecli
az monitor log-profiles delete --name <profile name>
```

## <a name="next-steps"></a>Nästa steg
* [Läs mer om aktivitetsloggen (tidigare granskningsloggar)](../azure-resource-manager/resource-group-audit.md)
* [Stream Azure-aktivitetsloggen till Event Hubs](monitoring-stream-activity-logs-event-hubs.md)
