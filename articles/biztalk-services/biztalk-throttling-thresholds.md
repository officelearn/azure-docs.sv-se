---
title: "Lär dig mer om hur du begränsar i BizTalk-tjänst | Microsoft Docs"
description: "Läs mer om begränsning tröskelvärden och resulterande runtime beteenden för BizTalk-tjänst. Begränsning är baserad på minnesanvändning och antalet meddelanden. MABS WABS"
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: f6663cf2-cda4-4bac-855e-27d2ad5c4fa4
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: 145e7470bbc01c676a1fb5856c0f9a8726e667fc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="biztalk-services-throttling"></a>BizTalk Services: Begränsning

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Azure BizTalk-tjänst implementerar tjänsten begränsning baserat på två villkor: minnesanvändning och antalet samtidiga meddelanden bearbetning. Det här avsnittet visar bandbreddsbegränsning tröskelvärden och beskriver beteende under körning när ett bandbreddsbegränsning villkor uppfylls.

## <a name="throttling-thresholds"></a>Begränsning av tröskelvärden
I följande tabell visas bandbreddsbegränsning käll- och tröskelvärden:

|  | Beskrivning | Lågtröskelövervakare | Tröskelvärde för hög |
| --- | --- | --- | --- |
| Minne |% av total system minne tillgängligt/PageFileBytes. <p><p>Totalt antal tillgängliga PageFileBytes är cirka 2 gånger mängden RAM-minne i systemet. |60% |70% |
| Meddelandebehandling |Antal meddelanden som bearbetar samtidigt |40 * antal kärnor |100 * antal kärnor |

När en hög tröskelvärde uppnås börjar Azure BizTalk-tjänst begränsning. Begränsning stoppas när det lägsta tröskelvärdet har uppnåtts. Till exempel använder din tjänst 65% av systemminnet. I så fall kan begränsning tjänsten inte. Tjänsten startar med 70% av systemminnet. I så fall kan tjänsten begränsar och fortsätter att begränsa förrän tjänsten använder 60% (låg tröskel) minne.

Azure BizTalk-tjänster spårar bandbreddsbegränsning status (normala tillstånd jämfört med begränsad tillstånd) och bandbreddsbegränsning varaktighet.

## <a name="runtime-behavior"></a>Runtime-beteende
När Azure BizTalk-tjänst anger tillståndet bandbreddsbegränsning, inträffar följande:

* Begränsning är per rollinstans. Exempel:<br/>
  RoleInstanceA begränsning. RoleInstanceB begränsning är inte. I den här situationen behandlas meddelanden i RoleInstanceB som förväntat. Meddelanden i RoleInstanceA ignoreras och misslyckas med följande fel:<br/><br/>
  **Servern är upptagen. Försök igen.**<br/><br/>
* Alla pull-källor inte söka eller hämta ett meddelande. Exempel:<br/>
  En pipeline tar emot meddelanden från en extern källa för FTP. Instansen gör pull hämtar i bandbreddsbegränsning läge. I den här situationen stoppar pipeline hämtar ytterligare meddelanden tills rollinstansen slutar begränsning.
* Ett svar skickas till klienten så att klienten kan skicka meddelandet.
* Du måste vänta tills den begränsning har lösts. Mer specifikt måste du vänta tills det lägsta tröskelvärdet har uppnåtts.

## <a name="important-notes"></a>Obs!
* Begränsning kan inte inaktiveras.
* Begränsning av tröskelvärden kan inte ändras.
* Begränsning har implementerats hela systemet.
* Azure SQL Database-Server har också inbyggd begränsning.

## <a name="additional-azure-biztalk-services-topics"></a>Avsnitt om ytterligare Azure BizTalk-tjänst
* [Installera Azure BizTalk Services SDK](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
* [Självstudier: Azure BizTalk-tjänst](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
* [Hur gör jag för att börja använda Azure BizTalk Services SDK?](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
* [Azure BizTalk-tjänst](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>

## <a name="see-also"></a>Se även
* [BizTalk-tjänst: Utvecklare, Basic, Standard och Premium-utgåvor diagram](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
* [BizTalk-tjänst: Etablering med hjälp av Azure klassiska portal](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
* [BizTalk Services: Etablering av statusdiagram](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
* [BizTalk Services: Flikarna Instrumentpanel, Övervakare och Skalning](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
* [BizTalk Services: Säkerhetskopiering och återställning](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
* [BizTalk Services: Utfärdarens namn och nyckel](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>

