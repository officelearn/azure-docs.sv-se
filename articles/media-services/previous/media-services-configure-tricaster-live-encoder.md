---
title: Konfigurera NewTek TriCaster-kodaren för att skicka en enda bit hastighet i real tid | Microsoft Docs
description: Det här avsnittet visar hur du konfigurerar TriCaster Live Encoder för att skicka en enda bit ström till AMS kanaler som är aktiverade för Live encoding.
services: media-services
documentationcenter: ''
author: cenkdin
manager: femila
editor: ''
ms.assetid: 8973181a-3059-471a-a6bb-ccda7d3ff297
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.reviewer: anilmur
ms.openlocfilehash: 11ee8f52a8fd4db2d052eeaeef1387b011d23050
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2020
ms.locfileid: "77131555"
---
# <a name="use-the-newtek-tricaster-encoder-to-send-a-single-bitrate-live-stream"></a>Använda NewTek TriCaster-kodaren för att skicka en enda bit hastighet i real tid  
> [!div class="op_single_selector"]
> * [TriCaster](media-services-configure-tricaster-live-encoder.md)
> * [Grundämne, Live](media-services-configure-elemental-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
>
>

Den här artikeln visar hur du konfigurerar [NewTek TriCaster](https://newtek.com/products/tricaster-40.html) Live Encoder för att skicka en enda bit ström till AMS kanaler som är aktiverade för direktsänd kodning. Mer information finns i [Arbeta med kanaler som är aktiverade för att utföra Live Encoding med Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

Den här självstudien visar hur du hanterar Azure Media Services (AMS) med Azure Media Services Explorer (AMSE)-verktyget. Det här verktyget körs bara på Windows-dator. Om du använder Mac eller Linux använder du Azure Portal för att skapa [kanaler](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) och [program](media-services-portal-creating-live-encoder-enabled-channel.md).

> [!NOTE]
> När du använder TriCaster för att skicka en bidrags väg till AMS-kanaler som är aktiverade för Live encoding, kan det finnas video-/ljud fel i din Live-händelse om du använder vissa funktioner i TriCaster, t. ex. snabb styckning mellan matningar eller växlar till/från arbets flöden. AMS-teamet arbetar på att åtgärda de här problemen tills du har det inte rekommenderas att använda dessa funktioner.
>
>

## <a name="prerequisites"></a>Förutsättningar

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

    ![TriCaster](./media/media-services-tricaster-live-encoder/media-services-tricaster1.png)

2. Ange ett Kanalnamn beskrivningsfältet är valfritt. Under kanal inställningar väljer du **standard** för alternativet Live encoding med inmatnings protokollet inställt på **RTMP**. Du kan lämna alla andra inställningar som är.

    Kontrol lera att **starta den nya kanalen nu** är markerat.

3. Klicka på **skapa kanal**.

   ![TriCaster](./media/media-services-tricaster-live-encoder/media-services-tricaster2.png)

> [!NOTE]
> Kanalen kan ta upp till 20 minuter att starta.
>
>

När kanalen startas kan du [Konfigurera kodaren](media-services-configure-tricaster-live-encoder.md#configure_tricaster_rtmp).

> [!IMPORTANT]
> Debiteringen börjar när kanalen hamnar i tillståndet redo. Mer information finns i [kanalens tillstånd](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a name="a-idconfigure_tricaster_rtmpconfigure-the-newtek-tricaster-encoder"></a><a id="configure_tricaster_rtmp"/>konfigurera NewTek TriCaster-kodare

I den här självstudien används följande utdatainställningar för. Resten av det här avsnittet beskriver konfigurationssteg i detalj.

**Video**:

* Codec: H.264
* Profil: Hög (nivå 4.0)
* Med flera bithastigheter: 5000 kbit/s
* Bildrutan: 2 sekunder (60 sekunder)
* RAM-pris: 30

**Ljud**:

* Codec: AAC (LC)
* Med flera bithastigheter: 192 kbit/s
* Samplingshastighet: 44.1 kHz

### <a name="configuration-steps"></a>Konfigurationssteg

1. Skapa ett nytt **NewTek TriCaster** -projekt beroende på vilken video ingångs källa som används.
2. När du är i projektet söker du efter **Stream** -knappen och klickar på kugg hjuls ikonen bredvid den för att få åtkomst till data Ströms konfigurations menyn.

    ![TriCaster](./media/media-services-tricaster-live-encoder/media-services-tricaster3.png)
3. När menyn har öppnats klickar du på **ny** under anslutnings rubriken. När du tillfrågas om anslutnings typen väljer du **Adobe Flash**.

    ![TriCaster](./media/media-services-tricaster-live-encoder/media-services-tricaster4.png)
4. Klicka på **OK**
5. Nu kan du importera en FMLE-profil genom att klicka på listpilen under **strömmande profil** och navigera till **Bläddra**.

    ![TriCaster](./media/media-services-tricaster-live-encoder/media-services-tricaster5.png)
6. Navigera till den plats där den konfigurerade FMLE-profilen sparades.
7. Markera det och tryck på **OK**.

    När profilen har laddats upp fortsätter du till nästa steg.
8. Hämta kanalens inmatnings-URL för att tilldela den till TriCaster **RTMP-slutpunkten**.

    Gå tillbaka till AMSE-verktyget och kontrollera status för slutförande kanal. När statusen har ändrats från att **Starta** till **körs**kan du hämta INgångs-URL: en.

    När kanalen är igång, högerklickar du på kanal namnet, navigerar ned till hovra över **Kopiera ingångs-URL till Urklipp** och väljer sedan **primär ingångs-URL**.  

    ![TriCaster](./media/media-services-tricaster-live-encoder/media-services-tricaster6.png)
9. Klistra in den här informationen i fältet **plats** under **Flash server** i TriCaster-projektet. Tilldela också ett data ström namn i fältet **Stream-ID** .

    Om strömmande information har lagts till i FMLE-profilen kan den också importeras till det här avsnittet genom att klicka på **Importera inställningar**, navigera till den sparade FMLE-profilen och klicka på **OK**. De relevanta Flash server-fälten fylls med informationen från FMLE.

    ![TriCaster](./media/media-services-tricaster-live-encoder/media-services-tricaster7.png)
10. När du är färdig klickar du på **OK** längst ned på skärmen. När video-och ljud inmatningar i TriCaster är klara, börjar du strömma till AMS genom att klicka på **ström** knappen.

     ![TriCaster](./media/media-services-tricaster-live-encoder/media-services-tricaster11.png)

> [!IMPORTANT]
> Innan du klickar på **Stream** **måste** du se till att kanalen är klar.
> Se dessutom till att du inte lämnar kanalen i tillståndet redo utan ett inkommande bidrag feed > 15 minuter.
>
>

## <a name="test-playback"></a>Testa uppspelning

Gå till AMSE-verktyget och högerklicka på kanalen som ska testas. Hovra över **uppspelning av för hands versionen på** menyn och välj **med Azure Media Player**.  

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster8.png)

Om strömmen visas i spelaren, har sedan kodaren konfigurerats korrekt för att ansluta till AMS.

Om ett fel tas emot måste kanalen återställas och kodarens inställningar justeras. Se [fel söknings](media-services-troubleshooting-live-streaming.md) artikeln för vägledning.  

## <a name="create-a-program"></a>Skapa ett program

1. När kanalen uppspelning har bekräftats kan du skapa ett program. Under fliken **Live** i AMSE-verktyget högerklickar du i program arean och väljer **Skapa nytt program**.  

    ![TriCaster](./media/media-services-tricaster-live-encoder/media-services-tricaster9.png)
2. Ge programmet ett namn och, om det behövs, ändra **Arkiv fönstrets längd** (som är standardvärdet fyra timmar). Du kan också ange en lagringsplats eller lämna som standard.  
3. Markera kryss rutan **starta programmet nu** .
4. Klicka på **skapa program**.  

    >[!NOTE]
    >Programmet tar mindre tid än att skapa en kanal.
        
5. När programmet har körts bekräftar du uppspelningen genom att högerklicka på programmet och navigera för att **spela upp program** och välja **med Azure Media Player**.  
6. När du har bekräftat, högerklickar du på programmet igen och väljer **Kopiera URL: en till Urklipp** (eller hämta informationen från **program information och inställnings** alternativ på menyn).

Dataströmmen är nu redo att vara inbäddad i en spelare eller distribueras till en målgrupp för visning av live.  

## <a name="troubleshooting"></a>Felsökning

Se [fel söknings](media-services-troubleshooting-live-streaming.md) artikeln för vägledning.

## <a name="next-step"></a>Nästa steg

Granska sökvägarna för Media Services-utbildning.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
