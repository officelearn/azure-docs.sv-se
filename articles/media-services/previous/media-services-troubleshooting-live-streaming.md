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
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: e6b135e14f06ecf4edfbb97913c411f55711854a
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2018
ms.locfileid: "49351460"
---
# <a name="troubleshooting-guide-for-live-streaming"></a>Felsökningsguide för liveuppspelning
Den här artikeln innehåller förslag om hur du felsöker vissa live direktuppspelning problem.

## <a name="issues-related-to-on-premises-encoders"></a>Problem relaterade till lokala kodare
Det här avsnittet innehåller förslag om hur du felsöker problem med lokala kodare som är konfigurerade för att skicka en enda bithastighet till AMS-kanaler som är aktiverade för live encoding.

### <a name="problem-would-like-to-see-logs"></a>Problem: Skulle vilja se loggar
* **Potentiella problem**: Det går inte att hitta encoder loggar som kan hjälpa dig att vid felsökning av problem.
  
  * **Telestream Wirecast**: vanligtvis finns det loggar under C:\Users\{användarnamn} \AppData\Roaming\Wirecast\ 
  * **Elemental Live**: du kan hitta finns länkar till loggar på hanteringsportalen. Klicka på **Stats**, sedan **loggar**. På den **loggfiler** sidan visas en lista över loggar för alla LiveEvent objekt, väljer du det alternativ som matchar den aktuella sessionen. 
  * **Flash Media Live Encoder**: du kan hitta den **loggkatalogen...**  genom att navigera till den **kodning Log** fliken.

### <a name="problem-there-is-no-option-for-outputting-a-progressive-stream"></a>Problem: Det finns inget alternativ för att mata ut en progressiv dataström
* **Potentiella problem**: kodaren används inte automatiskt Ej sammanflätning. 
  
    **Felsökningssteg**: Leta efter ett avlägsnande av sammanflätning alternativ inom encoder-gränssnittet. När ta bort sammanflätning har aktiverats, Kontrollera igen för progressiv utdatainställningar. 

### <a name="problem-tried-several-encoder-output-settings-and-still-unable-to-connect"></a>Problem: Försökte flera kodare utdatainställningar och fortfarande inte kan ansluta.
* **Potentiella problem**: Azure kodning kanal har inte återställts korrekt. 
  
    **Felsökningssteg**: Kontrollera att kodaren inte längre skicka till AMS, stoppa och återställa kanalen. När du kör igen kan du försöka ansluta kodaren med de nya inställningarna. Om du fortfarande inte kan lösa problemet, försök att skapa en ny kanal helt och hållet, ibland kanaler kan bli skadad efter flera misslyckade försök.  
* **Potentiella problem**: The GOP storlek eller viktiga ramens inställningarna inte är optimala. 
  
    **Felsökningssteg**: rekommenderas GOP storlek eller bildrutan intervall är två sekunder. Vissa kodare beräkna den här inställningen i antal bildrutor, medan andra använder sekunder. Till exempel: när du genererar 30 bilder per sekund, GOP-storlek är 60 ramar, vilket motsvarar 2 sekunder.  
* **Potentiella problem**: stängda portar blockerar dataströmmen. 
  
    **Felsökningssteg**: vid direktuppspelning via RTMP, kontrollera inställningarna för brandväggen och/eller proxyn för att bekräfta att utgående portar 1935 och 1936 är öppna. 

> [!NOTE]
> Om när du har följt anvisningarna du fortfarande inte kan har strömma, skicka ett supportärende i Azure Portal.
> 
> 

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

