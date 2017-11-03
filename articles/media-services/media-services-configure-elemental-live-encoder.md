---
title: "Konfigurera grundämne Live-kodaren om du vill skicka en direktsänd dataström med enkel bithastighet | Microsoft Docs"
description: "Det här avsnittet visar hur du konfigurerar grundämne Live-kodaren för att skicka en dataström med enkel bithastighet till AMS-kanaler som är aktiverade för live encoding."
services: media-services
documentationcenter: 
author: cenkdin
manager: cfowler
editor: 
ms.assetid: 9c6bf6a9-6273-4fdd-9477-f0e565280b5b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 01/05/2017
ms.author: cenkd;anilmur;juliako
ms.openlocfilehash: 668a3ab46a70c0ee25fa87031d27c0f4333ec89c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-elemental-live-encoder-to-send-a-single-bitrate-live-stream"></a>Använda kodaren grundämne Live för att skicka en direktsänd dataström med enkel bithastighet
> [!div class="op_single_selector"]
> * [Elemental Live](media-services-configure-elemental-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
> * [FMLE](media-services-configure-fmle-live-encoder.md)
>
>

Det här avsnittet visar hur du konfigurerar den [grundämne Live](http://www.elementaltechnologies.com/products/elemental-live) att skicka en dataström med enkel bithastighet till AMS kanaler som är aktiverade för live encoding.  Mer information finns i [Arbeta med kanaler som är aktiverade för att utföra Live Encoding med Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

Den här kursen visar hur du hanterar Azure Media Services (AMS) med Azure Media Services Explorer (AMSE)-verktyget. Det här verktyget körs bara på Windows-dator. Om du är på Mac- eller Linux använder Azure-portalen för att skapa [kanaler](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) och [program](media-services-portal-creating-live-encoder-enabled-channel.md).

## <a name="prerequisites"></a>Krav
* Måste ha kunskaper om med grundämne Live Webbgränssnitt för att skapa direktsända händelser.
* [Skapa ett Azure Media Services-konto](media-services-portal-create-account.md)
* Se till att det finns en Strömningsslutpunkt som körs. Mer information finns i [hanterar Strömningsslutpunkter i ett Media Services-konto](media-services-portal-manage-streaming-endpoints.md).
* Installera den senaste versionen av den [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) verktyget.
* Starta verktyget och Anslut till AMS-kontot.

## <a name="tips"></a>Tips
* När det är möjligt använda ett inbyggt internet-anslutning.
* En bra tumregel samband med fastställandet av krav på bandbredd är att dubbla strömmande bithastighet. Även om detta inte är en nödvändighet hjälper minimera effekten av överbelastning.
* När du använder programvara baserat kodare, Stäng alla onödiga program.

## <a name="elemental-live-with-rtp-ingest"></a>Mata in grundämne Live med RTP
Det här avsnittet visar hur du konfigurerar grundämne Live-kodaren som skickar en direktsänd dataström med enkel bithastighet över RTP.  Mer information finns i [MPEG-TS dataströmmen över RTP](media-services-manage-live-encoder-enabled-channels.md#channel).

### <a name="create-a-channel"></a>Skapa en kanal

1. I verktyget AMSE navigerar du till den **Live** fliken och högerklicka i området för kanal. Välj **skapa kanal...** från menyn.

    ![Grundämne](./media/media-services-elemental-live-encoder/media-services-elemental1.png)

2. Ange ett Kanalnamn beskrivningsfältet är valfritt. Markera under inställningar för kanal **Standard** för alternativet Live Encoding med protokollet indata som angetts till **RTP (MPEG-TS)**. Du kan lämna alla andra inställningar som är.

    Kontrollera att den **starta den nya kanalen nu** är markerad.

3. Klicka på **skapa kanal**.

   ![Grundämne](./media/media-services-elemental-live-encoder/media-services-elemental12.png)

> [!NOTE]
> Kanalen kan ta upp till 20 minuter för att starta.
>
>

När kanalen startar kan du [konfigurera kodaren](media-services-configure-elemental-live-encoder.md#configure_elemental_rtp).

> [!IMPORTANT]
> Observera att faktureringen påbörjas så snart kanal blir klar att användas. Mer information finns i [kanalens tillstånd](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

### <a id=configure_elemental_rtp></a>Konfigurera grundämne Live-kodaren
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

#### <a name="configuration-steps"></a>Konfigurationssteg
1. Navigera till den **grundämne Live** webbgränssnittet och ställa in kodaren för **UDP/TS** strömning.
2. När du har skapat en ny händelse rulla utdata-grupper och lägga till den **UDP/TS** utdata grupp.
3. Skapa en ny utdata genom att välja **ny ström** och sedan klicka på **lägga till utdata**.  

    ![Grundämne](./media/media-services-elemental-live-encoder/media-services-elemental13.png)

   > [!NOTE]
   > Vi rekommenderar att händelsen grundämne har Tidskoden angetts till ”systemklockan” för att kodaren återansluta vid misslyckanden dataströmmen.
   >
   >
4. Nu när utdata har skapats, klicka på **lägga till ett flöde**. Inställningar för utdata kan nu konfigureras.
5. Rulla ”dataströmmen 1” just har skapat, klicka på den **Video** fliken till vänster och expandera den **Avancerat** inställningar.

    ![Grundämne](./media/media-services-elemental-live-encoder/media-services-elemental4.png)

    Medan grundämne Live har ett stort antal tillgängliga anpassa, rekommenderas följande inställningar för att komma igång med strömning till AMS.

   * Lösning: minst 1 280 x 720
   * Ramhastighet: 30
   * GOP storlek: 60 ramar
   * Sammanfläta läge: progressiv
   * Bithastighet: 5000000 bit/s (Detta kan ställas in baserat på begränsningar)

    ![Grundämne](./media/media-services-elemental-live-encoder/media-services-elemental5.png)

1. Hämta kanalens indata-URL.

    Gå tillbaka till verktyget AMSE och kontrollera status för slutförande kanal. När läget har ändrats från **Start** till **kör**, du kan hämta indata-URL.

    När kanalen körs, högerklickar du på dess namn, navigera till hovra över **kopiera indata-URL till Urklipp** och välj sedan **primära indata-URL**.  

    ![Grundämne](./media/media-services-elemental-live-encoder/media-services-elemental6.png)
2. Klistra in informationen i den **primära mål** i Elemental. Alla andra inställningar kan förbli standardvärdet.

    ![Grundämne](./media/media-services-elemental-live-encoder/media-services-elemental14.png)

    Upprepa dessa steg med sekundära indata-URL genom att skapa en separat flik ”utdata” för direktuppspelning av UDP/TS för extra redundans.
3. Klicka på **skapa** (om en ny händelse skapades) eller **uppdatering** (om du redigerar en befintlig händelse) och fortsätt sedan att starta kodaren.

> [!IMPORTANT]
> Innan du klickar på **starta** på webbgränssnitt grundämne Live du **måste** se till att kanalen är klar.
> Kontrollera också att kanalen med tillståndet klart utan en händelse för > 15 minuter.
>
>

När dataströmmen har körts i 30 sekunder, gå tillbaka till AMSE-verktyget och testa uppspelning.  

### <a name="test-playback"></a>Testa uppspelning

Navigera till verktyget AMSE och högerklicka på kanalen som ska testas. Från menyn hovra över **uppspelning förhandsgranskningen** och välj **med Azure Media Player**.  

    ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental8.png)

Om strömmen visas i media player, har sedan kodaren konfigurerats korrekt för att ansluta till AMS.

Om ett fel tas emot kanalen kommer att behöva återställas och anpassa inställningar för kodare. Finns det [felsökning](media-services-troubleshooting-live-streaming.md) avsnittet som vägledning.   

### <a name="create-a-program"></a>Skapa ett program
1. När kanalen uppspelning bekräftas, skapa ett program. Under den **Live** i verktyget AMSE, högerklicka i området för programmet och välj **Skapa nytt Program**.  

    ![Grundämne](./media/media-services-elemental-live-encoder/media-services-elemental9.png)
2. Namnge programmet och, om det behövs, justera det **längd** (som standard 4 timmar). Du kan också ange en lagringsplats eller lämna som standard.  
3. Kontrollera den **starta programmet nu** rutan.
4. Klicka på **skapa Program**.  

    >[!NOTE]
    > Skapa en program tar mindre tid än att skapa en kanal.   
      
5. När programmet körs kan bekräfta uppspelning genom att högerklicka på programmet och gå till **uppspelning av program** och sedan välja **med Azure Media Player**.  
6. När bekräftat, högerklicka på programmet och välj **kopiera den URL som utdata till Urklipp** (eller hämta den här informationen från den **programmet information och inställningar** alternativ på menyn).

Dataströmmen är nu redo att vara inbäddat i ett player eller distribuerats till en målgrupp för live visning.  

## <a name="troubleshooting"></a>Felsökning
Finns det [felsökning](media-services-troubleshooting-live-streaming.md) avsnittet som vägledning.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
