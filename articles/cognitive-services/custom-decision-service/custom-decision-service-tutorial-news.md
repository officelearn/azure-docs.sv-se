---
title: 'Självstudier: Artikelanpassning – Custom Decision Service'
titlesuffix: Azure Cognitive Services
description: En självstudiekurs för artikelanpassning för kontextuellt beslutsfattande.
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: tutorial
ms.date: 05/08/2018
ms.author: slivkins
ms.openlocfilehash: d8ddafe20ff93e7ae4d51e2180bbd40447729234
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60863396"
---
# <a name="tutorial-article-personalization-for-contextual-decision-making"></a>Självstudier: Artikelanpassning för kontextuellt beslutsfattande

Den här självstudiekursen fokserar på valet av artiklar på en webbplats startsida. Custom Decision Service påverkar t.ex. *flera* listor med artiklar på framsidan. Sidan är kanske en nyhetswebbplats som endast omfattar politik och sport. Då skulle den visa tre rangordnade listor med artiklar: politik, sport och senaste nytt.

## <a name="applications-and-action-sets"></a>Program och åtgärdsuppsättningar

Så här passar du in ditt scenario i ramen. Låt oss föreställa oss tre program, ett för varje lista som optimeras: app-politik, app-sport och app-senaste. Om du vill ange kandidatartiklarna för varje program, så finns det två åtgärdsuppsättningar: en för politik och en för sport. Åtgärdsuppsättningen som har angetts för app-senaste ger automatiskt en union av de andra två uppsättningarna.

> [!TIP]
> Åtgärdsuppsättningarna kan delas mellan program i Custom Decision Service.

## <a name="prepare-action-set-feeds"></a>Förbereda åtgärdsuppsättningsflöden

Custom Decision Service förbrukar åtgärdsuppsättningar via RSS- eller Atom-feeds som tillhandahålls av kunden. Du kan ange två feeds: en för politik och en för sport. Anta att de hanteras från `http://www.domain.com/feeds/<feed-name>`.

Varje flöde innehåller en lista över artiklar. I RSS anges var och en av ett `<item>`-element på följande sätt:

```xml
<rss version="2.0"><channel>
   <item>
      <title><![CDATA[article title]]></title>
      <link>"article url"</link>
      <pubDate>publication date</pubDate>
    </item>
</channel></rss>
```

Sorteringen av artiklarna är viktig. Den anger standardrangordningen, vilket är din kvalificerade gissning för hur artiklarna bör sorteras. Standardrangordningen används sedan till prestandajämförelser på instrumentpanelen.

Mer information om feedformatet finns i [API-referensen](custom-decision-service-api-reference.md#action-set-api-customer-provided).

## <a name="register-a-new-app"></a>Registrera en ny app

1. Logga in med ditt [Microsoft-konto](https://portal.ds.microsoft.com/). Klicka på **Min portal** i menyfliksområdet.

2. Registrera ett nytt program genom att klicka på knappen **Ny app**.

    ![Custom Decision Service-portalen](./media/custom-decision-service-tutorial/portal.png)

3. Ange ett unikt namn för ditt program i textrutan **App-ID**. Om det här namnet redan används av en annan kund ombeds du att välja ett annat app-ID. Markera kryssrutan **Avancerat** och ange [anslutningssträngen](../../storage/common/storage-configure-connection-string.md) för ditt Azure Storage-konto. I vanliga fall använder du samma lagringskonto för alla dina program.

    ![Dialogrutan Ny app](./media/custom-decision-service-tutorial/new-app-dialog.png)

    När du har registrerat alla tre program för scenariot ovan listas de:

    ![Lista över appar](./media/custom-decision-service-tutorial/apps.png)

    Du kan gå tillbaka till den här listan genom att klicka på knappen **Appar**.

4. Ange en åtgärdsfeed i dialogrutan **Ny app**. Du kan även ange åtgärdsfeeds genom att klicka på knappen **Feeds** och sedan klicka på knappen **Ny feed**. Ange en **namn** för den nya feeden, ange den **URL** från vilken den har hämtats och ange **uppdateringstid**. Uppdateringstiden anger hur ofta Custom Decision Service bör uppdatera feeden.

    ![Dialogrutan Ny feed](./media/custom-decision-service-tutorial/new-feed-dialog.png)

    Åtgärdfeeds kan användas av vilken app som helst, oavsett var de anges. När du har angett båda åtgärdsfeeden i ett scenario listas de:

    ![Feedlista](./media/custom-decision-service-tutorial/feeds.png)

    Du kan gå tillbaka till den här listan genom att klicka på knappen **Feeds**.

## <a name="use-the-apis"></a>Använd API:erna

Custom Decision Service rangordnar artiklar via rangordnings-API:et. Infoga följande kod i HTML-huvudet på framsidan om du vill använda det här API:et:

```html
<!-- Define the "callback function" to render UI -->
<script> function callback(data) { … } </script>

<!--  call Ranking API after callback() is defined, separately for each app -->
<script src="https://ds.microsoft.com/api/v2/app-politics/rank/feed-politics" async></script>
<script src="https://ds.microsoft.com/api/v2/app-sports/rank/feed-sports" async></script>
<script src="https://ds.microsoft.com/api/v2/app-recent/rank/feed-politics/feed-sports" async></script>
<!-- NB: action feeds for 'app-recent' are listed one after another. -->
```

HTTP-svaret från rangordnings-API:et är en JSONP-formaterad sträng. För app-politik ser strängen t.ex. ut så här:

```json
callback({
   "ranking":[{"id":"url1"}, {"id":"url2"}, {"id":"url3"}],
   "eventId":"<opaque event string>",
   "appId":"app-politics",
   "actionSets":[{"id":"feed-politics","lastRefresh":"date"}] });
```

Webbläsaren kör sedan den här strängen som ett anrop till `callback()`-funktionen. `data`-argumentet i `callback()`-funktionen innehåller app-ID:t och rangordningen av de URL:er som ska återges. I synnerhet `callback()` bör använda `data.appId` för att skilja mellan de tre apparna. `eventId` används internt av Custom Decision Service för att matcha den angivna rangordningen med motsvarande eventuell klickning.

> [!TIP]
> `callback()` kan kontrollera varje åtgärdsfeeds uppdateringsstatus med hjälp av `lastRefresh`-fältet. Om en viss feed inte är tillräckligt uppdaterat kan `callback()` ignorera den angivna rangordningen, anropa feeden direkt och använda standardrangordningen från feeden.

Mer information om specifikationer och ytterligare alternativ som tillhandahålls av rangordnings-API:et finns i [API-referensen](custom-decision-service-api-reference.md).

Användaren huvudartikelsalternativ returneras genom att belönings-API:et anropas. När ett huvudartikelsval tas emot bör följande kod anropas på framsidan:

```javascript
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/<eventId>',
    contentType: "application/json" })
```

Att använda `appId` och `eventId` i klickningshanteringskoden kräver viss varsamhet. Du kan t.ex. implementera `callback()`-funktionen på följande sätt:

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

I det här exemplet implementerar vi `render()`-funktionen för att återge en viss artikel för ett visst program. Den här funktionen matar in app-ID:t och artikeln (i formatet från rangordning-API:et). `onClick`-parametern är den funktion som bör anropas från `render()` att hantera en klickning. Den kontrollerar huruvida klickningen sker på den översta platsen. Sedan anropas utmärkelse-API:et med lämpligt app-ID och händelse-ID.

## <a name="next-steps"></a>Nästa steg

* Mer information om de tillhandahållna funktionerna finns i [API-referensen](custom-decision-service-api-reference.md).
