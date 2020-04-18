---
title: Konfigurera Telestream Wirecast-kodaren för att skicka en enda bitrate live stream | Microsoft-dokument
description: 'Det här avsnittet visar hur du konfigurerar Wirecast live-kodaren för att skicka en enda bithastighetsström till AMS-kanaler som är aktiverade för live-kodning. '
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
ms.author: juliako
ms.reviewer: cenkdin;anilmur
ms.openlocfilehash: 8e3705aaecb0760513f0605aece89b7ffc0044a8
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641652"
---
# <a name="use-the-wirecast-encoder-to-send-a-single-bitrate-live-stream"></a>Använd Wirecast-kodaren för att skicka en enda bitrate live stream 
> [!div class="op_single_selector"]
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
>

Den här artikeln visar hur du konfigurerar [Telestream Wirecast](https://www.telestream.net/wirecast/overview.htm) live-kodaren för att skicka en enda bithastighetsström till AMS-kanaler som är aktiverade för live-kodning. Mer information finns i [Arbeta med kanaler som är aktiverade för att utföra Live Encoding med Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

Den här självstudien visar hur du hanterar Azure Media Services (AMS) med Azure Media Services Explorer (AMSE) verktyg. Det här verktyget körs bara på Windows PC. Om du använder Mac eller Linux använder du Azure-portalen för att skapa [kanaler](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) och [program](media-services-portal-creating-live-encoder-enabled-channel.md).

> [!NOTE]
> Kodare måste ha stöd för TLS 1.2 när RTMPS-protokoll används. Använd Wirecast version 13.0.2 eller senare på grund av TLS 1.2-kravet.

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

    ![wirecast (wirecast)](./media/media-services-wirecast-live-encoder/media-services-wirecast1.png)

2. Ange ett kanalnamn, beskrivningsfältet är valfritt. Under Kanalinställningar väljer du **Standard** för alternativet Direktkodning, med indataprotokollet inställt på **RTMP**. Du kan lämna alla andra inställningar som de är.

    Kontrollera att **starta den nya kanalen nu** är markerad.

3. Klicka på **Skapa kanal**.

   ![wirecast (wirecast)](./media/media-services-wirecast-live-encoder/media-services-wirecast2.png)

> [!NOTE]
> Kanalen kan ta upp till 20 minuter att starta.
>
>

När kanalen startar kan du [konfigurera kodaren](media-services-configure-wirecast-live-encoder.md#configure_wirecast_rtmp).

> [!IMPORTANT]
> Faktureringen startar så fort kanalen går in i ett färdigt tillstånd. Mer information finns i [Kanalens tillstånd](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a name="configure-the-telestream-wirecast-encoder"></a><a id="configure_wirecast_rtmp" />Konfigurera Telestream Wirecast-kodaren
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
1. Öppna Telestream Wirecast-programmet på den maskin som används och konfigurera för RTMP-strömning.
2. Konfigurera utdata genom att navigera till fliken **Utdata** och välja **Utdatainställningar...**.

    Kontrollera att **utdatamålet** är inställt på **RTMP Server**.
3. Klicka på **OK**.
4. På inställningssidan anger du fältet **Mål** som **Azure Media Services**.

    Kodningsprofilen är förvald i **Azure H.264 720p 16:9 (1280x720)**. Om du vill anpassa dessa inställningar väljer du kugghjulsikonen till höger om listrutan och väljer sedan **Ny förinställning**.

    ![wirecast (wirecast)](./media/media-services-wirecast-live-encoder/media-services-wirecast3.png)
5. Konfigurera kodarförinställningar.

    Namnge förinställningen och kontrollera följande rekommenderade inställningar:

    **Video**

   * Kodare: MainConcept H.264
   * Bilder per sekund: 30
   * Genomsnittlig bithastighet: 5000 kbits/sek (Kan justeras baserat på nätverksbegränsningar)
   * Profil: Main
   * Nyckelbildruta var: 60 bildrutor

     **Ljud**

   * Målbithastighet: 192 kbits/sek
   * Provhastighet: 44.100 kHz

     ![wirecast (wirecast)](./media/media-services-wirecast-live-encoder/media-services-wirecast4.png)
6. Tryck på **Spara**.

    Kodningsfältet har nu den nyskapade profilen tillgänglig för val.

    Kontrollera att den nya profilen är markerad.
7. Hämta kanalens indata-URL för att tilldela den till Wirecast **RTMP-slutpunkten**.

    Navigera tillbaka till AMSE-verktyget och kontrollera kanalens slutförandestatus. När tillståndet har ändrats från **Start** till **Löpning**kan du hämta indata-URL:en.

    När kanalen körs högerklickar du på kanalnamnet, navigerar nedåt för att hovra över **Url för kopiera indata till Urklipp** och välj sedan **Url för primär inmatning**.  

    ![wirecast (wirecast)](./media/media-services-wirecast-live-encoder/media-services-wirecast6.png)
8. I fönstret **Wirecast-utdatainställningar** klistrar du in den här informationen i **fältet Adress** i utdataavsnittet och tilldelar ett flödesnamn.

    ![wirecast (wirecast)](./media/media-services-wirecast-live-encoder/media-services-wirecast5.png)

1. Välj **OK**.
2. På den huvudsakliga **Wirecast-skärmen** bekräftar du att inmatningskällorna för video och ljud är klara och trycker sedan **på Stream** i det övre vänstra hörnet.

    ![wirecast (wirecast)](./media/media-services-wirecast-live-encoder/media-services-wirecast7.png)

> [!IMPORTANT]
> Innan du klickar på **Stream** **måste** du se till att kanalen är klar.
> Se också till att inte lämna kanalen i ett färdigt tillstånd utan inmatningsflöde längre än > 15 minuter.
>
>

## <a name="test-playback"></a>Testa uppspelning

Navigera till AMSE-verktyget och högerklicka på den kanal som ska testas. Hovra över **Uppspelning av förhandsgranskningen** på menyn och välj **med Azure Media Player**.  

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast8.png)

Om strömmen visas i spelaren har kodaren konfigurerats korrekt för att ansluta till AMS.

Om ett fel tas emot måste kanalen återställas och kodarinställningarna justeras. Mer [information](media-services-troubleshooting-live-streaming.md) finns i felsökningsartikeln.  

## <a name="create-a-program"></a>Skapa ett program
1. När kanaluppspelningen har bekräftats skapar du ett program. Högerklicka inom programområdet under fliken **Live** i AMSE-verktyget och välj **Skapa nytt program**.  

    ![wirecast (wirecast)](./media/media-services-wirecast-live-encoder/media-services-wirecast9.png)
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

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
