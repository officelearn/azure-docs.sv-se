---
title: Konfigurera Haivision KB-kodaren för att skicka en enda bitrate live stream till Azure | Microsoft-dokument
description: Det här avsnittet visar hur du konfigurerar Haivision KB live-kodaren för att skicka en enda bithastighetsström till AMS-kanaler som är aktiverade för live-kodning.
services: media-services
documentationcenter: ''
author: dbgeorge
manager: vsood
editor: ''
ms.assetid: 0d2f1e81-51a6-4ca9-894a-6dfa51ce4c70
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: afc0fcb6751a08b41010fa569c67a9827e0abec0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77131932"
---
# <a name="use-the-haivision-kb-live-encoder-to-send-a-single-bitrate-live-stream"></a>Använd Haivision KB live-kodaren för att skicka en enda bitrate live stream  
> [!div class="op_single_selector"]
> * [Haivision (på)](media-services-configure-kb-live-encoder.md)
> * [Tricaster (tre)](media-services-configure-tricaster-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)

Det här avsnittet visar hur du [konfigurerar Havision KB live-kodaren](https://www.haivision.com/products/kb-series/) för att skicka en enda bithastighetsström till AMS-kanaler som är aktiverade för live-kodning. Mer information finns i [Arbeta med kanaler som är aktiverade för att utföra Live Encoding med Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

Den här självstudien visar hur du hanterar Azure Media Services (AMS) med Azure Media Services Explorer (AMSE) verktyg. Det här verktyget körs bara på Windows PC. Om du använder Mac eller Linux använder du Azure-portalen för att skapa [kanaler](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) och [program](media-services-portal-creating-live-encoder-enabled-channel.md).

## <a name="prerequisites"></a>Krav
*   Åtkomst till en Haivision KB-kodare som kör SW v5.01 eller senare.
* [Skapa ett Azure Media Services-konto](media-services-portal-create-account.md)
* Se till att en slutpunkt för direktuppspelning körs. Mer information finns [i Hantera slutpunkter för direktuppspelning i ett Media Services-konto](media-services-portal-manage-streaming-endpoints.md)
* Installera den senaste versionen av [AMSE-verktyget.](https://github.com/Azure/Azure-Media-Services-Explorer)
* Starta verktyget och anslut till ditt AMS-konto.

## <a name="tips"></a>Tips
* Använd en direktkopplad internetanslutning när det är möjligt.
* En bra tumregel för att bestämma bandbreddskrav är att dubblera sändningens bithastighet. Även om detta inte är ett obligatoriskt krav, hjälper det till att minska effekterna av överbelastning i nätverket.
* När du använder programvarubaserade kodare stänger du alla onödiga program.

## <a name="create-a-channel"></a>Skapa en kanal
1. I AMSE-verktyget navigerar du till fliken **Live** och högerklickar inom kanalområdet. Välj **Skapa kanal...** från menyn.
[Haivision (på)](./media/media-services-configure-kb-live-encoder/channel.png)
2. Ange ett kanalnamn, beskrivningsfältet är valfritt. Under Kanalinställningar väljer du **Standard** för alternativet Direktkodning, med indataprotokollet inställt på **RTMP**. Du kan lämna alla andra inställningar som de är. Kontrollera att **starta den nya kanalen nu** är markerad.
3. Klicka på **Skapa kanal**.
[Haivision (på)](./media/media-services-configure-kb-live-encoder/livechannel.png)

> [!NOTE]
> Kanalen kan ta upp till 20 minuter att starta.

## <a name="configure-the-haivision-kb-encoder"></a>Konfigurera Haivision KB-kodaren
I den här självstudien används följande utdatainställningar. I resten av det här avsnittet beskrivs konfigurationsstegen mer i detalj.

Video:
-   Codec: H.264
-   Profil: Hög (nivå 4.0)
-   Bitrate: 5000 kbps
-   Nyckelbildruta: 2 sekunder (60 bildrutor)
-   Bildhastighet: 30

Ljud:
-   Codec: AAC (LC)
-   Bithastighet: 192 kbit/s
-   Provhastighet: 44,1 kHz

## <a name="configuration-steps"></a>Konfigurationssteg
1.  Logga in på Haivision KB-användargränssnittet.
2.  Klicka på **menyknappen** i kanalkontrollcentret och välj **Lägg till kanal**  
    ![Skärmdump 2017-08-14 kl 9.15.09](./media/media-services-configure-kb-live-encoder/step2.png)
3.  Skriv **kanalnamnet** i fältet Namn och klicka på nästa.  
    ![Skärmdump 2017-08-14 kl 09.19.07](./media/media-services-configure-kb-live-encoder/step3.png)
4.  Välj **kanalindatakällan** i listrutan **Indatakälla** och klicka på nästa.
    ![Skärmdump 2017-08-14 kl 09.20.44](./media/media-services-configure-kb-live-encoder/step4.png)
5.  Välj **H264-720-AAC-192** i listrutan **Kodarmall** och klicka på nästa.
    ![Skärmdump 2017-08-14 kl 9.23.15](./media/media-services-configure-kb-live-encoder/step5.png)
6.  Välj **RTMP** i listrutan **Välj ny utdata** och klicka på nästa.  
    ![Skärmdump 2017-08-14 kl 09.27.51](./media/media-services-configure-kb-live-encoder/step6.png)
7.  Fyll i Azure-ströminformation i fönstret **Kanalutdata.** Klistra in **RTMP-länken** från den första kanalinställningen i **serverområdet.** I området **Utdatanamn** skriver du in kanalens namn. I området Stream Name Template använder du mallen RTMPStreamName_%video_bitrate% för att namnge strömmen.
    ![Skärmdump 2017-08-14 kl 09.33.17](./media/media-services-configure-kb-live-encoder/step7.png)
8.  Klicka på nästa och sedan på Klar.
9.  Klicka på **play-knappen** för att starta kodarkanalen.  
    ![Haivision KB.png](./media/media-services-configure-kb-live-encoder/step9.png)

## <a name="test-playback"></a>Testa uppspelning
Navigera till AMSE-verktyget och högerklicka på den kanal som ska testas. Hovra över Uppspelning av förhandsgranskningen på menyn och välj med Azure Media Player.

Om strömmen visas i spelaren har kodaren konfigurerats korrekt för att ansluta till AMS.

Om ett fel tas emot måste kanalen återställas och kodarinställningarna justeras. Mer information finns i felsökningsartikeln.

## <a name="create-a-program"></a>Skapa ett program
1.  När kanaluppspelningen har bekräftats skapar du ett program. Högerklicka inom programområdet under fliken Live i AMSE-verktyget och välj Skapa nytt program.
[Haivision (på)](./media/media-services-configure-kb-live-encoder/program.png)
1.  Namnge programmet och justera arkivfönstrets längd om det behövs (som standard till fyra timmar). Du kan också ange en lagringsplats eller lämna som standard.
2.  Markera rutan Starta programmet nu.
3.  Klicka på Skapa program.
4.  När programmet är igång bekräftar du uppspelningen genom att högerklicka på programmet och navigera till Spela upp programmet/programen och sedan välja med Azure Media Player.
5.  När du har bekräftat det högerklickar du på programmet igen och väljer Kopiera utdata-URL:en till Urklipp (eller hämta den här informationen från alternativet Programinformation och inställningar på menyn).

Strömmen är nu redo att bäddas in i en spelare, eller distribueras till en publik för livevisning.

> [!NOTE]
> Det tar kortare tid än att skapa program.
