---
title: Skapa en Azure Media Services livestream
description: Lär dig hur du skapar en Azure Media Services live stream med hjälp av portalen och Wirecast
services: media-services
ms.service: media-services
ms.topic: quickstart
ms.author: inhenkel
author: IngridAtMicrosoft
ms.date: 03/25/2020
ms.openlocfilehash: 459f11844f873a911b3e5702e8c768b1cd22e504
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985005"
---
# <a name="create-an-azure-media-services-live-stream"></a>Skapa en Azure Media Services livestream

Den här snabbstarten hjälper dig att skapa en Azure Media Services live stream med hjälp av Azure-portalen och Telestream Wirecast. Det förutsätter att du har en Azure-prenumeration och har skapat ett Media Services-konto.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Öppna webbläsaren och gå till [Microsoft Azure-portalen](https://portal.azure.com/). Ange dina autentiseringsuppgifter och logga in på portalen. Standardvyn är instrumentpanelen.

I den här snabbstarten täcker vi:

- Ställa in en lokal kodare med en kostnadsfri utvärderingsversion av Telestream Wirecast.
- Ställa in en livestream.
- Ställa in livestream-utdata.
- Köra en standardslutpunkt för direktuppspelning.
- Använda Azure Media Player för att visa livestreamen och utdata på begäran.

För att hålla det enkelt använder vi en kodningsförinställning för Azure Media Services i Wirecast, genomströmning av molnkodning och RTMP.

## <a name="set-up-an-on-premises-encoder-by-using-wirecast"></a>Konfigurera en lokal kodare med Wirecast

1. Ladda ner och installera Wirecast för ditt operativsystem på [Telestreams webbplats](https://www.telestream.net).
1. Starta programmet och använd din favorit e-postadress för att registrera produkten. Håll programmet öppet.
1. Verifiera din e-postadress i e-postmeddelandet som du får. Då programmet kommer att starta den fria rättegången.
1. Rekommenderas: Titta på videohandledningen på öppningsprogrammets skärm.

## <a name="set-up-an-azure-media-services-live-stream"></a>Konfigurera en Azure Media Services livestream

1. Gå till Azure Media Services-kontot i portalen och välj sedan **Direktuppspelning** från **mediatjänstlistan.**

   ![Länk för livestreaming](media/live-events-wirecast-quickstart/select-live-streaming.png)
1. Välj **Lägg till livehändelse** om du vill skapa en ny livestreaminghändelse.

   ![Ikonen Lägg till livehändelse](media/live-events-wirecast-quickstart/add-live-event.png)
1. Ange ett namn på den nya händelsen, till exempel *TestLiveEvent,* i rutan **Live-händelsenamn.**

   ![Rutan Namnruta för liveevenemang](media/live-events-wirecast-quickstart/live-event-name.png)
1. Ange en valfri beskrivning av händelsen i rutan **Beskrivning.**
1. Välj **alternativet Direkt – inget molnkodningsalternativ.**

   ![Alternativ för molnkodning](media/live-events-wirecast-quickstart/cloud-encoding.png)
1. Välj **RTMP-alternativet.**
1. Kontrollera att alternativet **Nej** är markerat för **Starta livehändelse**, för att undvika att faktureras för livehändelsen innan det är klart. (Faktureringen börjar när livehändelsen startas.)

   ![Starta livehändelsealternativ](media/live-events-wirecast-quickstart/start-live-event-no.png)
1. Välj knappen **Granska + skapa** om du vill granska inställningarna.
1. Välj knappen **Skapa** för att skapa livehändelsen. Du returneras sedan till live-händelselistan.
1. Välj länken till den livehändelse som du just skapade. Observera att händelsen har stoppats.
1. Håll den här sidan öppen i din webbläsare. Vi återkommer till det senare.

## <a name="set-up-a-live-stream-by-using-wirecast-studio"></a>Konfigurera en livestream med Wirecast Studio

1. I Wirecast-programmet väljer du **Skapa tomt dokument** på huvudmenyn och väljer sedan **Fortsätt**.

   ![Startskärmen för kabelsändning](media/live-events-wirecast-quickstart/open-empty-document.png)
1. Hovra över det första lagret i området **Wirecast-lager.**  Välj ikonen **Lägg till** som visas och välj den videoinmatning som du vill strömma.

   ![Ikon för Wirecast-tillägg](media/live-events-wirecast-quickstart/add-icon.png)

   Dialogrutan **Huvudlager 1** öppnas.
1. Välj **Videoinspelning** på menyn och välj sedan den kamera som du vill använda.

   ![Förhandsgranskningsområde för videoinspelning](media/live-events-wirecast-quickstart/video-shot-selection.png)

   Vyn från kameran visas i förhandsgranskningsområdet.
1. Hovra över det andra lagret i området **Wirecast-lager.** Välj ikonen **Lägg till** som visas och välj den ljudinmatning som du vill strömma. Dialogrutan **Huvudlager 2** öppnas.
1. Välj **Ljudinspelning** på menyn och välj sedan den ljudinmatning som du vill använda.

   ![Ingångar för ljudinspelning](media/live-events-wirecast-quickstart/audio-shot-select.png)
1. Välj **Utdatainställningar**på huvudmenyn . Dialogrutan **Välj ett utdatamål** visas.
1. Välj **Azure Media Services** i listrutan **Mål.** Utdatainställningen för Azure Media Services fyller automatiskt i *de flesta* utdatainställningarna.

   ![Skärmen För kabelsändningsinställningar](media/live-events-wirecast-quickstart/azure-media-services.png)


I nästa procedur går du tillbaka till Azure Media Services i webbläsaren för att kopiera indata-URL:en för att ange utdatainställningarna:

1. På sidan Azure Media Services på portalen väljer du **Start** för att starta live stream-händelsen. (Faktureringen börjar nu.)

   ![Ikonen Start](media/live-events-wirecast-quickstart/start.png)
2. Ställ in den **säkra/inte säkra** växlingsknappen till **Inte säker**. Det här steget ställer in protokollet till RTMP i stället för RTMPS.
3. Kopiera URL:en till Urklipp i rutan **Indata-URL.**

   ![Url för indata](media/live-events-wirecast-quickstart/input-url.png)
4. Växla till Wirecast-programmet och klistra in **indata-URL:en** i **rutan Adress** i utdatainställningarna.

   ![Url för wirecast-indata](media/live-events-wirecast-quickstart/input-url-wirecast.png)
5. Välj **OK**.

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

## <a name="start-the-broadcast"></a>Starta sändningen

1. I Wirecast väljer du **Utdatastart** > **/ Sluta sända** > **Start Azure Media Services: Azure Media Services** på huvudmenyn.

   ![Menyalternativ för Start-sändning](media/live-events-wirecast-quickstart/start-broadcast.png)

   När strömmen har skickats till live-händelsen visas **livefönstret** i Wirecast i videospelaren på livehändelsesidan i Azure Media Services.

1. Välj knappen **Gå** under förhandsgranskningsfönstret om du vill börja sända den video och det ljud som du har valt för Wirecast-lagren.

   ![Knappen Wirecast Go](media/live-events-wirecast-quickstart/go-button.png)

   > [!TIP]
   > Om det finns ett fel kan du försöka ladda om spelaren genom att välja länken **Reload player** ovanför spelaren.

## <a name="run-the-default-streaming-endpoint"></a>Kör standardslutpunkten för direktuppspelning

1. Välj **Slutpunkter för direktuppspelning** i mediatjänstlistan.

   ![Menyalternativ för strömmande slutpunkter](media/live-events-wirecast-quickstart/streaming-endpoints.png)
1. Om standardstatusen för direktuppspelningsslutpunkt stoppas markerar du den. Det här steget tar dig till sidan för den slutpunkten.
1. Välj **Starta**.
   
   ![Start-knappen för slutpunkten för direktuppspelning](media/live-events-wirecast-quickstart/start.png)

## <a name="play-the-output-broadcast-by-using-azure-media-player"></a>Spela upp utdatasändningen med Hjälp av Azure Media Player

1. Kopiera den strömmande webbadressen under videospelaren **Output.**
1. Öppna [demoen för Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html)i en webbläsare .
1. Klistra in url:en för direktuppspelning i **URL-rutan** för Azure Media Player.
1. Välj knappen **Uppdatera spelare.**
1. Välj **ikonen Spela upp** på videon för att se din livestream.

## <a name="stop-the-broadcast"></a>Stoppa sändningen

När du tror att du har strömmat tillräckligt med innehåll stoppar du sändningen.

1. Välj knappen **Broadcast** i Wirecast. Det här steget stoppar sändningen från Wirecast.
1. Välj **Stoppa**i portalen . Du får då ett varningsmeddelande om att livestreamen kommer att stoppas, men utdata kommer nu att bli en on-demand-tillgång.
1. Välj **Stoppa** i varningsmeddelandet. Azure Media Player visar nu ett fel eftersom livestreamen inte längre är tillgänglig.

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
