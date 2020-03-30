---
title: Skapa en Azure Media Services Live-ström med portalen och Wirecast
description: Lär dig hur du skapar en Livestream för Azure Media Service
services: media-services
ms.service: media-services
ms.topic: quickstart
ms.author: inhenkel
author: IngridAtMicrosoft
ms.date: 03/25/2020
ms.openlocfilehash: e5bdd75ca61d53a64f003633d74e3d8f7992a98b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80336413"
---
# <a name="create-a-azure-media-services-live-stream-with-the-portal-and-wirecast"></a>Skapa en Azure Media Services Live-ström med portalen och Wirecast

Den här komma igång-guiden förutsätter att du har en Azure-prenumeration och har skapat ett Azure Media Services-konto.

Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="log-in-to-the-azure-portal"></a>Logga in på Azure Portal

Öppna webbläsaren och gå till [Microsoft Azure Portal](https://portal.azure.com/). Ange dina autentiseringsuppgifter och logga in på portalen. Standardvyn är instrumentpanelen.

I denna snabbstart kommer vi att täcka:

- Ställa in en lokal kodare med en kostnadsfri utvärderingsversion av Telestream Wirecast
- Konfigurera en livestream
- Ställa in utdata för livestream
- Köra en standardslutpunkt för direktuppspelning
- Använda Azure Media Player för att visa livestreamen och utdata på begäran

För att hålla det enkelt använder vi en kodningsförinställning för Azure Media Services i Wirecast, genomströmning av molnkodning och RTMP.

## <a name="setting-up-an-on-premises-encoder-with-wirecast"></a>Ställa in en lokal kodare med Wirecast

1. Ladda ned och installera Wirecast för ditt operativsystem påhttps://www.telestream.net
1. Starta programmet och använd din favorit e-postadress för att registrera produkten.  Håll programmet öppet.
1. Du kommer att få ett e-postmeddelande där du uppmanas att verifiera din e-postadress, då programmet kommer att starta den fria provperioden.
1. REKOMMENDERAS: Titta på video tutorial i den inledande ansökan skärmen.

## <a name="setting-up-an-azure-media-services-live-stream"></a>Konfigurera en Azure Media Services-livestream

1. När du har navigerat till Azure Media Services-kontot i portalen väljer du **Direktuppspelning** från Media Services-listan.<br/>
![Välj länk för direktuppspelning](media/live-events-wirecast-quickstart/select-live-streaming.png)<br/>
1. Klicka på **Lägg till live-evenemang** för att skapa en ny livestreaminghändelse.<br/>
![Ikonen Lägg till livehändelse](media/live-events-wirecast-quickstart/add-live-event.png)<br/>
1. Ange ett namn på din nya händelse, till exempel *TestLiveEvent* i fältet Live event **Name.**<br/>
![Textfält för livehändelsenamn](media/live-events-wirecast-quickstart/live-event-name.png)<br/>
1. Ange en valfri beskrivning av händelsen i fältet **Beskrivning.**
1. Välj **direktupppassning – ingen radioknapp för molnkodning.**<br/>
![Alternativknapp för molnkodning](media/live-events-wirecast-quickstart/cloud-encoding.png)
1. Välj **rtmp-alternativknappen.**
1. Kontrollera att **knappen Ingen** alternativ är markerad för Starta livehändelse, för att förhindra att den faktureras för livehändelsen innan den är klar.  (Faktureringen börjar när livehändelsen har startats.) ![Knappen Starta radioknapp för liveevenemang](media/live-events-wirecast-quickstart/start-live-event-no.png)<br/>
1. Klicka på knappen **Granska + skapa** om du vill granska inställningarna.
1. Klicka på knappen **Skapa** för att skapa livehändelsen. Du kommer då tillbaka till live-händelselistningsvyn.
1. Klicka på **länken till liveevenemanget** du just skapade. Observera att händelsen har stoppats.
1. Håll den här sidan öppen i din webbläsare.  Vi återkommer till det senare.

## <a name="setting-up-a-live-stream-with-wirecast-studio"></a>Konfigurera en livestream med Wirecast Studio

1. Om du antar att du fortfarande har Wirecast-programmet öppet väljer du **Skapa tomt dokument** på huvudmenyn och klickar sedan på **Fortsätt**.
![Startskärmen för kabelsändning](media/live-events-wirecast-quickstart/open-empty-document.png)
1. Hovra över det första lagret i området Wirecast-lager.  Klicka på ikonen **Lägg till** som visas och välj den videoinmatning som du vill strömma.  Dialogrutan Huvudlager 1 öppnas.<br/>
![Ikon för Wirecast-tillägg](media/live-events-wirecast-quickstart/add-icon.png)
1. Välj **Videoinspelning** på menyn och välj sedan den kamera som du vill använda. Om du väljer en kamera visas vyn från kameran i förhandsgranskningsområdet.
![Skärm för val av videobild av trådsändning](media/live-events-wirecast-quickstart/video-shot-selection.png)
1. Hovra över det andra lagret i området Wirecast-lager. Klicka på ikonen **Lägg till** som visas och välj den ljudinmatning som du vill strömma.  Dialogrutan Huvudlager 2 öppnas.
1. Välj **Ljudinspelning** på menyn och välj sedan den ljudinmatning som du vill använda.
![Valskärm för kabelsändning av ljudbilder](media/live-events-wirecast-quickstart/audio-shot-select.png)
1. Välj **Utdatainställningar**på huvudmenyn .  Dialogrutan Utdata visas.
1. Välj **Azure Media Services** i listrutan för utdata.  Utdatainställningen för Azure Media Services fyller automatiskt i *de flesta* utdatainställningarna.<br/>
![Skärmen För kabelsändningsinställningar](media/live-events-wirecast-quickstart/azure-media-services.png)
1. I nästa avsnitt går du tillbaka till Azure Media Services i webbläsaren för att kopiera *indata-URL:en* för att ange utdatainställningarna.

### <a name="copy-and-paste-the-input-url"></a>Kopiera och klistra in indata-URL:en

1. Klicka på **Start** på sidan Live Stream på sidan Azure Media Services på portalen. (Faktureringen börjar nu.)<br/>
![Ikonen Start](media/live-events-wirecast-quickstart/start.png)
2. Klicka på den **säkra växlingsknappen** för att ställa in den på Inte **säker**.  Detta ställer in protokollet till RTMP i stället för RTMPS.
3. Kopiera **indata-URL:en** till Urklipp.
![Url för indata](media/live-events-wirecast-quickstart/input-url.png)
4. Växla till Wirecast-programmet och klistra in **indata-URL:en** i **fältet Adress** i utdatainställningarna.<br/>
![Url för wirecast-indata](media/live-events-wirecast-quickstart/input-url-wirecast.png)
5. Klicka på **Okej.**

## <a name="setting-up-outputs"></a>Ställa in utdata

Den här delen ställer in dina utdata och gör att du kan spara en inspelning av din livestream.  

> [!NOTE]
> För att strömma utdata måste slutpunkten för direktuppspelning köras.  Se Köra standardavsnittet för direktuppspelning nedan.

1. Klicka på länken **Skapa utdata** under videovisaren Outputs.
1. Om du vill kan du redigera namnet på utdata i fältet **Namn** till något mer användarvänligt så att det är lätt att hitta senare.
![Fältet Utflödesnamn](media/live-events-wirecast-quickstart/output-name.png)
1. Lämna alla resten av fälten ensamma för tillfället.
1. Klicka på **Nästa** lägga till streaming locator.
1. Ändra namnet på positioneraren till något mer användarvänligt, om så önskas.
![Namnfält för positionerare](media/live-events-wirecast-quickstart/live-event-locator.png)
1. Lämna allt annat på den här skärmen ensam för nu.
1. Klicka på **Skapa**.

## <a name="starting-the-broadcast"></a>Starta sändningen

1. I Wirecast väljer du **Utdata > Start/ Sluta sända > Start Azure Media Services: Azure Media Services** på huvudmenyn.  När strömmen har skickats till live-evenemanget visas livefönstret i Wirecast i livehändelsevideospelaren på livehändelsesidan i Azure Media Services.

   ![Menyalternativ för Start-sändning](media/live-events-wirecast-quickstart/start-broadcast.png)

1. Klicka på knappen **Gå** under förhandsgranskningsfönstret om du vill börja sända den video och det ljud du valde för Wirecast-lagren.

   ![Knappen Wirecast Go](media/live-events-wirecast-quickstart/go-button.png)

   > [!TIP]
   > Om det finns ett fel kan du försöka ladda om spelaren genom att klicka på länken Ladda om spelaren ovanför spelaren.

## <a name="running-the-default-streaming-endpoint"></a>Köra standardslutpunkten för direktuppspelning

1. Kontrollera att slutpunkten för direktuppspelning körs genom att välja slutpunkter för **direktuppspelning** i mediatjänstelistan. Du kommer att tas till sidan för strömmande slutpunkter.<br/>
![Menyalternativ för direktuppspelning](media/live-events-wirecast-quickstart/streaming-endpoints.png)
1. Om standardslutpunktsstatusen för direktuppspelning stoppas klickar du på **standardslutpunkten för** direktuppspelning. Detta tar dig till sidan för den slutpunkten.
1. Klicka på **Starta**.  Då startas slutpunkten för direktuppspelning.<br/>
![Menyalternativ för direktuppspelning](media/live-events-wirecast-quickstart/start.png)

## <a name="play-the-output-broadcast-with-azure-media-player"></a>Spela upp utdatasändningen med Azure Media Player

1. Kopiera **webbadressen för direktuppspelning** under videospelaren Output.
1. Öppna demon Azure Media Player i en webbläsarehttps://ampdemo.azureedge.net/azuremediaplayer.html
1. Klistra in **url:en** för direktuppspelning i URL-fältet för Azure Media Player.
1. Klicka på knappen **Uppdatera spelare.**
1. Klicka på **uppspelningsikonen** på videon för att se din livestream.

## <a name="stopping-the-broadcast"></a>Stoppa sändningen

När du tror att du har strömmat tillräckligt med innehåll stoppar du sändningen.

1. I Wirecast klickar du på **broadcast-knappen.**  Detta kommer att stoppa sändningen från Wirecast.
1. Klicka på **Stoppa**i portalen . Du får ett varningsmeddelande om att livestreamen stoppas men utdata kommer nu att bli en on-demand-tillgång.
1. Klicka på **Stoppa** i varningsmeddelandet. Azure Media Player visar nu också ett fel eftersom livestreamen inte längre är tillgänglig.

## <a name="play-the-on-demand-output-with-the-azure-media-player"></a>Spela upp utdata på begäran med Azure Media Player

Utdata som du har skapat är nu tillgängligt för direktuppspelning på begäran så länge som slutpunkten för direktuppspelning körs.

1. Navigera till medietjänstlistan och välj **Tillgångar**.
1. Hitta händelseutdata som du skapade tidigare och klicka på **länken till tillgången**. Sidan för utdata öppnas.
1. Kopiera **webbadressen** för direktuppspelning under videospelaren för tillgången.
1. Gå tillbaka till Azure Media Player i webbläsaren och klistra in **url:en** för direktuppspelning i URL-fältet för Azure Media Player.
1. Klicka på **Uppdatera spelare**.
1. Klicka på **uppspelningsikonen** på videon för att visa tillgången på begäran.

## <a name="clean-up-resources"></a>Rensa resurser

> [!IMPORTANT]
> Stoppa servicen! När du har slutfört stegen i den här snabbstarten bör du se till att stoppa livehändelsen och slutpunkten för direktuppspelning, annars fortsätter du att faktureras för den tid de körs. Om du vill stoppa livehändelsen läser du Stoppa sändningen, steg 2 och 3 ovan.

### <a name="stopping-the-streaming-endpoint"></a>Stoppa slutpunkten för direktuppspelning

1. Välj Slutpunkter för **direktuppspelning**i listan Media Services .
2. Klicka på **standardslutpunkten** för direktuppspelning som du startade tidigare. Då öppnas slutpunktens sida.
3. Klicka på **Stoppa**.  Detta stoppar slutpunkten för direktuppspelning.

> [!TIP]
> Om du inte vill behålla tillgångarna från den här händelsen måste du ta bort dem för att förhindra att debiteras för lagring.

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Vilken artikel är nästa i följd](./live-events-outputs-concept.md)
