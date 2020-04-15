---
title: Hög tillgänglighet och belastningsutjämning - Azure AD Application Proxy
description: Så här fungerar trafikdistributionen med programproxydistributionen. Innehåller tips om hur du optimerar kontaktens prestanda och använder belastningsutjämning för backend-servrar.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 992075378737552e890bd2d6fed3c519e6c62aa7
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312936"
---
# <a name="high-availability-and-load-balancing-of-your-application-proxy-connectors-and-applications"></a>Hög tillgänglighet och belastningsutjämning av programproxyanslutningar och program

I den här artikeln beskrivs hur trafikdistribution fungerar med programproxydistributionen. Vi ska diskutera:

- Hur trafiken fördelas mellan användare och kontakter, tillsammans med tips för att optimera anslutningsprestanda

- Hur trafiken flödar mellan anslutningsappar och backend-appservrar, med rekommendationer för belastningsutjämning mellan flera backend-servrar

## <a name="traffic-distribution-across-connectors"></a>Trafikdistribution mellan anslutningar

Kopplingar upprättar sina anslutningar baserat på principer för hög tillgänglighet. Det finns ingen garanti för att trafiken alltid kommer att fördelas jämnt över kopplingar och det finns ingen sessionstillhörighet. Användningen varierar dock och begäranden skickas slumpmässigt till application proxy-tjänstinstanser. Därför fördelas trafiken vanligtvis nästan jämnt över kopplingarna. Diagrammet och stegen nedan illustrerar hur anslutningar upprättas mellan användare och kopplingar.

![Diagram som visar anslutningar mellan användare och kopplingar](media/application-proxy-high-availability-load-balancing/application-proxy-connections.png)

1. En användare på en klientenhet försöker komma åt ett lokalt program som publiceras via Programproxy.
2. Begäran går via en Azure Load Balancer för att avgöra vilken application proxy-tjänstinstans som ska ta begäran. Per region finns det tiotals instanser tillgängliga för att acceptera begäran. Den här metoden hjälper till att fördela trafiken jämnt över tjänstinstanserna.
3. Begäran skickas till [Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/).
4. Service Bus signaler till en tillgänglig kontakt. Anslutningen hämtar sedan begäran från Service Bus.
   - I steg 2 går begäranden till olika application proxy-tjänstinstanser, så det är mer sannolikt att anslutningar görs med olika anslutningsappar. Som ett resultat används kopplingar nästan jämnt inom gruppen.
5. Anslutningen skickar begäran till programmets server för server för server för server för server med server för server med server för server. Sedan skickar programmet tillbaka svaret till kopplingen.
6. Kopplingen slutför svaret genom att öppna en utgående anslutning till tjänstinstansen från den plats där begäran kom. Då stängs anslutningen omedelbart. Som standard är varje koppling begränsad till 200 samtidiga utgående anslutningar.
7. Svaret skickas sedan tillbaka till klienten från tjänstinstansen.
8. Efterföljande begäranden från samma anslutning upprepar stegen ovan.

Ett program har ofta många resurser och öppnar flera anslutningar när det läses in. Varje anslutning går igenom stegen ovan för att tilldelas en tjänstinstans, välj en ny tillgänglig anslutning om anslutningen ännu inte har parats ihop med en anslutning.


## <a name="best-practices-for-high-availability-of-connectors"></a>Metodtips för hög tillgänglighet av anslutningsappar

- På grund av hur trafiken fördelas mellan kopplingar för hög tillgänglighet är det viktigt att alltid ha minst två kopplingar i en kopplingsgrupp. Tre kopplingar är att föredra för att tillhandahålla ytterligare buffert mellan kopplingar. Om du vill ta reda på hur många kopplingar du behöver följer du dokumentationen för kapacitetsplanering.

- Placera kopplingar på olika utgående anslutningar för att undvika en enda felpunkt. Om anslutningsapparna använder samma utgående anslutning kan ett nätverksproblem med anslutningen påverka alla anslutningsappar som använder den.

- Undvik att tvinga kopplingar att starta om när de är anslutna till produktionsprogram. Om du gör det kan det påverka fördelningen av trafiken negativt mellan kopplingar. Om du startar om kopplingarna kan fler kopplingar vara otillgängliga och anslutningar till den återstående tillgängliga kopplingen. Resultatet är en ojämn användning av kontakterna inledningsvis.

- Undvik alla former av infogad inspektion på utgående TLS-kommunikation mellan anslutningar och Azure. Denna typ av inline inspektion orsakar försämring av kommunikationsflödet.

- Se till att automatiska uppdateringar körs för dina kontakter. Om tjänsten Application Proxy Connector Updater körs uppdateras anslutningsapparna automatiskt och tar emot den senaste uppgraderade. Om du inte ser tjänsten Anslutningsuppdatering på servern måste du installera om kopplingen för att få uppdateringar.

## <a name="traffic-flow-between-connectors-and-back-end-application-servers"></a>Trafikflöde mellan kopplingar och backend-programservrar

Ett annat nyckelområde där hög tillgänglighet är en faktor är anslutningen mellan kopplingar och backend-servrar. När ett program publiceras via Azure AD Application Proxy flödar trafiken från användarna till programmen via tre hopp:

1. Användaren ansluter till azure AD Application Proxy-tjänstens offentliga slutpunkt på Azure. Anslutningen upprättas mellan klientens ursprungs-IP-adress (offentlig) och IP-adressen för slutpunkten Programproxy.
2. Application Proxy-anslutningen hämtar HTTP-begäran för klienten från application proxy-tjänsten.
3. Application Proxy-anslutningen ansluter till målprogrammet. Anslutningen använder sin egen IP-adress för att upprätta anslutningen.

![Diagram över användare som ansluter till ett program via Application Proxy](media/application-proxy-high-availability-load-balancing/application-proxy-three-hops.png)

### <a name="x-forwarded-for-header-field-considerations"></a>X-Vidarebefordrade-För huvudfält överväganden
I vissa situationer (som granskning, belastningsutjämning etc.), dela den ursprungliga IP-adressen för den externa klienten med den lokala miljön är ett krav. För att åtgärda kravet lägger Azure AD Application Proxy-anslutning till fältet X-Forwarded-For-huvud med den ursprungliga klient-IP-adressen (offentlig) i HTTP-begäran. Lämplig nätverksenhet (belastningsutjämnare, brandvägg) eller webbservern eller backend-programmet kan sedan läsa och använda informationen.

## <a name="best-practices-for-load-balancing-among-multiple-app-servers"></a>Metodtips för belastningsutjämning mellan flera appservrar
När anslutningsgruppen som har tilldelats programmet Application Proxy har två eller flera kopplingar och du kör backend-webbprogrammet på flera servrar (servergrupp), krävs en bra strategi för belastningsutjämning. En bra strategi säkerställer att servrar plockar upp klientbegäranden jämnt och förhindrar över- eller underutnyttjande av servrar i servergruppen.
### <a name="scenario-1-back-end-application-does-not-require-session-persistence"></a>Scenario 1: Backend-program kräver inte sessionsbeständighet
Det enklaste scenariot är där backend-webbprogrammet inte kräver sessionss klibbighet (sessionsbeständighet). Alla förfrågningar från användaren kan hanteras av alla backend-programinstanser i servergruppen. Du kan använda en belastningsutjämnare för lager 4 och konfigurera den utan tillhörighet. Några alternativ är Microsoft Network Load Balancing och Azure Load Balancer eller en belastningsutjämnare från en annan leverantör. Alternativt kan round-robin DNS konfigureras.
### <a name="scenario-2-back-end-application-requires-session-persistence"></a>Scenario 2: Backend-program kräver sessionsbeständighet
I det här fallet kräver backend-webbprogrammet sessionssenighet (sessionsbeständighet) under den autentiserade sessionen. Alla begäranden från användaren måste hanteras av backend-programinstansen som körs på samma server i servergruppen.
Det här scenariot kan vara mer komplicerat eftersom klienten vanligtvis upprättar flera anslutningar till application proxy-tjänsten. Begäranden via olika anslutningar kan komma fram till olika anslutningsappar och servrar i servergruppen. Eftersom varje anslutning använder sin egen IP-adress för den här kommunikationen kan belastningsutjämnaren inte säkerställa sessionss klibbighet baserat på kopplingarnas IP-adress. Käll-IP-tillhörighet kan inte heller användas.
Här är några alternativ för scenario 2:

- Alternativ 1: Basera sessionens persistens på en sessionscookie som ställts in av belastningsutjämnaren. Det här alternativet rekommenderas eftersom det gör att belastningen kan spridas jämnare mellan backend-servrarna. Det kräver en nivå 7 belastningsutjämnare med den här funktionen och som kan hantera HTTP-trafik och avsluta TLS-anslutningen. Du kan använda Azure Application Gateway (Sessionstillhörighet) eller en belastningsutjämnare från en annan leverantör.

- Alternativ 2: Basera sessionens persistens på fältet X-Forwarded-For-huvud. Det här alternativet kräver en belastningsutjämnare för layer 7 med den här funktionen och som kan hantera HTTP-trafiken och avsluta TLS-anslutningen.  

- Alternativ 3: Konfigurera backend-programmet så att det inte kräver sessionsbeständighet.

Se programvaruleverantörens dokumentation för att förstå belastningsutjämningskraven för backend-programmet.

## <a name="next-steps"></a>Nästa steg

- [Aktivera programproxy](application-proxy-add-on-premises-application.md)
- [Aktivera enkel inloggning](application-proxy-configure-single-sign-on-with-kcd.md)
- [Aktivera villkorlig åtkomst](application-proxy-integrate-with-sharepoint-server.md)
- [Felsöka problem med Application Proxy](application-proxy-troubleshoot.md)
- [Lär dig hur Azure AD-arkitekturen har hög tillgänglighet](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-architecture)
