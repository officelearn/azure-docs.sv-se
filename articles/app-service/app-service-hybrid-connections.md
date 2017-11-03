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
ms.date: 04/22/2017
ms.author: ccompy
ms.openlocfilehash: fef9e7b280387934cb093f51b4c8aa134a3b87e7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-app-service-hybrid-connections"></a>Azure Apptjänst Hybridanslutningar #

## <a name="overview"></a>Översikt ##

Hybridanslutningar är både en tjänst i Azure som en funktion i Azure App Service.  Som en tjänst har användning och funktioner utöver de som utnyttjas i Azure App Service.  Mer information om Hybridanslutningar och deras användning utanför Azure App Service som du kan börja här [Azure Relay Hybridanslutningar][HCService]

I Azure App Service kan hybridanslutningar användas för att komma åt resurser i andra nätverk. Det ger åtkomst från din app till en slutpunkt för programmet.  Det kan inte ett alternativt möjlighet att komma åt programmet.  Som används i App Service motsvarar varje hybridanslutning en enstaka kombination av TCP-värd och port.  Detta innebär att hybrid Anslutningens slutpunkt kan finnas på alla operativsystem och alla program som du träffa en lyssnande TCP-port. Hybridanslutningar vet inte eller är protokollet är eller vad du ansluter till.  Den bara ger åtkomst till nätverket.  


## <a name="how-it-works"></a>Hur det fungerar ##
Funktionen hybrid anslutningar består av två utgående samtal till Service Bus Relay.  Det finns en anslutning från ett bibliotek på värden där appen körs i app service och det finns en anslutning från Hybrid anslutning Manager(HCM) till Service Bus relay.  HCM är en relay-tjänst som du distribuerar i nätverket värd 

Via två kopplade anslutningar har din app en TCP-tunnel till en fast värdnamn: port-kombination på andra sidan av HCM.  Anslutningen använder TLS 1.2 för säkerhets- och SAS-nycklar för autentisering/auktorisering.    

![][1]

När din app gör en DNS-begäran som matchar en konfigurera Hybridanslutning slutpunkt, dirigeras utgående TCP-trafik av hybridanslutningen.  

> [!NOTE]
> Det innebär att försök att alltid använda ett DNS-namn för din Hybridanslutning.  Vissa klientprogrammet utför inte i en DNS-sökning om slutpunkten används en IP-adress i stället.
>
>

Det finns två typer av hybridanslutningar nya hybridanslutningar som erbjuds som en tjänst under Azure Relay och äldre BizTalk-Hybridanslutningar.  Äldre BizTalk-Hybridanslutningar kallas klassiska Hybridanslutningar i portalen.  Det finns mer information i det här dokumentet senare om dem.

### <a name="app-service-hybrid-connection-benefits"></a>Fördelar med Apptjänst hybrid-anslutning ###

Det finns ett antal fördelar för hybrid anslutningar kapaciteten inklusive

- Appar kan på ett säkert sätt komma åt på lokala datorer och tjänster på ett säkert sätt
- funktionen kräver inte en tillgänglig internet-slutpunkt
- Det går snabbt och enkelt att konfigurera  
- varje hybridanslutning matchas med en enda värdnamn: port-kombination som är en del av utmärkt säkerhet
- Normalt behöver inte brandvägg hål som anslutningarna är alla utgående över standard webbportar
- eftersom funktionen är nätverksnivån som betyder också att som är oberoende språket som används av din app och den teknik som används av slutpunkten
- den kan användas för att ge åtkomst i flera nätverk från en enda app 

### <a name="things-you-cannot-do-with-hybrid-connections"></a>Saker du kan göra med Hybridanslutningar ###

Det finns några saker som du inte kan göra med hybridanslutningar och de inkluderar:

- montera en enhet
- med hjälp av UDP
- åtkomst till TCP baserade tjänster som använder dynamiska portar, till exempel FTP passivt läge eller utökad passivt läge
- LDAP-stöd, som ibland kräver UDP
- Active Directory-support

## <a name="adding-and-creating-a-hybrid-connection-in-your-app"></a>Lägga till och skapa en Hybridanslutning i din App ##

Hybridanslutningar kan skapas via portalen app eller från tjänstportalen Hybridanslutning.  Vi rekommenderar starkt att du använder app-portalen för att skapa Hybridanslutningar som du vill använda med din app.  Om du vill skapa en Hybridanslutning går du till den [Azure-portalen] [ portal] och gå till Gränssnittet för din app.  Välj **nätverk > Konfigurera slutpunkter för din hybridanslutning**.  Här hittar du Hybridanslutningar som är konfigurerade med din app  

![][2]

Om du vill lägga till en ny Hybridanslutning, klickar du på Lägg till Hybrid-anslutning.  Användargränssnittet som öppnas visar Hybrid-anslutningar som du redan har skapat.  Om du vill lägga till en eller flera av dem i appen, klicka på de som du vill använda och tryck **Lägg till valda hybridanslutning**.  

![][3]

Om du vill skapa en ny Hybridanslutning klickar du på **Skapa ny hybridanslutning**.  Här anger du det: 

- namnet på slutpunkten
- slutpunktens värdnamn
- port för slutpunkt
- servicebus-namnområde som du vill använda

![][4]

Varje hybridanslutning är knuten till en service bus-namnrymd och varje service bus-namnrymd är i en Azure-region.  Det är viktigt att försöka använda en service bus-namnrymd i samma region som din app för att undvika framkallas Nätverksfördröjningen.

Om du vill ta bort din hybridanslutning från din app, högerklicka på den och väljer **frånkoppling**.  

När en hybridanslutning läggs till ditt webbprogram, kan du se information på den genom att klicka på den.  

![][5]

## <a name="hybrid-connections-and-app-service-plans"></a>Hybridanslutningar och Apptjänstplaner ##

Endast hybridanslutningar som du nu är de nya hybridanslutningar.  De är bara tillgängliga i Basic, Standard, Premium och isolerad priser SKU: er.  Det finns begränsningar som är knutna till prisplanen.  

| Priser för planen | Antal hybridanslutningar som kan användas i planen |
|----|----|
| Basic | 5 |
| Standard | 25 |
| Premium | 200 |
| Isolerad | 200 |

Eftersom det finns begränsningar för App Service-Plan finns även Användargränssnittet i i App Service-Plan som visar hur många hybridanslutningar används och vilka appar.  

![][6]

Precis som med app-vyn, kan du se information på din hybridanslutning genom att klicka på den.  I egenskaperna som visas här kan se all information som du hade vid vyn appen men kan också se hur många andra appar i den samma App Service-Plan använder hybridanslutningen.

![][7]

När det finns en gräns för antalet slutpunkter för hybridanslutning som kan användas i en Apptjänstplan, kan varje hybridanslutning används användas till alla appar i den App Service-Plan.  Dvs om jag hade 1 hybridanslutning som jag använde i 5 separata appar i min App Service-Plan som inte är fortfarande bara 1 hybridanslutning.

Det finns en extra kostnad till hybridanslutningar utöver som endast kan användas i en Basic, Standard, Premium eller isolerade SKU.  För information om priser för hybrid anslutning finns här: [priser för Service Bus][sbpricing].

## <a name="hybrid-connection-manager"></a>Hybridanslutningshanterare ##

Hybridanslutningar ska fungera måste för en relay agent i nätverket som är värd för din hybrid Anslutningens slutpunkt.  Den relay agenten kallas Hybrid anslutning Manager (HCM).  Det här verktyget kan hämtas från den **nätverk > Konfigurera slutpunkter för din hybridanslutning** användargränssnitt som är tillgängliga från din app i den [Azure-portalen][portal].  

Det här verktyget körs på Windows server 2008 R2 och senare versioner av Windows.  När den har installerats HCM körs som en tjänst.  Den här tjänsten ansluter till Azure servicebus-relä baserat på de konfigurerade slutpunkterna.  Anslutningar från HCM är utgående portarna 80 och 443.    

HCM har ett gränssnitt för att konfigurera den.  När du har installerat HCM kan du ta fram Användargränssnittet genom att köra HybridConnectionManagerUi.exe som placeras i installationskatalogen för Hybridanslutningshanteraren.  Uppnås också enkelt på Windows 10 genom att skriva *Hybridanslutningshanteraren UI* i sökrutan.  

När HCM UI startas, är det första du ser en tabell som listar alla hybridanslutningar som är konfigurerade med den här instansen av HCM.  Om du vill göra några ändringar som du behöver för att autentisera med Azure. 

Lägga till en eller flera Hybridanslutningar i din HCM:

1. Starta Användargränssnittet för HCM
1. Klicka på Konfigurera en annan hybridanslutning![][8]

1. Logga in med ditt Azure-konto
1. Välj en prenumeration
1. Klicka på hybridanslutningar som du vill använda den här HCM vidarebefordra![][9]

1. Klicka på Spara

Nu visas hybridanslutningar som du har lagt till.  Du kan också klicka på den konfigurerade hybridanslutningen och se information om anslutningen.

![][10]

För din HCM för att kunna stödja hybridanslutningar konfigureras den med måste den:

- TCP-åtkomst till Azure via portarna 80 och 443
- TCP-åtkomst till hybrid Anslutningens slutpunkt
- möjligheten att göra DNS sökningar på endpoint-värd och azure servicebus-namnområde

HCM stöder både nya hybridanslutningar samt äldre BizTalk-hybridanslutningar.

### <a name="redundancy"></a>Redundans ###

Varje HCM kan stödja flera hybridanslutningar.  Alla angivna hybridanslutning stöds också av flera HCMs.  Standardinställningen är att resursallokering trafik över de konfigurerade HCMs för alla angivna slutpunkten.  Om du vill hög tillgänglighet på hybridanslutningar från ditt nätverk kan bara skapa en instans av flera HCMs på separata datorer. 

### <a name="manually-adding-a-hybrid-connection"></a>Att lägga till en hybridanslutning manuellt ###

Om du vill att någon utanför din prenumeration som värd för en HCM-instans för en given hybridanslutning, kan du dela med dem anslutningssträng för gateway för hybridanslutningen.  Du kan se detta i egenskaperna för en hybridanslutning i den [Azure-portalen][portal]. Om du vill använda denna sträng, klickar du på den **konfigurera manuellt** i HCM och klistra in i anslutningssträngen för gatewayen.


## <a name="troubleshooting"></a>Felsökning ##

När din hybridanslutning ställs in med ett program som körs och det finns minst en HCM som har konfigurerats hybridanslutningen och sedan status står **ansluten** i portalen.  Om meddelandet inte anger **ansluten** innebär att din app är nere eller din HCM kan inte ansluta ut till Azure på port 80 eller 443.  

Det främsta skälet som klienter inte kan ansluta till sina slutpunkten är eftersom slutpunkten angavs med en IP-adress i stället för ett DNS-namn.  Om din app går inte att nå slutpunkten som önskad och används av en IP-adress, växla till med DNS-namnet som är giltig på värden där HCM körs.  Annat att kontrollera är att DNS-namn matchas korrekt på värden där HCM körs och att det finns en anslutning från värden där HCM körs till hybrid Anslutningens slutpunkt.  

Det är ett verktyg i App Service som kan anropas från konsolen kallas tcpping.  Det här verktyget kan berätta om du har åtkomst till en TCP-slutpunkt men inte berättar om du har åtkomst till en hybrid Anslutningens slutpunkt.  När den används i konsolen mot en hybrid Anslutningens slutpunkt, talar en lyckad ping endast om att du har en hybridanslutning som konfigurerats för din app som använder som värdnamn: port-kombination.  

## <a name="biztalk-hybrid-connections"></a>BizTalks hybridanslutningar ##

Den äldre BizTalk Hybridanslutningar kapaciteten har stängts av att ytterligare BizTalk Hybridanslutning skapande.  Du kan fortsätta använda din befintliga BizTalk-Hybridanslutningar med dina appar, men du bör migrera till den nya tjänsten.  Bland fördelarna i den nya tjänsten via BizTalk-versionen finns:

- Det krävs inga ytterligare BizTalk-konto
- TLS är 1.2 i stället för 1.0 som BizTalk Hybridanslutningar
- Kommunikation är över portarna 80 och 443 med DNS-namnet till andra Azure i stället för IP-adresser och ett antal ytterligare portar.  

Om du vill lägga till en BizTalk hybridanslutning i appen, gå till din app i den [Azure-portalen] [ portal] och på **nätverk > Konfigurera slutpunkter för din hybridanslutning**.  I tabellen klassisk hybrid anslutningar klickar du på **lägga till klassiska hybridanslutning**.  Här visas en lista över dina BizTalk hybrid-anslutningar.  


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

