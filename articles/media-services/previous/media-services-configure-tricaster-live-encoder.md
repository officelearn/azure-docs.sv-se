---
title: Konfigurera NewTek TriCaster-kodaren för att skicka en enda bitrate live stream | Microsoft-dokument
description: Det här avsnittet visar hur du konfigurerar Tricaster live-kodaren för att skicka en enda bithastighetsström till AMS-kanaler som är aktiverade för live-kodning.
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
ms.openlocfilehash: 7909fbb958a66d00616d4ed1b844d02bb47d997e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152507"
---
# <a name="use-the-newtek-tricaster-encoder-to-send-a-single-bitrate-live-stream"></a>Använd NewTek TriCaster-kodaren för att skicka en enda bitrate live stream  
> [!div class="op_single_selector"]
> * [Tricaster (tre)](media-services-configure-tricaster-live-encoder.md)
> * [Elemental Live](media-services-configure-elemental-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
>
>

Den här artikeln visar hur du konfigurerar [NewTek TriCaster](https://newtek.com/products/tricaster-40.html) live-kodaren för att skicka en enda bithastighetsström till AMS-kanaler som är aktiverade för live-kodning. Mer information finns i [Arbeta med kanaler som är aktiverade för att utföra Live Encoding med Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

Den här självstudien visar hur du hanterar Azure Media Services (AMS) med Azure Media Services Explorer (AMSE) verktyg. Det här verktyget körs bara på Windows PC. Om du använder Mac eller Linux använder du Azure-portalen för att skapa [kanaler](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) och [program](media-services-portal-creating-live-encoder-enabled-channel.md).

När du använder Tricaster för att skicka in ett bidrag foder till AMS-kanaler som är aktiverade för live kodning, kan det finnas video / ljud buggar i din live-händelse om du använder vissa funktioner i Tricaster, till exempel snabb skärning mellan flöden, eller byta till / från skiffer. AMS-teamet arbetar med att åtgärda dessa problem, tills dess rekommenderas det inte att använda dessa funktioner.

> [!NOTE]
>  Överväg att flytta till TLS 1.2, den prefferred TLS-versionen.

## <a name="prerequisites"></a>Krav

* [Skapa ett Azure Media Services-konto](media-services-portal-create-account.md)
* Se till att en slutpunkt för direktuppspelning körs. Mer information finns [i Hantera slutpunkter för direktuppspelning i ett Media Services-konto](media-services-portal-manage-streaming-endpoints.md)
* Installera den senaste versionen av [AMSE-verktyget.](https://github.com/Azure/Azure-Media-Services-Explorer)
* Starta verktyget och anslut till ditt AMS-konto.

## <a name="tips"></a>Tips

* Använd en direktkopplad internetanslutning när det är möjligt.
* En bra tumregel för att bestämma bandbreddskrav är att dubblera sändningens bithastighet. Även om detta inte är ett obligatoriskt krav, hjälper det till att minska effekterna av överbelastning i nätverket.
* När du använder programvarubaserade kodare stänger du alla onödiga program.

## <a name="create-a-channel"></a>Skapa en kanal

1. I AMSE-verktyget navigerar du till fliken **Live** och högerklickar inom kanalområdet. Välj **Skapa kanal...** från menyn.

    ![tricaster (tre)](./media/media-services-tricaster-live-encoder/media-services-tricaster1.png)

2. Ange ett kanalnamn, beskrivningsfältet är valfritt. Under Kanalinställningar väljer du **Standard** för alternativet Direktkodning, med indataprotokollet inställt på **RTMP**. Du kan lämna alla andra inställningar som de är.

    Kontrollera att **starta den nya kanalen nu** är markerad.

3. Klicka på **Skapa kanal**.

   ![tricaster (tre)](./media/media-services-tricaster-live-encoder/media-services-tricaster2.png)

> [!NOTE]
> Kanalen kan ta upp till 20 minuter att starta.
>
>

När kanalen startar kan du [konfigurera kodaren](media-services-configure-tricaster-live-encoder.md#configure_tricaster_rtmp).

> [!IMPORTANT]
> Faktureringen startar så fort kanalen går in i ett färdigt tillstånd. Mer information finns i [Kanalens tillstånd](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a name="configure-the-newtek-tricaster-encoder"></a><a id="configure_tricaster_rtmp"/>Konfigurera NewTek TriCaster-kodaren

I den här självstudien används följande utdatainställningar. I resten av det här avsnittet beskrivs konfigurationsstegen mer i detalj.

**Video**:

* Codec: H.264
* Profil: Hög (nivå 4.0)
* Bitrate: 5000 kbps
* Nyckelbildruta: 2 sekunder (60 sekunder)
* Bildhastighet: 30

**Ljud:**

* Codec: AAC (LC)
* Bithastighet: 192 kbit/s
* Provhastighet: 44,1 kHz

### <a name="configuration-steps"></a>Konfigurationssteg

1. Skapa ett nytt **NewTek TriCaster-projekt** beroende på vilken videoingångskälla som används.
2. En gång i det projektet, leta reda på **knappen Stream** och klicka på kugghjulsikonen bredvid den för att komma åt menyn för strömkonfiguration.

    ![tricaster (tre)](./media/media-services-tricaster-live-encoder/media-services-tricaster3.png)
3. När menyn har öppnats klickar du på **Ny** under rubriken Anslutning. När du uppmanas att ange anslutningstypen väljer du **Adobe Flash**.

    ![tricaster (tre)](./media/media-services-tricaster-live-encoder/media-services-tricaster4.png)
4. Klicka på **OK**.
5. En FMLE-profil kan nu importeras genom att klicka på nedpilen under **Strömmande profil** och navigera till **Bläddra**.

    ![tricaster (tre)](./media/media-services-tricaster-live-encoder/media-services-tricaster5.png)
6. Navigera till den plats där den konfigurerade FMLE-profilen sparades.
7. Markera den och tryck på **OK**.

    När profilen har laddats upp går du vidare till nästa steg.
8. Hämta kanalens indata-URL för att tilldela den till Tricaster **RTMP-slutpunkten**.

    Navigera tillbaka till AMSE-verktyget och kontrollera kanalens slutförandestatus. När tillståndet har ändrats från **Start** till **Löpning**kan du hämta indata-URL:en.

    När kanalen körs högerklickar du på kanalnamnet, navigerar nedåt för att hovra över **Url för kopiera indata till Urklipp** och välj sedan **Url för primär inmatning**.  

    ![tricaster (tre)](./media/media-services-tricaster-live-encoder/media-services-tricaster6.png)
9. Klistra in den här informationen i fältet **Plats** under **Flash Server** i Tricaster-projektet. Tilldela också ett flödesnamn i fältet **Stream ID.**

    Om strömmande information har lagts till i FMLE-profilen kan den också importeras till det här avsnittet genom att klicka på **Importera inställningar,** navigera till den sparade FMLE-profilen och klicka på **OK**. De relevanta Flash Server-fälten ska fyllas i med informationen från FMLE.

    ![tricaster (tre)](./media/media-services-tricaster-live-encoder/media-services-tricaster7.png)
10. När du är klar klickar du på **OK** längst ned på skärmen. När video- och ljudingångarna till Tricaster är klara börjar **Stream** du strömma till AMS genom att klicka på stream-knappen.

     ![tricaster (tre)](./media/media-services-tricaster-live-encoder/media-services-tricaster11.png)

> [!IMPORTANT]
> Innan du klickar på **Stream** **måste** du se till att kanalen är klar.
> Se också till att inte lämna kanalen i ett färdigt tillstånd utan inmatningsflöde längre än > 15 minuter.
>
>

## <a name="test-playback"></a>Testa uppspelning

Navigera till AMSE-verktyget och högerklicka på den kanal som ska testas. Hovra över **Uppspelning av förhandsgranskningen** på menyn och välj **med Azure Media Player**.  

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster8.png)

Om strömmen visas i spelaren har kodaren konfigurerats korrekt för att ansluta till AMS.

Om ett fel tas emot måste kanalen återställas och kodarinställningarna justeras. Mer [information](media-services-troubleshooting-live-streaming.md) finns i felsökningsartikeln.  

## <a name="create-a-program"></a>Skapa ett program

1. När kanaluppspelningen har bekräftats skapar du ett program. Högerklicka inom programområdet under fliken **Live** i AMSE-verktyget och välj **Skapa nytt program**.  

    ![tricaster (tre)](./media/media-services-tricaster-live-encoder/media-services-tricaster9.png)
2. Namnge programmet och justera **arkivfönstrets längd** om det behövs (som standard till fyra timmar). Du kan också ange en lagringsplats eller lämna som standard.  
3. Markera rutan **Starta programmet nu.**
4. Klicka på **Skapa program**.  

    >[!NOTE]
    >Det tar kortare tid än att skapa program.
        
5. När programmet körs bekräftar du uppspelningen genom att högerklicka på programmet och navigera till **Uppspelning av programmet/programen** och sedan välja **med Azure Media Player**.  
6. När du har bekräftat det högerklickar du på programmet igen och väljer **Kopiera utdata-URL:en till Urklipp** (eller hämta den här informationen från alternativet **Programinformation och inställningar** på menyn).

Strömmen är nu redo att bäddas in i en spelare, eller distribueras till en publik för livevisning.  

## <a name="troubleshooting"></a>Felsökning

Mer [information](media-services-troubleshooting-live-streaming.md) finns i felsökningsartikeln.

## <a name="next-step"></a>Nästa steg

Granska sökvägarna för Media Services-utbildning.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
