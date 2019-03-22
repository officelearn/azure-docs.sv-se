---
title: Felsökningsguide för liveuppspelning | Microsoft Docs
description: Det här avsnittet innehåller förslag om hur du felsöker problem med direktsänd strömning.
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
ms.openlocfilehash: f502e3228274840d23b9f52512280fc0d9f0553b
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/21/2019
ms.locfileid: "58309902"
---
# <a name="troubleshooting-guide-for-live-streaming"></a>Felsökningsguide för liveuppspelning  

Den här artikeln innehåller förslag om hur du felsöker vissa live direktuppspelning problem.

## <a name="issues-related-to-on-premises-encoders"></a>Problem relaterade till lokala kodare
Det här avsnittet innehåller förslag om hur du felsöker problem med lokala kodare som är konfigurerade för att skicka en enda bithastighet till AMS-kanaler som är aktiverade för live encoding.

### <a name="problem-would-like-to-see-logs"></a>Problem: Skulle vilja se loggar
* **Potentiella problem**: Det går inte att hitta encoder-loggar som kan hjälpa dig att vid felsökning av problem.
  
  * **Telestream Wirecast**: Vanligtvis finns det loggar under C:\Users\{användarnamn} \AppData\Roaming\Wirecast\ 
  * **Elemental Live**: Du kan hitta finns länkar till loggar på hanteringsportalen. Klicka på **Stats**, sedan **loggar**. På den **loggfiler** sidan visas en lista över loggar för alla LiveEvent objekt, väljer du det alternativ som matchar den aktuella sessionen. 
  * **Flash Media Live Encoder**: Du hittar den **loggkatalogen...**  genom att navigera till den **kodning Log** fliken.

### <a name="problem-there-is-no-option-for-outputting-a-progressive-stream"></a>Problem: Det finns inget alternativ för att mata ut en progressiv dataström
* **Potentiella problem**: Kodaren används inte automatiskt ändra Ej sammanflätning. 
  
    **Felsökningssteg**: Leta efter ett avlägsnande av sammanflätning alternativ inom encoder-gränssnittet. När ta bort sammanflätning har aktiverats, Kontrollera igen för progressiv utdatainställningar. 

### <a name="problem-tried-several-encoder-output-settings-and-still-unable-to-connect"></a>Problem: Försökte flera kodare utdatainställningar och fortfarande inte kan ansluta.
* **Potentiella problem**: Azure kodning kanal har inte återställts korrekt. 
  
    **Felsökningssteg**: Kontrollera att kodaren inte längre push-överföring till AMS, stoppa och återställa kanalen. När du kör igen kan du försöka ansluta kodaren med de nya inställningarna. Om du fortfarande inte kan lösa problemet, försök att skapa en ny kanal helt och hållet, ibland kanaler kan bli skadad efter flera misslyckade försök.  
* **Potentiella problem**: GOP storlek eller viktiga ramens inställningar är inte optimala. 
  
    **Felsökningssteg**: Rekommenderade GOP-storlek eller bildrutan intervallet är två sekunder. Vissa kodare beräkna den här inställningen i antal bildrutor, medan andra använder sekunder. Exempel: När du genererar 30 bilder per sekund, är GOP-storlek 60 ramar, vilket motsvarar 2 sekunder.  
* **Potentiella problem**: Stängda portar blockerar dataströmmen. 
  
    **Felsökningssteg**: När strömmande via RTMP, kontrollerar du inställningarna för brandväggen och/eller proxyn för att bekräfta att utgående portar 1935 och 1936 är öppna. 

> [!NOTE]
> Om när du har följt anvisningarna du fortfarande inte kan har strömma, skicka ett supportärende i Azure Portal.
> 
> 

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

