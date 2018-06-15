---
title: Felsökningsguide för direktsänd strömning | Microsoft Docs
description: Det här avsnittet innehåller förslag på hur du felsöker problem med direktsänd strömning.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: 3a7f6c1d-ce57-4fa4-a7a6-edb526b3ffbf
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: juliako
ms.openlocfilehash: fa91baf7c494941fccf0e6ca38b930f3c2a521ce
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23860523"
---
# <a name="troubleshooting-guide-for-live-streaming"></a>Felsökningsguide för liveuppspelning
Det här avsnittet innehåller förslag på hur du felsöker direktsänd strömning problem.

## <a name="issues-related-to-on-premises-encoders"></a>Problem med lokala kodare
Det här avsnittet innehåller förslag på hur du felsöker problem med lokala kodare som har konfigurerats för att skicka en dataström med enkel bithastighet till AMS-kanaler som är aktiverade för live encoding.

### <a name="problem-would-like-to-see-logs"></a>Problem: Skulle vilja se loggar
* **Potentiella problem**: Det går inte att hitta kodare loggar som kan bidra till felsökning av problem.
  
  * **Telestream Wirecast**: vanligtvis finns det loggar under C:\Users\{username} \AppData\Roaming\Wirecast\ 
  * **Grundämne Live**: du kan hitta har länkar till loggar i hanteringsportalen. Klicka på **Stats**, sedan **loggar**. På den **loggfiler** sidan visas en lista av loggar för alla objekt i LiveEvent; väljer du det alternativ som matchar den aktuella sessionen. 
  * **Flash Live mediekodare**: du kan hitta den **loggkatalogen...**  genom att navigera till den **kodning loggen** fliken.

### <a name="problem-there-is-no-option-for-outputting-a-progressive-stream"></a>Problem: Det finns inget alternativ för att mata ut en progressiv dataström
* **Potentiella problem**: kodaren används inte automatiskt Ej sammanflätning. 
  
    **Felsökningssteg**: Leta efter en de-interlacing alternativ i kodaren-gränssnittet. Kontrollera igen för inställningar för progressiv utdata när Frigör sammanflätning har aktiverats. 

### <a name="problem-tried-several-encoder-output-settings-and-still-unable-to-connect"></a>Problem: Försökte flera kodare utdata inställningar och fortfarande inte kan ansluta.
* **Potentiella problem**: Azure kodning kanal har inte återställts korrekt. 
  
    **Felsökningssteg**: Kontrollera kodaren inte längre push-installation till AMS, stoppa och återställa kanalen. Kör en gång igen och försök att ansluta din kodare med de nya inställningarna. Om du fortfarande inte kan lösa problemet, försök att skapa en ny kanal helt, ibland kanaler kan bli skadad efter flera misslyckade försök.  
* **Potentiella problem**: det GOP storlek eller nyckelbilden inställningar inte är optimala. 
  
    **Felsökningssteg**: rekommenderas GOP storlek eller keyframe är 2 sekunder. Vissa kodare beräkna den här inställningen i antal bildrutor, medan andra använder sekunder. Exempel: när mata ut 30fps GOP storleken är 60 ramar, vilket motsvarar 2 sekunder.  
* **Potentiella problem**: stängda portar blockerar dataströmmen. 
  
    **Felsökningssteg**: när strömning via RTMP Kontrollera inställningarna för brandväggen och/eller proxy för att bekräfta att utgående portar 1935 och 1936 är öppna. När du använder RTP strömning, bekräfta att utgående port 2010 är öppen. 

### <a name="problem-when-configuring-the-encoder-to-stream-with-the-rtp-protocol-there-is-no-place-to-enter-a-host-name"></a>Problem: När du konfigurerar kodaren till en dataström med protokollet RTP, det finns ingen plats för att ange ett värdnamn.
* **Potentiella problem**: Tillåt inte många RTP kodare för värdnamn och en IP-adress måste förvärvas.  
  
    **Felsökningssteg**: Om du vill hitta IP-adress, öppna en kommandotolk på varje dator. Öppna kör programstart (WIN + R) för att göra detta i Windows, och Skriv ”cmd” att öppna.  
  
    När Kommandotolken är öppen skriver du ”Ping [AMS värdnamn]”. 
  
    Värdnamn kan härledas genom att utelämna portnumret från Azure infognings-URL, som i följande exempel: 
  
    RTP://Test2-amstest009.RTP.Channel.mediaservices.Windows.NET:2010 / 
  
    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle10.png)

> [!NOTE]
> Skicka ett supportärende i Azure Portal om du efter följande felsökningssteg du fortfarande inte kan kunna strömma.
> 
> 

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

