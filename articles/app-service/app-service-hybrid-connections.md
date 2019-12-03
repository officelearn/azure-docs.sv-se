---
title: Hybridanslutningar
description: Lär dig hur du skapar och använder hybrid anslutningar i Azure App Service för att få åtkomst till resurser i olika nätverk.
author: ccompy
ms.assetid: 66774bde-13f5-45d0-9a70-4e9536a4f619
ms.topic: article
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: fasttrack-edit
ms.openlocfilehash: ffc5ee32541cfbbda2ae54fd229c1436f133d730
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671516"
---
# <a name="azure-app-service-hybrid-connections"></a>Azure App Service Hybridanslutningar #

Hybridanslutningar är både en tjänst i Azure och en funktion i Azure App Service. Som tjänst har den användning och funktioner utöver de som används i App Service. Mer information om Hybridanslutningar och deras användning utanför App Service finns i [Azure Relay hybridanslutningar][HCService].

I App Service kan Hybridanslutningar användas för att få åtkomst till program resurser i andra nätverk. Den ger åtkomst från din app till en program slut punkt. Den aktiverar inte en alternativ funktion för att få åtkomst till ditt program. Som används i App Service motsvarar varje hybrid anslutning en enskild TCP-värd och port-kombination. Det innebär att hybrid anslutnings slut punkten kan finnas på alla operativ system och program, förutsatt att du har åtkomst till en TCP-lyssnings port. Hybridanslutningar funktionen känner inte igen eller bryr sig om vad applikations protokollet är, eller vad du får åtkomst till. Det ger bara nätverks åtkomst.  


## <a name="how-it-works"></a>Så här fungerar det ##
Funktionen Hybridanslutningar består av två utgående anrop till Azure Service Bus relä. Det finns en anslutning från ett bibliotek på värden där appen körs i App Service. Det finns också en anslutning från Hybridanslutningshanteraren (HCM) till Service Bus Relay. HCM är en relä tjänst som du distribuerar i det nätverk som är värd för resursen som du försöker få åtkomst till. 

Via de två anslutna anslutningarna har din app en TCP-tunnel till en fast värd: port kombination på den andra sidan av HCM. Anslutningen använder TLS 1,2 för säkerhets-och SAS-nycklar (signatur för delad åtkomst) för autentisering och auktorisering.    

![Diagram över hög nivå flöde för Hybrid anslutning][1]

När din app gör en DNS-begäran som matchar en konfigurerad hybrid anslutnings slut punkt omdirigeras utgående TCP-trafik via hybrid anslutningen.  

> [!NOTE]
> Det innebär att du alltid bör försöka använda ett DNS-namn för din hybrid anslutning. Vissa klient program gör ingen DNS-sökning om slut punkten använder en IP-adress i stället. 
>

### <a name="app-service-hybrid-connection-benefits"></a>App Service hybrid anslutnings förmåner ###

Det finns ett antal fördelar med Hybridanslutningar-kapaciteten, inklusive:

- Appar kan komma åt lokala system och tjänster på ett säkert sätt.
- Funktionen kräver inte en tillgänglig slut punkt för Internet.
- Det går snabbt och enkelt att konfigurera. 
- Varje hybrid anslutning matchar en enda värd: port kombination, vilket är användbart för säkerhet.
- Det kräver normalt inte brand Väggs hål. Anslutningarna är alla utgående via standard webb portar.
- Eftersom funktionen är nätverks nivå, är den oberoende till det språk som används av din app och den teknik som används av slut punkten.
- Den kan användas för att ge åtkomst i flera nätverk från en enda app. 

### <a name="things-you-cannot-do-with-hybrid-connections"></a>Saker som du inte kan göra med Hybridanslutningar ###

Saker som du inte kan göra med Hybridanslutningar inkluderar:

- Montera en enhet.
- Använd UDP.
- Få åtkomst till TCP-baserade tjänster som använder dynamiska portar, till exempel FTP passivt läge eller utökat passivt läge.
- Stöd för LDAP, eftersom det kan kräva UDP.
- Stöd Active Directory, eftersom du inte kan ansluta till en App Service Worker.

### <a name="prerequisites"></a>Krav ###
 - Windows app service krävs. Den är endast tillgänglig i Windows.  

## <a name="add-and-create-hybrid-connections-in-your-app"></a>Lägga till och skapa Hybridanslutningar i din app ##

Om du vill skapa en hybrid anslutning går du till [Azure Portal][portal] och väljer din app. Välj **nätverks** > **Konfigurera dina hybrid anslutnings slut punkter**. Här kan du se de Hybridanslutningar som har kon figurer ATS för din app.  

![Skärm bild av hybrid anslutnings lista][2]

Om du vill lägga till en ny hybrid anslutning väljer du **[+] Lägg till hybrid anslutning**.  Du ser en lista över de Hybridanslutningar som du redan har skapat. Om du vill lägga till en eller flera av dem i din app väljer du de som du vill använda och väljer sedan **Lägg till vald hybrid anslutning**.  

![Skärm bild av hybrid anslutnings portalen][3]

Om du vill skapa en ny hybrid anslutning väljer du **Skapa ny hybrid anslutning**. Ange: 

- Namn på Hybrid anslutning.
- Slut punkts namn.
- Slut punkts port.
- Service Bus namn område som du vill använda.

![Skärm bild av dialog rutan skapa ny hybrid anslutning][4]

Varje hybrid anslutning är kopplad till ett Service Bus-namnområde och varje Service Bus-namnrymd finns i en Azure-region. Det är viktigt att du försöker använda ett Service Bus-namnområde i samma region som din app, för att undvika en fördröjning i nätverket.

Om du vill ta bort din hybrid anslutning från appen högerklickar du på den och väljer **Koppla från**.  

När du har lagt till en hybrid anslutning i din app kan du se information om den genom att välja den. 

![Skärm bild av information om hybrid anslutningar][5]

### <a name="create-a-hybrid-connection-in-the-azure-relay-portal"></a>Skapa en hybrid anslutning i Azure Relay portalen ###

Förutom Portal upplevelsen från din app, kan du skapa Hybridanslutningar inifrån Azure Relay portalen. För att en hybrid anslutning ska kunna användas av App Service måste den:

* Kräv klient auktorisering.
* Ha ett metadataobjekt med namnet Endpoint som innehåller en värd: port kombination som värde.

## <a name="hybrid-connections-and-app-service-plans"></a>Hybridanslutningar och App Services planer ##

App Service Hybridanslutningar är bara tillgängliga i SKU: er för Basic, standard, Premium och isolerade priser. Det finns gränser som är knutna till pris planen.  

| Pris plan | Antal Hybridanslutningar användbara i planen |
|----|----|
| Basic | 5 |
| Standard | 25 |
| Premium | 200 |
| Isolerad | 200 |

App Service plan användar gränssnitt visar hur många Hybridanslutningar som används och av vilka appar.  

![Skärm bild av App Service plan egenskaper][6]

Välj hybrid anslutning för att se information. Du kan se all information som du såg i vyn app. Du kan också se hur många andra appar i samma plan som använder hybrid anslutningen.

Det finns en gräns för antalet hybrid anslutnings slut punkter som kan användas i en App Service plan. Varje hybrid anslutning som används kan dock användas över valfritt antal appar i planen. Till exempel räknas en enskild hybrid anslutning som används i fem separata appar i ett App Service plan som en hybrid anslutning.

### <a name="pricing"></a>Prissättning ###

Förutom att det finns ett App Service plan SKU-krav finns det ytterligare kostnader att använda Hybridanslutningar. Det finns en avgift för varje lyssnare som används av en hybrid anslutning. Lyssnaren är Hybridanslutningshanteraren. Om du har fem Hybridanslutningar stöd för två hybrid anslutnings hanterare, är det 10 lyssnare. Mer information finns i [Service Bus prissättning][sbpricing].

## <a name="hybrid-connection-manager"></a>Hybridanslutningshanteraren ##

Den Hybridanslutningar funktionen kräver en Relay-Agent i nätverket som är värd för Hybrid anslutnings slut punkten. Den Relay-agenten kallas för Hybridanslutningshanteraren (HCM). Om du vill ladda ned HCM från din app i [Azure Portal][portal]väljer du **nätverks** > **konfigurerar dina hybrid anslutnings slut punkter**.  

Verktyget körs på Windows Server 2012 och senare. HCM körs som en tjänst och ansluter utgående till Azure Relay på port 443.  

När du har installerat HCM kan du köra HybridConnectionManagerUi. exe för att använda användar gränssnittet för verktyget. Den här filen finns i installations katalogen för Hybridanslutningshanteraren. I Windows 10 kan du också bara söka efter *Hybridanslutningshanteraren användar gränssnitt* i sökrutan.  

![Skärm bild av Hybridanslutningshanteraren][7]

När du startar HCM-ANVÄNDARGRÄNSSNITTET är det första du ser en tabell med en lista över alla Hybridanslutningar som har kon figurer ATS med den här instansen av HCM. Om du vill göra några ändringar måste du först autentisera med Azure. 

Så här lägger du till en eller flera Hybridanslutningar till din HCM:

1. Starta HCM-ANVÄNDARGRÄNSSNITTET.
2. Välj **Konfigurera en annan hybrid anslutning**.
![skärm bild av konfigurera nya Hybridanslutningar][8]

1. Logga in med ditt Azure-konto för att få din Hybridanslutningar tillgänglig med dina prenumerationer. HCM fortsätter inte att använda ditt Azure-konto utöver det. 
1. Välj en prenumeration.
1. Välj den Hybridanslutningar som du vill att HCM ska vidarebefordra.
![skärm bild av Hybridanslutningar][9]

1. Välj **Spara**.

Nu kan du se Hybridanslutningar du har lagt till. Du kan också välja den konfigurerade hybrid anslutningen för att se information.

![Skärm bild av hybrid anslutnings information][10]

HCM kräver följande för att stödja den Hybridanslutningar den är konfigurerad med:

- TCP-åtkomst till Azure via port 443.
- TCP-åtkomst till hybrid anslutnings slut punkten.
- Möjligheten att göra DNS-sökning på slut punkts värden och Service Bus namnrymd.

> [!NOTE]
> Azure Relay är beroende av Web Sockets för anslutning. Den här funktionen är endast tillgänglig på Windows Server 2012 eller senare. Därför stöds inte HCM på något som är tidigare än Windows Server 2012.
>

### <a name="redundancy"></a>Redundans ###

Varje HCM kan stödja flera Hybridanslutningar. Dessutom kan en eventuell hybrid anslutning stödjas av flera HCMs. Standard beteendet är att dirigera trafik över de konfigurerade HCMs för alla angivna slut punkter. Om du vill ha hög tillgänglighet på din Hybridanslutningar från nätverket kan du köra flera HCMs på separata datorer. Algoritmen för belastnings fördelning som används av relä tjänsten för att distribuera trafik till HCMs är slumpmässig tilldelning. 

### <a name="manually-add-a-hybrid-connection"></a>Lägg till en hybrid anslutning manuellt ###

Om du vill att någon utanför din prenumeration ska vara värd för en HCM-instans för en specifik hybrid anslutning, delar du Gateway-anslutningssträngen för Hybrid anslutningen med dem. Du kan se Gateway-anslutningssträngen i egenskaperna för Hybrid anslutningen i [Azure Portal][portal]. Om du vill använda den strängen väljer du **ange manuellt** i HCM och klistrar in anslutnings strängen för gatewayen.

![Lägg till en hybrid anslutning manuellt][11]

### <a name="upgrade"></a>Uppgradera ###

Det finns periodiska uppdateringar av Hybridanslutningshanteraren för att åtgärda problem eller tillhandahålla förbättringar. När uppgraderingar publiceras visas en popup-meny i HCM-ANVÄNDARGRÄNSSNITTET. Om du använder uppgraderingen tillämpas ändringarna och du kan starta om HCM. 

## <a name="adding-a-hybrid-connection-to-your-app-programmatically"></a>Lägga till en hybrid anslutning till appen program mässigt ##

API: erna som anges nedan kan användas direkt för att hantera de Hybridanslutningar som är anslutna till dina appar. 

    /subscriptions/[subscription name]/resourceGroups/[resource group name]/providers/Microsoft.Web/sites/[app name]/hybridConnectionNamespaces/[relay namespace name]/relays/[hybrid connection name]?api-version=2016-08-01

JSON-objektet som är associerat med en hybrid anslutning ser ut så här:

    {
      "name": "[hybrid connection name]",
      "type": "Microsoft.Relay/Namespaces/HybridConnections",
      "location": "[location]",
      "properties": {
        "serviceBusNamespace": "[namespace name]",
        "relayName": "[hybrid connection name]",
        "relayArmUri": "/subscriptions/[subscription id]/resourceGroups/[resource group name]/providers/Microsoft.Relay/namespaces/[namespace name]/hybridconnections/[hybrid connection name]",
        "hostName": "[endpoint host name]",
        "port": [port],
        "sendKeyName": "defaultSender",
        "sendKeyValue": "[send key]"
      }
    }

Ett sätt att använda den här informationen är med armclient, som du kan hämta från [armclient][armclient] GitHub-projektet. Här är ett exempel på hur du kopplar en befintlig hybrid anslutning till din app. Skapa en JSON-fil enligt schemat ovan, t. ex.:

    {
      "name": "relay-demo-hc",
      "type": "Microsoft.Relay/Namespaces/HybridConnections",
      "location": "North Central US",
      "properties": {
        "serviceBusNamespace": "demo-relay",
        "relayName": "relay-demo-hc",
        "relayArmUri": "/subscriptions/ebcidic-asci-anna-nath-rak1111111/resourceGroups/myrelay-rg/providers/Microsoft.Relay/namespaces/demo-relay/hybridconnections/relay-demo-hc",
        "hostName": "my-wkstn.home",
        "port": 1433,
        "sendKeyName": "defaultSender",
        "sendKeyValue": "Th9is3is8a82lot93of3774stu887ff122235="
      }
    }

Om du vill använda det här API: et behöver du skicka nyckel och vidarebefordra resurs-ID. Om du har sparat informationen med fil namnet hctest. JSON skickar du det här kommandot för att ansluta din hybrid anslutning till din app: 

    armclient login
    armclient put /subscriptions/ebcidic-asci-anna-nath-rak1111111/resourceGroups/myapp-rg/providers/Microsoft.Web/sites/myhcdemoapp/hybridConnectionNamespaces/demo-relay/relays/relay-demo-hc?api-version=2016-08-01 @hctest.json

## <a name="secure-your-hybrid-connections"></a>Skydda din Hybridanslutningar ##

En befintlig hybrid anslutning kan läggas till i andra App Service Web Apps av alla användare som har tillräcklig behörighet för det underliggande Azure Service Bus reläet. Det innebär att om du måste hindra andra från att återanvända samma hybrid anslutning (till exempel när mål resursen är en tjänst som inte har några ytterligare säkerhets åtgärder för att förhindra obehörig åtkomst) måste du låsa åtkomsten till Azure Service Bus Relay.

Alla som har `Reader` åtkomst till reläet kan _Se_ hybrid anslutningen vid försök att lägga till den i webbappen i Azure-portalen, men de kan inte _lägga till_ den eftersom de saknar behörighet att hämta anslutnings strängen som används för att upprätta relä anslutningen. För att kunna lägga till hybrid anslutningen måste de ha `listKeys` behörighet (`Microsoft.Relay/namespaces/hybridConnections/authorizationRules/listKeys/action`). `Contributor` rollen eller någon annan roll som innehåller den här behörigheten för reläet gör att användarna kan använda hybrid anslutningen och lägga till den i sina egna Web Apps.

## <a name="troubleshooting"></a>Felsöka ##

Statusen "ansluten" innebär att minst en HCM har kon figurer ATS med hybrid anslutningen och att den kan komma åt Azure. Om statusen för din hybrid anslutning inte är **ansluten**konfigureras inte din hybrid anslutning på någon HCM som har åtkomst till Azure.

Den primära orsaken till att klienterna inte kan ansluta till slut punkten beror på att slut punkten angavs med en IP-adress i stället för ett DNS-namn. Om din app inte kan komma åt den önskade slut punkten och du använde en IP-adress, byter du till att använda ett DNS-namn som är giltigt på den värd där HCM körs. Kontrol lera också att DNS-namnet matchas korrekt på den värd där HCM körs. Bekräfta att det finns en anslutning från värden där HCM körs till hybrid anslutningens slut punkt.  

I App Service kan kommando rads verktyget **tcpping** anropas från kudu-konsolen (Advanced tools). Det här verktyget kan meddela dig om du har åtkomst till en TCP-slutpunkt, men om du har åtkomst till en hybrid anslutnings slut punkt. När du använder verktyget i-konsolen mot en hybrid anslutnings slut punkt bekräftar du bara att den använder en värd: port kombination.  

Om du har en kommando rads klient för slut punkten kan du testa anslutningen från App-konsolen. Du kan till exempel testa åtkomst till webb server slut punkter med hjälp av sväng.

## <a name="biztalk-hybrid-connections"></a>BizTalks hybridanslutningar ##

Det tidigaste formuläret för den här funktionen heter BizTalk Hybridanslutningar. Den här funktionen slutade liv den 31 maj 2018 och upphör att användas. BizTalk Hybrid Connections har tagits bort från alla appar och är inte tillgängliga via portalen eller API: et. Om du fortfarande har de här äldre anslutningarna konfigurerade i Hybridanslutningshanteraren, ser du statusen upphör att gälla och visar ett slut på Life-uttryck längst ned.

![BizTalk-Hybridanslutningar i HCM][12]


<!--Image references-->
[1]: ./media/app-service-hybrid-connections/hybridconn-connectiondiagram.png
[2]: ./media/app-service-hybrid-connections/hybridconn-portal.png
[3]: ./media/app-service-hybrid-connections/hybridconn-addhc.png
[4]: ./media/app-service-hybrid-connections/hybridconn-createhc.png
[5]: ./media/app-service-hybrid-connections/hybridconn-properties.png
[6]: ./media/app-service-hybrid-connections/hybridconn-aspproperties.png
[7]: ./media/app-service-hybrid-connections/hybridconn-hcm.png
[8]: ./media/app-service-hybrid-connections/hybridconn-hcmadd.png
[9]: ./media/app-service-hybrid-connections/hybridconn-hcmadded.png
[10]: ./media/app-service-hybrid-connections/hybridconn-hcmdetails.png
[11]: ./media/app-service-hybrid-connections/hybridconn-manual.png
[12]: ./media/app-service-hybrid-connections/hybridconn-bt.png

<!--Links-->
[HCService]: https://docs.microsoft.com/azure/service-bus-relay/relay-hybrid-connections-protocol/
[portal]: https://portal.azure.com/
[oldhc]: https://docs.microsoft.com/azure/biztalk-services/integration-hybrid-connection-overview/
[sbpricing]: https://azure.microsoft.com/pricing/details/service-bus/
[armclient]: https://github.com/projectkudu/ARMClient/
