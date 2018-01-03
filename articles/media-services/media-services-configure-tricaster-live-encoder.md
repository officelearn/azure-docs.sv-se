---
title: "Konfigurera NewTek TriCaster-kodaren om du vill skicka en direktsänd dataström med enkel bithastighet | Microsoft Docs"
description: "Det här avsnittet visar hur du konfigurerar Tricaster livekodaren för att skicka en dataström med enkel bithastighet till AMS-kanaler som är aktiverade för live encoding."
services: media-services
documentationcenter: 
author: cenkdin
manager: cfowler
editor: 
ms.assetid: 8973181a-3059-471a-a6bb-ccda7d3ff297
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 01/05/2017
ms.author: juliako;cenkd;anilmur
ms.openlocfilehash: 42b012fb98bd0504c931ce391d63aecca8c3d311
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2017
---
# <a name="use-the-newtek-tricaster-encoder-to-send-a-single-bitrate-live-stream"></a>Använda kodaren NewTek TriCaster för att skicka en direktsänd dataström med enkel bithastighet
> [!div class="op_single_selector"]
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Elemental Live](media-services-configure-elemental-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
> * [FMLE](media-services-configure-fmle-live-encoder.md)
>
>

Det här avsnittet visar hur du konfigurerar den [NewTek TriCaster](http://newtek.com/products/tricaster-40.html) livekodare för att skicka en dataström med enkel bithastighet till AMS kanaler som är aktiverade för live encoding. Mer information finns i [Arbeta med kanaler som är aktiverade för att utföra Live Encoding med Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

Den här kursen visar hur du hanterar Azure Media Services (AMS) med Azure Media Services Explorer (AMSE)-verktyget. Det här verktyget körs bara på Windows-dator. Om du är på Mac- eller Linux använder Azure-portalen för att skapa [kanaler](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) och [program](media-services-portal-creating-live-encoder-enabled-channel.md).

> [!NOTE]
> När du använder Tricaster för att skicka i ett bidrag till AMS-kanaler som är aktiverade för live encoding, kan det finnas problem med ljud och i din direktsända händelse om du använder vissa funktioner i Tricaster, till exempel snabb klippa ut mellan feeds eller växla till/från pekdatorer. AMS-teamet arbetar på att åtgärda dessa problem fram till dess, är den rekommenderar inte att använda dessa funktioner.
>
>

## <a name="prerequisites"></a>Förutsättningar
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

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster1.png)

2. Ange ett Kanalnamn beskrivningsfältet är valfritt. Markera under inställningar för kanal **Standard** för alternativet Live Encoding med protokollet indata som angetts till **RTMP**. Du kan lämna alla andra inställningar som är.

    Kontrollera att den **starta den nya kanalen nu** är markerad.

3. Klicka på **skapa kanal**.

   ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster2.png)

> [!NOTE]
> Kanalen kan ta upp till 20 minuter för att starta.
>
>

När kanalen startar kan du [konfigurera kodaren](media-services-configure-tricaster-live-encoder.md#configure_tricaster_rtmp).

> [!IMPORTANT]
> Observera att faktureringen påbörjas så snart kanal blir klar att användas. Mer information finns i [kanalens tillstånd](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a id=configure_tricaster_rtmp></a>Konfigurera NewTek TriCaster-kodaren
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
1. Skapa en ny **NewTek TriCaster** projekt beroende på vilka inkommande videokällan används.
2. En gång i det aktuella projektet att hitta den **dataströmmen** och klickar på ikonen växeln bredvid komma åt menyn direktuppspelning configuration.

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster3.png)
3. När du har öppnat menyn, klickar du på **ny** under rubriken anslutning. När du tillfrågas om vilken typ av anslutning, Välj **Adobe Flash**.

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster4.png)
4. Klicka på **OK**.
5. En profil för FMLE kan nu importeras genom att klicka på listrutepilen under **strömning profil** och navigera till **Bläddra**.

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster5.png)
6. Gå till där konfigurerade FMLE profilen sparades.
7. Markera den och tryck på **OK**.

    När profilen har överförts, fortsätter du till nästa steg.
8. Hämta kanalen input URL: en för att tilldela Tricaster **RTMP Endpoint**.

    Gå tillbaka till verktyget AMSE och kontrollera status för slutförande kanal. När läget har ändrats från **Start** till **kör**, du kan hämta indata-URL.

    När kanalen körs, högerklickar du på dess namn, navigera till hovra över **kopiera indata-URL till Urklipp** och välj sedan **primära indata-URL**.  

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster6.png)
9. Klistra in informationen i den **plats** fältet under **Flash Server** inom Tricaster-projekt. Tilldela ett stream-namn i den **dataström-ID** fältet.

    Om strömmen information har lagts till FMLE profilen också importeras till det här avsnittet genom att klicka på **importinställningarna**, navigera till den sparade FMLE profilen och klickar på **OK**. Fälten Flash Server bör fyllas i automatiskt med information från FMLE.

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster7.png)
10. När du är klar klickar du på **OK** längst ned på skärmen. När video och ljud indata till Tricaster är klar börjar strömning till AMS genom att klicka på den **dataströmmen** knappen.

     ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster11.png)

> [!IMPORTANT]
> Innan du klickar på **dataströmmen**, du **måste** se till att kanalen är klar.
> Kontrollera också att kanalen klar utan ett inkommande bidrag feed för > 15 minuter.
>
>

## <a name="test-playback"></a>Testa uppspelning
Navigera till verktyget AMSE och högerklicka på kanalen som ska testas. Från menyn hovra över **uppspelning förhandsgranskningen** och välj **med Azure Media Player**.  

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster8.png)

Om strömmen visas i media player, har sedan kodaren konfigurerats korrekt för att ansluta till AMS.

Om ett fel tas emot kanalen kommer att behöva återställas och anpassa inställningar för kodare. Finns det [felsökning](media-services-troubleshooting-live-streaming.md) avsnittet som vägledning.  

## <a name="create-a-program"></a>Skapa ett program
1. När kanalen uppspelning bekräftas, skapa ett program. Under den **Live** i verktyget AMSE, högerklicka i området för programmet och välj **Skapa nytt Program**.  

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster9.png)
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

## <a name="next-step"></a>Nästa steg
Granska sökvägarna för Media Services-utbildning.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
