---
title: Konfigurera Haivision KB-kodaren för att skicka en enda bit Ströms bit ström till Azure | Microsoft Docs
description: Det här avsnittet visar hur du konfigurerar Haivision KB Live Encoder för att skicka en enda bit ström till AMS kanaler som är aktiverade för Live encoding.
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
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2020
ms.locfileid: "77131932"
---
# <a name="use-the-haivision-kb-live-encoder-to-send-a-single-bitrate-live-stream"></a>Använd Haivision KB Live Encoder för att skicka en enda bit Ströms bit ström  
> [!div class="op_single_selector"]
> * [Haivision](media-services-configure-kb-live-encoder.md)
> * [TriCaster](media-services-configure-tricaster-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)

Det här avsnittet visar hur du konfigurerar [HAVISION KB Live Encoder](https://www.haivision.com/products/kb-series/) Encoder för att skicka en enda bit ström till AMS kanaler som är aktiverade för direktsänd kodning. Mer information finns i [Arbeta med kanaler som är aktiverade för att utföra Live Encoding med Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

Den här självstudien visar hur du hanterar Azure Media Services (AMS) med Azure Media Services Explorer (AMSE)-verktyget. Det här verktyget körs bara på Windows-dator. Om du använder Mac eller Linux använder du Azure Portal för att skapa [kanaler](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) och [program](media-services-portal-creating-live-encoder-enabled-channel.md).

## <a name="prerequisites"></a>Förutsättningar
*   Åtkomst till en Haivision KB-kodare som kör SW v 5.01 eller senare.
* [Skapa ett Azure Media Services konto](media-services-portal-create-account.md)
* Se till att det finns en slutpunkt för direktuppspelning som körs. Mer information finns i [Hantera strömnings slut punkter i ett Media Services konto](media-services-portal-manage-streaming-endpoints.md)
* Installera den senaste versionen av [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) -verktyget.
* Starta verktyget och ansluta till AMS-kontot.

## <a name="tips"></a>Tips
* När det är möjligt använda ett inbyggt internet-anslutning.
* En bra tumregel när du fastställer kraven på nätverksbandbredd är att dubbla strömmande bithastighet. Detta är inte ett obligatoriskt krav, hjälper det att undvika påverkan av överbelastning på nätverket.
* När du använder programvarubaserad kodare kan du stänga alla onödiga program.

## <a name="create-a-channel"></a>Skapa en kanal
1. I AMSE-verktyget går du till fliken **Live** och högerklickar i kanalområdet. Välj **skapa kanal...** på menyn.
[Haivision](./media/media-services-configure-kb-live-encoder/channel.png)
2. Ange ett Kanalnamn beskrivningsfältet är valfritt. Under kanal inställningar väljer du **standard** för alternativet Live encoding med inmatnings protokollet inställt på **RTMP**. Du kan lämna alla andra inställningar som är. Kontrol lera att **starta den nya kanalen nu** är markerat.
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
-   Nyckel bild: 2 sekunder (60 bild rutor)
-   RAM-pris: 30

In
-   Codec: AAC (LC)
-   Med flera bithastigheter: 192 kbit/s
-   Samplingshastighet: 44.1 kHz

## <a name="configuration-steps"></a>Konfigurationssteg
1.  Logga in på Haivision KB-användargränssnittet.
2.  Klicka på **meny knappen** i kanal kontroll centret och välj **Lägg till kanal**  
    ![skärm bild 2017-08-14 på 9.15.09](./media/media-services-configure-kb-live-encoder/step2.png)
3.  Skriv **kanal namnet** i fältet namn och klicka på Nästa.  
    ![skärm bild 2017-08-14 på 9.19.07](./media/media-services-configure-kb-live-encoder/step3.png)
4.  Välj **kanalens indatakälla** **från List rutan indatakälla och** Klicka på Nästa.
    ![skärm bild 2017-08-14 på 9.20.44](./media/media-services-configure-kb-live-encoder/step4.png)
5.  Från List rutan **Encoder-mall** väljer du **H264,-720-AAC-192** och klickar på Nästa.
    ![skärm bild 2017-08-14 på 9.23.15](./media/media-services-configure-kb-live-encoder/step5.png)
6.  I list rutan **Välj ny utdata** väljer du **RTMP** och klickar på Nästa.  
    ![skärm bild 2017-08-14 på 9.27.51](./media/media-services-configure-kb-live-encoder/step6.png)
7.  I fönstret **kanal utdata** fyller du i Azure Stream-informationen. Klistra in **RTMP** -länken från den första kanal konfigurationen i **Server** delen. Skriv namnet på kanalen i fältet **utdatafil** . I mallen för Stream-namn mal len använder du mallen RTMPStreamName_% video_bitrate% för att ge data strömmen ett namn.
    ![skärm bild 2017-08-14 på 9.33.17](./media/media-services-configure-kb-live-encoder/step7.png)
8.  Klicka på Nästa och sedan på Slutför.
9.  Starta Encoder-kanalen genom att klicka på **uppspelnings knappen** .  
    ![Haivision KB. png](./media/media-services-configure-kb-live-encoder/step9.png)

## <a name="test-playback"></a>Testa uppspelning
Gå till AMSE-verktyget och högerklicka på kanalen som ska testas. Hovra över uppspelning av för hands versionen på menyn och välj med Azure Media Player.

Om strömmen visas i spelaren, har sedan kodaren konfigurerats korrekt för att ansluta till AMS.

Om ett fel tas emot kanalen måste återställas och anpassa inställningar för kodare. Se fel söknings artikeln för vägledning.

## <a name="create-a-program"></a>Skapa ett program
1.  När kanalen uppspelning har bekräftats kan du skapa ett program. Under fliken Live i AMSE-verktyget högerklickar du i program arean och väljer Skapa nytt program.
[Haivision](./media/media-services-configure-kb-live-encoder/program.png)
1.  Ge programmet ett namn och, om det behövs, ändra Arkiv fönstrets längd (som är standardvärdet fyra timmar). Du kan också ange en lagringsplats eller lämna som standard.
2.  Markera kryss rutan Starta programmet nu.
3.  Klicka på Skapa program.
4.  När programmet har körts bekräftar du uppspelningen genom att högerklicka på programmet och navigera för att spela upp program och välja med Azure Media Player.
5.  När du har bekräftat, högerklickar du på programmet igen och väljer Kopiera URL: en till Urklipp (eller hämta informationen från program information och inställnings alternativ på menyn).

Dataströmmen är nu redo att vara inbäddad i en spelare eller distribueras till en målgrupp för visning av live.

> [!NOTE]
> Programmet tar mindre tid än att skapa en kanal.
