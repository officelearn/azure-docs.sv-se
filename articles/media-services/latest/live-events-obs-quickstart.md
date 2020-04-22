---
title: Skapa en Azure Media Services livestream med OBS Studio
description: Lär dig hur du skapar en Azure Media Services live stream med hjälp av portalen och OBS Studio
services: media-services
ms.service: media-services
ms.topic: quickstart
ms.author: inhenkel
author: IngridAtMicrosoft
ms.date: 04/16/2020
ms.openlocfilehash: 74aa17fb17d682449bd817945c3b8bbf3f95363e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726615"
---
# <a name="create-an-azure-media-services-live-stream-with-obs"></a>Skapa en Azure Media Services livestream med OBS

Den här snabbstarten hjälper dig att skapa en Azure Media Services live stream med hjälp av Azure-portalen och Open Broadcasting Studio (OBS). Det förutsätter att du har en Azure-prenumeration och har skapat ett Media Services-konto.

I den här snabbstarten täcker vi:

- Ställa in en lokal kodare med OBS.
- Ställa in en livestream.
- Ställa in livestream-utdata.
- Köra en standardslutpunkt för direktuppspelning.
- Använda Azure Media Player för att visa livestreamen och utdata på begäran.

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Öppna webbläsaren och gå till [Microsoft Azure-portalen](https://portal.azure.com/). Ange dina autentiseringsuppgifter och logga in på portalen. Standardvyn är instrumentpanelen.

## <a name="set-up-an-on-premises-encoder-by-using-obs"></a>Konfigurera en lokal kodare med hjälp av OBS

1. Ladda ner och installera OBS för ditt operativsystem på [Open Broadcaster Software webbplats](https://obsproject.com/).
1. Starta programmet och håll det öppet.

## <a name="run-the-default-streaming-endpoint"></a>Kör standardslutpunkten för direktuppspelning

1. Välj **Slutpunkter för direktuppspelning** i mediatjänstlistan.

   ![Menyalternativ för strömmande slutpunkter](media/live-events-obs-quickstart/streaming-endpoints.png)
1. Om standardstatusen för direktuppspelningsslutpunkt stoppas markerar du den. Det här steget tar dig till sidan för den slutpunkten.
1. Välj **Starta**.

   ![Start-knappen för slutpunkten för direktuppspelning](media/live-events-obs-quickstart/start.png)

## <a name="set-up-an-azure-media-services-live-stream"></a>Konfigurera en Azure Media Services livestream

1. Gå till Azure Media Services-kontot i portalen och välj sedan **Direktuppspelning** från **mediatjänstlistan.**

   ![Länk för livestreaming](media/live-events-obs-quickstart/select-live-streaming.png)
1. Välj **Lägg till livehändelse** om du vill skapa en ny livestreaminghändelse.

   ![Ikonen Lägg till livehändelse](media/live-events-obs-quickstart/add-live-event.png)
1. Ange ett namn på den nya händelsen, till exempel *TestLiveEvent,* i rutan **Live-händelsenamn.**

   ![Rutan Namnruta för liveevenemang](media/live-events-obs-quickstart/live-event-name.png)
1. Ange en valfri beskrivning av händelsen i rutan **Beskrivning.**
1. Välj **alternativet Direkt – inget molnkodningsalternativ.**

   ![Alternativ för molnkodning](media/live-events-obs-quickstart/cloud-encoding.png)
1. Välj **RTMP-alternativet.**
1. Kontrollera att alternativet **Nej** är markerat för **Starta livehändelse**, för att undvika att faktureras för livehändelsen innan det är klart. (Faktureringen börjar när livehändelsen startas.)

   ![Starta livehändelsealternativ](media/live-events-obs-quickstart/start-live-event-no.png)
1. Välj knappen **Granska + skapa** om du vill granska inställningarna.
1. Välj knappen **Skapa** för att skapa livehändelsen. Du returneras sedan till live-händelselistan.
1. Välj länken till den livehändelse som du just skapade. Observera att händelsen har stoppats.
1. Håll den här sidan öppen i din webbläsare. Vi återkommer till det senare.

## <a name="set-up-a-live-stream-by-using-obs-studio"></a>Konfigurera en livestream med hjälp av OBS Studio

OBS börjar med en standardscen men inga indata är markerade.

   ![Standardskärm för OBS](media/live-events-obs-quickstart/live-event-obs-default-screen.png)

### <a name="add-a-video-source"></a>Lägga till en videokälla

1. På **panelen Källor** klickar du på **ikonen Lägg** till för att välja en ny källenhet. Menyn **Källor** öppnas.

1. Välj **Videoinspelningsenhet** på källenhetsmenyn. Menyn **Skapa/Välj källa** öppnas.

   ![MENYN OBS-källor med videoenhet markerad](media/live-events-obs-quickstart/live-event-obs-video-device-menu.png)

1. Markera knappen **Lägg till befintlig** alternativ och klicka sedan på **OK**. Menyn **Egenskaper för videoenhet** öppnas.

   ![OBS ny video källmeny med lägga till befintliga markerade](media/live-events-obs-quickstart/live-event-obs-new-video-source.png)

1. Välj den videoinmatning som du vill använda för sändningen i listrutan **Enhet.** Lämna resten av inställningarna ensamma för tillfället och klicka på **OK**. Indatakällan läggs till på **panelen Källor** och videoinmatningsvyn visas i **förhandsgranskningsområdet.**

   ![Inställningar för OBS-kamera](media/live-events-obs-quickstart/live-event-surface-camera.png)

### <a name="add-an-audio-source"></a>Lägga till en ljudkälla

1. På **panelen Källor** klickar du på **ikonen Lägg** till för att välja en ny källenhet. Menyn Källenhet öppnas.

1. Välj **Ljudinmatningsinspelning** på källenhetsmenyn. Menyn **Skapa/Välj källa** öppnas.

   ![MENYN OBS-källor med ljudenhet markerad](media/live-events-obs-quickstart/live-event-obs-audio-device-menu.png)

1. Markera knappen **Lägg till befintlig** alternativ och klicka sedan på **OK**. **Menyn Egenskaper för ljudinmatning** öppnas.

   ![OBS-ljudkälla med lägga till befintliga markerade ](media/live-events-obs-quickstart/live-event-obs-new-audio-source.png)

1. Välj den ljudinspelningsenhet som du vill använda för sändningen i listrutan **Enhet.** Lämna resten av inställningarna ensamma för tillfället och klicka på OK. Ljudinspelningsenheten läggs till på ljudmixerpanelen.

   ![Listruta för val av OBS-ljudenhet](media/live-events-obs-quickstart/live-event-select-audio-device.png)

### <a name="set-up-streaming-in-obs"></a>Konfigurera direktuppspelning i OBS

I nästa procedur går du tillbaka till Azure Media Services i webbläsaren för att kopiera indata-URL:en för att ange utdatainställningarna:

1. På sidan Azure Media Services på portalen väljer du **Start** för att starta live stream-händelsen. (Faktureringen börjar nu.)

   ![Ikonen Start](media/live-events-obs-quickstart/start.png)
1. Ställ **RTMP** in RTMP-växlingsknappen på **RTMPS**.
1. Kopiera URL:en till Urklipp i rutan **Indata-URL.**

   ![Url för indata](media/live-events-obs-quickstart/input-url.png)

1. Växla till OBS-programmet.

1. Klicka på knappen **Inställningar** **på** kontrollpanelen. Alternativen Inställningar öppnas.

   ![PANELEN SÄKERHETSkontroller med valda inställningar](media/live-events-obs-quickstart/live-event-obs-settings.png)

1. Välj **Strömma** på **menyn Inställningar.**

1. Välj Visa alla i listrutan **Tjänst** och välj sedan **Anpassad...**.

1. Klistra in RTMPS-URL:en som du kopierade till Urklipp i fältet **Server.**

1. Ange något i **fältet Stream-nyckel.**  Det spelar egentligen ingen roll vad det är, men det måste ha ett värde.

    ![Inställningar för OBS-ström](media/live-events-obs-quickstart/live-event-obs-stream-settings.png)

1. Välj **Utdata** på **menyn Inställningar.**

1. Ange *2* i **intervallfältet Nyckelbildruta.** Detta ställer in fragmentlängden till 2 sekunder. För liveleverans med lägre latens använder du värdet 1 sekund.

1. VALFRITT: Ställ in **förinställningen för CPU-användning** *på mycket snabbt* om du använder en dator som har ont om processorkraft. Du kan också ställa in kbps till något lägre om det finns oönskade nätverksvillkor.

   ![Inställningar för OBS-utdata](media/live-events-obs-quickstart/live-event-obs-advanced-output-settings.png)

1. Lämna resten av inställningarna okanalade och klicka på **OK**.

### <a name="start-streaming"></a>Börja strömma

1. Klicka **på** Starta **direktuppspelning**på kontrollpanelen.

    ![OBS start streaming-knappen](media/live-events-obs-quickstart/live-event-obs-start-streaming.png)

2. Växla till händelseskärmen för Azure Media Services Live i webbläsaren och klicka på länken **Ladda om player.** Du bör nu se din stream i förhandsgranskningsspelaren.

## <a name="set-up-outputs"></a>Ställ in utdata

Den här delen ställer in dina utdata och gör att du kan spara en inspelning av din livestream.  

> [!NOTE]
> För att du ska kunna strömma ut måste slutpunkten för direktuppspelning köras. Se det senare [avsnittet Kör standardslutpunktsavsnittet för direktuppspelning.](#run-the-default-streaming-endpoint)

1. Välj länken **Skapa utdata** under videovisningen **Utdata.**
1. Om du vill kan du redigera namnet på utdata i rutan **Namn** till något mer användarvänligt så att det är lätt att hitta senare.

   ![Rutan Utdatanamn](media/live-events-wirecast-quickstart/output-name.png)
1. Lämna alla resten av lådorna ifred för tillfället.
1. Välj **Nästa** om du vill lägga till en streamingpositionerare.
1. Ändra namnet på positioneraren till något mer användarvänligt, om du vill.

   ![Rutan Lokaliseringsnamn](media/live-events-wirecast-quickstart/live-event-locator.png)
1. Lämna allt annat på den här skärmen ensam för nu.
1. Välj **Skapa**.

## <a name="play-the-output-broadcast-by-using-azure-media-player"></a>Spela upp utdatasändningen med Hjälp av Azure Media Player

1. Kopiera den strömmande webbadressen under videospelaren **Output.**
1. Öppna [demoen för Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html)i en webbläsare .
1. Klistra in url:en för direktuppspelning i **URL-rutan** för Azure Media Player.
1. Välj knappen **Uppdatera spelare.**
1. Välj **ikonen Spela upp** på videon för att se din livestream.

## <a name="stop-the-broadcast"></a>Stoppa sändningen

När du tror att du har strömmat tillräckligt med innehåll stoppar du sändningen.

1. Välj **Stoppa**i portalen .

1. I OBS väljer du knappen **Stoppa strömning** på **kontrollpanelen.** Det här steget stoppar sändningen från OBS.

## <a name="play-the-on-demand-output-by-using-azure-media-player"></a>Spela upp utdata på begäran med hjälp av Azure Media Player

Utdata som du har skapat är nu tillgängligt för direktuppspelning på begäran så länge som slutpunkten för direktuppspelning körs.

1. Gå till mediatjänstlistan och välj **Tillgångar**.
1. Leta reda på händelseutdata som du skapade tidigare och välj länken till tillgången. Utdatasidan öppnas.
1. Kopiera den strömmande webbadressen under videospelaren för tillgången.
1. Gå tillbaka till Azure Media Player i webbläsaren och klistra in url:en för direktuppspelning i url-rutan.
1. Välj **Uppdatera spelare**.
1. Välj **ikonen Spela upp** i videon om du vill visa tillgången på begäran.

## <a name="clean-up-resources"></a>Rensa resurser

> [!IMPORTANT]
> Stoppa servicen! När du har slutfört stegen i den här snabbstarten ska du se till att stoppa livehändelsen och slutpunkten för direktuppspelning, annars debiteras du för den tid de körs. Om du vill stoppa livehändelsen läser du [Proceduren Stoppa sändningen,](#stop-the-broadcast) steg 2 och 3.

Så här stoppar du slutpunkten för direktuppspelning:

1. Välj Slutpunkter för **direktuppspelning**i listan Media Services .
2. Välj standardslutpunkten för direktuppspelning som du startade tidigare. Det här steget öppnar slutpunktens sida.
3. Välj **Stopp**.

> [!TIP]
> Om du inte vill behålla tillgångarna från den här händelsen måste du ta bort dem så att du inte faktureras för lagring.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Live-evenemang och live-resultat i Medietjänster](./live-events-outputs-concept.md)
