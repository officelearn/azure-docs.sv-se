---
title: Felsök BizTalk Services med driftsloggar | Microsoft Docs
description: Felsök BizTalk Services med driftsloggar. MABS, WABS
services: biztalk-services
documentationcenter: ''
author: MandiOhlinger
manager: anneta
editor: ''
ms.assetid: 1081a9c6-58cc-4a76-8ac8-11e5e7a6ea27
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: e58a62761284e0671c0083d41f5dde4c13b32fe2
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58108264"
---
# <a name="biztalk-services-troubleshoot-using-operation-logs"></a>BizTalk Services: Felsök med hjälp av åtgärdsloggar

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]
> 
> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

## <a name="what-are-the-operation-logs"></a>Vilka är de Åtgärdsloggar
Åtgärdsloggar är en Management Services-funktion som gör det möjligt att visa historiska loggarna för åtgärder som utförs på dina Azure-tjänster, inklusive BizTalk Services. På så sätt kan du visa historiska data som rör hanteringsåtgärder på din BizTalk Service-prenumeration så långt tillbaka som 180 dagar.

> [!NOTE]
> Den här funktionen endast samlar in loggar för hanteringsåtgärder på BizTalk Services, till exempel när tjänsten har startats säkerhetskopieras upp, och så vidare. Sådana åtgärder spåras med hjälp av den [BizTalk Service REST API: er](https://msdn.microsoft.com/library/azure/dn232347.aspx). En fullständig lista över åtgärder som spåras med hjälp av tjänster finns i [Operations spårade med hjälp av Azure Management Services](#bizops).<br/><br/>
> Detta in inte loggar för aktiviteter som rör BizTalk Service-körning (t.ex (meddelande bearbetas av bryggor, osv.). Använd vyn spårning från BizTalk Services-portalen om du vill visa dessa loggar. Mer information finns i [spårning av meddelanden](https://msdn.microsoft.com/library/azure/hh949805.aspx).
> 
> 

## <a name="view-biztalk-services-operation-logs"></a>Visa loggar för BizTalk Services
1. I portalen, väljer **hanteringstjänster**, och välj sedan den **Åtgärdsloggar** fliken.
2. Du kan filtrera loggarna baserat på olika parametrar som prenumerationen, datumintervall, typ av tjänst (t.ex. BizTalk Services), namn eller status för åtgärden (lyckades, misslyckades).
3. Välj bockmarkeringen för att visa den filtrerade listan. Följande bild visar aktiviteter relaterade till testbiztalkservice: ![Visa loggar][ViewLogs] 
4. Om du vill visa mer om en viss åtgärd, Välj raden och klicka på **information** i Aktivitetsfältet längst ned på sidan.

## <a name="bizops"></a>Åtgärder som spåras med hjälp av Azure-hanteringstjänster
I följande tabell visas vilka åtgärder som spåras med hjälp av Azure-hanteringstjänster:

| Åtgärdsnamn | Aktivitet |
| --- | --- |
| CreateBizTalkService |Att skapa en ny BizTalk Service |
| DeleteBizTalkService |Åtgärden att ta bort en BizTalk Service |
| RestartBizTalkService |Åtgärd för att starta om en BizTalk Service |
| StartBizTalkService |Att starta en BizTalk Service |
| StopBizTalkService |Åtgärd för att stoppa en BizTalk Service |
| DisableBizTalkService |Åtgärd för att inaktivera en BizTalk Service |
| EnableBizTalkService |Åtgärd för att aktivera en BizTalk Service |
| BackupBizTalkService |Åtgärd för att säkerhetskopiera en BizTalk Service |
| RestoreBizTalkService |Åtgärden att återställa en BizTalk Service från angivna säkerhetskopia |
| SuspendBizTalkService |Åtgärd för att pausa en BizTalk Service |
| ResumeBizTalkService |Åtgärd för att återuppta en BizTalk Service |
| ScaleBizTalkService |Åtgärd för att skala en BizTalk Service upp eller ned |
| ConfigUpdateBizTalkService |Åtgärd för att uppdatera konfigurationen av en BizTalk Service |
| ServiceUpdateBizTalkService |Åtgärd för att uppgradera eller nedgradera en BizTalk Service till en annan version |
| PurgeBackupBizTalkService |Åtgärd för att rensa säkerhetskopior av BizTalk Service utanför kvarhållningsperioden |

## <a name="see-also"></a>Se även
* [Säkerhetskopiera BizTalk-tjänst](https://go.microsoft.com/fwlink/p/?LinkID=325584)
* [Återställa BizTalk-tjänst från en säkerhetskopia](https://go.microsoft.com/fwlink/p/?LinkID=325582)
* [BizTalk Services: Developer, Basic, Standard och Premium diagram över utgåvor](https://go.microsoft.com/fwlink/p/?LinkID=302279)
* [BizTalk Services: Etablering](https://go.microsoft.com/fwlink/p/?LinkID=302280)
* [BizTalk Services: Etablering av statusdiagram](https://go.microsoft.com/fwlink/p/?LinkID=329870)
* [BizTalk Services: Flikarna instrumentpanel, Övervakare och skalning](https://go.microsoft.com/fwlink/p/?LinkID=302281)
* [BizTalk Services: Begränsning](https://go.microsoft.com/fwlink/p/?LinkID=302282)
* [BizTalk Services: Utfärdarens namn och nyckel](https://go.microsoft.com/fwlink/p/?LinkID=303941)
* [Hur gör jag för att börja använda Azure BizTalk Services SDK?](https://go.microsoft.com/fwlink/p/?LinkID=302335)

[ViewLogs]: ./media/biztalk-troubleshoot-using-ops-logs/Operation-Logs.png

