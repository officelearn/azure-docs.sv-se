---
title: Lär dig mer om begränsning i BizTalk Services | Microsoft Docs
description: Läs mer om tröskelvärden för begränsningar och resulterande körningsbeteenden för BizTalk Services. Begränsning baseras på användning av minne och antalet meddelanden. MABS, WABS
services: biztalk-services
documentationcenter: ''
author: MandiOhlinger
manager: anneta
editor: ''
ms.assetid: f6663cf2-cda4-4bac-855e-27d2ad5c4fa4
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: daab61a0ea9321b0fb918c60688215c80088e0bc
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51243359"
---
# <a name="biztalk-services-throttling"></a>BizTalk Services: Begränsning

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Azure BizTalk-tjänster implementerar tjänsten begränsas baserat på två villkor: minnesanvändning och antalet samtidiga meddelanden bearbetning. Det här avsnittet listar tröskelvärdena för begränsningar och beskriver beteende under körning när ett begränsningstillstånd inträffar.

## <a name="throttling-thresholds"></a>Tröskelvärden för begränsningar
I följande tabell visas begränsningar käll- och tröskelvärden:

|  | Beskrivning | Lågtröskelövervakare | Tröskelvärde för hög |
| --- | --- | --- | --- |
| Minne |% av hela systemet minne tillgängligt/PageFileBytes. <p><p>Totalt antal tillgängliga PageFileBytes är cirka 2 gånger mängden RAM-minne i systemet. |60% |70 % |
| Meddelandebehandling |Antal meddelanden som bearbetades samtidigt |40 * antal kärnor |100 * antal kärnor |

När en hög gränsen nås startar Azure BizTalk Services att begränsa. Begränsning stoppas när det lägre tröskelvärdet har uppnåtts. Tjänsten använder till exempel 65% systemminnet. I så fall kan begränsa inte tjänsten. Tjänsten startar med 70% av systemminnet. I så fall kan tjänsten begränsar och fortsätter att begränsa förrän tjänsten använder 60% (lågtröskelövervakare) minne.

Azure BizTalk Services spårar begränsning status (normala tillstånd jämfört med begränsade tillstånd) och begränsning varaktigheten.

## <a name="runtime-behavior"></a>Runtime-beteende
När Azure BizTalk Services försätts i ett tillstånd som begränsning, inträffar följande:

* Begränsning är per rollinstansen. Exempel:<br/>
  RoleInstanceA begränsning. RoleInstanceB begränsas inte. I det här fallet bearbetas meddelanden i RoleInstanceB som förväntat. Meddelanden i RoleInstanceA ignoreras och misslyckas med följande fel:<br/><br/>
  **Servern är upptagen. Försök igen.**<br/><br/>
* Alla pull-datakällor inte söka eller hämta ett meddelande. Exempel:<br/>
  En pipeline hämtar meddelanden från en extern FTP-källa. Rollinstansen göra pull hämtar i en begränsning tillstånd. I den här situationen kan stoppas pipelinen laddas ned ytterligare meddelanden innan rollinstansen stoppas begränsning.
* Ett svar skickas till klienten så att klienten kan skicka meddelandet.
* Du måste vänta tills den begränsning har lösts. Mer specifikt måste du vänta tills det lägre tröskelvärdet har uppnåtts.

## <a name="important-notes"></a>Obs!
* Begränsning kan inte inaktiveras.
* Tröskelvärden för begränsningar kan inte ändras.
* Begränsning har implementerats hela systemet.
* Azure SQL Database-Server har också inbyggd begränsning.

## <a name="additional-azure-biztalk-services-topics"></a>Avsnitt om ytterligare Azure BizTalk Services
* [Installera SDK för Azure BizTalk Services](https://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
* [Självstudier: Azure BizTalk Services](https://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
* [Hur gör jag för att börja använda Azure BizTalk Services SDK?](https://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
* [Azure BizTalk Services](https://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>

## <a name="see-also"></a>Se även
* [BizTalk Services: Developer, Basic, Standard och Premium diagram över utgåvor](https://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
* [BizTalk Services: Etablering av statusdiagram](https://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
* [BizTalk Services: Flikarna Instrumentpanel, Övervakare och Skalning](https://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
* [BizTalk Services: Säkerhetskopiering och återställning](https://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
* [BizTalk Services: Utfärdarens namn och nyckel](https://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>

