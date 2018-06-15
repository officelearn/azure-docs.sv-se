---
title: Konfigurera Haivision KB kodaren för att skicka en direktsänd dataström med enkel bithastighet till Azure | Microsoft Docs
description: Det här avsnittet visar hur du konfigurerar Haivision KB livekodaren för att skicka en dataström med enkel bithastighet till AMS-kanaler som är aktiverade för live encoding.
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
ms.date: 02/02/2018
ms.author: juliako;dbgeorge
ms.openlocfilehash: 25077cd9338a2764c6dff9e755812033685f6641
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788790"
---
# <a name="use-the-haivision-kb-live-encoder-to-send-a-single-bitrate-live-stream"></a>Använd Haivision KB livekodaren för att skicka en direktsänd dataström med enkel bithastighet
> [!div class="op_single_selector"]
> * [Elemental Live](media-services-configure-elemental-live-encoder.md)
> * [FMLE](media-services-configure-fmle-live-encoder.md)
> * [Haivision](media-services-configure-kb-live-encoder.md)
> * [tricaster](media-services-configure-tricaster-live-encoder.md)
> * [wirecast](media-services-configure-wirecast-live-encoder.md)

Det här avsnittet visar hur du konfigurerar den [Havision KB livekodaren](https://www.haivision.com/products/kb-series/) att skicka en dataström med enkel bithastighet till AMS kanaler som är aktiverade för live encoding. Mer information finns i [Arbeta med kanaler som är aktiverade för att utföra Live Encoding med Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

Den här kursen visar hur du hanterar Azure Media Services (AMS) med Azure Media Services Explorer (AMSE)-verktyget. Det här verktyget körs bara på Windows-dator. Om du är på Mac- eller Linux använder Azure-portalen för att skapa [kanaler](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) och [program](media-services-portal-creating-live-encoder-enabled-channel.md).

## <a name="prerequisites"></a>Förutsättningar
*   Åtkomst till en Haivision KB-kodaren kör SW v5.01 eller större.
* [Skapa ett Azure Media Services-konto](media-services-portal-create-account.md)
* Se till att det finns en Strömningsslutpunkt som körs. Mer information finns i [hanterar Strömningsslutpunkter i ett Media Services-konto](media-services-portal-manage-streaming-endpoints.md)
* Installera den senaste versionen av den [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) verktyget.
* Starta verktyget och Anslut till AMS-kontot.

## <a name="tips"></a>Tips
* När det är möjligt använda ett inbyggt internet-anslutning.
* En bra tumregel samband med fastställandet av krav på bandbredd är att dubbla strömmande bithastighet. Detta är inte ett obligatoriskt krav, minimeras det effekten av överbelastning.
* När du använder programvarubaserad kodare, Stäng alla onödiga program.

## <a name="create-a-channel"></a>Skapa en kanal
1. I verktyget AMSE navigerar du till den **Live** fliken och högerklicka på inom området kanal. Välj **skapa kanal...** från menyn.
[Haivision](./media/media-services-configure-kb-live-encoder/channel.png)
2. Ange ett Kanalnamn beskrivningsfältet är valfritt. Markera under inställningar för kanal **Standard** för alternativet Live Encoding med protokollet indata som angetts till **RTMP**. Du kan lämna alla andra inställningar som är. Kontrollera att den **starta den nya kanalen nu** är markerad.
3. Klicka på **skapa kanal**.
[Haivision](./media/media-services-configure-kb-live-encoder/livechannel.png)

> [!NOTE]
> Kanalen kan ta upp till 20 minuter för att starta.

## <a name="configure-the-haivision-kb-encoder"></a>Konfigurera kodaren Haivision KB
I den här kursen används följande inställningar för utdata. Resten av det här avsnittet beskriver konfigurationssteg i detalj.

Video:
-   Codec: H.264
-   Profil: Hög (nivå 4.0)
-   Bithastighet: 5000 kbit/s
-   Keyframe: 2 sekunder (60 sekunder)
-   RAM-hastighet: 30

Ljud:
-   Codec: AAC (LC)
-   Bithastighet: 192 kbit/s
-   Samplingsfrekvens: 44.1 kHz

## <a name="configuration-steps"></a>Konfigurationssteg
1.  Logga in på Haivision KB-användargränssnittet.
2.  Klicka på den **knapp** i kanalen kontrollcenter och välj **Lägg till kanal**  
    ![Skärmbild som visar 2017-08-14 på 9.15.09 AM.png](./media/media-services-configure-kb-live-encoder/step2.png)
3.  Typ av **Kanalnamn** i namnet på fältet och klickar på Nästa.  
    ![Skärmbild som visar 2017-08-14 på 9.19.07 AM.png](./media/media-services-configure-kb-live-encoder/step3.png)
4.  Välj den **kanal indatakälla** från den **Indatakällan** listrutan och klicka på Nästa.
    ![Skärmbild som visar 2017-08-14 på 9.20.44 AM.png](./media/media-services-configure-kb-live-encoder/step4.png)
5.  Från den **kodare mallen** listrutan Välj **H264-720-AAC-192** och klicka på Nästa.
    ![Skärmbild som visar 2017-08-14 på 9.23.15 AM.png](./media/media-services-configure-kb-live-encoder/step5.png)
6.  Från den **Välj ny utdata** listrutan Välj **RTMP** och klicka på Nästa.  
    ![Skärmbild som visar 2017-08-14 på 9.27.51 AM.png](./media/media-services-configure-kb-live-encoder/step6.png)
7.  Från den **kanal utdata** och fylla i informationen om Azure strömmen. Klistra in den **RTMP** länk från den första kanal i den **Server** område. I den **utdatanamn** områdestyp i kanalen. Använd mallen RTMPStreamName_ % video_bitrate för att namnge dataströmmen i området för mall för dataströmmen.
    ![Skärmbild som visar 2017-08-14 på 9.33.17 AM.png](./media/media-services-configure-kb-live-encoder/step7.png)
8.  Klicka på Nästa och klicka på klar.
9.  Klicka på den **spela upp** starta kodare-kanalen.  
    ![Haivision KB.png](./media/media-services-configure-kb-live-encoder/step9.png)

## <a name="test-playback"></a>Testa uppspelning
Navigera till verktyget AMSE och högerklicka på kanalen som ska testas. Hovra över uppspelning förhandsversionen och välj med Azure Media Player på menyn.

Om strömmen visas i media player, har sedan kodaren konfigurerats korrekt för att ansluta till AMS.

Om ett fel tas emot kanalen måste återställas och anpassa inställningar för kodare. Se felsökningsartikel anvisningar.

## <a name="create-a-program"></a>Skapa ett program
1.  När kanalen uppspelning bekräftas, skapa ett program. Under fliken Live i verktyget AMSE högerklickar du i området för programmet och väljer Skapa nytt Program.
[Haivision](./media/media-services-configure-kb-live-encoder/program.png)
1.  Namnge programmet och om det behövs, justera det längd (som standard fyra timmar). Du kan också ange en lagringsplats eller lämna som standard.
2.  Markera kryssrutan Starta programmet nu.
3.  Klicka på Skapa Program.
4.  När programmet körs kan bekräfta uppspelning genom att högerklicka på programmet och navigera till spela upp program och sedan välja med Azure Media Player.
5.  När bekräftat, högerklicka på programmet igen och väljer att kopiera URL-Adressen för utdata till Urklipp (eller hämta den här informationen från programinformation och inställningsalternativ från menyn).

Dataströmmen är nu redo att vara inbäddat i ett player eller distribuerats till en målgrupp för live visning.

> [!NOTE]
> Skapa en program tar mindre tid än att skapa en kanal.