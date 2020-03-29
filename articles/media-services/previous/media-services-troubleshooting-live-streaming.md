---
title: Felsökningsguide för livestreaming | Microsoft-dokument
description: Den här artikeln innehåller förslag på hur du felsöker problem med livestreaming av Azure Media Services.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: b00df5e8176aaad86be5cf3ae4e04c736f36cf5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74885610"
---
# <a name="troubleshooting-guide-for-live-streaming"></a>Felsökningsguide för liveuppspelning  

Den här artikeln innehåller förslag på hur du felsöker vissa problem med livestreaming.

## <a name="issues-related-to-on-premises-encoders"></a>Frågor som rör lokala kodare
I det här avsnittet visas förslag på hur du felsöker problem relaterade till lokala kodare som är konfigurerade för att skicka en enda bithastighetsström till AMS-kanaler som är aktiverade för live-kodning.

### <a name="problem-would-like-to-see-logs"></a>Problem: Vill se loggar
* **Potentiellt problem:** Det går inte att hitta kodarloggar som kan hjälpa till att felsöka problem.
  
  * **Telestream Wirecast**: Du kan vanligtvis hitta\{loggar under C:\Användare användarnamn}\AppData\Roaming\Wirecast\ 
  * **Elemental Live:** Du kan hitta har länkar till loggar på hanteringsportalen. Klicka på **Statistik**, sedan **loggar**. På sidan **Loggfiler** visas en lista med loggar för alla LiveEvent-objekt. välja den som matchar den aktuella sessionen. 
  * **Flash Media Live Encoder:** Du kan hitta **loggkatalogen ...** genom att navigera till fliken **Kodningslogg.**

### <a name="problem-there-is-no-option-for-outputting-a-progressive-stream"></a>Problem: Det finns inget alternativ för att mata ut en progressiv ström
* **Potentiellt problem**: Kodaren som används inte automatiskt deinterlace. 
  
    **Felsökningssteg:** Leta efter ett avförstöringsalternativ i kodargränssnittet. När de-interlacing är aktiverat, kontrollera igen för progressiva utdatainställningar. 

### <a name="problem-tried-several-encoder-output-settings-and-still-unable-to-connect"></a>Problem: Försökte flera inställningar kodare utdata och fortfarande inte kan ansluta.
* **Potentiellt problem:** Azure-kodningskanal återställdes inte korrekt. 
  
    **Felsökningssteg:** Kontrollera att kodaren inte längre trycker på AMS, stoppar och återställer kanalen. När du kör igen kan du försöka ansluta kodaren till de nya inställningarna. Om detta fortfarande inte löser problemet kan du prova att skapa en ny kanal helt och hållet, ibland kan kanaler skadas efter flera misslyckade försök.  
* **Potentiella problem:** GOP-storleken eller nyckelbildrutan är inte optimala. 
  
    **Felsökningssteg:** Rekommenderad GOP-storlek eller nyckelbildrutasintervall är två sekunder. Vissa kodare beräknar den här inställningen i antal ramar, medan andra använder sekunder. Till exempel: När du matar ut 30 fps, gop storlek skulle vara 60 bilder, vilket motsvarar 2 sekunder.  
* **Potentiellt problem**: Stängda portar blockerar strömmen. 
  
    **Felsökningssteg:** När du streamar via RTMP kontrollerar du brandväggs- och/eller proxyinställningarna för att bekräfta att utgående portar 1935 och 1936 är öppna. 

> [!NOTE]
> Om du efter att ha följt felsökningsstegen fortfarande inte kan strömma skickar du en supportbiljett med Azure-portalen.
> 
> 

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

