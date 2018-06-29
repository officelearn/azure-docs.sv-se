---
title: Vanliga frågor och svar om Azure-trafik analytics | Microsoft Docs
description: Få svar på några av de vanligaste frågorna om trafik analytics.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2018
ms.author: jdial
ms.openlocfilehash: a4b87d92751c84d96bc70915d16adae7943c145e
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37062885"
---
# <a name="traffic-analytics-frequently-asked-questions"></a>Vanliga och frågor svar om trafik analytics

Den här artikeln samlar in på ett ställe många av de vanligaste frågorna om trafik analyser i Azure Nätverksbevakaren.

## <a name="what-are-the-prerequisites-to-use-traffic-analytics"></a>Vad är förutsättningar för att använda trafik analytics?

Trafik analytics kräver följande förutsättningar:

- En prenumeration Nätverksbevakaren aktiverat.
- Nätverkssäkerhetsgrupp (NSG) flödet loggar har aktiverats för NSG: er som du vill övervaka.
- Ett Azure Storage-konto för lagring av rådata flog loggar.
- Ett Azure logganalys-arbetsyta, med läs- och skrivbehörighet.

Ditt konto måste uppfylla något av följande för att aktivera trafik analytics:

- Ditt konto måste vara tilldelade till en av följande roller på prenumerationsnivån: kontoadministratör, administratör eller medadministratör.
- Kontot måste ha något av följande roller för rollbaserad åtkomstkontroll (RBAC) i omfånget för prenumerationen: ägare, deltagare, reader eller network-deltagare.
- Om ditt konto inte har tilldelats någon av ovanstående roller, måste den tilldelas en anpassad roll som tilldelats följande åtgärder på prenumerationsnivån.
            
    - Microsoft.Network/applicationGateways/read
    - Microsoft.Network/connections/read
    - Microsoft.Network/loadBalancers/read 
    - Microsoft.Network/localNetworkGateways/read 
    - Microsoft.Network/networkInterfaces/read 
    - Microsoft.Network/networkSecurityGroups/read 
    - Microsoft.Network/publicIPAddresses/read
    - Microsoft.Network/routeTables/read
    - Microsoft.Network/virtualNetworkGateways/read 
    - Microsoft.Network/virtualNetworks/read
        
Så här kontrollerar roller som tilldelas en användare för en prenumeration:

1. Logga in på Azure med hjälp av **Login-AzureRmAccount**. 

2. Välj prenumerationen som krävs med hjälp av **Select-AzureRmSubscription**. 

3. Om du vill visa en lista med alla de roller som har tilldelats en angiven användare använda **AzureRmRoleAssignment Get - SignInName [användarens e-postadress] - IncludeClassicAdministrators**. 

Kontakta respektive prenumeration-administratören för att få åtkomst till kör kommandon om du inte ser några utdata. Mer information finns i [Hantera rollbaserad åtkomstkontroll med Azure PowerShell](https://docs.microsoft.com/en-us/azure/role-based-access-control/role-assignments-powershell).


## <a name="in-which-azure-regions-are-traffic-analytics-available"></a>I vilka Azure regioner finns trafik analytics?

Du kan använda trafik analytics för NSG: er i någon av de följande regionerna som stöds: Väst centrala USA, östra USA, östra USA 2, norra centrala USA, södra centrala USA, centrala USA, västra USA, västra USA 2, Västeuropa, Nordeuropa, Storbritannien, Väst, Storbritannien, Syd, östra, Australien, sydost och Sydostasien. Logganalys-arbetsytan måste finnas i den Väst centrala USA, östra USA, västra Europa, Storbritannien, Syd, Australien, sydost eller Sydostasien region.

## <a name="can-the-nsgs-i-enable-flow-logs-for-be-in-different-regions-than-my-workspace"></a>Kan NSG: er som jag aktivera flödet loggar för finnas i olika regioner än Min arbetsyta?

Ja, kan dessa NSG: er i olika regioner än logganalys-arbetsytan.

## <a name="can-multiple-nsgs-be-configured-within-a-single-workspace"></a>Kan flera NSG: er konfigureras i en enda arbetsyta?

Ja.

## <a name="can-i-use-an-existing-workspace"></a>Kan jag använda en befintlig arbetsyta?

Ja. Om du väljer en befintlig arbetsyta se till att den har migrerats till det nya språket i fråga. Om du inte vill uppgradera arbetsytan måste du skapa en ny. Mer information om nya frågespråket finns [Azure logganalys uppgradera till ny logg sökning](../log-analytics/log-analytics-log-search-upgrade.md).

## <a name="can-my-azure-storage-account-be-in-one-subscription-and-my-operations-management-suite-workspace-be-in-a-different-subscription"></a>Kan min Azure Storage-konto i en prenumeration och min Operations Management Suite-arbetsyta vara i en annan prenumeration?

Ja, Azure Storage-konto kan vara i en prenumeration och din Operations Management Suite-arbetsyta som kan vara i en annan prenumeration.

## <a name="can-i-store-raw-logs-in-a-different-subscription"></a>Kan jag lagra loggarna i en annan prenumeration?

Nej. Du kan lagra loggarna i alla storage-konto där en NSG är aktiverat för flöde loggar. Både lagringskontot och på loggarna måste vara i samma prenumeration och region.

## <a name="what-if-i-cant-configure-an-nsg-for-traffic-analytics-due-to-a-not-found-error"></a>Vad händer om jag kan inte konfigurera en NSG för analys av trafik på grund av felet ”kunde inte hittas”?

Välj en region som stöds. Om du väljer en område som inte stöds visas felmeddelandet ”kunde inte hittas”. Regionerna som stöds anges tidigare i den här artikeln.

## <a name="what-if-i-am-getting-the-status-failed-to-load-under-the-nsg-flow-logs-page"></a>Vad händer om jag får status, ”kunde inte läsas in”, under NSG flödet loggar sidan?

Providern Microsoft.Insights måste registreras för flöde loggning för att fungera korrekt. Om du inte är säker på om Microsoft.Insights-providern är registrerad för din prenumeration ersätter *xxxxx-xxxxx-xxxxxx-xxxx* i följande kommando och kör följande kommandon från PowerShell:

```powershell-interactive
**Select-AzureRmSubscription** -SubscriptionId xxxxx-xxxxx-xxxxxx-xxxx
**Register-AzureRmResourceProvider** -ProviderNamespace Microsoft.Insights
```

## <a name="i-have-configured-the-solution-why-am-i-not-seeing-anything-on-the-dashboard"></a>Jag har konfigurerat lösningen. Varför inte visas något på instrumentpanelen?

Instrumentpanelen kan ta upp till 30 minuter innan den visas första gången. Lösningen måste först aggregera tillräckligt med data att härleda insikter som beskrivande. Sedan genererar rapporter. 

## <a name="what-if-i-get-this-message-we-could-not-find-any-data-in-this-workspace-for-selected-time-interval-try-changing-the-time-interval-or-select-a-different-workspace"></a>Vad händer om jag får det här meddelandet: ”Vi kunde inte hitta några data i den här arbetsytan för valda tidsintervallet. Försök att ändra tidsintervallet eller välj en annan arbetsyta ”.?

Prova följande alternativ:
- Ändra tidsintervallet i det övre fältet.
- Välj en annan logganalys-arbetsyta i det övre fältet.
- Försök att öppna trafik analytics efter 30 minuter om det nyligen har aktiverats.
    
Om problemet kvarstår ökar du frågor i den [röst användarforum](https://feedback.azure.com/forums/217313-networking?category_id=195844).

## <a name="what-if-i-get-this-message-analyzing-your-nsg-flow-logs-for-the-first-time-this-process-may-take-20-30-minutes-to-complete-check-back-after-some-time-2-if-the-above-step-doesnt-work-and-your-workspace-is-under-the-free-sku-then-check-your-workspace-usage-here-to-validate-over-quota-else-refer-to-faqs-for-further-information"></a>Vad händer om jag får det här meddelandet: ”analysera din NSG trafikflödet loggar för första gången. Den här processen kan ta 20 – 30 minuter att slutföra. Kontrollera igen efter en stund. 2) om steget ovan fungerar inte och din arbetsyta är under den kostnadsfria SKU: N, kontrollera din användning av arbetsytan här för att verifiera över kvoten, eller Läs i vanliga frågor och svar för ytterligare information ”.?

Du kan se det här meddelandet eftersom:
- Trafik analytics nyligen har aktiverats och kan inte ännu har sammanställd tillräckligt med data att härleda insikter som beskrivande.
- Du använder den kostnadsfria versionen av Operations Management Suite-arbetsyta och den överskrider kvotgränserna. Du kan behöva använda en arbetsyta med en större kapacitet.
    
Om problemet kvarstår ökar du frågor i den [röst användarforum](https://feedback.azure.com/forums/217313-networking?category_id=195844).
    
## <a name="what-if-i-get-this-message-looks-like-we-have-resources-data-topology-and-no-flows-information-meanwhile-click-here-to-see-resources-data-and-refer-to-faqs-for-further-information"></a>Vad händer om jag får det här meddelandet: ”verkar som om vi har resursdata (topologin) och ingen information om flöden. Under tiden Klicka här för att se resursdata och ytterligare information finns i vanliga frågor och svar ”.?

Du ser resurser information på instrumentpanelen; dock finns inga flödet-relaterad statistik. Data kan inte finnas på grund av ingen kommunikation som flödar mellan resurser. Vänta 60 minuter och kontrollera status. Om problemet kvarstår och du är säker på att kommunikationsflöden mellan resurserna finns, öka frågor i den [röst användarforum](https://feedback.azure.com/forums/217313-networking?category_id=195844).

## <a name="can-i-configure-traffic-analytics-using-powershell-or-an-azure-resource-manager-template-or-client"></a>Kan jag konfigurera trafik analytics med hjälp av PowerShell eller Azure Resource Manager-mall eller klient?

Du kan konfigurera trafik analytics med hjälp av Windows PowerShell från och med version 6.2.1. Om du vill konfigurera flödet loggning och analys av trafik för en specifik NSG med hjälp av cmdleten Set finns [Set AzureRmNetworkWatcherConfigFlowLog](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermnetworkwatcherconfigflowlog?view=azurermps-6.3.0). Flödet loggnings- och trafik analytics status för en specifik NSG finns [Get-AzureRmNetworkWatcherFlowLogStatus](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermnetworkwatcherflowlogstatus?view=azurermps-6.3.0).

För närvarande kan du använda en Azure Resource Manager-mall för att konfigurera trafik analytics.

Om du vill konfigurera trafik analytics med hjälp av en Azure Resource Manager-klienten finns i följande exempel.

**Ange cmdlet-exemplet:**
```
#Requestbody parameters
$TAtargetUri ="/subscriptions/<NSG subscription id>/resourceGroups/<NSG resource group name>/providers/Microsoft.Network/networkSecurityGroups/<name of NSG>"
$TAstorageId = "/subscriptions/<storage subscription id>/resourcegroups/<storage resource group name> /providers/microsoft.storage/storageaccounts/<storage account name>"
$networkWatcherResourceGroupName = "<network watcher resource group name>"
$networkWatcherName = "<network watcher name>"

$requestBody = 
@"
{
    'targetResourceId': '${TAtargetUri}',
    'properties': 
    {
        'storageId': '${TAstorageId}',
        'enabled': '<true to enable flow log or false to disable flow log>',
        'retentionPolicy' : 
        {
            days: <enter number of days like to retail flow logs in storage account>,
            enabled: <true to enable retention or false to disable retention>
        }
    },
    'flowAnalyticsConfiguration':
    {
                'networkWatcherFlowAnalyticsConfiguration':
      {
        'enabled':,<true to enable traffic analytics or false to disable traffic analytics>
        'workspaceId':'bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb',
        'workspaceRegion':'<workspace region>',
        'workspaceResourceId':'/subscriptions/<workspace subscription id>/resourcegroups/<workspace resource group name>/providers/microsoft.operationalinsights/workspaces/<workspace name>'
        
      }

    }
}
"@
$apiversion = "2016-09-01"

armclient login
armclient post "https://management.azure.com/subscriptions/<NSG subscription id>/resourceGroups/<network watcher resource group name>/providers/Microsoft.Network/networkWatchers/<network watcher name>/configureFlowlog?api-version=${apiversion}" $requestBody
```
**Hämta cmdlet-exemplet:**
```
#Requestbody parameters
$TAtargetUri ="/subscriptions/<NSG subscription id>/resourceGroups/<NSG resource group name>/providers/Microsoft.Network/networkSecurityGroups/<NSG name>"


$requestBody = 
@"
{
    'targetResourceId': '${TAtargetUri}'
}
“@


armclient login
armclient post "https://management.azure.com/subscriptions/<NSG subscription id>/resourceGroups/<network watcher resource group name>/providers/Microsoft.Network/networkWatchers/<network watcher name>/queryFlowLogStatus?api-version=${apiversion}" $requestBody
```



## <a name="how-is-traffic-analytics-priced"></a>Hur prissätts trafik analytics?

Trafik analytics mäts. Den avläsning baseras på bearbetning av loggdata för flödet av tjänsten och lagra den resulterande förbättrad loggar i logganalys-arbetsytan. Mer information finns i [priser plan](https://azure.microsoft.com/en-us/pricing/details/network-watcher/). 

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-geo-map-view"></a>Hur kan navigera med hjälp av tangentbordet i kartvyn geo?

Sidan geo kartan innehåller två huvudavsnitt:
    
- **Banderoll**: banderoll överst i geo kartan innehåller knappar för att välja trafikfilter distribution (till exempel distribution, trafik från länder och skadliga). När du väljer en knapp har motsvarande filter tillämpats på kartan. Om du klickar på aktiva, visar kartan active datacenter i distributionen.
- **Kartan**: under banderollen, avsnittet karta visar Trafikfördelningen mellan Azure-datacenter och länder.
    
### <a name="keyboard-navigation-on-the-banner"></a>Tangentbordsnavigering i popup-meddelandet
    
- Som standard är val på sidan geo kartan för popup-meddelandet ”Azure domänkontrollanter”.
- Om du vill flytta till ett annat filter kan använda antingen den `Tab` eller `Right arrow` nyckel. Flytta bakåt med antingen den `Shift+Tab` eller `Left arrow` nyckel. Navigering framåt från vänster till höger, följt av längst upp och ned.
- Tryck på `Enter` eller `Down` för att använda det valda filtret. Utifrån filterval och distribution av markeras en eller flera noder under avsnittet kartan.
- Växla mellan banderoll och mappar, trycka på `Ctrl+F6`.
        
### <a name="keyboard-navigation-on-the-map"></a>Tangentbordsnavigering på kartan
    
- När du har valt filter i popup-meddelandet och trycks ned `Ctrl+F6`, fokus flyttas till en av de markerade noderna (**Azure-datacenter** eller **Land/Region**) i kartvyn.
- Om du vill flytta till andra noder som är markerade i kartan, kan du använda antingen `Tab` eller `Right arrow` nyckel för flyttning framåt. Använd `Shift+Tab` eller `Left arrow` nyckel för bakåt rörelse.
- Markera alla markerade noder i kartan med hjälp av `Enter` eller `Down arrow` nyckel.
- På val av dessa noder flyttar fokus till den **Information verktygslådan** för noden. Som standard fokus flyttas till knappen stängd den **Information verktygslådan**. Ytterligare flytta inuti den **rutan** visa använder `Right arrow` och `Left arrow` för att gå framåt och bakåt, respektive. När du trycker på `Enter` har samma effekt som att välja knappen fokuserad i den **Information verktygslådan**.
- När du trycker på `Tab` när fokus ligger på den **Information verktygslådan**, fokus flyttas till slutpunkter i samma kontinent som den markerade noden. Använd den `Right arrow` och `Left arrow` för att gå igenom dessa slutpunkter.
- Flytta till andra flödet slutpunkter eller kontinent kluster med `Tab` för flyttning framåt och `Shift+Tab` för bakåt rörelse.
- När fokus ligger på **kontinent kluster**, använda den `Enter` eller `Down` för att markera slutpunkterna i det kontinent klustret. Om du vill flytta slutpunkter och Stäng i informationsrutan i det kontinent klustret, kan du använda antingen den `Right arrow` eller `Left arrow` nyckel för framåt och bakåt flytt respektive. Du kan använda på valfri slutpunkt `Shift+L` växla till raden anslutning från den markerade noden till slutpunkten. Du kan trycka på `Shift+L` igen för att flytta till den valda slutpunkten.
        
### <a name="keyboard-navigation-at-any-stage"></a>Tangentbordsnavigering när som helst
    
- `Esc` döljer expanderade markeringen.
- Den `Up arrow` nyckel utför samma åtgärd som `Esc`. Den `Down arrow` nyckel utför samma åtgärd som `Enter`.
- Använd `Shift+Plus` att zooma in, och `Shift+Minus` att zooma ut.

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-virtual-network-topology-view"></a>Hur kan navigera med hjälp av tangentbordet i vyn virtuella nätverkets topologi?

Virtuella nätverk topologi sidan innehåller två huvudavsnitt:
    
- **Banderoll**: banderoll överst på den virtuella nätverk topologin innehåller knappar för att välja distribution trafikfilter (till exempel anslutna virtuella nätverk, frånkopplade virtuella nätverk och offentliga IP-adresser). När du väljer en knapp har motsvarande filter tillämpats på topologin. Om du klickar på aktiva, visar topologin aktiva virtuella nätverk i din distribution.
- **Topologi**: under banderollen, avsnittet topologi visar Trafikfördelningen mellan virtuella nätverk.
    
### <a name="keyboard-navigation-on-the-banner"></a>Tangentbordsnavigering i popup-meddelandet
    
- Som standard är val på sidan virtuella nätverk topologi för popup-meddelandet ”ansluten Vnet”.
- Flytta till ett annat filter genom att använda den `Tab` för att flytta framåt. Flytta bakåt med den `Shift+Tab` nyckel. Navigering framåt från vänster till höger, följt av längst upp och ned.
- Tryck på `Enter` att tillämpa det valda filtret. En eller flera noder (virtuella nätverk) under avsnittet topologi markeras utifrån filterval och distribution.
- Växla mellan informationssidan och topologi, trycka på `Ctrl+F6`.
        
### <a name="keyboard-navigation-on-the-topology"></a>Tangentbordsnavigering på topologin
    
- När du har valt filter i popup-meddelandet och trycks ned `Ctrl+F6`, fokus flyttas till en av de markerade noderna (**VNet**) i vyn topologi.
- Flytta till andra noder som är markerade i vyn topologi med den `Shift+Right arrow` nyckel för flyttning framåt. 
- För markerade noder, flyttar fokus till den **Information verktygslådan** för noden. Som standard flyttar fokus till den **mer** knappen på den **Information verktygslådan**. Ytterligare flytta inuti den **rutan** visa använder den `Right arrow` och `Left arrow` för att gå framåt och bakåt, respektive. När du trycker på `Enter` har samma effekt som att välja knappen fokuserad i den **Information verktygslådan**.
- På val av dessa noder du besöker dess anslutningar, en i taget, genom att trycka på den `Shift+Left arrow` nyckel. Fokus flyttas till den **Information verktygslådan** för anslutningen. När som helst fokus kan flyttas tillbaka till noden genom att trycka på `Shift+Right arrow` igen.
    

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-subnet-topology-view"></a>Hur kan navigera med hjälp av tangentbordet i undernätet topologiska vyn?

Virtuella undernät topologi sidan innehåller två huvudavsnitt:
    
- **Banderoll**: banderoll överst på den virtuella undernät-topologin innehåller knappar för att välja trafikfilter distribution (till exempel Active, medel och Gateway-undernät). När du väljer en knapp har motsvarande filter tillämpats på topologin. Om du klickar på aktiva, visar topologin aktiva virtuella undernätverk i distributionen.
- **Topologi**: under banderollen, avsnittet topologi visar Trafikfördelningen mellan virtuella undernät.
    
### <a name="keyboard-navigation-on-the-banner"></a>Tangentbordsnavigering i popup-meddelandet
    
- Som standard är val på sidan virtuella undernät-topologi för popup-meddelandet ”undernät”.
- Flytta till ett annat filter genom att använda den `Tab` för att flytta framåt. Flytta bakåt med den `Shift+Tab` nyckel. Navigering framåt från vänster till höger, följt av längst upp och ned.
- Tryck på `Enter` att tillämpa det valda filtret. Utifrån filterval och distribution av markeras en eller flera noder (undernät) under avsnittet topologi.
- Växla mellan informationssidan och topologi, trycka på `Ctrl+F6`.
        
### <a name="keyboard-navigation-on-the-topology"></a>Tangentbordsnavigering på topologin
    
- När du har valt filter i popup-meddelandet och trycks ned `Ctrl+F6`, fokus flyttas till en av de markerade noderna (**undernät**) i vyn topologi.
- Flytta till andra noder som är markerade i vyn topologi med den `Shift+Right arrow` nyckel för flyttning framåt. 
- För markerade noder, flyttar fokus till den **Information verktygslådan** för noden. Som standard flyttar fokus till den **mer** knappen på den **Information verktygslådan**. Ytterligare flytta inuti den **rutan** visa använder `Right arrow` och `Left arrow` för att gå framåt och bakåt, respektive. När du trycker på `Enter` har samma effekt som att välja knappen fokuserad i den **Information verktygslådan**.
- På val av dessa noder du besöker dess anslutningar, en i taget, genom att trycka på `Shift+Left arrow` nyckel. Fokus flyttas till den **Information verktygslådan** för anslutningen. När som helst fokus kan flyttas tillbaka till noden genom att trycka på `Shift+Right arrow` igen.    

