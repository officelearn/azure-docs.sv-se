---
title: "Konfigurera FMLE kodaren om du vill skicka en direktsänd dataström med enkel bithastighet | Microsoft Docs"
description: "Det här avsnittet visar hur du konfigurerar kodaren Flash Media Live kodare (FMLE) för att skicka en dataström med enkel bithastighet till AMS-kanaler som är aktiverade för live encoding."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 3113f333-517a-47a1-a1b3-57e200c6b2a2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 01/05/2017
ms.author: juliako;cenkdin;anilmur
ms.openlocfilehash: e831048f34ecf6e89595adc4bfd58b5977e04bdb
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
---
# <a name="use-the-fmle-encoder-to-send-a-single-bitrate-live-stream"></a>Använda kodaren FMLE för att skicka en direktsänd dataström med enkel bithastighet
> [!div class="op_single_selector"]
> * [FMLE](media-services-configure-fmle-live-encoder.md)
> * [Elemental Live](media-services-configure-elemental-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
>
>

Det här avsnittet visar hur du konfigurerar den [Flash Live mediekodare](http://www.adobe.com/products/flash-media-encoder.html) kodare (FMLE) att skicka en dataström med enkel bithastighet till AMS kanaler som är aktiverade för live encoding. Mer information finns i [Arbeta med kanaler som är aktiverade för att utföra Live Encoding med Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

Den här kursen visar hur du hanterar Azure Media Services (AMS) med Azure Media Services Explorer (AMSE)-verktyget. Det här verktyget körs bara på Windows-dator. Om du är på Mac- eller Linux använder Azure-portalen för att skapa [kanaler](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) och [program](media-services-portal-creating-live-encoder-enabled-channel.md).

Observera att den här självstudiekursen beskriver hur du använder AAC. FMLE stöder inte AAC som standard. Du behöver köpa ett plugin-program för AAC kodning till exempel från MainConcept: [AAC plugin-program](http://www.mainconcept.com/products/plug-ins/plug-ins-for-adobe/aac-encoder-fmle.html)

## <a name="prerequisites"></a>Krav
* [Skapa ett Azure Media Services-konto](media-services-portal-create-account.md)
* Se till att det finns en Strömningsslutpunkt som körs. Mer information finns i [hanterar Strömningsslutpunkter i ett Media Services-konto](media-services-portal-manage-streaming-endpoints.md)
* Installera den senaste versionen av den [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) verktyget.
* Starta verktyget och Anslut till AMS-kontot.

## <a name="tips"></a>Tips
* När det är möjligt använda ett inbyggt internet-anslutning.
* En bra tumregel samband med fastställandet av krav på bandbredd är att dubbla strömmande bithastighet. Även om detta inte är en nödvändighet hjälper minimera effekten av överbelastning.
* När du använder programvara baserat kodare, Stäng alla onödiga program.

## <a name="create-a-channel"></a>Skapa en kanal
1. I verktyget AMSE navigerar du till den **Live** fliken och högerklicka i området för kanal. Välj **skapa kanal...** från menyn.

    ![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle1.png)

2. Ange ett Kanalnamn beskrivningsfältet är valfritt. Markera under inställningar för kanal **Standard** för alternativet Live Encoding med protokollet indata som angetts till **RTMP**. Du kan lämna alla andra inställningar som är.

    Kontrollera att den **starta den nya kanalen nu** är markerad.

3. Klicka på **skapa kanal**.

   ![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle2.png)

> [!NOTE]
> Kanalen kan ta upp till 20 minuter för att starta.
>
>

När kanalen startar kan du [konfigurera kodaren](media-services-configure-fmle-live-encoder.md#configure_fmle_rtmp).

> [!IMPORTANT]
> Observera att faktureringen påbörjas så snart kanal blir klar att användas. Mer information finns i [kanalens tillstånd](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a id=configure_fmle_rtmp></a>Konfigurera FMLE kodaren
I den här kursen används följande inställningar för utdata. Resten av det här avsnittet beskriver konfigurationssteg i detalj.

**Video**:

* Codec: H.264
* Profil: Hög (nivå 4.0)
* Bithastighet: 5000 kbit/s
* Keyframe: 2 sekunder (60 sekunder)
* RAM-hastighet: 30

**Ljud**:

* Codec: AAC (LC)
* Bithastighet: 192 kbit/s
* Samplingsfrekvens: 44.1 kHz

### <a name="configuration-steps"></a>Konfigurationssteg
1. Navigera till den Flash Media Live kodarens (FMLE) gränssnitt på den dator som används.

    Gränssnittet är en huvudsidan för inställningar. Kom ihåg följande rekommenderade inställningar för att komma igång med strömning med FMLE.

   * Format: H.264 bildfrekvens: 30,00
   * Inkommande storlek: minst 1 280 x 720
   * Bithastighet: 5000 kbit/s (kan justeras utifrån nätverksbegränsningar)  

     ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle3.png)

     När du använder sammanflätad källor, du markera alternativet ”Ej sammanflätning”
2. Välj skiftnyckelikonen bredvid Format, dessa ytterligare inställningar ska vara:

   * Profil: Main
   * Nivå: 4.0
   * Keyframe frekvens: 2 sekunder

     ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle4.png)
3. Ange följande viktiga ljud inställning:

   * Format: AAC
   * Samplingsfrekvens: 44100 Hz
   * Bithastighet: 192 kbit/s

     ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle5.png)
4. Hämta kanalen input URL: en för att tilldela FMLE **RTMP Endpoint**.

    Gå tillbaka till verktyget AMSE och kontrollera status för slutförande kanal. När läget har ändrats från **Start** till **kör**, du kan hämta indata-URL.

    När kanalen körs, högerklickar du på dess namn, navigera till hovra över **kopiera indata-URL till Urklipp** och välj sedan **primära indata-URL**.  

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle6.png)
5. Klistra in informationen i den **FMS URL** i utdata och tilldela ett namn på dataströmmen.

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle7.png)

    Upprepa dessa steg med sekundära indata-URL för extra redundans.
6. Välj **Anslut**.

> [!IMPORTANT]
> Innan du klickar på **Anslut**, du **måste** se till att kanalen är klar.
> Kontrollera också att kanalen klar utan ett inkommande bidrag feed för > 15 minuter.
>
>

## <a name="test-playback"></a>Testa uppspelning

Navigera till verktyget AMSE och högerklicka på kanalen som ska testas. Från menyn hovra över **uppspelning förhandsgranskningen** och välj **med Azure Media Player**.  

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle8.png)

Om strömmen visas i media player, har sedan kodaren konfigurerats korrekt för att ansluta till AMS.

Om ett fel tas emot kanalen kommer att behöva återställas och anpassa inställningar för kodare. Finns det [felsökning](media-services-troubleshooting-live-streaming.md) avsnittet som vägledning.  

## <a name="create-a-program"></a>Skapa ett program
1. När kanalen uppspelning bekräftas, skapa ett program. Under den **Live** i verktyget AMSE, högerklicka i området för programmet och välj **Skapa nytt Program**.  

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle9.png)
2. Namnge programmet och, om det behövs, justera det **längd** (som standard 4 timmar). Du kan också ange en lagringsplats eller lämna som standard.  
3. Kontrollera den **starta programmet nu** rutan.
4. Klicka på **skapa Program**.  

    >[!NOTE]
    >Skapa en program tar mindre tid än att skapa en kanal.
        
5. När programmet körs kan bekräfta uppspelning genom att högerklicka på programmet och gå till **uppspelning av program** och sedan välja **med Azure Media Player**.  
6. När bekräftat, högerklicka på programmet och välj **kopiera den URL som utdata till Urklipp** (eller hämta den här informationen från den **programmet information och inställningar** alternativ på menyn).

Dataströmmen är nu redo att vara inbäddat i ett player eller distribuerats till en målgrupp för live visning.  

## <a name="troubleshooting"></a>Felsökning
Finns det [felsökning](media-services-troubleshooting-live-streaming.md) avsnittet som vägledning.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
