---
title: Felsöka BizTalk-tjänster med hjälp av åtgärdsloggar | Microsoft Docs
description: Felsöka BizTalk-tjänster med hjälp av åtgärdsloggar. MABS WABS
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
ms.openlocfilehash: 7d3a357e1a3929153288a9d99e21f2379bcac891
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2017
ms.locfileid: "24102026"
---
# <a name="biztalk-services-troubleshoot-using-operation-logs"></a>BizTalk-tjänst: Felsökning med åtgärdsloggar

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

## <a name="what-are-the-operation-logs"></a>Vad är den Åtgärdsloggar
Åtgärdsloggar är en funktion i tjänster där du kan visa historiska loggar av åtgärder som utförs på Azure-tjänster, inklusive BizTalk-tjänst. På så sätt kan du visa historiska data som rör hanteringsåtgärder på prenumerationen BizTalk Service så långt tillbaka 180 dagar.

> [!NOTE]
> Den här funktionen endast samlar in loggar för hanteringsåtgärder på BizTalk-tjänster, till exempel när tjänsten startades backas upp, och så vidare. Dessa åtgärder spåras med hjälp av den [BizTalk Service REST API: er](http://msdn.microsoft.com/library/azure/dn232347.aspx). En fullständig lista över åtgärder som spåras använder tjänster finns [Operations spårade med hjälp av Azure hanteringstjänster](#bizops).<br/><br/>
> Detta in inte loggar för aktiviteter som rör BizTalk Service runtime (t.ex (meddelande bearbetas av bryggor osv.). Om du vill visa loggarna använder du vyn spårning från BizTalk-Services-portalen. Mer information finns i [spårning av meddelanden](http://msdn.microsoft.com/library/azure/hh949805.aspx).
> 
> 

## <a name="view-biztalk-services-operation-logs"></a>Visa BizTalk Services Åtgärdsloggar
1. I portalen, Välj **hanteringstjänster**, och välj sedan den **Åtgärdsloggar** fliken.
2. Du kan filtrera loggarna baserat på olika parametrar som prenumeration, datumintervall, service-typen (t.ex. BizTalk-tjänst), tjänstnamn eller status för åtgärden (lyckades, misslyckades).
3. Välj på bockmarkeringen för att visa en filtrerad lista. Följande bild visar aktiviteter relaterade till testbiztalkservice: ![visa åtgärdsloggar][ViewLogs] 
4. Om du vill visa mer om en specifik åtgärd, Välj raden och klicka på **information** i Aktivitetsfältet längst ned.

## <a name="bizops"></a>Åtgärder som spåras med hjälp av Azure hanteringstjänster
I följande tabell visas de åtgärder som spåras med Azure Management Services:

| Åtgärdsnamn | Aktivitet |
| --- | --- |
| CreateBizTalkService |Åtgärden för att skapa en ny BizTalk Service |
| DeleteBizTalkService |Åtgärden ta bort en BizTalk Service |
| RestartBizTalkService |Åtgärden för att starta om en BizTalk Service |
| StartBizTalkService |Åtgärden för att starta en BizTalk Service |
| StopBizTalkService |Åtgärden för att stoppa en BizTalk Service |
| DisableBizTalkService |Att inaktivera en BizTalk Service |
| EnableBizTalkService |Åtgärden för att aktivera en BizTalk Service |
| BackupBizTalkService |Åtgärden för att säkerhetskopiera en BizTalk Service |
| RestoreBizTalkService |Åtgärden för att återställa en BizTalk Service från angivna säkerhetskopia |
| SuspendBizTalkService |Åtgärden pausa en BizTalk Service |
| ResumeBizTalkService |Åtgärden återuppta en BizTalk Service |
| ScaleBizTalkService |Åtgärden att skala en BizTalk Service uppåt eller nedåt |
| ConfigUpdateBizTalkService |Åtgärden för att uppdatera konfigurationen av en BizTalk Service |
| ServiceUpdateBizTalkService |Åtgärd för att uppgradera eller nedgradera en BizTalk Service till en annan version |
| PurgeBackupBizTalkService |Åtgärden för att rensa säkerhetskopior av BizTalk Service gjorts utanför kvarhållningsperioden |

## <a name="see-also"></a>Se även
* [Säkerhetskopiera BizTalk-tjänst](http://go.microsoft.com/fwlink/p/?LinkID=325584)
* [Återställa BizTalk-tjänst från en säkerhetskopia](http://go.microsoft.com/fwlink/p/?LinkID=325582)
* [BizTalk-tjänst: Utvecklare, Basic, Standard och Premium-utgåvor diagram](http://go.microsoft.com/fwlink/p/?LinkID=302279)
* [BizTalk-tjänst: etablering](http://go.microsoft.com/fwlink/p/?LinkID=302280)
* [BizTalk Services: Etablering av statusdiagram](http://go.microsoft.com/fwlink/p/?LinkID=329870)
* [BizTalk Services: Flikarna Instrumentpanel, Övervakare och Skalning](http://go.microsoft.com/fwlink/p/?LinkID=302281)
* [BizTalk Services: Begränsning](http://go.microsoft.com/fwlink/p/?LinkID=302282)
* [BizTalk Services: Utfärdarens namn och nyckel](http://go.microsoft.com/fwlink/p/?LinkID=303941)
* [Hur gör jag för att börja använda Azure BizTalk Services SDK?](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[ViewLogs]: ./media/biztalk-troubleshoot-using-ops-logs/Operation-Logs.png

