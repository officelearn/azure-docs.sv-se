---
title: Konfigurera FMLE-kodaren för att skicka en enskild bit hastighet i real tid | Microsoft Docs
description: Det här avsnittet visar hur du konfigurerar FMLE-kodaren (Flash Media Live Encoder) att skicka en enskild bit hastighet till AMS kanaler som är aktiverade för direktsänd kodning.
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
ms.author: juliako
ms.reviewer: cenkdin;anilmur
ms.openlocfilehash: 09d9bdffefe9204e9f58b8f07af5b21228269f6c
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/15/2019
ms.locfileid: "69016759"
---
# <a name="use-the-fmle-encoder-to-send-a-single-bitrate-live-stream"></a>Använda FMLE-kodaren för att skicka en enda bit hastighet i real tid 
> [!div class="op_single_selector"]
> * [FMLE](media-services-configure-fmle-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
>
>

Den här artikeln visar hur du konfigurerar FMLE-kodaren ( [Flash Media Live Encoder](https://www.adobe.com/products/flash-media-encoder.html) ) för att skicka en data ström med en bit hastighet till AMS kanaler som är aktiverade för direktsänd kodning. Mer information finns i [Arbeta med kanaler som är aktiverade för att utföra Live Encoding med Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

Den här självstudien visar hur du hanterar Azure Media Services (AMS) med Azure Media Services Explorer (AMSE)-verktyget. Det här verktyget körs bara på Windows-dator. Om du använder Mac- eller Linux kan du använda Azure-portalen för att skapa [kanaler](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) och [program](media-services-portal-creating-live-encoder-enabled-channel.md).

I den här självstudien beskrivs användning av AAC. FMLE stöder dock inte AAC som standard. Du måste köpa ett plugin-program för AAC-kodning, t. ex. från MainConcept: [AAC-plugin](https://www.mainconcept.com/products/plug-ins/plug-ins-for-adobe/aac-encoder-fmle.html)

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
> Observera att faktureringen börjar så snart som kanalen försätts i ett klart tillstånd. Mer information finns i [kanalens tillstånd](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a id=configure_fmle_rtmp></a>Konfigurera FMLE-kodare
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
1. Navigera till gränssnittet för Flash Media Live Encoder (FMLE) på den dator som används.

    Gränssnittet är en huvud sida med inställningar. Anteckna följande rekommenderade inställningar för att komma igång med strömning med FMLE.

   * Format: H. 264 bild hastighet: 30,00
   * Indatatyp: 1280 x 720
   * Bit hastighet: 5000 kbit/s (kan justeras baserat på nätverks begränsningar)  

     ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle3.png)

     När du använder sammanflätade källor, markera alternativet "friflätning"
2. Om du väljer Skift nyckel ikonen bredvid format ska dessa ytterligare inställningar vara:

   * Upphandlarprofil Huvud
   * Nivå: 4.0
   * Frekvens för nyckel bild rutor: 2 sekunder

     ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle4.png)
3. Ange följande viktiga ljud inställning:

   * Format: AAC
   * Samplings frekvens: 44100 Hz
   * Hastigheten 192 kbps

     ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle5.png)
4. Hämta kanalens inmatnings-URL för att tilldela den till FMLE: s **RTMP-slutpunkt**.

    Gå tillbaka till AMSE-verktyget och kontrollera status för slutförande kanal. När tillståndet har ändrats från **startar** till **kör**, du kan hämta den angivna URL-Adressen.

    När kanalen körs, högerklicka på kanalnamnet på, navigera till hovra över **kopia indata-URL till Urklipp** och välj sedan **primära indata-URL**.  

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle6.png)
5. Klistra in den här informationen i fältet **FMS URL** i avsnittet utdata och tilldela ett data ström namn.

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle7.png)

    Upprepa dessa steg med den sekundära indata-URL: en för ytterligare redundans.
6. Välj **Anslut**.

> [!IMPORTANT]
> Innan du klickar på **Anslut** **måste** du se till att kanalen är klar.
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
2. Ge programmet ett namn och, om det behövs, ändra **Arkiv fönstrets längd** (som är standardvärdet 4 timmar). Du kan också ange en lagringsplats eller lämna som standard.  
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
