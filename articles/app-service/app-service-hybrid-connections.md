---
title: Hybridanslutningar – Azure App Service | Microsoft Docs
description: Hur du skapar och använda Hybridanslutningar för att komma åt resurser i olika nätverk
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
editor: ''
ms.assetid: 66774bde-13f5-45d0-9a70-4e9536a4f619
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 40ff05e9fbc00747145c653878010ad9da0c37ec
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/20/2018
ms.locfileid: "53653398"
---
# <a name="azure-app-service-hybrid-connections"></a>Azure App Service-Hybridanslutningar #

Hybrid Connections är en tjänst i Azure såväl som en funktion i Azure App Service. Som en tjänst har användning och funktioner utöver de som används i App Service. Läs mer om Hybridanslutningar och deras användning utanför App Service i [Azure Relay-Hybridanslutningar][HCService].

I App Service kan Hybrid Connections användas för att få åtkomst till resurser i andra nätverk. Det ger åtkomst från din app till en programslutpunkt. En annan möjlighet att komma åt ditt program aktiveras inte. Som används i App Service, motsvarar varje Hybridanslutning en enskild kombination av TCP-värd och port. Det innebär att Hybrid Anslutningens slutpunkt kan vara på alla operativsystem och alla program som du ansluter till en TCP-lyssningsporten. Funktionen Hybridanslutningar vet inte eller hand programprotokoll är eller vilka du använder. Det är bara att tillhandahålla åtkomst till nätverket.  


## <a name="how-it-works"></a>Hur det fungerar ##
Funktionen Hybridanslutningar består av två utgående samtal till Azure Service Bus Relay. Det finns en anslutning från ett bibliotek på värden där din app körs i App Service. Det finns också en anslutning från Hybrid Connection Manager (HCM) till Service Bus Relay. Den HCM-system är en relay-tjänsten som du distribuerar i nätverket som är värd för den resurs som du försöker komma åt. 

Via två kopplade anslutningar har din app en TCP-tunnel till en fast värd: port-kombination på andra sidan av den HCM-system. Anslutningen använder TLS 1.2 för säkerhet och nycklar för delad åtkomst (signatur) för autentisering och auktorisering.    

![Diagram över Hybridanslutning övergripande flöde][1]

När din app skickar en DNS-begäran som matchar en konfigurerad Hybridanslutning slutpunkt, dirigeras utgående TCP-trafik via Hybridanslutningen.  

> [!NOTE]
> Det innebär att du ska försöker alltid använda ett DNS-namn för din Hybridanslutning. Vissa klientprogrammet gör inte en DNS-sökning om slutpunkten använder en IP-adress i stället.
>


### <a name="app-service-hybrid-connection-benefits"></a>App Service-Hybridanslutning fördelar ###

Det finns ett antal fördelar med att funktionen för Hybridanslutningar, inklusive:

- Appar kan komma åt lokala system och tjänster på ett säkert sätt.
- Funktionen kräver inte en internet-tillgänglig slutpunkt.
- Det går snabbt och enkelt att konfigurera. 
- Varje Hybridanslutning matchar till en enda värd: port-kombination, användbara för säkerhet.
- Normalt kräver inte brandväggen hål. Anslutningarna är all utgående över standard webbportar.
- Eftersom funktionen är nätverksnivån, är det oberoende av det språk som används av din app och den teknik som används av slutpunkten.
- Det kan användas för att ge åtkomst i flera nätverk från en enda app. 

### <a name="things-you-cannot-do-with-hybrid-connections"></a>Saker du kan göra med Hybrid Connections ###

Saker du kan göra med Hybrid Connections är:

- Montera en enhet.
- Använda UDP.
- Åtkomst till TCP-baserade tjänster som använder dynamiska portar, till exempel inaktivt läge för FTP eller utökat inaktivt läge.
- Stöd för LDAP, eftersom det kan kräva UDP.
- Stöd för Active Directory, eftersom det går inte att ansluta en App Service-arbetare.

## <a name="add-and-create-hybrid-connections-in-your-app"></a>Lägg till och skapa Hybridanslutningar i din app ##

Om du vill skapa en Hybridanslutning går du till den [Azure-portalen] [ portal] och välj din app. Välj **nätverk** > **konfigurera slutpunkterna för din Hybridanslutning**. Här kan du se Hybridanslutningar som är konfigurerade för din app.  

![Skärmbild av Hybridanslutningen lista][2]

Om du vill lägga till en ny Hybridanslutning, Välj **[+] Lägg till hybridanslutning**.  En lista över Hybridanslutningar som du redan har skapat visas. Om du vill lägga till en eller flera av dem till din app, väljer du de som du vill och välj sedan **Lägg till markerade Hybridanslutning**.  

![Skärmbild av Hybridanslutningen portal][3]

Om du vill skapa en ny Hybridanslutning väljer **Skapa ny hybridanslutning**. Ange den: 

- Namn på hybridanslutning.
- Slutpunktens värdnamn.
- Slutpunktsport.
- Service Bus-namnområde som du vill använda.

![Skärmbild av skapa nya hybrid dialogrutan anslutning][4]

Varje Hybridanslutning är knuten till en Service Bus-namnrymd, och varje Service Bus-namnområdet är i en Azure-region. Det är viktigt att försöka använda en Service Bus-namnområde i samma region som din app för att undvika nätverksfördröjning initierats.

Om du vill ta bort din Hybrid-anslutning från din app, högerklicka och välj **Disconnect**.  

När en Hybridanslutning har lagts till din app kan se du information på den genom att välja den. 

![Skärmbild av Hybrid anslutningar information][5]

### <a name="create-a-hybrid-connection-in-the-azure-relay-portal"></a>Skapa en Hybridanslutning i Azure Relay-portalen ###

Förutom en Portal från din app, kan du skapa Hybridanslutningar från i Azure Relay-portalen. För en Hybridanslutning som ska användas av App Service, måste den:

* Kräv klientautentisering.
* Ha ett metadata-objekt med namnet slutpunkt, som består av en värd: port-kombination som värde.

## <a name="hybrid-connections-and-app-service-plans"></a>Hybridanslutningar och App Service-planer ##

App Service-Hybridanslutningar är endast tillgängliga i Basic, Standard, Premium och isolerad prissättning SKU: er. Det finns begränsningar som är kopplad till prisplanen.  

| Prisavtal | Antalet Hybridanslutningar som kan användas i planen |
|----|----|
| Basic | 5 |
| Standard | 25 |
| Premium | 200 |
| Isolerad | 200 |

App Service-planen Användargränssnittet visar hur många Hybridanslutningar används och vilka appar.  

![Skärmbild för App Service-plan egenskaper][6]

Välj Hybridanslutningen att se information. Du kan se all information som du såg i vyn app. Du kan också se hur många andra appar i samma plan som använder den Hybridanslutning.

Det finns en gräns för antalet Hybridanslutning slutpunkter som kan användas i en App Service-plan. Varje Hybridanslutning används, men kan användas i alla appar i den här planen. Exempelvis kan räknas en enda Hybridanslutning som används i fem separata appar i en App Service-plan som en Hybridanslutning.

### <a name="pricing"></a>Prissättning ###

Förutom att det finns ett App Service-plan SKU-krav, finns det en ytterligare kostnad till med hjälp av Hybridanslutningar. Det finns en avgift för varje lyssnare som används av en Hybridanslutning. Lyssnaren är Hybridanslutningshanteraren. Om du har fem Hybridanslutningar som stöds av två Hybridanslutningshanterare, som är 10 lyssnare. Mer information finns i [priser för Service Bus][sbpricing].

## <a name="hybrid-connection-manager"></a>Hybridanslutningshanterare ##

Funktionen Hybridanslutningar kräver en relay agent i nätverket som är värd för din Hybridanslutning slutpunkt. Den reläagent kallas Hybrid Connection Manager (HCM). Ladda ned HCM-system, från din app i den [Azure-portalen][portal]väljer **nätverk** > **konfigurera dina slutpunkterförHybridanslutning**.  

Det här verktyget körs på Windows Server 2012 och senare. HCM körs som en tjänst och ansluter utgående trafik till Azure Relay på port 443.  

Du kan köra HybridConnectionManagerUi.exe för att använda Användargränssnittet för verktyget när du har installerat HCM-system. Den här filen finns i installationskatalogen för Hybridanslutningshanteraren. I Windows 10, kan du också söka efter *Hybridanslutningshanteraren UI* i Sök-rutan.  

![Skärmbild av Hybridanslutningshanteraren][7]

När du startar HCM UI, är det första som du ser en tabell med alla Hybridanslutningar som är konfigurerade med den här instansen av den HCM-system. Om du vill göra några ändringar först autentisera med Azure. 

Att lägga till en eller flera Hybridanslutningar i din HCM:

1. Starta HCM-Användargränssnittet.
2. Välj **konfigurera en annan Hybridanslutning**.
![Skärmbild av konfigurera nya Hybridanslutningar][8]

1. Logga in med ditt Azure-konto.
1. Välj en prenumeration.
1. Välj Hybridanslutningar som du vill HCM vidarebefordra.
![Skärmbild av Hybridanslutningar][9]

1. Välj **Spara**.

Du kan nu se Hybridanslutningar som du har lagt till. Du kan också välja konfigurerade Hybridanslutningen att se information.

![Skärmbild av Hybridanslutningsinformationen][10]

För att stödja Hybridanslutningar som den är konfigurerad med kräver HCM-system:

- TCP-åtkomst till Azure via port 443.
- TCP-åtkomst till Hybrid Anslutningens slutpunkt.
- Möjlighet att göra DNS look-ups på slutpunktsvärd och Service Bus-namnområdet.

> [!NOTE]
> Azure Relay förlitar sig på Web Sockets för anslutning. Den här funktionen är endast tillgänglig på Windows Server 2012 eller senare. På grund av att stöds HCM inte på något tidigare än Windows Server 2012.
>

### <a name="redundancy"></a>Redundans ###

Varje HCM-system har stöd för flera Hybridanslutningar. Alla angivna Hybridanslutning stöds också av flera HCMs. Standardinställningen är att dirigera trafik över de konfigurerade HCMs för alla angivna slutpunkten. Om du vill ha hög tillgänglighet på Hybrid Connections från ditt nätverk, kan du köra flera HCMs på separata datorer. Den distribution algoritmen används av den vidarebefordrande tjänsten för att distribuera trafik till HCMs är slumpmässig tilldelning. 

### <a name="manually-add-a-hybrid-connection"></a>Lägg manuellt till en Hybridanslutning ###

Dela anslutningssträng för gateway för Hybridanslutning med dem om du vill aktivera någon utanför din prenumeration för att köra en HCM-instans för en viss Hybridanslutningen. Du kan se anslutningssträng för gateway i egenskaperna för Hybridanslutning i det [Azure-portalen][portal]. Om du vill använda strängen, Välj **ange manuellt** i HCM-system och klistra in anslutningssträngen för gatewayen.

![Lägg manuellt till en Hybridanslutning][11]

### <a name="upgrade"></a>Uppgradera ###

Det är periodiska uppdateringar till Hybridanslutningshanteraren åtgärda problem eller tillhandahålla förbättringar. När uppgraderingar släpps, visas ett popup-fönster i HCM UI. Tillämpa uppgraderingen tillämpa ändringarna och starta om den HCM-system. 

## <a name="adding-a-hybrid-connection-to-your-app-programmatically"></a>Att lägga till en Hybridanslutning i din app via programmering ##

API: er som anges nedan kan användas direkt för att hantera Hybridanslutningar som är ansluten till dina appar. 

    /subscriptions/[subscription name]/resourceGroups/[resource group name]/providers/Microsoft.Web/sites/[app name]/hybridConnectionNamespaces/[relay namespace name]/relays/[hybrid connection name]?api-version=2016-08-01

JSON-objekt som är associerade med en Hybridanslutning ser ut som:

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

Ett sätt att använda den här informationen är med armclient som du kan hämta från den [ARMClient] [ armclient] GitHub-projektet. Här är ett exempel på bifoga en befintlig Hybrid-anslutning till din app. Skapa en JSON-fil per ovan schemat som:

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

Om du vill använda detta API måste skicka nyckel och relay resurs-ID. Om du har sparat din information med filename hctest.json kör detta kommando för att koppla din Hybrid-anslutning till din app: 

    armclient login
    armclient put /subscriptions/ebcidic-asci-anna-nath-rak1111111/resourceGroups/myapp-rg/providers/Microsoft.Web/sites/myhcdemoapp/hybridConnectionNamespaces/demo-relay/relays/relay-demo-hc?api-version=2016-08-01 @hctest.json

## <a name="troubleshooting"></a>Felsökning ##

Status för ”ansluten” innebär att minst ett HCM-system har konfigurerats med den Hybridanslutning och kan nå Azure. Om statusen för din Hybridanslutning inte anger **ansluten**, din Hybridanslutning har inte konfigurerats på alla HCM-system som har åtkomst till Azure.

Den främsta orsaken som klienter inte kan ansluta till sina slutpunkt beror på slutpunkten som angavs med en IP-adress i stället för ett DNS-namn. Om din app inte går att nå den önskade slutpunkten och du använde en IP-adress, växla till ett DNS-namn som är giltig på värden där HCM körs. Kontrollera också att DNS-namnet matchas korrekt på den värd där HCM körs. Bekräfta att det finns en anslutning från den värd där HCM körs till Hybrid Anslutningens slutpunkt.  

I App Service, kan verktyget tcpping anropas från konsolen avancerade verktyg (Kudu). Det här verktyget kan berätta om du har åtkomst till en TCP-slutpunkt, men den inte den information om du har åtkomst till en Hybridanslutning slutpunkt. När du använder verktyget i konsolen mot en Hybridanslutning slutpunkt, bekräftar du endast som används för en värd: port-kombination.  

## <a name="biztalk-hybrid-connections"></a>BizTalks hybridanslutningar ##

Tidig form av den här funktionen anropades BizTalk-Hybridanslutningar. Den här funktionen slutet av livslängd har gått den 31 maj 2018 och upphört med sin verksamhet. BizTalk-hybridanslutningar har tagits bort från alla appar och kan inte nås via portalen eller API: et. Om du fortfarande har dessa äldre anslutningar som konfigurerats i Hybridanslutningshanteraren kommer du se statusen utgått och visar en instruktion i slutet av liv längst ned på sidan.

![BizTalk-Hybridanslutningar i den HCM-system][12]


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
