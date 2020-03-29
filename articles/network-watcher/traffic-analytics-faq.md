---
title: Vanliga frågor och svar om Azure-trafikanalys | Microsoft-dokument
description: Få svar på några av de vanligaste frågorna om trafikanalys.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2018
ms.author: damendo
ms.openlocfilehash: 5e31ed905f05070c8715a63ef3386b0006df0a75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840629"
---
# <a name="traffic-analytics-frequently-asked-questions"></a>Vanliga frågor och svar om Traffic Analytics

Den här artikeln samlar på ett ställe många av de vanligaste frågorna om trafikanalys i Azure Network Watcher.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-are-the-prerequisites-to-use-traffic-analytics"></a>Vilka är förutsättningarna för att använda trafikanalys?

Traffic Analytics kräver följande förutsättningar:

- En Network Watcher-aktiverad prenumeration.
- NSG-flödesloggar (Network Security Group) aktiverade för de NSG:er som du vill övervaka.
- Ett Azure Storage-konto för att lagra råflödesloggar.
- En Azure Log Analytics-arbetsyta med läs- och skrivåtkomst.

Ditt konto måste uppfylla något av följande för att möjliggöra trafikanalys:

- Ditt konto måste ha någon av följande rollbaserade åtkomstkontroll (RBAC) roller på prenumerationsomfattningen: ägare, deltagare, läsare eller nätverksdeltagare.
- Om ditt konto inte har tilldelats någon av de tidigare listade rollerna måste det tilldelas en anpassad roll som tilldelas följande åtgärder på prenumerationsnivå.
            
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
        
Så här kontrollerar du roller som tilldelats en användare för en prenumeration:

1. Logga in på Azure med **Login-AzAccount**. 

2. Välj den prenumeration som krävs med **Select-AzSubscription**. 

3. Om du vill visa en lista över alla roller som har tilldelats en angiven användare använder du **Get-AzRoleAssignment -SignInName [användares e-post] -IncludeClassicAdministrators**. 

Om du inte ser några utdata kontaktar du respektive prenumerationsadministratör för att få åtkomst för att köra kommandona. Mer information finns i [Hantera rollbaserad åtkomstkontroll med Azure PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell).


## <a name="in-which-azure-regions-is-traffic-analytics-available"></a>I vilka Azure-regioner är Traffic Analytics tillgängligt?

Du kan använda trafikanalys för NSG:er i någon av följande regioner som stöds:
- Kanada, centrala
- USA, västra centrala
- USA, östra
- USA, östra 2
- USA, norra centrala
- USA, södra centrala
- USA, centrala
- USA, västra
- USA, västra 2
- Frankrike, centrala
- Europa, västra
- Europa, norra
- Brasilien, södra
- Storbritannien, västra
- Storbritannien, södra
- Australien, östra
- Australien, sydöstra 
- Asien, östra
- Sydostasien
- Sydkorea, centrala
- Indien, centrala
- Indien, södra
- Japan, östra
- Japan, västra
- US Gov, Virginia
- Kina Öst 2

Arbetsytan Log Analytics måste finnas i följande regioner:
- Kanada, centrala
- USA, västra centrala
- USA, östra
- USA, östra 2
- USA, norra centrala
- USA, södra centrala
- USA, centrala
- USA, västra
- USA, västra 2
- Frankrike, centrala
- Europa, västra
- Europa, norra
- Storbritannien, västra
- Storbritannien, södra
- Australien, östra
- Australien, sydöstra
- Asien, östra
- Sydostasien 
- Sydkorea, centrala
- Indien, centrala
- Japan, östra
- US Gov, Virginia
- Kina Öst 2

## <a name="can-the-nsgs-i-enable-flow-logs-for-be-in-different-regions-than-my-workspace"></a>Kan NSGs I aktivera flödesloggar för vara i olika regioner än min arbetsyta?

Ja, dessa NSG:er kan finnas i andra regioner än din Log Analytics-arbetsyta.

## <a name="can-multiple-nsgs-be-configured-within-a-single-workspace"></a>Kan flera NSGs konfigureras inom en enda arbetsyta?

Ja.

## <a name="can-i-use-an-existing-workspace"></a>Kan jag använda en befintlig arbetsyta?

Ja. Om du väljer en befintlig arbetsyta kontrollerar du att den har migrerats till det nya frågespråket. Om du inte vill uppgradera arbetsytan måste du skapa en ny. Mer information om det nya frågespråket finns i [Uppgradering av Azure Monitor-loggar till ny loggsökning](../log-analytics/log-analytics-log-search-upgrade.md).

## <a name="can-my-azure-storage-account-be-in-one-subscription-and-my-log-analytics-workspace-be-in-a-different-subscription"></a>Kan mitt Azure Storage-konto finnas i en prenumeration och min Log Analytics-arbetsyta finns i en annan prenumeration?

Ja, ditt Azure Storage-konto kan finnas i en prenumeration och din Log Analytics-arbetsyta kan finnas i en annan prenumeration.

## <a name="can-i-store-raw-logs-in-a-different-subscription"></a>Kan jag lagra råloggar i en annan prenumeration?

Nej. Du kan lagra råloggar i alla lagringskonto där en NSG är aktiverad för flödesloggar. Både lagringskontot och råloggarna måste dock finnas i samma prenumeration och region.

## <a name="what-if-i-cant-configure-an-nsg-for-traffic-analytics-due-to-a-not-found-error"></a>Vad händer om jag inte kan konfigurera en NSG för trafikanalys på grund av ett "Hittades" fel?

Välj en region som stöds. Om du väljer en region som inte stöds visas felet "Hittades inte". De regioner som stöds visas tidigare i den här artikeln.

## <a name="what-if-i-am-getting-the-status-failed-to-load-under-the-nsg-flow-logs-page"></a>Vad händer om jag får status, "Det gick inte att läsa in", under NSG-flödesloggar sidan?

Microsoft.Insights-providern måste vara registrerad för att flödesloggningen ska fungera korrekt. Om du är osäker på om Microsoft.Insights-leverantören är registrerad för din prenumeration ersätter du *xxxxx-xxxxx-xxxxxx-xxxx* i följande kommando och kör följande kommandon från PowerShell:

```powershell-interactive
**Select-AzSubscription** -SubscriptionId xxxxx-xxxxx-xxxxxx-xxxx
**Register-AzResourceProvider** -ProviderNamespace Microsoft.Insights
```

## <a name="i-have-configured-the-solution-why-am-i-not-seeing-anything-on-the-dashboard"></a>Jag har konfigurerat lösningen. Varför ser jag ingenting på instrumentpanelen?

Instrumentpanelen kan ta upp till 30 minuter att visas första gången. Lösningen måste först aggregera tillräckligt med data för att den ska kunna härleda meningsfulla insikter. Då genererar rapporter. 

## <a name="what-if-i-get-this-message-we-could-not-find-any-data-in-this-workspace-for-selected-time-interval-try-changing-the-time-interval-or-select-a-different-workspace"></a>Vad händer om jag får det här meddelandet: "Vi kunde inte hitta några data i den här arbetsytan för valt tidsintervall. Prova att ändra tidsintervallet eller välja en annan arbetsyta."?

Prova följande alternativ:
- Ändra tidsintervallet i det övre fältet.
- Välj en annan Log Analytics-arbetsyta i det övre fältet.
- Prova att komma åt trafikanalys efter 30 minuter, om den nyligen har aktiverats.
    
Om problemen kvarstår, ta upp frågor i [användaren röst forum](https://feedback.azure.com/forums/217313-networking?category_id=195844).

## <a name="what-if-i-get-this-message-analyzing-your-nsg-flow-logs-for-the-first-time-this-process-may-take-20-30-minutes-to-complete-check-back-after-some-time-2-if-the-above-step-doesnt-work-and-your-workspace-is-under-the-free-sku-then-check-your-workspace-usage-here-to-validate-over-quota-else-refer-to-faqs-for-further-information"></a>Tänk om jag får detta meddelande: "Analysera din NSG flödesloggar för första gången. Denna process kan ta 20-30 minuter att slutföra. Kom tillbaka efter en tid. 2) Om ovanstående steg inte fungerar och din arbetsyta är under den fria SKU, kontrollera sedan din arbetsyta användning här för att validera över kvot, annars hänvisa till vanliga frågor för ytterligare information."?

Det här meddelandet kan visas eftersom:
- Traffic Analytics har nyligen aktiverats och kanske ännu inte har aggregerat tillräckligt med data för att den ska kunna härleda meningsfulla insikter.
- Du använder den kostnadsfria versionen av Log Analytics-arbetsytan och den överskred kvotgränserna. Du kan behöva använda en arbetsyta med större kapacitet.
    
Om problemen kvarstår, ta upp frågor i [användaren röst forum](https://feedback.azure.com/forums/217313-networking?category_id=195844).
    
## <a name="what-if-i-get-this-message-looks-like-we-have-resources-data-topology-and-no-flows-information-meanwhile-click-here-to-see-resources-data-and-refer-to-faqs-for-further-information"></a>Vad händer om jag får detta meddelande: "Ser ut som vi har resurser data (Topologi) och inga flöden information. Under tiden, klicka här för att se resursdata och hänvisa till vanliga frågor för ytterligare information."?

Du ser resursinformationen på instrumentpanelen. Det finns dock ingen flödesrelaterad statistik. Data kanske inte finns på grund av inga kommunikationsflöden mellan resurserna. Vänta i 60 minuter och kontrollera statusen igen. Om problemet kvarstår, och du är säker på att kommunikationsflöden mellan resurser finns, ta upp frågor i [user voice forum](https://feedback.azure.com/forums/217313-networking?category_id=195844).

## <a name="can-i-configure-traffic-analytics-using-powershell-or-an-azure-resource-manager-template-or-client"></a>Kan jag konfigurera trafikanalyser med PowerShell eller en Azure Resource Manager-mall eller klient?

Du kan konfigurera trafikanalys med hjälp av Windows PowerShell från version 6.2.1 och framåt. Information om hur du konfigurerar flödesloggning och trafikanalys för en viss NSG med cmdlet set finns i [Set-AzNetworkWatcherConfigFlowLog](https://docs.microsoft.com/powershell/module/az.network/set-aznetworkwatcherconfigflowlog). Information om hur du hämtar statusen för flödesloggning och trafikanalys för en viss NSG finns i [Get-AzNetworkWatcherFlowLogStatus](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkwatcherflowlogstatus).

För närvarande kan du inte använda en Azure Resource Manager-mall för att konfigurera trafikanalys.

Information om hur du konfigurerar trafikanalys med hjälp av en Azure Resource Manager-klient finns i följande exempel.

**Ange cmdlet-exempel:**
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
**Få cmdlet exempel:**
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


## <a name="how-is-traffic-analytics-priced"></a>Hur är Traffic Analytics prissatt?

Traffic Analytics mäts. Mätningen baseras på bearbetning av flödesloggdata av tjänsten och lagring av de förbättrade loggarna som blir resultatet i en Log Analytics-arbetsyta. 

Till exempel, enligt [prisplanen](https://azure.microsoft.com/pricing/details/network-watcher/), med tanke på Västra centrala USA region, om flödesloggar data som lagras i ett lagringskonto som bearbetas av Traffic Analytics är 10 GB och förbättrade loggar intas i Log Analytics arbetsyta är 1 GB då tillämpliga avgifter är: 10 x 2,3 $ + 1 x 2,76 $ = 25,76 $

## <a name="how-frequently-does-traffic-analytics-process-data"></a>Hur ofta bearbetar Traffic Analytics data?

Se [avsnittet dataaggregering](https://docs.microsoft.com/azure/network-watcher/traffic-analytics-schema#data-aggregation) i Traffic Analytics schema- och dataaggregeringsdokument

## <a name="how-does-traffic-analytics-decide-that-an-ip-is-malicious"></a>Hur bestämmer Traffic Analytics att en IP-adress är skadlig? 

Traffic Analytics förlitar sig på Microsofts interna system för hotinformation för att anse att en IP-adress är skadlig. Dessa system utnyttjar olika telemetrikällor som Microsofts produkter och tjänster, Microsoft Digital Crimes Unit (DCU), Microsoft Security Response Center (MSRC) och externa flöden och bygger mycket intelligens ovanpå den. En del av dessa data är Microsoft Internal. Om en känd IP flaggas som skadlig, vänligen höja en supportbiljett för att veta detaljerna.

## <a name="how-can-i-set-alerts-on-traffic-analytics-data"></a>Hur ställer jag in aviseringar på Traffic Analytics-data?

Traffic Analytics har inte inbyggt stöd för aviseringar. Men eftersom Traffic Analytics-data lagras i Log Analytics kan du skriva anpassade frågor och ställa in aviseringar på dem. Steg:
- Du kan använda kortlänken för Logganalys i Traffic Analytics. 
- Använd [schemat som dokumenteras här](traffic-analytics-schema.md) för att skriva dina frågor 
- Klicka på "Ny varningsregel" för att skapa aviseringen
- Se [dokumentation för loggaviseringar](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) för att skapa aviseringen

## <a name="how-do-i-check-which-vms-are-receiving-most-on-premise-traffic"></a>Hur kontrollerar jag vilka virtuella datorer som får mest lokal trafik

            AzureNetworkAnalytics_CL
            | where SubType_s == "FlowLog" and FlowType_s == "S2S" 
            | where <Scoping condition>
            | mvexpand vm = pack_array(VM1_s, VM2_s) to typeof(string)
            | where isnotempty(vm) 
             | extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + OutboundBytes_d 
            | make-series TotalTraffic = sum(traffic) default = 0 on FlowStartTime_t from datetime(<time>) to datetime(<time>) step 1m by vm
            | render timechart

  För IPs:

            AzureNetworkAnalytics_CL
            | where SubType_s == "FlowLog" and FlowType_s == "S2S" 
            //| where <Scoping condition>
            | mvexpand IP = pack_array(SrcIP_s, DestIP_s) to typeof(string)
            | where isnotempty(IP) 
            | extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + OutboundBytes_d 
            | make-series TotalTraffic = sum(traffic) default = 0 on FlowStartTime_t from datetime(<time>) to datetime(<time>) step 1m by IP
            | render timechart

För tid, använd format: yyyy-mm-dd 00:00:00

## <a name="how-do-i-check-standard-deviation-in-traffic-recieved-by-my-vms-from-on-premise-machines"></a>Hur kontrollerar jag standardavvikelse i trafik som tas emot av mina virtuella datorer från lokala maskiner

            AzureNetworkAnalytics_CL
            | where SubType_s == "FlowLog" and FlowType_s == "S2S" 
            //| where <Scoping condition>
            | mvexpand vm = pack_array(VM1_s, VM2_s) to typeof(string)
            | where isnotempty(vm) 
            | extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + OutboundBytes_d
            | summarize deviation = stdev(traffic)  by vm


För IPs:

            AzureNetworkAnalytics_CL
            | where SubType_s == "FlowLog" and FlowType_s == "S2S" 
            //| where <Scoping condition>
            | mvexpand IP = pack_array(SrcIP_s, DestIP_s) to typeof(string)
            | where isnotempty(IP) 
            | extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + OutboundBytes_d
            | summarize deviation = stdev(traffic)  by IP
            
## <a name="how-do-i-check-which-ports-are-reachable-or-bocked-between-ip-pairs-with-nsg-rules"></a>Hur kontrollerar jag vilka portar som kan nås (eller bockas) mellan IP-par med NSG-regler

            AzureNetworkAnalytics_CL
            | where SubType_s == "FlowLog" and TimeGenerated between (startTime .. endTime)
            | extend sourceIPs = iif(isempty(SrcIP_s), split(SrcPublicIPs_s, " ") , pack_array(SrcIP_s)),
            destIPs = iif(isempty(DestIP_s), split(DestPublicIPs_s," ") , pack_array(DestIP_s))
            | mvexpand SourceIp = sourceIPs to typeof(string)
            | mvexpand DestIp = destIPs to typeof(string)
            | project SourceIp = tostring(split(SourceIp, "|")[0]), DestIp = tostring(split(DestIp, "|")[0]), NSGList_s, NSGRule_s, DestPort_d, L4Protocol_s, FlowStatus_s 
            | summarize DestPorts= makeset(DestPort_d) by SourceIp, DestIp, NSGList_s, NSGRule_s, L4Protocol_s, FlowStatus_s

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-geo-map-view"></a>Hur navigerar jag med hjälp av tangentbordet i geokartvyn?

Geo kartsidan innehåller två huvudavsnitt:
    
- **Banner:** Banderollen högst upp på geokartan innehåller knappar för att välja trafikdistributionsfilter (till exempel Distribution, Trafik från länder/regioner och Skadlig). När du väljer en knapp används respektive filter på kartan. Om du till exempel väljer knappen Aktiv markerar kartan de aktiva datacenter i distributionen.
- **Karta**: Under banderollen visar kartavsnittet trafikdistribution mellan Azure-datacenter och länder/regioner.
    
### <a name="keyboard-navigation-on-the-banner"></a>Tangentbordsnavigering på banderollen
    
- Som standard är valet på geokartsidan för bannern filtret "Azure DCs".
- Om du vill flytta till `Tab` ett `Right arrow` annat filter använder du antingen eller nyckeln. Om du vill flytta `Shift+Tab` bakåt `Left arrow` använder du antingen eller nyckeln. Navigering framåt är från vänster till höger, följt av uppifrån och ned.
- Tryck `Enter` på `Down` eller piltangenten för att använda det markerade filtret. Baserat på filterval och distribution markeras en eller flera noder under kartavsnittet.
- Om du vill växla `Ctrl+F6`mellan banderoll och karta trycker du på .
        
### <a name="keyboard-navigation-on-the-map"></a>Tangentbordsnavigering på kartan
    
- När du har valt ett filter `Ctrl+F6`på banderollen och tryckt på flyttas fokus till en av de markerade noderna **(Azure-datacenter** eller **Land/Region)** i kartvyn.
- Om du vill gå till andra markerade `Tab` noder `Right arrow` på kartan använder du antingen eller nyckeln för framåtriktad rörelse. Använd `Shift+Tab` eller `Left arrow` nyckeln för bakåtrörelser.
- Om du vill markera en markerad `Enter` nod i kartan använder du tangenten eller. `Down arrow`
- Vid val av sådana noder flyttas fokus till **informationsverktygsrutan** för noden. Som standard flyttas fokus till den stängda knappen i **informationsverktygsrutan**. Om du vill flytta längre `Right arrow` `Left arrow` in i **vyn Box** använder du och tangenter för att gå framåt respektive bakåt. Att `Enter` trycka har samma effekt som att välja den fokuserade knappen i **informationsverktygslådan**.
- När du `Tab` trycker medan fokus ligger på **informationsverktygsrutan**flyttas fokus till slutpunkterna på samma kontinent som den markerade noden. Använd `Right arrow` tangenterna och `Left arrow` för att gå igenom dessa slutpunkter.
- Om du vill flytta till andra flödesslutpunkter eller kontinentkluster använder du `Tab` för framåtrörelse och `Shift+Tab` för bakåtrörelser.
- När fokus ligger på **kontinentkluster**använder du piltangenterna `Enter` eller `Down` för att markera slutpunkterna i kontinentklustret. Om du vill gå igenom slutpunkter och stängningsknappen i informationsrutan i kontinentklustret använder du antingen `Right arrow` `Left arrow` eller nyckeln för framåt- respektive bakåtrörelser. På valfri slutpunkt kan `Shift+L` du använda för att växla till anslutningsraden från den valda noden till slutpunkten. Du kan `Shift+L` trycka på igen för att gå till den markerade slutpunkten.
        
### <a name="keyboard-navigation-at-any-stage"></a>Tangentbordsnavigering när som helst
    
- `Esc`komprimerar den utökade markeringen.
- Nyckeln `Up arrow` utför samma åtgärd `Esc`som . Nyckeln `Down arrow` utför samma åtgärd `Enter`som .
- Används `Shift+Plus` för att `Shift+Minus` zooma in och för att zooma ut.

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-virtual-network-topology-view"></a>Hur navigerar jag med hjälp av tangentbordet i vyn för virtuellt nätverk?

Den virtuella nätverkstopologisidan innehåller två huvudavsnitt:
    
- **Banner:** Banderollen högst upp i den virtuella nätverk topologin innehåller knappar för att välja trafikdistributionsfilter (till exempel anslutna virtuella nätverk, frånkopplade virtuella nätverk och offentliga IP-adresser). När du väljer en knapp används respektive filter på topologin. Om du till exempel väljer knappen Aktiv framhäver topologin de aktiva virtuella nätverken i distributionen.
- **Topologi**: Under bannern visar topologin avsnittet trafikdistribution mellan virtuella nätverk.
    
### <a name="keyboard-navigation-on-the-banner"></a>Tangentbordsnavigering på banderollen
    
- Som standard är valet på topologisidan för virtuella nätverk för banderollen filtret "Anslutna virtuella nätverk".
- Om du vill gå `Tab` till ett annat filter använder du nyckeln för att gå framåt. Om du vill `Shift+Tab` flytta bakåt använder du nyckeln. Navigering framåt är från vänster till höger, följt av uppifrån och ned.
- Tryck `Enter` på för att använda det markerade filtret. Baserat på filterval och distribution markeras en eller flera noder (virtuellt nätverk) under topologiavsnittet.
- Om du vill växla mellan banderollen och topologin trycker du på `Ctrl+F6`.
        
### <a name="keyboard-navigation-on-the-topology"></a>Tangentbordsnavigering på topologin
    
- När du har markerat ett filter `Ctrl+F6`på banderollen och tryckt på flyttas fokus till en av de markerade noderna **(VNet)** i topologivyn.
- Om du vill gå till andra markerade noder `Shift+Right arrow` i topologivyn använder du nyckeln för vidare förflyttning. 
- På markerade noder flyttas fokus till **informationsverktygsrutan** för noden. Som standard flyttas fokus till knappen **Mer information** i **informationsverktygsrutan**. Om du vill flytta längre `Right arrow` in `Left arrow` i **vyn Box** använder du tangenterna och för att gå framåt respektive bakåt. Att `Enter` trycka har samma effekt som att välja den fokuserade knappen i **informationsverktygslådan**.
- Vid val av sådana noder kan du besöka alla dess anslutningar, `Shift+Left arrow` en efter en, genom att trycka på knappen. Fokus flyttas till **informationsverktygslådan** för den anslutningen. När som helst kan fokus flyttas tillbaka till noden genom att trycka igen. `Shift+Right arrow`
    

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-subnet-topology-view"></a>Hur navigerar jag med hjälp av tangentbordet i topologivyn för undernät?

Den virtuella topologisidan för undernätverk innehåller två huvudavsnitt:
    
- **Banderoll:** Banderollen högst upp i den virtuella undernätverkstopologin innehåller knappar för att välja trafikdistributionsfilter (till exempel undernäten Aktiv, Medel och Gateway). När du väljer en knapp används respektive filter på topologin. Om du till exempel väljer knappen Aktiv markeras det aktiva virtuella undernätet i distributionen.
- **Topologi**: Under bannern visar topologin avsnittet trafikdistribution mellan virtuella undernätverk.
    
### <a name="keyboard-navigation-on-the-banner"></a>Tangentbordsnavigering på banderollen
    
- Som standard är valet på den virtuella topologisidan för undernät för banderollen filtret "Undernät".
- Om du vill gå `Tab` till ett annat filter använder du nyckeln för att gå framåt. Om du vill `Shift+Tab` flytta bakåt använder du nyckeln. Navigering framåt är från vänster till höger, följt av uppifrån och ned.
- Tryck `Enter` på för att använda det markerade filtret. Baserat på filterval och distribution markeras en eller flera noder (undernät) under topologiavsnittet.
- Om du vill växla mellan banderollen och topologin trycker du på `Ctrl+F6`.
        
### <a name="keyboard-navigation-on-the-topology"></a>Tangentbordsnavigering på topologin
    
- När du har markerat ett filter `Ctrl+F6`på banderollen och tryckt på flyttas fokus till en av de markerade noderna (**Undernät**) i topologivyn.
- Om du vill gå till andra markerade noder `Shift+Right arrow` i topologivyn använder du nyckeln för vidare förflyttning. 
- På markerade noder flyttas fokus till **informationsverktygsrutan** för noden. Som standard flyttas fokus till knappen **Mer information** i **informationsverktygsrutan**. Om du vill flytta längre `Right arrow` `Left arrow` in i **vyn Box** använder du och tangenter för att gå framåt respektive bakåt. Att `Enter` trycka har samma effekt som att välja den fokuserade knappen i **informationsverktygslådan**.
- Vid val av sådana noder kan du besöka alla dess anslutningar, en efter en, genom att trycka på `Shift+Left arrow` knappen. Fokus flyttas till **informationsverktygslådan** för den anslutningen. När som helst kan fokus flyttas tillbaka till noden genom att trycka igen. `Shift+Right arrow`    

