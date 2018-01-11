---
title: "Konfigurera Telestream Wirecast-kodaren om du vill skicka en direktsänd dataström med enkel bithastighet | Microsoft Docs"
description: "Det här avsnittet visar hur du konfigurerar Wirecast livekodaren för att skicka en dataström med enkel bithastighet till AMS-kanaler som är aktiverade för live encoding. "
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 0d2f1e81-51a6-4ca9-894a-6dfa51ce4c70
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 01/05/2017
ms.author: juliako;cenkdin;anilmur
ms.openlocfilehash: 0e4fb0b7c915969da1760eaccc77aa399030752e
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2018
---
# <a name="use-the-wirecast-encoder-to-send-a-single-bitrate-live-stream"></a>Använda Wirecast-kodaren för att skicka en direktsänd dataström med enkel bithastighet
> [!div class="op_single_selector"]
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
> * [Elemental Live](media-services-configure-elemental-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [FMLE](media-services-configure-fmle-live-encoder.md)
>
>

Den här artikeln visar hur du konfigurerar den [Telestream Wirecast](http://www.telestream.net/wirecast/overview.htm) livekodare för att skicka en dataström med enkel bithastighet till AMS kanaler som är aktiverade för live encoding.  Mer information finns i [Arbeta med kanaler som är aktiverade för att utföra Live Encoding med Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

Den här kursen visar hur du hanterar Azure Media Services (AMS) med Azure Media Services Explorer (AMSE)-verktyget. Det här verktyget körs bara på Windows-dator. Om du är på Mac- eller Linux använder Azure-portalen för att skapa [kanaler](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) och [program](media-services-portal-creating-live-encoder-enabled-channel.md).

## <a name="prerequisites"></a>Förutsättningar
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

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast1.png)

2. Ange ett Kanalnamn beskrivningsfältet är valfritt. Markera under inställningar för kanal **Standard** för alternativet Live Encoding med protokollet indata som angetts till **RTMP**. Du kan lämna alla andra inställningar som är.

    Kontrollera att den **starta den nya kanalen nu** är markerad.

3. Klicka på **skapa kanal**.

   ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast2.png)

> [!NOTE]
> Kanalen kan ta upp till 20 minuter för att starta.
>
>

Medan startar kanalen kan [konfigurera kodaren](media-services-configure-wirecast-live-encoder.md#configure_wirecast_rtmp).

> [!IMPORTANT]
> Faktureringen påbörjas så snart kanal blir klar att användas. Mer information finns i [kanalens tillstånd](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a id=configure_wirecast_rtmp></a>Konfigurera Telestream Wirecast-kodaren
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
1. Öppna Telestream Wirecast-programmet på datorn som används och konfigurera för RTMP strömning.
2. Konfigurera utdata genom att navigera till den **utdata** fliken och markera **utdata inställningar...** .

    Kontrollera att den **utdata mål** är inställd på **RTMP-Server**.
3. Klicka på **OK**.
4. På sidan Ange den **mål** fältet ska vara **Azure Media Services**.

    Kodning profilen är förvald till **Azure H.264 720 p 16:9 (minst 1 280 x 720)**. Om du vill anpassa dessa inställningar väljer du växeln ikonen till höger om i listrutan och väljer sedan **nya förinställningen**.

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast3.png)
5. Konfigurera kodare förinställningar.

    Namn för förinställningen och Sök efter följande rekommenderade inställningar:

    **Video**

   * Kodaren: MainConcept H.264
   * Bildrutor per sekund: 30
   * Genomsnittlig bithastighet: 5000 kbits per sekund (kan justeras utifrån nätverksbegränsningar)
   * Profil: Main
   * Nyckelbilden var: 60 ramar

    **Ljud**

   * Mål bithastighet: 192 kbits per sekund
   * Samplingsfrekvens: 44 100 kHz

     ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast4.png)
6. Tryck på **spara**.

    Fältet avkodning har nu den nya profilen kan väljas.

    Kontrollera att den nya profilen är markerad.
7. Hämta kanalen input URL: en för att tilldela Wirecast **RTMP Endpoint**.

    Gå tillbaka till verktyget AMSE och kontrollera status för slutförande kanal. När läget har ändrats från **Start** till **kör**, du kan hämta indata-URL.

    När kanalen körs, högerklicka på dess namn, navigera till hovra över **kopiera indata-URL till Urklipp** och välj sedan **primära indata-URL**.  

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast6.png)
8. I Wirecast **inställningar för utdata** och klistra in den här informationen i den **adress** i utdata och tilldela ett namn på dataströmmen.

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast5.png)

1. Välj **OK**.
2. På primära **Wirecast** skärmen, bekräfta indatakällor för video och ljud är klar och tryck sedan på **dataströmmen** i det övre vänstra hörnet.

   ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast7.png)

> [!IMPORTANT]
> Innan du klickar på **dataströmmen**, du **måste** se till att kanalen är klar.
> Kontrollera också att kanalen klar utan ett inkommande bidrag feed för > 15 minuter.
>
>

## <a name="test-playback"></a>Testa uppspelning

Navigera till verktyget AMSE och högerklicka på kanalen som ska testas. Från menyn hovra över **uppspelning förhandsgranskningen** och välj **med Azure Media Player**.  

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast8.png)

Om strömmen visas i media player, har sedan kodaren konfigurerats korrekt för att ansluta till AMS.

Om ett fel tas emot kanalen måste återställas och anpassa inställningar för kodare. Finns det [felsökning](media-services-troubleshooting-live-streaming.md) artikeln som vägledning.  

## <a name="create-a-program"></a>Skapa ett program
1. När kanalen uppspelning bekräftas, skapa ett program. Under den **Live** i verktyget AMSE, högerklickar du i området för programmet och välj **Skapa nytt Program**.  

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast9.png)
2. Namnge programmet och, om det behövs, justera det **längd** (som standard fyra timmar). Du kan också ange en lagringsplats eller lämna som standard.  
3. Kontrollera den **starta programmet nu** rutan.
4. Klicka på **skapa Program**.  

   >[!NOTE]
   >Skapa en program tar mindre tid än att skapa en kanal.
       
5. När programmet körs kan bekräfta uppspelning genom att högerklicka på programmet och navigera till **uppspelning av program** och sedan välja **med Azure Media Player**.  
6. När bekräftat, högerklicka på programmet igen och välj **kopiera den URL som utdata till Urklipp** (eller hämta den här informationen från den **programmet information och inställningar** alternativ på menyn).

Dataströmmen är nu redo att vara inbäddat i ett player eller distribuerats till en målgrupp för live visning.  

## <a name="troubleshooting"></a>Felsökning
Finns det [felsökning](media-services-troubleshooting-live-streaming.md) artikeln som vägledning.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
