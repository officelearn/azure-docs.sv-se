---
title: Artikel anpassning - kognitiva Azure-tjänster | Microsoft Docs
description: En självstudiekurs för artikeln anpassning med Azure-tjänsten för beslut av anpassade, ett moln-baserad API för kontextuella beslutsfattande.
services: cognitive-services
author: slivkins
manager: slivkins
ms.service: cognitive-services
ms.topic: article
ms.date: 05/08/2018
ms.author: slivkins;marcozo;alekh;marossi
ms.openlocfilehash: 35d0567f81a23d4726461059eb6fd31e04228697
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354588"
---
# <a name="article-personalization"></a>Artikel anpassning

Den här självstudiekursen fokuserar på Anpassa valet av artiklar på sidan klient till en webbplats. Anpassad beslut tjänst påverkar *flera* en lista över artiklar på fram sidan för instansen. Sidan är kanske en webbplats för nyheter som täcker endast politik och sport. Visas tre rangordnas listor över artiklar: politik, sport, och det senaste.

## <a name="applications-and-action-sets"></a>Program och åtgärden uppsättningar

Här är hur du anpassar din situation till ramen. Vi anta tre program, ett för varje lista är optimeras: app politik, app-sport, och de senaste app. Om du vill ange kandidat artiklar för varje program, det finns två uppsättningar för åtgärd: en för politik och en för sport. Åtgärden som angetts för de senaste app har automatiskt en union av två mängder.

> [!TIP]
> Åtgärd anger kan delas mellan program i anpassade beslut Service.

## <a name="prepare-action-set-feeds"></a>Förbereda åtgärden set-flöden

Anpassade beslut Service förbrukar åtgärd anger via RSS- eller Atom-flöden som tillhandahålls av kunden. Du kan ange två feeds: en för politik och en för sport. Anta att de hanteras från `http://www.domain.com/feeds/<feed-name>`.

Varje feed innehåller en lista över artiklar. RSS-, var och en har angetts av en `<item>` element på följande sätt:

```xml
<rss version="2.0"><channel>
   <item>
      <title><![CDATA[article title]]></title>
      <link>"article url"</link>
      <pubDate>publication date</pubDate>
    </item>
</channel></rss>
```

Ordningen för artiklar frågor. Det anger den standard-rangordning som är en bästa gissning på hur artiklarna ska sorteras. Standard-rangordning används sedan för jämförelse prestanda på den [instrumentpanelen](#performance-dashboard).

Mer information om Feedformat finns i [API-referens för](custom-decision-service-api-reference.md#action-set-api-customer-provided).

## <a name="register-a-new-app"></a>Registrera en ny app

1. Logga in med ditt [Microsoft-konto](https://account.microsoft.com/account). Klicka på menyfliken **Mina Portal**.

2. Registrera ett nytt program genom att klicka på den **ny App** knappen.

    ![Custom Decision Service-portalen](./media/custom-decision-service-tutorial/portal.png)

3. Ange ett unikt namn för ditt program i den **App-ID** textruta. Om det här namnet används redan av en annan kund, tillfrågas du om du vill välja ett annat app-ID. Välj den **Avancerat** kryssrutan och ange den [anslutningssträngen](../../storage/common/storage-configure-connection-string.md) för Azure storage-konto. Normalt sett kan du använda samma lagringskonto för dina program.

    ![Dialogrutan Ny app](./media/custom-decision-service-tutorial/new-app-dialog.png)

    När du har registrerat alla tre programmen i scenariot ovan visas de:

    ![Lista över appar](./media/custom-decision-service-tutorial/apps.png)

    Du kan gå tillbaka till den här listan genom att klicka på den **appar** knappen.

4. I den **ny App** dialogrutan anger du en åtgärd-feed. Åtgärden feeds kan också anges genom att klicka på den **Feeds** knappen och sedan genom att klicka på den **nya Feed** knappen. Ange en **namn** ny feed, ange den **URL** från som den hanteras och ange den **uppdatera tid**. Uppdateringstid anger hur ofta anpassad beslut tjänsten bör uppdatera denna feed.

    ![Dialogrutan för nytt feed](./media/custom-decision-service-tutorial/new-feed-dialog.png)

    Åtgärden feeds kan användas av någon app, oavsett om de angivna. När du har angett både åtgärden feeds i ett scenario som de visas:

    ![Lista över feeds](./media/custom-decision-service-tutorial/feeds.png)

    Du kan gå tillbaka till den här listan genom att klicka på den **Feeds** knappen.

## <a name="use-the-apis"></a>API: er i

Tjänsten anpassad beslut rangordnar artiklar via API: et rangordning. Infoga följande kod i HTML-huvudet på främre sidan om du vill använda den här API:

```html
<!-- Define the "callback function" to render UI -->
<script> function callback(data) { … } </script>

<!--  call Ranking API after callback() is defined, separately for each app -->
<script src="https://ds.microsoft.com/api/v2/app-politics/rank/feed-politics" async></script>
<script src="https://ds.microsoft.com/api/v2/app-sports/rank/feed-sports" async></script>
<script src="https://ds.microsoft.com/api/v2/app-recent/rank/feed-politics/feed-sports" async></script>
<!-- NB: action feeds for 'app-recent' are listed one after another. -->
```

HTTP-svaret från rangordning API är en hanteras JSONP-formaterad sträng. För app-politik, till exempel strängen ser ut som:

```json
callback({
   "ranking":[{"id":"url1"}, {"id":"url2"}, {"id":"url3"}],
   "eventId":"<opaque event string>",
   "appId":"app-politics",
   "actionSets":[{"id":"feed-politics","lastRefresh":"date"}] });
```

Webbläsaren körs sedan den här strängen som ett anrop till den `callback()` funktion. Den `data` argument i den `callback()` funktion innehåller app-ID och rangordning URL: er som ska renderas. I synnerhet `callback()` ska använda `data.appId` att skilja mellan de tre program. `eventId` används internt av anpassade beslut tjänsten så att den matchar den angivna rangordning motsvarande klickar eventuella.

> [!TIP]
> `callback()` kan du kontrollera varje åtgärd feed för dokumentens med hjälp av den `lastRefresh` fältet. Om en viss feed inte är tillräckligt ny `callback()` kan ignorera den tillhandahållna rangordningen, anropar denna feed direkt och använda standard rangordning som hanteras av feeden.

Mer information om specifikationer och ytterligare alternativ som tillhandahålls av rangordning-API finns i [API-referens för](custom-decision-service-api-reference.md).

Användaren Huvudartikel alternativen returneras genom att anropa API för ersättning. Följande kod ska aktiveras på sidan främre när Huvudartikel välja tas emot:

```javascript
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/<eventId>',
    contentType: "application/json" })
```

Med hjälp av `appId` och `eventId` i koden klickar du på hantering kräver viss precision. Du kan exempelvis implementera den `callback()` fungerar på följande sätt:

```javascript
function callback(data) {
    $.map(data.ranking, function (element) {
        //custom rendering function given content info
        render({appId:data.appId,
                article:element,
                onClick: element.id != data.rewardAction ? null :
                   function() {
                       $.ajax({
                       type: "POST",
                       url: '//ds.microsoft.com/api/v2/' + data.appId + '/reward/' + data.eventId,
                       contentType: "application/json" })}
                });
}}
```

I det här exemplet kan du implementera den `render()` funktionen för att återge identitetsnummer för ett visst program. Den här funktionen anger app-ID och artikel (i formatet från rangordning API). Den `onClick` parametern är den funktion som ska anropas från `render()` att hantera ett klick. Den kontrollerar om användaren klickar på den översta platsen. Anropar sedan ersättning API: et med lämpliga app-ID och händelse-ID.

## <a name="next-steps"></a>Nästa steg

* Läs den [API-referens](custom-decision-service-api-reference.md) lära dig mer om de angivna funktionerna.