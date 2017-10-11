---
title: "Azure Mobile Engagement felsökningsguide för - tjänsten"
description: "Felsökning för Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: 8b4275da-c0b4-4690-824a-48e9d7a1fc6e
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: f13fd0540b783120014b3a8d4e41f78808c7fade
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="troubleshooting-guide-for-service-issues"></a>Felsökningsguide för problem med tjänsten
Följande är möjliga problem som kan uppstå med hur Azure Mobile Engagement körs.

## <a name="service-outages"></a>Serviceavbrott
### <a name="issue"></a>Problem
* Problem som kan orsakas av driftstörningar i Azure Mobile Engagement tjänsten visas.

### <a name="causes"></a>Orsaker
* Problem som visas kan orsakas av serviceavbrott för Azure Mobile Engagement kan orsakas av flera olika problem:
  * Isolerade problem som ursprungligen visas systemfel till alla Azure Mobile Engagement
  * Kända problem som orsakas av server-avbrott (inte alltid visas i status-servern):
  * Schemaläggning av fördröjningar, målinriktning fel, Aktivitetsikon uppdateringsproblem, statistik sluta samla in Push slutar fungera kan API: er slutar att fungera, nya appar eller användare inte skapas, DNS-fel och Timeout-fel i Användargränssnittet, API eller appar på en enhet.
  * Molnet beroende avbrott [Azure Tjänststatus](http://status.azure.com/)
  * Push Notification Services (PNS) beroende avbrott
  * App Store avbrott

1) Om du vill testa om problemet är systemfel kan du testa samma funktion i en annan

* Azure Mobile Engagement integrerat program
* Testenhet
* Testa enhetens OS-version
* Kampanj
* Användarkonto
* Webbläsaren (IE, Firefox, Chrome osv.)
* Dator

2) Kontrollera att problemet påverkar endast av Användargränssnittet eller API: er:

* Testa samma funktion från Gränssnittet för Azure Mobile Engagement och i Azure Mobile Engagement API: er.

3) Kontrollera att problemet har med din mobiltelefon nätverk:

* Testa medan du är ansluten till Internet via Wi-Fi och medan du är ansluten via din mobiltelefon 3G-nätverk.
* Bekräfta att brandväggen inte blockerar alla Azure Mobile Engagement IP-adresser och portar.

4) Kontrollera att problemet har med din enhet:

* Testa om enheten är ansluta till Azure Mobile Engagement med en annan integrerad Azure Mobile Engagement-app.
* Kontrollera att du kan generera händelser, jobb och krascher från din telefon kan ses i Användargränssnittet för Azure Mobile Engagement. 
* Testa om du kan skicka push-meddelanden från Azure Mobile Engagement-Gränssnittet till enheten baserat på sin enhet-ID. 

5) Kontrollera att problemet har med din App:

* Installera och testa programmet från en emulator i stället för från en fysisk enhet:

6) Att testa om problemet med OS-uppgraderingar till slutanvändare enheter, vilket kräver uppgradering SDK för att matcha:

* Testa programmet på olika enheter med olika versioner av Operativsystemet.
* Bekräfta att du använder den senaste versionen av SDK.

## <a name="connectivity-and-incorrect-information-issues"></a>Anslutning och felaktig Information problem
### <a name="issue"></a>Problem
* Problem med att logga in på Azure Mobile Engagement-Användargränssnittet.
* Anslutningsfel med den Azure Mobile Engagement API: er.
* Problem med att överföra appen Info taggar via Device API.
* Problem vid hämtning av loggar eller exporterade data från Azure Mobile Engagement.
* Felaktig information som visas i Användargränssnittet för Azure Mobile Engagement.
* Felaktig information visas i Azure Mobile Engagement-loggar.

### <a name="causes"></a>Orsaker
* Bekräfta ditt användarkonto har behörighet att utföra åtgärden.
* Kontrollera att problemet inte är isolerad för att en dator eller det lokala nätverket.
* Bekräfta att att Azure Mobile Engagement-tjänsten inte har någon rapporterat avbrott.
* Bekräfta att din App Info märka filer följer alla de här reglerna:
  * Använd endast den UTF8 teckenuppsättning (ANSI-teckenuppsättningen inte stöds).
  * Använda ett kommatecken ””, som avgränsningstecken (du kan öppna en tjänst begäran till begäran att ändra avgränsningstecknet CSV från en kommaavgränsad ””, tecken, till exempel ett semikolon ””;).
  * Använd alla gemen för booleska värden ”true” och ”false”.
  * Använda en fil som är mindre än den maximala filstorleken 35 MB.

