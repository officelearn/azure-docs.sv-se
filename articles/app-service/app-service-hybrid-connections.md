---
title: Azure Apptjänst Hybridanslutningar | Microsoft Docs
description: Hur du skapar och använda Hybridanslutningar och komma åt resurser i olika nätverk
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
ms.date: 10/20/2017
ms.author: ccompy
ms.openlocfilehash: 677642e4e97523ed71ff5857ae27263743dca535
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2017
ms.locfileid: "25990827"
---
# <a name="azure-app-service-hybrid-connections"></a>Azure Apptjänst Hybridanslutningar #

Hybridanslutningar är både en tjänst i Azure och en funktion i Azure App Service. Som en tjänst har den använder och funktioner utöver de som används i App Service. Mer information om Hybridanslutningar och deras användning utanför App Service finns [Azure Relay Hybridanslutningar][HCService].

I App Service kan Hybridanslutningar användas för att komma åt resurser i andra nätverk. Det ger åtkomst från din app till en slutpunkt för programmet. En alternativ möjlighet att komma åt programmet aktiveras inte. Som används i App Service motsvarar varje Hybridanslutning en enstaka kombination av TCP-värd och port. Detta innebär att Hybrid Anslutningens slutpunkt kan finnas på alla operativsystem och alla program som du använder en lyssnande TCP-port. Funktionen Hybridanslutningar vet inte eller är protokollet är eller vad du ansluter till. Den bara ger åtkomst till nätverket.  


## <a name="how-it-works"></a>Hur det fungerar ##
Funktionen Hybridanslutningar består av två utgående anrop till Azure Service Bus Relay. Det finns en anslutning från ett bibliotek på värden där appen körs i Apptjänst. Det finns också en anslutning från Hybrid anslutning Manager (HCM) till Service Bus Relay. HCM är en relay-tjänst som du distribuerar i nätverket där du försöker komma åt resursen. 

Via två kopplade anslutningar har din app en TCP-tunnel till en fast värdnamn: port-kombination på andra sidan av HCM. Anslutningen använder TLS 1.2 för säkerhet och nycklar för delad åtkomst signatur (SAS) för autentisering och auktorisering.    

![Diagram över Hybridanslutning hög nivå flöde][1]

När din app gör en DNS-begäran som matchar en konfigurerad slutpunkt Hybridanslutning, dirigeras utgående TCP-trafik via Hybrid-anslutningen.  

> [!NOTE]
> Det innebär att försök att alltid använda ett DNS-namn för din Hybridanslutning. Vissa klientprogrammet utför inte i en DNS-sökning om slutpunkten används en IP-adress i stället.
>
>

Funktionen Hybridanslutningar har två typer: Hybridanslutningar som erbjuds som en tjänst med Service Bus Relay och Hybridanslutningar för äldre Azure BizTalk-tjänster. Dessa är kallas klassiska Hybridanslutningar i portalen. Det finns mer information om dem senare i den här artikeln.

### <a name="app-service-hybrid-connection-benefits"></a>Fördelar med App Service Hybridanslutning ###

Det finns ett antal fördelar för Hybridanslutningar funktion, inklusive:

- Appar kan komma åt lokalt system och tjänster på ett säkert sätt.
- Funktionen kräver inte en internet-tillgänglig slutpunkt.
- Det går snabbt och enkelt att konfigurera. 
- Varje Hybridanslutning matchar till en enda värd: port-kombination, användbara för säkerhet.
- Normalt kräver inte brandvägg hål. Anslutningarna är alla utgående över standard webbportar.
- Eftersom funktionen är nätverksnivån, är oberoende språket som används av din app och den teknik som används av slutpunkten.
- Det kan användas för att ge åtkomst i flera nätverk från en enda app. 

### <a name="things-you-cannot-do-with-hybrid-connections"></a>Saker du kan göra med Hybridanslutningar ###

Det finns några saker som du inte kan göra med Hybridanslutningar, inklusive:

- Montera en enhet.
- Med hjälp av UDP.
- Åtkomst till TCP-baserade tjänster som använder dynamiska portar, till exempel FTP passivt läge eller utökad passivt läge.
- Stöd för LDAP, eftersom den kräver ibland UDP.
- Stöd för Active Directory.

## <a name="add-and-create-hybrid-connections-in-your-app"></a>Lägga till och skapa Hybridanslutningar i din app ##

Du kan skapa Hybridanslutningar via din Apptjänst-app i Azure-portalen och Azure Relay i Azure-portalen. Vi rekommenderar att du skapar Hybridanslutningar via App Service-appen som du vill använda med Hybrid-anslutning. Om du vill skapa en Hybridanslutning, gå till den [Azure-portalen] [ portal] och välj din app. Välj **nätverk** > **konfigurera dina slutpunkter för din Hybridanslutning**. Här kan se du Hybridanslutningar som är konfigurerade för din app.  

![Skärmbild av Hybridanslutning lista][2]

Om du vill lägga till en ny Hybridanslutning, Välj **lägga till hybridanslutning**.  En lista över Hybridanslutningar som du redan har skapat visas. Om du vill lägga till en eller flera av dem till din app, markerar du dem och väljer sedan **Lägg till valda Hybridanslutning**.  

![Skärmbild av Hybridanslutning portal][3]

Om du vill skapa en ny Hybridanslutning väljer **Skapa ny hybridanslutning**. Ange den: 

- Namnet på slutpunkten.
- Slutpunktens värdnamn.
- Port för slutpunkt.
- Service Bus-namnområde som du vill använda.

![Skärmbild av skapar nya hybrid dialogrutan anslutning][4]

Varje Hybridanslutning är knuten till en Service Bus-namnrymd och varje Service Bus-namnrymd är i en Azure-region. Det är viktigt att försöka använda en Service Bus-namnrymd i samma region som din app för att undvika framkallas Nätverksfördröjningen.

Om du vill ta bort din Hybridanslutning från din app, högerklicka och välj **frånkoppling**.  

När en Hybridanslutning har lagts till din app kan se du information på den genom att välja den. 

![Skärmbild av Hybrid anslutningar information][5]

### <a name="create-a-hybrid-connection-in-the-azure-relay-portal"></a>Skapa en Hybrid-anslutning i Azure Relay-portal ###

Förutom portal erfarenheterna i din app, kan du skapa Hybridanslutningar från Relay i Azure-portalen. För en Hybridanslutning som ska användas av App Service, måste den:

* Klienten tillstånd krävs.
* Har ett metadata-objekt med namnet slutpunkt, som innehåller en värdnamn: port-kombination som värde.

## <a name="hybrid-connections-and-app-service-plans"></a>Hybridanslutningar och Apptjänst-planer ##

Hybridanslutningar-funktionen är endast tillgänglig i Basic, Standard, Premium och isolerad priser SKU: er. Det finns begränsningar som är knutna till prisplanen.  

> [!NOTE] 
> Du kan bara skapa nya Hybridanslutningar baserat på Azure Relay. Du kan inte skapa ny BizTalk-Hybridanslutningar.
>

| priser för planen | Antal Hybridanslutningar som kan användas i planen |
|----|----|
| Basic | 5 |
| Standard | 25 |
| Premium | 200 |
| Isolerad | 200 |

Observera att programtjänstplanen visar hur många Hybridanslutningar som används och vilka appar.  

![Skärmbild av App Service-plan egenskaper][6]

Välj Hybridanslutning att se information. Du kan se all information som du såg app-vy. Du kan också se hur många andra appar i samma plan som använder Hybridanslutningen.

Det finns en gräns för antalet Hybridanslutning slutpunkter som kan användas i en apptjänstplan. Varje Hybridanslutning används, men kan användas till alla appar i planen. Till exempel räknar en enda Hybridanslutning som används i fem olika appar i App Service-plan som en Hybrid-anslutning.

Det finns en extra kostnad för att använda Hybridanslutningar. Mer information finns i [priser för Service Bus][sbpricing].

## <a name="hybrid-connection-manager"></a>Hybridanslutningshanterare ##

Hybridanslutningar-funktionen kräver en relay agent i nätverket som är värd för din Hybridanslutning slutpunkt. Den relay agenten kallas Hybrid anslutning Manager (HCM). Hämta HCM, från din app i den [Azure-portalen][portal]väljer **nätverk** > **konfigurera dina slutpunkter för din Hybridanslutning**.  

Det här verktyget körs på Windows Server 2012 och senare. När den installeras, körs HCM som en tjänst som ansluter till Service Bus Relay, baserat på de konfigurerade slutpunkterna. Anslutningar från HCM är utgående till Azure via port 443.    

Du kan köra HybridConnectionManagerUi.exe för att använda Användargränssnittet för verktyget när du har installerat HCM. Den här filen finns i installationskatalogen Hybridanslutningshanteraren. I Windows 10 kan du också söka efter *Hybridanslutningshanteraren UI* i sökrutan.  

![Skärmbild av Hybrid Anslutningshanteraren][7]

När du startar HCM UI, är det första du ser en tabell med alla Hybridanslutningar som är konfigurerade med den här instansen av HCM. Om du vill göra ändringar först autentisera med Azure. 

Lägga till en eller flera Hybridanslutningar i din HCM:

1. Starta HCM-Användargränssnittet.
1. Välj **konfigurera en annan Hybridanslutning**.
![Skärmbild av konfigurera nya Hybridanslutningar][8]

1. Logga in med ditt Azure-konto.
1. Välj en prenumeration.
1. Välj Hybridanslutningar som du vill HCM vidarebefordra.
![Skärmbild av Hybridanslutningar][9]

1. Välj **Spara**.

Du kan nu se Hybridanslutningar som du har lagt till. Du kan också välja den konfigurerade Hybridanslutning att se information.

![Skärmbild av Hybrid anslutningsinformation][10]

För att stödja Hybridanslutningar konfigureras den med kräver HCM:

- TCP-åtkomst till Azure via portarna 80 och 443.
- TCP-åtkomst till Hybrid Anslutningens slutpunkt.
- Möjligheten att göra DNS-look-ups på endpoint-värd och Service Bus-namnrymd.

HCM stöder både nya Hybridanslutningar och BizTalk-Hybridanslutningar.

> [!NOTE]
> Azure Relay bygger på webben Sockets för anslutningen. Den här funktionen är endast tillgänglig på Windows Server 2012 eller senare. På grund av att stöds HCM inte på något tidigare än Windows Server 2012.
>

### <a name="redundancy"></a>Redundans ###

Varje HCM kan stödja flera Hybridanslutningar. Alla angivna Hybridanslutning stöds också av flera HCMs. Standardinställningen är att dirigera trafik över de konfigurerade HCMs för alla angivna slutpunkten. Om du vill hög tillgänglighet på Hybrid-anslutningar från nätverket, kan du köra flera HCMs på separata datorer. 

### <a name="manually-add-a-hybrid-connection"></a>Manuellt lägga till en Hybridanslutning ###

Dela anslutningssträng för gateway för Hybridanslutning med dem. Om du vill aktivera någon utanför din prenumeration på en HCM-instans vara värd för en given Hybrid-anslutning. Du kan se det i egenskaperna för en Hybridanslutning i den [Azure-portalen][portal]. Om du vill använda denna sträng **ange manuellt** i HCM och klistra in anslutningssträngen gateway.


## <a name="troubleshooting"></a>Felsökning ##

Status för ”ansluten” innebär att minst en HCM har konfigurerats med Hybridanslutningen och det går att nå Azure. Om statusen för din Hybridanslutning inte säger **ansluten**, Hybrid-anslutningen har inte konfigurerats på några HCM som har åtkomst till Azure.

Det främsta skälet som klienter inte kan ansluta till sina slutpunkten är eftersom slutpunkten angavs med en IP-adress i stället för ett DNS-namn. Om din app går inte att nå slutpunkten som önskad och används av en IP-adress, växla till med DNS-namnet som är giltig på värden där HCM körs. Kontrollera också att DNS-namn matchas korrekt på värden där HCM körs. Bekräfta att det finns en anslutning från värden där HCM körs till Hybrid Anslutningens slutpunkt.  

Verktyget tcpping kan anropas från konsolen avancerade verktyg (Kudu) i App Service. Det här verktyget kan berätta om du har åtkomst till en TCP-slutpunkt, men den inte berättar om du har åtkomst till en Hybridanslutning slutpunkt. När du använder verktyget i konsolen mot en Hybridanslutning slutpunkt, bekräftar du bara den använder en kombination av värdnamn: port.  

## <a name="biztalk-hybrid-connections"></a>BizTalks hybridanslutningar ##

Den äldre BizTalk Hybridanslutningar kapaciteten har stängts till ny BizTalk-Hybridanslutningar. Du kan fortsätta använda din befintliga BizTalk-Hybridanslutningar med dina appar, men du bör migrera till nya Hybrid-anslutningar som använder Azure Relay. Bland fördelarna i den nya tjänsten via BizTalk-versionen finns:

- Inga ytterligare BizTalk-konto krävs.
- TLS är version 1.2 i stället för version 1.0.
- Kommunikation över portarna 80 och 443 och använder ett DNS-namn till Azure, i stället för IP-adresser och ett antal ytterligare portar.  

Om du vill lägga till en befintlig BizTalk Hybridanslutning till din app, gå till din app i den [Azure-portalen][portal], och välj **nätverk** > **konfigurera dina slutpunkter för din Hybridanslutning**. Välj i tabellen klassiska Hybridanslutningar **lägga till klassiska Hybridanslutning**. Du kan sedan se en lista över dina BizTalk Hybrid-anslutningar.  


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
