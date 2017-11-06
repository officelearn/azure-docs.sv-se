---
title: "Azure Apptjänst Hybridanslutningar | Microsoft Docs"
description: "Hur du skapar och använda Hybridanslutningar och komma åt resurser i olika nätverk"
services: app-service
documentationcenter: 
author: ccompy
manager: stefsch
editor: 
ms.assetid: 66774bde-13f5-45d0-9a70-4e9536a4f619
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: ccompy
ms.openlocfilehash: f0b148cb9c304c54b47be9601740e7634462d59b
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2017
---
# <a name="azure-app-service-hybrid-connections"></a>Azure Apptjänst Hybridanslutningar #

## <a name="overview"></a>Översikt ##

Hybridanslutningar är både en tjänst i Azure som en funktion i Azure App Service.  Som en tjänst har den använder och funktioner utöver de som utnyttjas i Azure App Service.  Mer information om Hybridanslutningar och deras användning utanför Azure App Service som du kan börja här [Azure Relay Hybridanslutningar][HCService]

I Azure App Service kan Hybridanslutningar användas för att komma åt resurser i andra nätverk. Det ger åtkomst från din app till en slutpunkt för programmet.  En alternativ möjlighet att komma åt programmet aktiveras inte.  Som används i App Service motsvarar varje Hybridanslutning en enstaka kombination av TCP-värd och port.  Detta innebär att Hybrid Anslutningens slutpunkt kan finnas på alla operativsystem och alla program som du träffa en lyssnande TCP-port. Hybridanslutningar vet inte eller är protokollet är eller vad du ansluter till.  De bara ger åtkomst till nätverket.  


## <a name="how-it-works"></a>Hur det fungerar ##
Funktionen Hybridanslutningar består av två utgående samtal till Service Bus Relay.  Det finns en anslutning från ett bibliotek på värden när appen körs i Apptjänst och det finns en anslutning från Hybrid anslutning Manager (HCM) till Service Bus Relay.  HCM är en relay-tjänst som du distribuerar i nätverket där du försöker komma åt resursen. 

Via två kopplade anslutningar har din app en TCP-tunnel till en fast värdnamn: port-kombination på andra sidan av HCM.  Anslutningen använder TLS 1.2 för säkerhets- och SAS-nycklar för autentisering/auktorisering.    

![Hybridanslutningen hög nivå flöde][1]

När din app gör en DNS-begäran som matchar en konfigurerad slutpunkt Hybridanslutning, dirigeras utgående TCP-trafik via Hybrid-anslutningen.  

> [!NOTE]
> Det innebär att försök att alltid använda ett DNS-namn för din Hybridanslutning.  Vissa klientprogrammet utför inte i en DNS-sökning om slutpunkten används en IP-adress i stället.
>
>

Det finns två typer av Hybridanslutningar: nya Hybridanslutningar som erbjuds som en tjänst under Azure Relay och äldre BizTalk-Hybridanslutningar.  Äldre BizTalk-Hybridanslutningar kallas klassiska Hybridanslutningar i portalen.  Det finns mer information i det här dokumentet senare om dem.

### <a name="app-service-hybrid-connection-benefits"></a>Fördelar med App Service Hybridanslutning ###

Det finns ett antal fördelar för Hybridanslutningar funktion, inklusive:

- Appar kan på ett säkert sätt komma åt lokalt system och tjänster på ett säkert sätt.
- Funktionen kräver inte en Internet-tillgänglig slutpunkt.
- Det går snabbt och enkelt att konfigurera. 
- Varje Hybridanslutning matchar till en enda värd: port-kombination som är en utmärkt säkerhet aspekt.
- Normalt kräver inte att brandväggen hål som anslutningarna är alla utgående över standard webbportar.
- Eftersom funktionen är nätverksnivån, är oberoende språket som används av din app och den teknik som används av slutpunkten.
- Det kan användas för att ge åtkomst i flera nätverk från en enda app. 

### <a name="things-you-cannot-do-with-hybrid-connections"></a>Saker du kan göra med Hybridanslutningar ###

Det finns några saker som du inte kan göra med Hybridanslutningar, inklusive:

- montera en enhet
- med hjälp av UDP
- Åtkomst till TCP baserade tjänster som använder dynamiska portar, till exempel FTP passivt läge eller utökad passivt läge
- LDAP-stöd, som ibland kräver UDP
- Active Directory-support

## <a name="adding-and-creating-a-hybrid-connection-in-your-app"></a>Lägga till och skapa en Hybridanslutning i din App ##

Hybridanslutningar kan skapas via din Apptjänst-app i Azure-portalen eller från Azure Relay i Azure-portalen.  Vi rekommenderar att du skapar Hybridanslutningar via App Service-appen som du vill använda med Hybrid-anslutning.  Om du vill skapa en Hybridanslutning, gå till den [Azure-portalen] [ portal] och välj din app.  Välj **nätverk > Konfigurera dina slutpunkter för din Hybridanslutning**.  Här visas Hybridanslutningar som har konfigurerats för din app.  

![Listan med hybrid anslutningar][2]

Om du vill lägga till en ny Hybridanslutning, klickar du på Lägg till Hybrid-anslutning.  Användargränssnittet som öppnas visar Hybrid-anslutningar som du redan har skapat.  Om du vill lägga till en eller flera av dem i appen, klicka på de som du vill använda och tryck **Lägg till valda Hybridanslutning**.  

![Hybrid anslutning portal][3]

Om du vill skapa en ny Hybridanslutning klickar du på **Skapa ny Hybridanslutning**.  Här kan du ange den: 

- namnet på slutpunkten
- slutpunktens värdnamn
- port för slutpunkt
- Service Bus-namnområde som du vill använda

![Skapa en hybridanslutning][4]

Varje Hybridanslutning är knuten till en Service Bus-namnrymd och varje Service Bus-namnrymd är i en Azure-region.  Det är viktigt att försöka använda ett Service Bus-namnområde i samma region som din app för att undvika framkallas Nätverksfördröjningen.

Om du vill ta bort din Hybridanslutning från din app, högerklicka på den och väljer **frånkoppling**.  

När en Hybridanslutning har lagts till i din app, kan du se information på den genom att klicka på den. 

![Hybrid anslutningsinformation][5]

### <a name="creating-a-hybrid-connection-in-the-azure-relay-portal"></a>Skapa en Hybrid-anslutning i Azure Relay-portal ###

Förutom portal erfarenheterna i din app finns det också en möjlighet att skapa Hybridanslutningar från Relay i Azure-portalen. Det måste uppfylla två villkor för en Hybridanslutning som ska användas av Azure App Service. Det måste:

* Klienten tillstånd krävs
* Har ett metadataelement med namnet slutpunkt som innehåller en värdnamn: port-kombination som värde

## <a name="hybrid-connections-and-app-service-plans"></a>Hybridanslutningar och Apptjänstplaner ##

Hybridanslutningar är bara tillgängliga i Basic, Standard, Premium och isolerad priser SKU: er.  Det finns begränsningar som är knutna till prisplanen.  

> [!NOTE] 
> Du kan bara skapa nya Hybridanslutningar baserat på Azure Relay. Du kan inte skapa ny BizTalk-Hybridanslutningar.
>

| Priser för planen | Antal Hybridanslutningar som kan användas i planen |
|----|----|
| Basic | 5 |
| Standard | 25 |
| Premium | 200 |
| Isolerad | 200 |

Eftersom det finns begränsningar för App Service-Plan, finns det också Användargränssnittet i i App Service-Plan som visar hur många Hybridanslutningar används och vilka appar.  

![Egenskaper för appen Servie nivå][6]

Du kan se information på en Hybrid-anslutning genom att klicka på den.  Du kan se all information som du såg i vyn app i egenskaperna som visas här, och du kan också se hur många andra appar i den samma App Service-Plan använder Hybridanslutningen.

När det finns en gräns för antalet Hybridanslutning slutpunkter som kan användas i en Apptjänstplan, kan varje Hybridanslutning används användas till alla appar i den App Service-Plan.  Med andra ord räknas en enda Hybridanslutning som används i 5 separat appar i en Apptjänstplan som 1 Hybridanslutning.

Det finns en extra kostnad för att använda Hybridanslutningar.  För information om Hybridanslutning priser finns här: [priser för Service Bus][sbpricing].

## <a name="hybrid-connection-manager"></a>Hybridanslutningshanterare ##

För att Hybridanslutningar ska fungera måste en relay agent i nätverket som är värd för din Hybridanslutning slutpunkt.  Den relay agenten kallas Hybrid anslutning Manager (HCM).  Det här verktyget kan hämtas från den **nätverk > Konfigurera dina slutpunkter för din Hybridanslutning** användargränssnitt som är tillgängliga från din app i den [Azure-portalen][portal].  

Det här verktyget körs på Windows server 2012 och senare versioner av Windows.  När den har installerats HCM körs som en tjänst.  Den här tjänsten ansluter till Azure Service Bus Relay baserat på de konfigurerade slutpunkterna.  Anslutningar från HCM är utgående till Azure via port 443.    

HCM har ett gränssnitt för att konfigurera den.  När HCM har installerats kan ta du fram Användargränssnittet genom att köra HybridConnectionManagerUi.exe som placeras i installationskatalogen för Hybridanslutningshanteraren.  Uppnås också enkelt på Windows 10 genom att skriva *Hybridanslutningshanteraren UI* i sökrutan.  

![Hybrid anslutning portal][7]

När HCM UI startas, är det första du ser en tabell som listar alla Hybridanslutningar som är konfigurerade med den här instansen av HCM.  Om du vill göra ändringar måste autentisera med Azure. 

Lägga till en eller flera Hybridanslutningar i din HCM:

1. Starta Användargränssnittet för HCM
1. Klicka på Konfigurera en annan Hybridanslutning ![lägga till en HC i HCM][8]

1. Logga in med ditt Azure-konto
1. Välj en prenumeration
1. Klicka på den Hybridanslutningar du vill HCM till relä ![väljer en HC][9]

1. Klicka på Spara

Nu visas Hybridanslutningar som du har lagt till.  Du kan också klicka på den konfigurerade Hybridanslutning och se information om it.

![HC information][10]

För din HCM för att kunna stödja Hybridanslutningar konfigureras den med måste den:

- TCP-åtkomst till Azure via portarna 80 och 443
- TCP-åtkomst till slutpunkten för Hybridanslutning
- Möjligheten att göra DNS-look-ups på endpoint-värd och Azure Service Bus-namnområde

HCM stöder både nya Hybridanslutningar, samt äldre BizTalk-Hybridanslutningar.

> [!NOTE]
> Azure Relay bygger på webben Sockets för anslutningen. Den här funktionen är endast tillgänglig på Windows Server 2012 eller senare. På grund av som Hybridanslutningshanteraren stöds inte på något tidigare än Windows Server 2012.
>

### <a name="redundancy"></a>Redundans ###

Varje HCM kan stödja flera Hybridanslutningar.  Alla angivna Hybridanslutning stöds också av flera HCMs.  Standardinställningen är att resursallokering trafik över de konfigurerade HCMs för alla angivna slutpunkten.  Om du vill hög tillgänglighet på Hybrid-anslutningar från ditt nätverk kan bara skapa en instans av flera HCMs på separata datorer. 

### <a name="manually-adding-a-hybrid-connection"></a>Att lägga till en Hybridanslutning manuellt ###

Om du vill att någon utanför din prenumeration på en HCM-instans vara värd för en given Hybridanslutning, kan du dela anslutningssträng för gateway för Hybridanslutning med dem.  Du kan se det i egenskaperna för en Hybridanslutning i den [Azure-portalen][portal]. Om du vill använda denna sträng, klickar du på den **ange manuellt** i HCM och klistra in i anslutningssträngen för gatewayen.


## <a name="troubleshooting"></a>Felsökning ##

Anslutningsstatusen för en Hybridanslutning innebär att minst en HCM konfigureras med den Hybrid-anslutningen och det går att nå Azure.  Om statusen för din Hybridanslutning inte säger **ansluten**, och sedan Hybrid-anslutningen inte har konfigurerats på några HCM som har åtkomst till Azure.

Det främsta skälet som klienter inte kan ansluta till sina slutpunkten är eftersom slutpunkten angavs med en IP-adress i stället för ett DNS-namn.  Om din app går inte att nå slutpunkten som önskad och används av en IP-adress, växla till med DNS-namnet som är giltig på värden där HCM körs.  Annat att kontrollera är att DNS-namn matchas korrekt på värden där HCM körs och att det finns en anslutning från värden där HCM körs till Hybrid Anslutningens slutpunkt.  

Det är ett verktyg i App Service som kan anropas från konsolen avancerade verktyg (Kudu) kallas tcpping.  Det här verktyget kan berätta om du har åtkomst till en TCP-slutpunkt, men den inte berättar om du har åtkomst till en Hybridanslutning slutpunkt.  När den används i konsolen mot en Hybridanslutning slutpunkt, talar en lyckad ping endast om att du har en Hybridanslutning som konfigurerats för din app som använder som värdnamn: port-kombination.  

## <a name="biztalk-hybrid-connections"></a>BizTalks hybridanslutningar ##

Den äldre BizTalk Hybridanslutningar kapaciteten har stängts av till ny BizTalk-Hybridanslutningar.  Du kan fortsätta använda din befintliga BizTalk-Hybridanslutningar med dina appar, men bör migrera till nya Hybrid-anslutningar som använder Azure Relay.  Bland fördelarna i den nya tjänsten via BizTalk-versionen finns:

- Inga ytterligare BizTalk-konto krävs.
- TLS är 1.2 i stället för 1.0 som BizTalk Hybridanslutningar.
- Kommunikation är över portarna 80 och 443 med DNS-namnet till andra Azure i stället för IP-adresser och ett antal ytterligare portar.  

Om du vill lägga till en befintlig BizTalk Hybridanslutning till din app, gå till din app i den [Azure-portalen] [ portal] och på **nätverk > Konfigurera dina slutpunkter för din Hybridanslutning**.  Klicka på tabellen klassiska Hybridanslutningar **lägga till klassiska Hybridanslutning**.  Här visas en lista över dina BizTalk Hybrid-anslutningar.  


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

<!--Links-->
[HCService]: http://docs.microsoft.com/azure/service-bus-relay/relay-hybrid-connections-protocol/
[portal]: http://portal.azure.com/
[oldhc]: http://docs.microsoft.com/azure/biztalk-services/integration-hybrid-connection-overview/
[sbpricing]: http://azure.microsoft.com/pricing/details/service-bus/
