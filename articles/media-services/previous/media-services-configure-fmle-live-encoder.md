---
title: Konfigurera FMLE-kodare för att skicka en direktsänd dataström med enkel bithastighet | Microsoft Docs
description: Det här avsnittet visar hur du konfigurerar kodaren Flash Media Live Encoder (FMLE) för att skicka en enda bithastighet till AMS-kanaler som är aktiverade för live encoding.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 3113f333-517a-47a1-a1b3-57e200c6b2a2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako;cenkdin;anilmur
ms.openlocfilehash: 01bb628a6520488dcebf49a1e868213b955abc31
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61466020"
---
# <a name="use-the-fmle-encoder-to-send-a-single-bitrate-live-stream"></a>Använd FMLE-kodare för att skicka en direktsänd dataström med enkel bithastighet 
> [!div class="op_single_selector"]
> * [FMLE](media-services-configure-fmle-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
>
>

Den här artikeln visar hur du konfigurerar den [Flash Media Live Encoder](https://www.adobe.com/products/flash-media-encoder.html) encoder (FMLE) att skicka en enda bithastighet till AMS kanaler som är aktiverade för live encoding. Mer information finns i [Arbeta med kanaler som är aktiverade för att utföra Live Encoding med Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

Den här självstudien visar hur du hanterar Azure Media Services (AMS) med Azure Media Services Explorer (AMSE)-verktyget. Det här verktyget körs bara på Windows-dator. Om du använder Mac- eller Linux kan du använda Azure-portalen för att skapa [kanaler](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) och [program](media-services-portal-creating-live-encoder-enabled-channel.md).

Den här självstudien beskrivs hur du använder AAC. FMLE stöder inte AAC som standard. Du behöver du köpa ett plugin-program för AAC kodning som från MainConcept: [AAC-pluginprogram](https://www.mainconcept.com/products/plug-ins/plug-ins-for-adobe/aac-encoder-fmle.html)

## <a name="prerequisites"></a>Nödvändiga komponenter
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

    ![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle1.png)

2. Ange ett Kanalnamn beskrivningsfältet är valfritt. Välj under inställningar för kanalen **Standard** för alternativet Live Encoding med indata-protokollet som är inställd på **RTMP**. Du kan lämna alla andra inställningar som är.

    Kontrollera att den **starta den nya kanalen nu** har valts.

3. Klicka på **skapa kanal**.

   ![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle2.png)

> [!NOTE]
> Kanalen kan ta upp till 20 minuter att starta.
>
>

Medan kanalen startar kan du [konfigurera kodaren](media-services-configure-fmle-live-encoder.md).

> [!IMPORTANT]
> Observera att faktureringen påbörjas när kanalen hamnar i tillståndet redo. Mer information finns i [kanalens tillstånd](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a id=configure_fmle_rtmp></a>Konfigurera FMLE-kodare
I den här självstudien används följande utdatainställningar för. Resten av det här avsnittet beskriver konfigurationssteg i detalj.

**Video**:

* Codec: H.264
* Profil: Hög (nivå 4.0)
* Med flera bithastigheter: 5000 kbit/s
* Bildrutan: 2 sekunder (60 sekunder)
* Bildfrekvens: 30

**Ljud**:

* Codec: AAC (LC)
* Med flera bithastigheter: 192 kbit/s
* Samplingshastighet: 44.1 kHz

### <a name="configuration-steps"></a>Konfigurationssteg
1. Navigera till den Flash Media Live Encoder's (FMLE) gränssnitt på den datorn som används.

    Gränssnittet är en huvudsidan för inställningar. Anteckna följande rekommenderade inställningar för att komma igång med strömning med FMLE.

   * Format: H.264 bildfrekvens: 30.00
   * Inkommande storlek: 1280 x 720
   * Bithastighet: 5000 kbit/s (kan justeras utifrån nätverksbegränsningar)  

     ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle3.png)

     När du använder sammanflätad källor, kan du markera alternativet ”Ej sammanflätning”
2. Välj skiftnyckelikonen bredvid Format, dessa ytterligare inställningar ska vara:

   * Profil: Huvud
   * Nivå: 4.0
   * Bildrutan frekvens: 2 sekunder

     ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle4.png)
3. Ange följande viktiga ljud inställning:

   * Format: AAC
   * Samplingshastighet: 44100 Hz
   * Med flera bithastigheter: 192 kbit/s

     ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle5.png)
4. Hämta kanalen användarens indata URL: en för att tilldela den till FMLE **RTMP Endpoint**.

    Gå tillbaka till AMSE-verktyget och kontrollera status för slutförande kanal. När tillståndet har ändrats från **startar** till **kör**, du kan hämta den angivna URL-Adressen.

    När kanalen körs, högerklicka på kanalnamnet på, navigera till hovra över **kopia indata-URL till Urklipp** och välj sedan **primära indata-URL**.  

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle6.png)
5. Klistra in den här informationen i den **FMS URL** i outputs-avsnittet och tilldela ett namn för stream.

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle7.png)

    Upprepa dessa steg med sekundära indata-URL för extra redundans.
6. Välj **Anslut**.

> [!IMPORTANT]
> Innan du klickar på **Connect**, du **måste** se till att kanalen är redo.
> Se dessutom till att du inte lämnar kanalen i tillståndet redo utan ett inkommande bidrag feed > 15 minuter.
>
>

## <a name="test-playback"></a>Testa uppspelning

Gå till AMSE-verktyget och högerklicka på kanalen som ska testas. Från menyn, hovrar du över **uppspelning förhandsversionen** och välj **med Azure Media Player**.  

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle8.png)

Om strömmen visas i spelaren, har sedan kodaren konfigurerats korrekt för att ansluta till AMS.

Om ett fel tas emot kanalen måste återställas och anpassa inställningar för kodare. Se den [felsökning](media-services-troubleshooting-live-streaming.md) artikeln vägledning.  

## <a name="create-a-program"></a>Skapa ett program
1. När kanalen uppspelning har bekräftats kan du skapa ett program. Under den **Live** fliken AMSE-verktyget, högerklicka i området för programmet och välj **Skapa nytt Program**.  

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle9.png)
2. Namnge programmet och, om det behövs, justera den **Arkiveringsfönstret** (som standard är 4 timmar). Du kan också ange en lagringsplats eller lämna som standard.  
3. Kontrollera den **starta programmet nu** box.
4. Klicka på **skapa Program**.  

    >[!NOTE]
    >Programmet tar mindre tid än att skapa en kanal.
        
5. När programmet körs kan bekräfta uppspelning genom att högerklicka på programmet och navigera till **uppspelning på program** och sedan välja **med Azure Media Player**.  
6. När du bekräftat, högerklicka på programmet igen och välj **kopiera den URL som utdata till Urklipp** (eller hämta den här informationen från den **programmet information och inställningar** alternativ på menyn).

Dataströmmen är nu redo att vara inbäddad i en spelare eller distribueras till en målgrupp för visning av live.  

## <a name="troubleshooting"></a>Felsökning
Se den [felsökning](media-services-troubleshooting-live-streaming.md) artikeln vägledning.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
