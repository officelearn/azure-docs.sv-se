---
title: Konfigurera Telestream Wirecast-kodare för att skicka en direktsänd dataström med enkel bithastighet | Microsoft Docs
description: 'Det här avsnittet visar hur du konfigurerar Wirecast livekodaren för att skicka en enda bithastighet till AMS-kanaler som är aktiverade för live encoding. '
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 0d2f1e81-51a6-4ca9-894a-6dfa51ce4c70
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako;cenkdin;anilmur
ms.openlocfilehash: d0da69601bfc6fd09c10b30d45195722781d87d6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61232140"
---
# <a name="use-the-wirecast-encoder-to-send-a-single-bitrate-live-stream"></a>Använd Wirecast-kodare för att skicka en direktsänd dataström med enkel bithastighet 
> [!div class="op_single_selector"]
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [FMLE](media-services-configure-fmle-live-encoder.md)
>
>

Den här artikeln visar hur du konfigurerar den [Telestream Wirecast](https://www.telestream.net/wirecast/overview.htm) livekodare för att skicka en enda bithastighet till AMS kanaler som är aktiverade för live encoding.  Mer information finns i [Arbeta med kanaler som är aktiverade för att utföra Live Encoding med Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

Den här självstudien visar hur du hanterar Azure Media Services (AMS) med Azure Media Services Explorer (AMSE)-verktyget. Det här verktyget körs bara på Windows-dator. Om du använder Mac- eller Linux kan du använda Azure-portalen för att skapa [kanaler](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) och [program](media-services-portal-creating-live-encoder-enabled-channel.md).

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

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast1.png)

2. Ange ett Kanalnamn beskrivningsfältet är valfritt. Välj under inställningar för kanalen **Standard** för alternativet Live Encoding med indata-protokollet som är inställd på **RTMP**. Du kan lämna alla andra inställningar som är.

    Kontrollera att den **starta den nya kanalen nu** har valts.

3. Klicka på **skapa kanal**.

   ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast2.png)

> [!NOTE]
> Kanalen kan ta upp till 20 minuter att starta.
>
>

Medan kanalen startar kan du [konfigurera kodaren](media-services-configure-wirecast-live-encoder.md#configure_wirecast_rtmp).

> [!IMPORTANT]
> Debiteringen börjar när kanalen hamnar i tillståndet redo. Mer information finns i [kanalens tillstånd](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a name="a-idconfigurewirecastrtmp-configure-the-telestream-wirecast-encoder"></a><a id="configure_wirecast_rtmp" />Konfigurera Telestream Wirecast-kodare
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
1. Öppna Telestream Wirecast-programmet på datorn som används och konfigureras för RTMP strömning.
2. Konfigurera utdata genom att navigera till den **utdata** och markera **utdatainställningar...** .

    Kontrollera att den **utdata mål** är inställd på **RTMP-Server**.
3. Klicka på **OK**.
4. Ange på inställningssidan i **mål** fältet ska vara **Azure Media Services**.

    Encoding-profil är förvalda till **Azure H.264 720 p 16:9 (1 280 x 720)**. Om du vill anpassa dessa inställningar väljer du kugghjulsikonen till höger om i listrutan och välj sedan **nya förinställda**.

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast3.png)
5. Konfigurera kodare förinställningar.

    Namnge förinställningen och Sök efter följande rekommenderade inställningar:

    **Video**

   * Kodaren: MainConcept H.264
   * Bildrutor per sekund: 30
   * Genomsnittlig bithastighet: 5000 kbits/sek (kan justeras utifrån nätverksbegränsningar)
   * Profil: Huvud
   * Viktiga ramens varje: 60 bildrutor

     **Ljud**

   * Bithastighet för mål: 192 kbits/sek
   * Samplingshastighet: 44.100 kHz

     ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast4.png)
6. Tryck på **Spara**.

    Fältet avkodning har nu den nyligen skapade profilen väljas.

    Kontrollera att den nya profilen har valts.
7. Hämta kanalen användarens indata URL: en för att tilldela den till Wirecast **RTMP Endpoint**.

    Gå tillbaka till AMSE-verktyget och kontrollera status för slutförande kanal. När tillståndet har ändrats från **startar** till **kör**, du kan hämta den angivna URL-Adressen.

    När kanalen körs, högerklicka på kanalnamnet på, navigera till hovra över **kopia indata-URL till Urklipp** och välj sedan **primära indata-URL**.  

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast6.png)
8. I Wirecast **utdatainställningar** och klistra in den här informationen i den **adress** i outputs-avsnittet och tilldela ett namn för stream.

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast5.png)

1. Välj **OK**.
2. På huvudsidan **Wirecast** skärmen, bekräfta indatakällor för video och ljud är klar och tryck sedan på **Stream** i det övre vänstra hörnet.

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast7.png)

> [!IMPORTANT]
> Innan du klickar på **Stream**, du **måste** se till att kanalen är redo.
> Se dessutom till att du inte lämnar kanalen i tillståndet redo utan ett inkommande bidrag feed > 15 minuter.
>
>

## <a name="test-playback"></a>Testa uppspelning

Gå till AMSE-verktyget och högerklicka på kanalen som ska testas. Från menyn, hovrar du över **uppspelning förhandsversionen** och välj **med Azure Media Player**.  

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast8.png)

Om strömmen visas i spelaren, har sedan kodaren konfigurerats korrekt för att ansluta till AMS.

Om ett fel tas emot kanalen måste återställas och anpassa inställningar för kodare. Se den [felsökning](media-services-troubleshooting-live-streaming.md) artikeln vägledning.  

## <a name="create-a-program"></a>Skapa ett program
1. När kanalen uppspelning har bekräftats kan du skapa ett program. Under den **Live** fliken AMSE-verktyget, högerklicka i området för programmet och välj **Skapa nytt Program**.  

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast9.png)
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

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
