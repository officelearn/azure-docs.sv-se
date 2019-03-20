---
title: Konfigurera NewTek TriCaster-kodare för att skicka en direktsänd dataström med enkel bithastighet | Microsoft Docs
description: Det här avsnittet visar hur du konfigurerar Tricaster livekodaren för att skicka en enda bithastighet till AMS-kanaler som är aktiverade för live encoding.
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
ms.author: juliako;cenkd;anilmur
ms.openlocfilehash: adacce5c8307f3be972920bd3aef6d8ea912eb30
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57991990"
---
# <a name="use-the-newtek-tricaster-encoder-to-send-a-single-bitrate-live-stream"></a>Använd NewTek TriCaster-kodare för att skicka en direktsänd dataström med enkel bithastighet  
> [!div class="op_single_selector"]
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Elemental Live](media-services-configure-elemental-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
> * [FMLE](media-services-configure-fmle-live-encoder.md)
>
>

Den här artikeln visar hur du konfigurerar den [NewTek TriCaster](http://newtek.com/products/tricaster-40.html) livekodare för att skicka en enda bithastighet till AMS kanaler som är aktiverade för live encoding. Mer information finns i [Arbeta med kanaler som är aktiverade för att utföra Live Encoding med Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

Den här självstudien visar hur du hanterar Azure Media Services (AMS) med Azure Media Services Explorer (AMSE)-verktyget. Det här verktyget körs bara på Windows-dator. Om du använder Mac- eller Linux kan du använda Azure-portalen för att skapa [kanaler](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) och [program](media-services-portal-creating-live-encoder-enabled-channel.md).

> [!NOTE]
> När du använder Tricaster för att skicka i ett bidrag till AMS-kanaler som är aktiverade för live encoding, kan det finnas problem med ljud och i live-händelsen om du använder vissa funktioner i Tricaster, till exempel snabb cutting mellan feeds eller växla till/från pekdatorer. AMS-teamet arbetar på att åtgärda problemen, fram till dess, rekommenderas inte att använda dessa funktioner.
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

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster1.png)

2. Ange ett Kanalnamn beskrivningsfältet är valfritt. Välj under inställningar för kanalen **Standard** för alternativet Live Encoding med indata-protokollet som är inställd på **RTMP**. Du kan lämna alla andra inställningar som är.

    Kontrollera att den **starta den nya kanalen nu** har valts.

3. Klicka på **skapa kanal**.

   ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster2.png)

> [!NOTE]
> Kanalen kan ta upp till 20 minuter att starta.
>
>

Medan kanalen startar kan du [konfigurera kodaren](media-services-configure-tricaster-live-encoder.md#configure_tricaster_rtmp).

> [!IMPORTANT]
> Debiteringen börjar när kanalen hamnar i tillståndet redo. Mer information finns i [kanalens tillstånd](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a name="a-idconfiguretricasterrtmpconfigure-the-newtek-tricaster-encoder"></a><a id="configure_tricaster_rtmp"/>Konfigurera NewTek TriCaster-kodare

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

1. Skapa en ny **NewTek TriCaster** projekt beroende på vilka inkommande videokälla som används.
2. En gång i projektet, hitta den **Stream** knappen och klicka på kugghjulsikonen bredvid komma åt menyn stream konfiguration.

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster3.png)
3. När du har öppnat menyn, klickar du på **New** under rubriken anslutning. När du tillfrågas om vilken typ av anslutning, välja **Adobe Flash**.

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster4.png)
4. Klicka på **OK**.
5. FMLE-profil kan nu importeras genom att klicka på nedrullningsbara pilen under **Streaming profil** och navigera till **Bläddra**.

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster5.png)
6. Gå till där den konfigurera FMLE profilen sparades.
7. Markera den och tryck på **OK**.

    När profilen har överförts, fortsätter du till nästa steg.
8. Hämta kanalen användarens indata URL: en för att tilldela den till Tricaster **RTMP Endpoint**.

    Gå tillbaka till AMSE-verktyget och kontrollera status för slutförande kanal. När tillståndet har ändrats från **startar** till **kör**, du kan hämta den angivna URL-Adressen.

    När kanalen körs, högerklickar du på dess namn, navigera till hovra över **kopia indata-URL till Urklipp** och välj sedan **primära indata-URL**.  

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster6.png)
9. Klistra in den här informationen i den **plats** fältet under **Flash Server** inom Tricaster-projektet. Också tilldela en stream-namn i den **Stream ID** fält.

    Om stream information har lagts till FMLE profilen, det kan också importeras till det här avsnittet genom att klicka på **importinställningar**, navigera till den sparade FMLE-profilen och klickar på **OK**. Fälten Flash Server bör fyllas i automatiskt med information från FMLE.

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster7.png)
10. När du är klar klickar du på **OK** längst ned på skärmen. När video och ljud indata i Tricaster är redo kan du påbörja direktuppspelning till AMS genom att klicka på den **Stream** knappen.

     ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster11.png)

> [!IMPORTANT]
> Innan du klickar på **Stream**, du **måste** se till att kanalen är redo.
> Se dessutom till att du inte lämnar kanalen i tillståndet redo utan ett inkommande bidrag feed > 15 minuter.
>
>

## <a name="test-playback"></a>Testa uppspelning

Gå till AMSE-verktyget och högerklicka på kanalen som ska testas. Från menyn, hovrar du över **uppspelning förhandsversionen** och välj **med Azure Media Player**.  

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster8.png)

Om strömmen visas i spelaren, har sedan kodaren konfigurerats korrekt för att ansluta till AMS.

Om ett fel tas emot, kanalen kommer att behöva återställas och anpassa inställningar för kodare. Se den [felsökning](media-services-troubleshooting-live-streaming.md) artikeln vägledning.  

## <a name="create-a-program"></a>Skapa ett program

1. När kanalen uppspelning har bekräftats kan du skapa ett program. Under den **Live** fliken AMSE-verktyget, högerklicka i området för programmet och välj **Skapa nytt Program**.  

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster9.png)
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
