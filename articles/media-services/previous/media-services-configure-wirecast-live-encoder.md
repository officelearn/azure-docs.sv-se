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
ms.author: juliako
ms.reviewer: cenkdin;anilmur
ms.openlocfilehash: 1d9d63aa6b3da1b8d8389722bd5af0eeed585d03
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2020
ms.locfileid: "77134979"
---
# <a name="use-the-wirecast-encoder-to-send-a-single-bitrate-live-stream"></a>Använd Wirecast-kodare för att skicka en direktsänd dataström med enkel bithastighet 
> [!div class="op_single_selector"]
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
> * [TriCaster](media-services-configure-tricaster-live-encoder.md)
>
>

Den här artikeln visar hur du konfigurerar [NetStream Wirecast](https://www.telestream.net/wirecast/overview.htm) Live Encoder för att skicka en enda bit ström till AMS kanaler som är aktiverade för direktsänd kodning. Mer information finns i [Arbeta med kanaler som är aktiverade för att utföra Live Encoding med Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

Den här självstudien visar hur du hanterar Azure Media Services (AMS) med Azure Media Services Explorer (AMSE)-verktyget. Det här verktyget körs bara på Windows-dator. Om du använder Mac eller Linux använder du Azure Portal för att skapa [kanaler](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) och [program](media-services-portal-creating-live-encoder-enabled-channel.md).

> [!NOTE]
> Kodare måste ha stöd för TLS 1,2 när du använder RTMP-protokoll. Använd Wirecast version 13.0.2 eller högre på grund av kraven för TLS 1,2.

## <a name="prerequisites"></a>Förutsättningar
* [Skapa ett Azure Media Services konto](media-services-portal-create-account.md)
* Se till att det finns en slutpunkt för direktuppspelning som körs. Mer information finns i [Hantera strömnings slut punkter i ett Media Services konto](media-services-portal-manage-streaming-endpoints.md)
* Installera den senaste versionen av [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) -verktyget.
* Starta verktyget och ansluta till AMS-kontot.

## <a name="tips"></a>Tips
* När det är möjligt använda ett inbyggt internet-anslutning.
* En bra tumregel när du fastställer kraven på nätverksbandbredd är att dubbla strömmande bithastighet. Detta är inte ett obligatoriskt krav, hjälper det att undvika påverkan av överbelastning på nätverket.
* När du använder programvarubaserad kodare kan du stänga alla onödiga program.

## <a name="create-a-channel"></a>Skapa en kanal
1. I AMSE-verktyget går du till fliken **Live** och högerklickar i kanalområdet. Välj **skapa kanal...** på menyn.

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast1.png)

2. Ange ett Kanalnamn beskrivningsfältet är valfritt. Under kanal inställningar väljer du **standard** för alternativet Live encoding med inmatnings protokollet inställt på **RTMP**. Du kan lämna alla andra inställningar som är.

    Kontrol lera att **starta den nya kanalen nu** är markerat.

3. Klicka på **skapa kanal**.

   ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast2.png)

> [!NOTE]
> Kanalen kan ta upp till 20 minuter att starta.
>
>

När kanalen startas kan du [Konfigurera kodaren](media-services-configure-wirecast-live-encoder.md#configure_wirecast_rtmp).

> [!IMPORTANT]
> Debiteringen börjar när kanalen hamnar i tillståndet redo. Mer information finns i [kanalens tillstånd](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a name="a-idconfigure_wirecast_rtmp-configure-the-telestream-wirecast-encoder"></a><a id="configure_wirecast_rtmp" />konfigurera Wirecast-kodaren för Telestream
I den här självstudien används följande utdatainställningar för. Resten av det här avsnittet beskriver konfigurationssteg i detalj.

**Video**:

* Codec: H.264
* Profil: Hög (nivå 4.0)
* Med flera bithastigheter: 5000 kbit/s
* Bildrutan: 2 sekunder (60 sekunder)
* RAM-pris: 30

**Ljud**:

* Codec: AAC (LC)
* Med flera bithastigheter: 192 kbit/s
* Samplingshastighet: 44.1 kHz

### <a name="configuration-steps"></a>Konfigurationssteg
1. Öppna Telestream Wirecast-programmet på datorn som används och konfigureras för RTMP strömning.
2. Konfigurera utdata genom att gå till fliken **utdata** och välja inställningar för **utdata...** .

    Kontrol lera att **utmatnings destinationen** är **RTMP-Server**.
3. Klicka på **OK**
4. På sidan inställningar anger du att **mål** fältet ska **Azure Media Services**.

    Kodnings profilen är förvald till **Azure H. 264 720p 16:9 (1280x720)** . Om du vill anpassa inställningarna väljer du kugg hjuls ikonen till höger om List rutan och väljer sedan ny för **inställning**.

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast3.png)
5. Konfigurera kodare förinställningar.

    Namnge förinställningen och Sök efter följande rekommenderade inställningar:

    **Grafik**

   * Kodaren: MainConcept H.264
   * Bildrutor per sekund: 30
   * Genomsnittlig bithastighet: 5000 kbits/sek (kan justeras utifrån nätverksbegränsningar)
   * Profil: Main
   * Viktiga ramens var: 60 bildrutor

     **In**

   * Target bithastighet: 192 kbits/sek
   * Samplingshastighet: 44 100 kHz

     ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast4.png)
6. Tryck på **Spara**.

    Fältet avkodning har nu den nyligen skapade profilen väljas.

    Kontrollera att den nya profilen har valts.
7. Hämta kanalens inmatnings-URL för att tilldela den till Wirecast **RTMP-slutpunkten**.

    Gå tillbaka till AMSE-verktyget och kontrollera status för slutförande kanal. När statusen har ändrats från att **Starta** till **körs**kan du hämta INgångs-URL: en.

    När kanalen körs högerklickar du på kanal namnet, navigerar ned till hovra över **Kopiera ingångs-URL till Urklipp** och väljer sedan **primär URL för indatakälla**.  

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast6.png)
8. I fönstret Wirecast- **utdata** klistrar du in den här informationen i fältet **adress** i avsnittet utdata och tilldelar ett ström namn.

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast5.png)

1. Välj **OK**.
2. På **Wirecast** -skärmen bekräftar du att indata-källor för video och ljud är klara och trycker sedan på **Stream** i det övre vänstra hörnet.

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast7.png)

> [!IMPORTANT]
> Innan du klickar på **Stream** **måste** du se till att kanalen är klar.
> Se dessutom till att du inte lämnar kanalen i tillståndet redo utan ett inkommande bidrag feed > 15 minuter.
>
>

## <a name="test-playback"></a>Testa uppspelning

Gå till AMSE-verktyget och högerklicka på kanalen som ska testas. Hovra över **uppspelning av för hands versionen på** menyn och välj **med Azure Media Player**.  

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast8.png)

Om strömmen visas i spelaren, har sedan kodaren konfigurerats korrekt för att ansluta till AMS.

Om ett fel tas emot kanalen måste återställas och anpassa inställningar för kodare. Se [fel söknings](media-services-troubleshooting-live-streaming.md) artikeln för vägledning.  

## <a name="create-a-program"></a>Skapa ett program
1. När kanalen uppspelning har bekräftats kan du skapa ett program. Under fliken **Live** i AMSE-verktyget högerklickar du i program arean och väljer **Skapa nytt program**.  

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast9.png)
2. Ge programmet ett namn och, om det behövs, ändra **Arkiv fönstrets längd** (som är standardvärdet fyra timmar). Du kan också ange en lagringsplats eller lämna som standard.  
3. Markera kryss rutan **starta programmet nu** .
4. Klicka på **skapa program**.  

   >[!NOTE]
   >Programmet tar mindre tid än att skapa en kanal.
       
5. När programmet har körts bekräftar du uppspelningen genom att högerklicka på programmet och navigera för att **spela upp program** och välja **med Azure Media Player**.  
6. När du har bekräftat, högerklickar du på programmet igen och väljer **Kopiera URL: en till Urklipp** (eller hämta informationen från **program information och inställnings** alternativ på menyn).

Dataströmmen är nu redo att vara inbäddad i en spelare eller distribueras till en målgrupp för visning av live.  

## <a name="troubleshooting"></a>Felsökning
Se [fel söknings](media-services-troubleshooting-live-streaming.md) artikeln för vägledning.

## <a name="media-services-learning-paths"></a>Utbildningsvägar för Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
