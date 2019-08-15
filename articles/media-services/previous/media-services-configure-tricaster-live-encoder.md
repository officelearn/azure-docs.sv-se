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
ms.openlocfilehash: 0e793a5aa7d619b0bb7a1d3efcdf665ea400c555
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2019
ms.locfileid: "69016740"
---
# <a name="use-the-newtek-tricaster-encoder-to-send-a-single-bitrate-live-stream"></a>Använda NewTek TriCaster-kodaren för att skicka en enda bit hastighet i real tid  
> [!div class="op_single_selector"]
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Grundämne, Live](media-services-configure-elemental-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
> * [FMLE](media-services-configure-fmle-live-encoder.md)
>
>

Den här artikeln visar hur du konfigurerar [NewTek TriCaster](https://newtek.com/products/tricaster-40.html) Live Encoder för att skicka en enda bit ström till AMS kanaler som är aktiverade för direktsänd kodning. Mer information finns i [Arbeta med kanaler som är aktiverade för att utföra Live Encoding med Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

Den här självstudien visar hur du hanterar Azure Media Services (AMS) med Azure Media Services Explorer (AMSE)-verktyget. Det här verktyget körs bara på Windows-dator. Om du använder Mac- eller Linux kan du använda Azure-portalen för att skapa [kanaler](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) och [program](media-services-portal-creating-live-encoder-enabled-channel.md).

> [!NOTE]
> När du använder TriCaster för att skicka en bidrags väg till AMS-kanaler som är aktiverade för Live encoding, kan det finnas video-/ljud fel i din Live-händelse om du använder vissa funktioner i TriCaster, t. ex. snabb styckning mellan matningar eller växlar till/från arbets flöden. AMS-teamet arbetar på att åtgärda de här problemen tills du har det inte rekommenderas att använda dessa funktioner.
>
>

## <a name="prerequisites"></a>Förutsättningar

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

    ![TriCaster](./media/media-services-tricaster-live-encoder/media-services-tricaster1.png)

2. Ange ett Kanalnamn beskrivningsfältet är valfritt. Välj under inställningar för kanalen **Standard** för alternativet Live Encoding med indata-protokollet som är inställd på **RTMP**. Du kan lämna alla andra inställningar som är.

    Kontrollera att den **starta den nya kanalen nu** har valts.

3. Klicka på **skapa kanal**.

   ![TriCaster](./media/media-services-tricaster-live-encoder/media-services-tricaster2.png)

> [!NOTE]
> Kanalen kan ta upp till 20 minuter att starta.
>
>

Medan kanalen startar kan du [konfigurera kodaren](media-services-configure-tricaster-live-encoder.md#configure_tricaster_rtmp).

> [!IMPORTANT]
> Debiteringen börjar när kanalen hamnar i tillståndet redo. Mer information finns i [kanalens tillstånd](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a name="a-idconfigure_tricaster_rtmpconfigure-the-newtek-tricaster-encoder"></a><a id="configure_tricaster_rtmp"/>Konfigurera NewTek TriCaster-kodare

I den här självstudien används följande utdatainställningar för. Resten av det här avsnittet beskriver konfigurationssteg i detalj.

**Video**:

* ADPCM H. 264
* Upphandlarprofil Hög (nivå 4,0)
* Hastigheten 5000 kbps
* Nyckel bild: 2 sekunder (60 sekunder)
* Bild Rute frekvens: 30

**Ljud**:

* ADPCM AAC (LC)
* Hastigheten 192 kbps
* Samplings frekvens: 44,1 kHz

### <a name="configuration-steps"></a>Konfigurationssteg

1. Skapa ett nytt **NewTek TriCaster** -projekt beroende på vilken video ingångs källa som används.
2. När du är i projektet söker du efter **Stream** -knappen och klickar på kugg hjuls ikonen bredvid den för att få åtkomst till data Ströms konfigurations menyn.

    ![TriCaster](./media/media-services-tricaster-live-encoder/media-services-tricaster3.png)
3. När menyn har öppnats klickar du på **ny** under anslutnings rubriken. När du tillfrågas om anslutnings typen väljer du **Adobe Flash**.

    ![TriCaster](./media/media-services-tricaster-live-encoder/media-services-tricaster4.png)
4. Klicka på **OK**.
5. Nu kan du importera en FMLE-profil genom att klicka på listpilen under **strömmande profil** och navigera till **Bläddra**.

    ![TriCaster](./media/media-services-tricaster-live-encoder/media-services-tricaster5.png)
6. Navigera till den plats där den konfigurerade FMLE-profilen sparades.
7. Markera det och tryck på **OK**.

    När profilen har laddats upp fortsätter du till nästa steg.
8. Hämta kanalens inmatnings-URL för att tilldela den till TriCaster **RTMP**-slutpunkten.

    Gå tillbaka till AMSE-verktyget och kontrollera status för slutförande kanal. När tillståndet har ändrats från **startar** till **kör**, du kan hämta den angivna URL-Adressen.

    När kanalen är igång, högerklickar du på kanal namnet, navigerar ned till hovra över kopiera ingångs- **URL till Urklipp** och väljer sedan primär ingångs- **URL**.  

    ![TriCaster](./media/media-services-tricaster-live-encoder/media-services-tricaster6.png)
9. Klistra in den här informationen i fältet **plats** under **Flash server** i TriCaster-projektet. Tilldela också ett data ström namn i fältet **Stream-ID** .

    Om strömmande information har lagts till i FMLE-profilen kan den också importeras till det här avsnittet genom att klicka på **Importera inställningar**, navigera till den sparade FMLE-profilen och klicka på **OK**. De relevanta Flash server-fälten fylls med informationen från FMLE.

    ![TriCaster](./media/media-services-tricaster-live-encoder/media-services-tricaster7.png)
10. När du är färdig klickar du på **OK** längst ned på skärmen. När video-och ljud inmatningar i TriCaster är klara, börjar du strömma till AMS genom att klicka på **ström** knappen.

     ![TriCaster](./media/media-services-tricaster-live-encoder/media-services-tricaster11.png)

> [!IMPORTANT]
> Innan du klickar på **Stream**, du **måste** se till att kanalen är redo.
> Se dessutom till att du inte lämnar kanalen i tillståndet redo utan ett inkommande bidrag feed > 15 minuter.
>
>

## <a name="test-playback"></a>Testa uppspelning

Gå till AMSE-verktyget och högerklicka på kanalen som ska testas. Från menyn, hovrar du över **uppspelning förhandsversionen** och välj **med Azure Media Player**.  

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster8.png)

Om strömmen visas i spelaren, har sedan kodaren konfigurerats korrekt för att ansluta till AMS.

Om ett fel tas emot måste kanalen återställas och kodarens inställningar justeras. Se den [felsökning](media-services-troubleshooting-live-streaming.md) artikeln vägledning.  

## <a name="create-a-program"></a>Skapa ett program

1. När kanalen uppspelning har bekräftats kan du skapa ett program. Under den **Live** fliken AMSE-verktyget, högerklicka i området för programmet och välj **Skapa nytt Program**.  

    ![TriCaster](./media/media-services-tricaster-live-encoder/media-services-tricaster9.png)
2. Namnge programmet och, om det behövs, justera den **Arkiveringsfönstret** (som standard är fyra timmar). Du kan också ange en lagringsplats eller lämna som standard.  
3. Kontrollera den **starta programmet nu** box.
4. Klicka på **skapa Program**.  

    >[!NOTE]
    >Programmet tar mindre tid än att skapa en kanal.
        
5. När programmet körs kan bekräfta uppspelning genom att högerklicka på programmet och navigera till **uppspelning på program** och sedan välja **med Azure Media Player**.  
6. När du bekräftat, högerklicka på programmet igen och välj **kopiera den URL som utdata till Urklipp** (eller hämta den här informationen från den **programmet information och inställningar** alternativ på menyn).

Dataströmmen är nu redo att vara inbäddad i en spelare eller distribueras till en målgrupp för visning av live.  

## <a name="troubleshooting"></a>Felsökning

Se den [felsökning](media-services-troubleshooting-live-streaming.md) artikeln vägledning.

## <a name="next-step"></a>Nästa steg

Granska sökvägarna för Media Services-utbildning.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
