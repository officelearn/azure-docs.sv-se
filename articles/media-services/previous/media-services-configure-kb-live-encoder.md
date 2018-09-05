---
title: Konfigurera Haivision KB-kodare för att skicka en direktsänd dataström med enkel bithastighet till Azure | Microsoft Docs
description: Det här avsnittet visar hur du konfigurerar Haivision KB livekodaren för att skicka en enda bithastighet till AMS-kanaler som är aktiverade för live encoding.
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
ms.openlocfilehash: a36e12080cbbcb1a98bf786a6634959362cb52a7
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2018
ms.locfileid: "43666457"
---
# <a name="use-the-haivision-kb-live-encoder-to-send-a-single-bitrate-live-stream"></a>Använd Haivision KB livekodaren för att skicka en direktsänd dataström med enkel bithastighet
> [!div class="op_single_selector"]
> * [FMLE](media-services-configure-fmle-live-encoder.md)
> * [Haivision](media-services-configure-kb-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)

Det här avsnittet visar hur du konfigurerar den [Havision KB livekodare](https://www.haivision.com/products/kb-series/) encoder att skicka en enda bithastighet till AMS kanaler som är aktiverade för live encoding. Mer information finns i [Arbeta med kanaler som är aktiverade för att utföra Live Encoding med Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

Den här självstudien visar hur du hanterar Azure Media Services (AMS) med Azure Media Services Explorer (AMSE)-verktyget. Det här verktyget körs bara på Windows-dator. Om du använder Mac- eller Linux kan du använda Azure-portalen för att skapa [kanaler](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) och [program](media-services-portal-creating-live-encoder-enabled-channel.md).

## <a name="prerequisites"></a>Förutsättningar
*   Åtkomst till en Haivision KB-kodare som kör SV v5.01 eller större.
* [Skapa ett Azure Media Services-konto](media-services-portal-create-account.md)
* Se till att det finns en slutpunkt för direktuppspelning som körs. Mer information finns i [hanterar Strömningsslutpunkter i ett Media Services-konto](media-services-portal-manage-streaming-endpoints.md)
* Installera den senaste versionen av den [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) verktyget.
* Starta verktyget och ansluta till AMS-kontot.

## <a name="tips"></a>Tips
* När det är möjligt använda ett inbyggt internet-anslutning.
* En bra tumregel när du fastställer kraven på nätverksbandbredd är att dubbla strömmande bithastighet. Detta är inte ett obligatoriskt krav, hjälper det att undvika påverkan av överbelastning på nätverket.
* När du använder programvarubaserad kodare kan du stänga alla onödiga program.

## <a name="create-a-channel"></a>Skapa en kanal
1. I AMSE-verktyget går du till den **Live** fliken och högerklicka i området för kanalen. Välj **skapa kanal...** på menyn.
[Haivision](./media/media-services-configure-kb-live-encoder/channel.png)
2. Ange ett Kanalnamn beskrivningsfältet är valfritt. Välj under inställningar för kanalen **Standard** för alternativet Live Encoding med indata-protokollet som är inställd på **RTMP**. Du kan lämna alla andra inställningar som är. Kontrollera att den **starta den nya kanalen nu** har valts.
3. Klicka på **skapa kanal**.
[Haivision](./media/media-services-configure-kb-live-encoder/livechannel.png)

> [!NOTE]
> Kanalen kan ta upp till 20 minuter att starta.

## <a name="configure-the-haivision-kb-encoder"></a>Konfigurera Haivision KB-kodare
I den här självstudien används följande utdatainställningar för. Resten av det här avsnittet beskriver konfigurationssteg i detalj.

Video:
-   Codec: H.264
-   Profil: Hög (nivå 4.0)
-   Med flera bithastigheter: 5000 kbit/s
-   Bildrutan: 2 sekunder (60 sekunder)
-   RAM-pris: 30

Ljud:
-   Codec: AAC (LC)
-   Med flera bithastigheter: 192 kbit/s
-   Samplingshastighet: 44.1 kHz

## <a name="configuration-steps"></a>Konfigurationssteg
1.  Logga in till användargränssnittet Haivision KB.
2.  Klicka på den **menyknappen** i kanalen kontrollcenter och välj **Lägg till kanal**  
    ![Skärmbild som visar 2017-08-14 i 9.15.09 AM.png](./media/media-services-configure-kb-live-encoder/step2.png)
3.  Skriv den **Kanalnamn** i namnet på fältet och klicka på Nästa.  
    ![Skärmbild som visar 2017-08-14 i 9.19.07 AM.png](./media/media-services-configure-kb-live-encoder/step3.png)
4.  Välj den **kanal Indatakällan** från den **Indatakällan** listrutan och klicka på Nästa.
    ![Skärmbild som visar 2017-08-14 i 9.20.44 AM.png](./media/media-services-configure-kb-live-encoder/step4.png)
5.  Från den **Encoder mallen** listrutan Välj **H264-720-AAC-192** och klicka på Nästa.
    ![Skärmbild som visar 2017-08-14 i 9.23.15 AM.png](./media/media-services-configure-kb-live-encoder/step5.png)
6.  Från den **Välj nya utdata** listrutan Välj **RTMP** och klicka på Nästa.  
    ![Skärmbild som visar 2017-08-14 i 9.27.51 AM.png](./media/media-services-configure-kb-live-encoder/step6.png)
7.  Från den **kanal utdata** och fylla i informationen om Azure stream. Klistra in den **RTMP** länk från den första kanal i den **Server** området. I den **Utdatanamnet** området Skriv namnet på kanalen. Använd mallen RTMPStreamName_ % video_bitrate % för att namnge dataströmmen i området för mallen för Stream-namn.
    ![Skärmbild som visar 2017-08-14 i 9.33.17 AM.png](./media/media-services-configure-kb-live-encoder/step7.png)
8.  Klicka på Nästa och sedan klickar du på klar.
9.  Klicka på den **spela upp** att starta encoder-kanalen.  
    ![Haivision KB.png](./media/media-services-configure-kb-live-encoder/step9.png)

## <a name="test-playback"></a>Testa uppspelning
Gå till AMSE-verktyget och högerklicka på kanalen som ska testas. Hovra över uppspelning förhandsversionen från menyn och välj med Azure Media Player.

Om strömmen visas i spelaren, har sedan kodaren konfigurerats korrekt för att ansluta till AMS.

Om ett fel tas emot kanalen måste återställas och anpassa inställningar för kodare. Se felsökningsartikeln anvisningar.

## <a name="create-a-program"></a>Skapa ett program
1.  När kanalen uppspelning har bekräftats kan du skapa ett program. Högerklicka i området program under fliken Live i AMSE-verktyget och välj Skapa nytt Program.
[Haivision](./media/media-services-configure-kb-live-encoder/program.png)
1.  Namnge programmet och om det behövs, justera Arkiveringsfönstret (som standard är fyra timmar). Du kan också ange en lagringsplats eller lämna som standard.
2.  Kryssrutan början programmet nu.
3.  Klicka på Skapa Program.
4.  När programmet körs kan bekräfta uppspelning genom att högerklicka på programmet och navigera till spela upp program och sedan välja med Azure Media Player.
5.  När du bekräftat, högerklicka på programmet igen och välj Kopiera URL-Adressen för utdata till Urklipp (eller hämta den här informationen från programinformation och inställningsalternativ från menyn).

Dataströmmen är nu redo att vara inbäddad i en spelare eller distribueras till en målgrupp för visning av live.

> [!NOTE]
> Programmet tar mindre tid än att skapa en kanal.