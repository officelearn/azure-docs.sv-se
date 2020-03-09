---
title: Skapa en Azure Media Services Live Stream med portalen och Wirecast
description: Lär dig hur du skapar en Live-dataström i Azure Media service
services: media-services
ms.service: media-services
ms.topic: quickstart
ms.author: inhenkel
author: IngridAtMicrosoft
ms.date: 03/06/2020
ms.openlocfilehash: c0eaf3907cbfcd86424b1d2cbc03930a7af72786
ms.sourcegitcommit: 9cbd5b790299f080a64bab332bb031543c2de160
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2020
ms.locfileid: "78927628"
---
# <a name="create-a-azure-media-services-live-stream-with-the-portal-and-wirecast"></a>Skapa en Azure Media Services Live Stream med portalen och Wirecast

I den här Komma igångs guiden förutsätter vi att du har en Azure-prenumeration och har skapat ett Azure Media Services-konto.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="log-in-to-the-azure-portal"></a>Logga in på Azure-portalen

Öppna webbläsaren och gå till [Microsoft Azure Portal](https://portal.azure.com/). Ange dina autentiseringsuppgifter och logga in på portalen. Standardvyn är instrumentpanelen.

I den här snabb starten tar vi upp:

- Konfigurera en lokal kodare med en kostnads fri utvärderings version av Wirecast för multistream
- Konfigurera en Live-dataström
- Konfigurera Live Stream-utdata
- Köra en standard slut punkt för direkt uppspelning
- Använda Azure Media Player för att Visa Live Stream och utdata på begäran

För att det ska vara enkelt kommer vi att använda en kodnings för inställning för Azure Media Services i Wirecast, genom strömnings moln kodning och RTMP.

## <a name="setting-up-an-on-premises-encoder-with-wirecast"></a>Konfigurera en lokal kodare med Wirecast

1. Hämta och installera Wirecast för operativ systemet på https://www.telestream.net
1. Starta programmet och Använd din favorit-e-postadress för att registrera produkten.  Se till att programmet är öppet.
1. Du kommer att få ett e-postmeddelande som ber dig att verifiera din e-postadress. sedan startar programmet den kostnads fria utvärderings versionen.
1. REKOMMENDERAt: titta på video självstudien på skärmen öppna program.

## <a name="setting-up-an-azure-media-services-live-stream"></a>Konfigurera en Azure Media Services Live Stream

1. När du har navigerat till Azure Media Services kontot i portalen väljer du **Live streaming** från Media Services listan.
1. Klicka på **Lägg till direktsänd händelse** om du vill skapa en ny direkt uppspelnings händelse.
1. Ange ett namn för den nya händelsen, till exempel *TestLiveEvent* i fältet Live **-händelseloggen.**
1. Ange en valfri beskrivning av händelsen i fältet **Beskrivning** .
1. Välj alternativ knappen **genom strömning – ingen moln kodning** .
1. Välj alternativ knappen för **RTMP** . 
1. Se till att **ingen** alternativ knapp är markerad för att starta direkt sändningen för att förhindra att den aktiva händelsen faktureras innan den är klar.  (Faktureringen påbörjas när Live-händelsen startas.)
1. Granska inställningarna genom att klicka på knappen **Granska + skapa** .
1. Klicka på knappen **skapa** för att skapa en Live-händelse. Du kommer sedan tillbaka till vyn Live Event List.
1. Klicka på **länken till den Live-händelse** som du nyss skapade. Observera att din händelse har stoppats.
1. Se till att sidan är öppen i webbläsaren.  Vi kommer att komma tillbaka till den senare.

## <a name="setting-up-a-live-stream-with-wirecast-studio"></a>Konfigurera en Live-ström med Wirecast Studio

1. Förutsatt att du fortfarande har Wirecast-programmet öppet väljer du **Skapa tomt dokument** från huvud menyn och klickar sedan på **Fortsätt**.
1. Hovra över det första lagret i Wirecast lager-ytan.  Klicka på ikonen **Lägg till** som visas och välj den video indata som du vill strömma.  Dialog rutan Master Layer 1 öppnas.
1. Välj **video insamling** på menyn och välj sedan den kamera som du vill använda. Vyn från kameran visas i förhands gransknings avsnittet.
1. Hovra över det andra lagret i Wirecast lager-ytan. Klicka på ikonen **Lägg till** som visas och välj den ljud inspelning som du vill strömma.  Dialog rutan Master Layer 2 öppnas.
1. Välj **ljud inspelning** på menyn och välj sedan den ljud inspelning som du vill använda. 
1. Från huvud menyn väljer du **Inställningar för utdata**.  Dialog rutan utdata visas.
1. Välj **Azure Media Services** i list rutan utdata.  Utmatnings inställningen för Azure Media Services fyller automatiskt i *de flesta* av inställningarna för utdata.
1. I nästa avsnitt ska du gå tillbaka till Azure Media Services i webbläsaren för att kopiera *inmatnings-URL: en* för att komma in i inställningarna för utdata.

### <a name="copy-and-paste-the-input-url"></a>Kopiera och klistra in URL: en för indataport

1. Gå tillbaka till sidan Azure Media Services i portalen, klicka på **Starta** för att starta händelsen Live Stream. (Faktureringen startar nu.)
2. Klicka på säker **/** osäker växling för att ange att den **inte är säker**.  Detta anger att protokollet ska vara RTMP i stället för RTMP.
3. Kopiera **URL: en för indataport** till Urklipp.
4. Växla till Wirecast-programmet och klistra in **inmatnings-URL** i fältet **adress** i inställningarna för utdata.
5. Klicka på **OK**.

## <a name="setting-up-outputs"></a>Konfigurera utdata

Den här delen konfigurerar dina utdata och gör att du kan spara en inspelning av din Live Stream.  

> [!NOTE]
> För att strömma dessa utdata måste strömnings slut punkten köras.  Se köra standard slut punkten för direkt uppspelning nedan.

1. Klicka på länken **skapa utdata** under utdata i video visnings programmet.
1. Om du vill kan du redigera namnet på utdata i fältet **namn** så att det blir mer användarvänligt, så att det är lätt att hitta senare.
1. Lämna endast resten av fälten för tillfället.
1. Klicka på **Nästa** Lägg till strömmande positionerare.
1. Ändra namnet på lokaliseraren till något mer användarvänligt, om det önskas.
1. Lämna allt annat på den här skärmen separat för tillfället.
1. Klicka på **Skapa**.

## <a name="starting-the-broadcast"></a>Startar sändningen

1. I Wirecast väljer du **utdata > starta/stoppa sändning > starta Azure Media Services: Azure Media Services** från huvud menyn.  När strömmen har skickats till direkt sändningen visas live-fönstret i Wirecast i video spelaren för live event på sidan live event i Azure Media Services.

1. Klicka på knappen **gå** under förhands gransknings fönstret för att starta sändningen av video och ljud som du har valt för Wirecast-skiktet.

> [!TIP]
> Om det uppstår ett fel kan du prova att läsa in spelaren igen genom att klicka på länken Läs in Player ovanför spelaren.

## <a name="running-the-default-streaming-endpoint"></a>Köra standard slut punkten för direkt uppspelning

1. Kontrol lera att slut punkten för direkt uppspelning körs genom att välja **strömnings slut punkter** i Media Services listan. Du kommer att gå till sidan för strömnings slut punkter.
1. Om statusen för direkt uppspelnings slut punkten stoppas klickar du på **standard** slut punkten för direkt uppspelning. Det tar dig till sidan för den slut punkten.
1. Klicka på **Starta**.  Då startas slut punkten för direkt uppspelning.

## <a name="play-the-output-broadcast-with-azure-media-player"></a>Spela upp utdata-sändningen med Azure Media Player

1. Kopiera den **strömmande URL: en** under utdata Videos pelaren. 
1. Öppna demo-Azure Media Player i en webbläsare https://ampdemo.azureedge.net/azuremediaplayer.html
1. Klistra in **URL: en för strömningen** i URL-fältet för Azure Media Player.
1. Klicka på knappen **Uppdatera spelare** .
1. Klicka på ikonen **spela upp** på videon om du vill se din real tids ström.

## <a name="stopping-the-broadcast"></a>Stoppa sändningen

Stoppa sändningen när du tror att du har strömmat tillräckligt med innehåll.

1. Klicka på **sändnings** knappen i Wirecast.  Detta kommer att stoppa sändningen från Wirecast.
1. Klicka på **stoppa**i portalen. Du får ett varnings meddelande om att Live Stream-objektet stoppas, men utdata kommer nu att bli en till gång på begäran.
1. Klicka på **stoppa** i varnings meddelandet. Azure Media Player visas nu ett fel eftersom direkt strömmen inte längre är tillgänglig.

## <a name="play-the-on-demand-output-with-the-azure-media-player"></a>Spela upp utdata på begäran med Azure Media Player

De utdata du har skapat är nu tillgängliga för strömning på begäran så länge som slut punkten för direkt uppspelning körs.

1. Navigera till Media Services listan och välj **till gångar**.
1. Hitta de utdata som du skapade tidigare och klicka på **länken till till gången**. Sidan till gångs utmatning öppnas.
1. Kopiera **strömnings-URL: en** under Videos pelaren för till gången.
1. Gå tillbaka till Azure Media Player i webbläsaren och klistra in **URL-adressen för strömning** i URL-fältet för Azure Media Player.
1. Klicka på **Uppdatera Player**.
1. Klicka på ikonen **spela upp** på videon om du vill visa en till gång på begäran.

## <a name="clean-up-resources"></a>Rensa resurser

  > [!IMPORTANT]
  > Stoppa tjänsterna! När du har slutfört stegen i den här snabb starten måste du vara säker på att du vill stoppa direkt sändningen och slut punkten för direkt uppspelning, annars fortsätter du att faktureras för den tid de fortfarande körs. Om du vill stoppa direkt sändningen, se steg 2 och 3 ovan för att stoppa sändningen.

### <a name="stopping-the-streaming-endpoint"></a>Stoppa slut punkten för direkt uppspelning

1. I listan Media Services väljer du **slut punkter för direkt uppspelning**.
2. Klicka på den **standard** slut punkt för direkt uppspelning som du startade tidigare. Då öppnas sidan slut punkt.
3. Klicka på **stoppa**.  Detta stoppar slut punkten för direkt uppspelning.

>[!TIP]
>Om du inte vill behålla till gångarna från den här händelsen måste du ta bort dem för att undvika att debiteras för lagring.

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Vilken artikel är härnäst i följd](./live-events-outputs-concept.md)
