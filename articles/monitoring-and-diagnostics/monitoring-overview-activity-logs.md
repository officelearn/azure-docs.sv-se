---
title: Översikt över Azure aktivitetsloggen | Microsoft Docs
description: Lär dig vad Azure-aktivitetsloggen är och hur du kan använda den för att förstå händelser i din Azure-prenumeration.
author: johnkemnetz
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: c274782f-039d-4c28-9ddb-f89ce21052c7
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2018
ms.author: johnkem
ms.openlocfilehash: 060f91e4bdd1dd2690a3e1f148f7c5e5e13d13ef
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2018
ms.locfileid: "35235672"
---
# <a name="monitor-subscription-activity-with-the-azure-activity-log"></a>Övervakaraktiviteten i prenumerationen med Azure-aktivitetsloggen

Den **Azure-aktivitetsloggen** är en prenumerationslogg som ger inblick i prenumerationsnivån händelser som har inträffat i Azure. Detta omfattar en mängd data från Azure Resource Manager användningsdata till uppdateringar på händelser för Hälsotjänst. Aktivitetsloggen tidigare kallades ”granskningsloggar” eller ”operativa loggar” eftersom de administrativa kategorin rapporter kontroll-plan händelserna för dina prenumerationer. Använder aktivitetsloggen, kan du bestämma den ' vad som, och när ' för alla skrivåtgärder (PUT, POST, ta bort) tas för de resurser i din prenumeration. Du kan också få status för åtgärden och andra relevanta egenskaper. Aktivitetsloggen omfattar inte läsåtgärder (GET) eller åtgärder för resurser som använder klassiskt / ”RDFE” modellen.

![Aktiviteten loggar eller andra typer av loggar ](./media/monitoring-overview-activity-logs/Activity_Log_vs_other_logs_v5.png)

Bild 1: Skicka aktivitetsloggar vs andra typer av loggar

Aktivitetsloggen skiljer sig från [diagnostikloggar](monitoring-overview-of-diagnostic-logs.md). Aktivitetsloggar ger information om åtgärder på en resurs från utsidan (”kontrollplan”). Diagnostik loggar orsakat av en resurs och ger information om handhavandet av resursen (”dataplan”).

> [!WARNING]
> Azure-aktivitetsloggen är främst för aktiviteter som sker i Azure Resource Manager. Den spårar inte resurser med hjälp av den klassiska/RDFE-modellen. Vissa typer av klassiska resurser har en proxy-resursprovidern i Azure Resource Manager (till exempel Microsoft.ClassicCompute). Om du interagerar med en klassisk resurstyp via Azure Resource Manager med hjälp av dessa providers för proxy-resurs visas åtgärderna i aktivitetsloggen. Om du interagerar med en klassisk resurstyp utanför Azure Resource Manager-proxyservrar registrerade dina åtgärder endast i loggen igen. Att det går Bläddra i Åtgärdsloggen i ett separat avsnitt i portalen.
>
>

Du kan hämta händelser från din aktivitetsloggen med Azure-portalen, CLI, PowerShell-cmdletar och REST-API för Azure-Monitor.

> [!NOTE]
>  [Nyare aviseringar](monitoring-overview-unified-alerts.md) ger en förbättrad upplevelse när skapa och hantera aktivitet logga Varningsregler.  [Läs mer](monitoring-activity-log-alerts-new-experience.md).

Visa följande videoklipp introduktion till aktivitetsloggen.
> [!VIDEO https://channel9.msdn.com/Blogs/Seth-Juarez/Logs-John-Kemnetz/player]


## <a name="categories-in-the-activity-log"></a>Kategorier i aktivitetsloggen
Aktivitetsloggen innehåller flera kategorier av data. Mer information om scheman av dessa kategorier [finns i den här artikeln](monitoring-activity-log-schema.md). Exempel på dessa är:
* **Administrativa** -den här kategorin innehåller posten för alla skapa, uppdatera, ta bort och åtgärden åtgärder utföras via Resource Manager. Exempel på vilka typer av händelser som visas i den här kategorin är ”Skapa virtuell dator” och ”ta bort nätverkssäkerhetsgrupp” varje åtgärd som en användare eller program med hjälp av hanteraren för filserverresurser modelleras som en åtgärd på en viss resurstyp. Om åtgärdstypen är Write-, Delete- eller åtgärd, är start-och lyckas eller misslyckas för denna åtgärd registrerade i den administrativa kategorin. Den administrativa kategorin omfattar även ändringar rollbaserad åtkomstkontroll i en prenumeration.
* **Tjänstens hälsa** -den här kategorin innehåller posten för alla service hälsa incidenter som har inträffat i Azure. Ett exempel på typen av händelse visas i den här kategorin är ”SQL Azure i östra USA upplever driftstopp”. Hälsa av tjänsten-händelser finns i fem sorter: åtgärd krävs stödd återställning, Incident, underhåll, Information eller säkerhet, och visas endast om du har en resurs i den prenumeration som skulle påverkas av händelsen.
* **Varning** -den här kategorin innehåller posten för alla aktiveringar av Azure-aviseringar. Ett exempel på typen av händelse visas i den här kategorin är ”processor på myVM har varit över 80 under de senaste fem minuterna”. En mängd Azure system har en aviseringar begrepp – du kan definiera en regel av något slag och ett meddelande när villkor matchar regeln. Varje gång en stöds Azure aviseringstyp 'aktiveras,' eller villkoren är uppfyllda för att generera ett meddelande, en post på aktiveringen skickas även till den här kategorin för aktivitetsloggen.
* **Autoskala** -den här kategorin innehåller posten för alla händelser relaterade till åtgärden Autoskala motorns baserat på automatiska inställningar du har definierat i din prenumeration. Ett exempel på typen av händelse visas i den här kategorin är ”Autoskala skalas upp misslyckades”. Använda autoskalning kan du automatiskt skala ut eller skala antalet instanser i en stöds resurstyp baserat på tid på dagen och/eller belastningen (mått) data med hjälp av en autoskalningsinställning. När villkoren är uppfyllda för att skala upp eller ned, start- och lyckades eller misslyckades händelser registreras i den här kategorin.
* **Rekommendation** -den här kategorin innehåller rekommendation händelser från Azure Advisor.
* **Säkerhet** -den här kategorin innehåller posten för alla aviseringar som genereras av Azure Security Center. Ett exempel på typen av händelse visas i den här kategorin är ”misstänkta dubbla tilläggsfilen utförs”.
* **Principen och Resource Health** -dessa kategorier innehåller inte några händelser, de är reserverad för framtida användning.

## <a name="event-schema-per-category"></a>Händelseschema per kategori
[Se den här artikeln för att förstå aktivitetsloggen Händelseschema per kategori.](monitoring-activity-log-schema.md)

## <a name="what-you-can-do-with-the-activity-log"></a>Vad du kan göra med aktivitetsloggen
Här följer några av de saker som du kan göra med aktivitetsloggen:

![Azure-aktivitetslogg](./media/monitoring-overview-activity-logs/Activity_Log_Overview_v3.png)


* Fråga efter och visa den i den **Azure-portalen**.
* [Skapa en avisering för en aktivitet händelselogg.](monitoring-activity-log-alerts.md)
* [Strömma den till en **Händelsehubb** ](monitoring-stream-activity-logs-event-hubs.md) för införandet av en tjänst från tredje part eller anpassade analytics lösning, till exempel PowerBI.
* Analysera i PowerBI med hjälp av den [ **PowerBI Innehållspaketet**](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).
* [Spara den till en **Lagringskonto** för arkivering eller Manuell kontroll](monitoring-archive-activity-log.md). Du kan ange kvarhållning tid (i dagar) med den **loggen profil**.
* Fråga den via PowerShell-cmdleten, CLI eller REST API.

## <a name="query-the-activity-log-in-the-azure-portal"></a>Frågan aktivitetsloggen i Azure-portalen
Du kan visa din aktivitetsloggen på flera platser i Azure-portalen:
* Den **aktivitetsloggen** som du har åtkomst till genom att söka efter aktivitetsloggen under **alla tjänster** i det vänstra navigeringsfönstret.
* **Övervakaren** visas som standard i det vänstra navigeringsfönstret. Aktivitetsloggen är en del av Azure-Monitor.
* Alla resurser **resurs**, till exempel bladet konfigurationen för en virtuell dator. Aktivitetsloggen är vara något av avsnitten om de flesta av dessa resurs-blad och klicka på den automatiskt filtrerar händelser som rör specifika resursen.

Du kan filtrera din aktivitetsloggen av fälten i Azure-portalen:
* TimeSpan - tiden för start- och slutdatum för händelser.
* Kategori – händelsekategori enligt beskrivningen ovan.
* Prenumerationen - namn för en eller flera Azure-prenumeration.
* Resursgrupp – en eller flera resursgrupper i dessa prenumerationer.
* Resurs (namn) - namnet på en viss resurs.
* Resurstyp - typ av resurs, till exempel Microsoft.Compute/virtualmachines.
* Åtgärdsnamnet - namnet på en Azure Resource Manager-åtgärd, till exempel Microsoft.SQL/servers/Write.
* Allvarlighetsgrad - allvarlighetsgrad för händelse (kritisk information, varning, fel,).
* Händelse som initieras av - 'anroparen' eller användaren som utförde åtgärden.
* Öppna sökning - detta är en öppen Sök textruta som söker efter strängen i alla fält i alla händelser.

När du har definierat en uppsättning filter kan spara du den som en fråga som är beständiga mellan sessioner om du behöver utföra samma fråga med dessa filter igen i framtiden. Du kan även fästa en fråga på instrumentpanelen i Azure ska alltid hålla ett öga på specifika händelser.

Klicka på ”Använd” kör frågan och visa alla matchande händelser. När du klickar på en händelse i listan visar en sammanfattning av händelsen samt att händelsen fullständig rådata JSON.

För ännu mer kraft kan du klicka på den **loggen Sök** ikon som visar data i aktivitetsloggen den [Log Analytics aktivitet logganalys lösning](../log-analytics/log-analytics-activity.md). Bladet aktivitetsloggen erbjuder en grundläggande filter/Bläddra upplevelse på loggar, men Log Analytics gör det möjligt att pivotera, fråga och visualisera dina data mer kraftfulla sätt.

## <a name="export-the-activity-log-with-a-log-profile"></a>Exportera aktivitetsloggen med en logg-profil
En **loggen profil** styr hur din aktivitetsloggen exporteras. Med en logg-profil kan konfigurera du:

* Där aktivitetsloggen ska skickas (Storage-konto eller Händelsehubbar)
* Vilka kategorier (, ta bort, skrivåtgärd) ska skickas. *Enligt ”kategori” i loggen profiler och aktivitetsloggen händelser är olika. I profilen loggen representerar ”kategori” typ av åtgärd (, ta bort, skrivåtgärd). Egenskapen ”kategori” representerar käll- eller typen av händelse (till exempel Administration, ServiceHealth, aviseringen och mer) i en aktivitet händelselogg.*
* Vilka regioner (platser) ska exporteras. Se till att inkludera ”globala” som många händelser i aktivitetsloggen är globala händelser.
* Hur länge aktivitetsloggen ska behållas i ett Lagringskonto.
    - En kvarhållning av noll dagar innebär loggar behålls alltid. I annat fall kan värdet vara valfritt antal dagar mellan 1 och 2147483647.
    - Om bevarandeprinciper har angetts men lagring loggar i ett Storage-konto har inaktiverats (till exempel om endast Händelsehubbar eller logganalys alternativen är markerade), har bevarandeprinciper ingen effekt.
    - Bevarandeprinciper är tillämpade per dag, så i slutet av dagen (UTC) loggar från den dagen är nu utöver kvarhållning princip tas bort. Till exempel om du har en bevarandeprincip på en dag skulle i början av dagen idag loggar från dag före igår tas bort. Ta bort börjar vid midnatt UTC-tid, men Observera att det kan ta upp till 24 timmar för loggar som ska tas bort från ditt lagringskonto.

Du kan använda ett lagringsutrymme konto eller händelse hubb namnområde som inte är i samma prenumeration som en avger loggar. Den användare som konfigurerar inställningen måste ha RBAC lämplig åtkomst till båda prenumerationer.

> [!NOTE]
>  Du kan inte arkivera data till en lagringsplats för kontot som bakom skyddade virtuella nätverk.

De här inställningarna kan konfigureras via alternativet ”Export” i bladet aktivitetsloggen i portalen. De kan också konfigureras via programmering [med hjälp av REST API för Azure-Monitor](https://msdn.microsoft.com/library/azure/dn931927.aspx), PowerShell-cmdlets eller CLI. En prenumeration kan bara ha en logg-profil.

### <a name="configure-log-profiles-using-the-azure-portal"></a>Konfigurera loggen profiler med hjälp av Azure portal
Du kan strömma aktivitetsloggen till en Händelsehubb eller lagra dem i ett Lagringskonto med hjälp av alternativet ”Export” i Azure-portalen.

1. Gå till **aktivitetsloggen** med hjälp av menyn till vänster i portalen.

    ![Navigera till aktivitetsloggen i portalen](./media/monitoring-overview-activity-logs/activity-logs-portal-navigate.png)
2. Klicka på den **exportera** längst upp på bladet.

    ![Exportera-knappen i portalen](./media/monitoring-overview-activity-logs/activity-logs-portal-export.png)
3. Du kan välja i bladet som visas:  
  * regioner som du vill exportera händelser
  * Storage-konto som du vill spara händelser
  * antal dagar som du vill behålla dessa händelser i lagringen. En inställning på 0 dagar behåller alltid loggarna.
  * Service Bus Namespace som du vill att en Händelsehubb skapas för direktuppspelning av dessa händelser.

     ![Exportera aktivitetsloggen bladet](./media/monitoring-overview-activity-logs/activity-logs-portal-export-blade.png)
4. Klicka på **spara** dessa inställningar ska sparas. Inställningarna tillämpas omedelbart på din prenumeration.

### <a name="configure-log-profiles-using-the-azure-powershell-cmdlets"></a>Konfigurera loggen profiler med hjälp av Azure PowerShell-Cmdlets

#### <a name="get-existing-log-profile"></a>Hämta befintlig logg-profil

```
Get-AzureRmLogProfile
```

#### <a name="add-a-log-profile"></a>Lägg till en logg-profil

```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action
```

| Egenskap  | Krävs | Beskrivning |
| --- | --- | --- |
| Namn |Ja |Namnet på loggen profilen. |
| StorageAccountId |Nej |Resurs-ID för det Lagringskonto där aktivitetsloggen ska sparas. |
| serviceBusRuleId |Nej |Service Bus regel-ID för Service Bus-namnområde som har skapats i händelsehubbar. Är en sträng med formatet: `{service bus resource ID}/authorizationrules/{key name}`. |
| Platser |Ja |Kommaavgränsad lista över regioner som du vill samla in händelser för aktivitetsloggen. |
| retentionInDays |Ja |Antal dagar för vilka händelser som ska behållas, mellan 1 och 2147483647. Värdet noll lagrar loggarna på obestämd tid (alltid). |
| Kategorier |Nej |Kommaavgränsad lista över kategorier som ska samlas in. Möjliga värden är skriva och ta bort åtgärd. |

#### <a name="remove-a-log-profile"></a>Ta bort en logg-profil
```
Remove-AzureRmLogProfile -name my_log_profile
```

### <a name="configure-log-profiles-using-the-azure-cli-20"></a>Konfigurera loggen profiler som använder Azure CLI 2.0

#### <a name="get-existing-log-profile"></a>Hämta befintlig logg-profil

```azurecli
az monitor log-profiles list
az monitor log-profiles show --name <profile name>
```

Den `name` egenskapen ska vara namnet på loggen-profilen.

#### <a name="add-a-log-profile"></a>Lägg till en logg-profil

```azurecli
az monitor log-profiles create --name <profile name> \
    --locations <location1 location2 ...> \
    --location <location> \
    --categories <category1 category2 ...>
```

Fullständig dokumentation för att skapa en profil för övervakaren med CLI finns det [CLI kommandoreferens](/cli/azure/monitor/log-profiles#az-monitor-log-profiles-create)

#### <a name="remove-a-log-profile"></a>Ta bort en logg-profil

```azurecli
az monitor log-profiles delete --name <profile name>
```

## <a name="next-steps"></a>Nästa steg
* [Mer information om aktivitetsloggen (tidigare granskningsloggar)](../azure-resource-manager/resource-group-audit.md)
* [Dataströmmen Azure aktivitetsloggen i Händelsehubbar](monitoring-stream-activity-logs-event-hubs.md)
