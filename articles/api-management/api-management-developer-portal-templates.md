---
title: Anpassa utvecklarportalen för API Management med hjälp av mallar
titleSuffix: Azure API Management
description: Lär dig hur du anpassar utvecklarportalen för Azure API Management med hjälp av mallar.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: a195675b-f7d0-4fc9-90bf-860e6f17ccf7
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 7a8c348340be143f7059ce7e64a1c66b66074a45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430779"
---
# <a name="how-to-customize-the-azure-api-management-developer-portal-using-templates"></a>Anpassa utvecklarportalen för Azure API Management med hjälp av mallar

Det finns tre grundläggande metoder för att anpassa utvecklarportalen i Azure API Management:

* [Redigera innehållet på statiska sidor och sidlayoutelement][modify-content-layout]
* [Uppdatera formaten som används för sidelement i utvecklingsportalen][customize-styles]
* [Ändra mallarna som används för sidor som genereras av portalen (förklaras][portal-templates] i den här guiden)

Mallar används för att anpassa innehållet på systemgenererade utvecklarportalsidor (till exempel API-dokument, produkter, användarautentisering osv.). Med [dotliquid-syntax](http://dotliquidmarkup.org/) och en uppsättning lokaliserade strängresurser, ikoner och sidkontroller har du stor flexibilitet att konfigurera innehållet på sidorna som du vill.

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="developer-portal-templates-overview"></a>Översikt över utvecklarportalmallar

Redigeringsmallar görs från **utvecklarportalen** när de loggas in som administratör. Om du vill komma dit öppnar du först Azure-portalen och klickar på **utvecklarportalen** från tjänstverktygsfältet i DIN API Management-instans.

Om du vill komma åt utvecklarportalmallarna klickar du på anpassa ikonen till vänster för att visa **anpassningsmenyn**och klickar på Mallar .

![Mallar för utvecklarportal][api-management-customize-menu]

Malllistan visar flera kategorier av mallar som täcker de olika sidorna i utvecklarportalen. Varje mall är olika, men stegen för att redigera dem och publicera ändringarna är desamma. Om du vill redigera en mall klickar du på namnet på mallen.

![Mallar för utvecklarportal][api-management-templates-menu]

Om du klickar på en mall kommer du till utvecklarportalsidan som kan anpassas med mallen. I det här exemplet visas **mallen Produktlista.** **Mallen Produktlista** styr det område på skärmen som indikeras av den röda rektangeln.

![Mall för produktlista][api-management-developer-portal-templates-overview]

Vissa mallar, till exempel **mallarna för användarprofiler,** anpassar olika delar av samma sida.

![Mallar för användarprofiler][api-management-user-profile-templates]

Redigeraren för varje utvecklarportalmall har två avsnitt som visas längst ned på sidan. På vänster sida visas redigeringsfönstret för mallen och på höger sida visas mallens datamodell.

Mallredigeringsfönstret innehåller markeringen som styr utseendet och beteendet för motsvarande sida i utvecklarportalen. Markeringen i mallen använder syntaxen [DotLiquid.](http://dotliquidmarkup.org/) En populär redaktör för DotLiquid är [DotLiquid för designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers). Alla ändringar som görs i mallen under redigeringen visas i realtid i webbläsaren, men är inte synliga för kunderna förrän du [sparar](#to-save-a-template) och [publicerar](#to-publish-a-template) mallen.

![Mallmarkering][api-management-template]

Datafönstret **Mall** innehåller en guide till datamodellen för de entiteter som är tillgängliga för användning i en viss mall. Den här guiden visas genom att visa de realtidsdata som för närvarande visas i utvecklarportalen. Du kan expandera mallrutorna genom att klicka på rektangeln i det övre högra hörnet i **fönstret Malldata.**

![Malldatamodell][api-management-template-data]

I föregående exempel visas två produkter i utvecklarportalen som hämtades från data som visas i **datafönstret mall,** vilket visas i följande exempel:

```json
{
    "Paging": {
        "Page": 1,
        "PageSize": 10,
        "TotalItemCount": 2,
        "ShowAll": false,
        "PageCount": 1
    },
    "Filtering": {
        "Pattern": null,
        "Placeholder": "Search products"
    },
    "Products": [
        {
            "Id": "56ec64c380ed850042060001",
            "Title": "Starter",
            "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",
            "Terms": "",
            "ProductState": 1,
            "AllowMultipleSubscriptions": false,
            "MultipleSubscriptionsCount": 1
        },
        {
            "Id": "56ec64c380ed850042060002",
            "Title": "Unlimited",
            "Description": "Subscribers have completely unlimited access to the API. Administrator approval is required.",
            "Terms": null,
            "ProductState": 1,
            "AllowMultipleSubscriptions": false,
            "MultipleSubscriptionsCount": 1
        }
    ]
}
```

Pålägget i **mallen Produktlista** bearbetar data för att ge önskad utdata genom att iterera genom insamling av produkter för att visa information och en länk till varje enskild produkt. Observera `<search-control>` elementen och `<page-control>` i markeringen. Dessa styr visningen av sök- och växlingskontrollerna på sidan. `ProductsStrings|PageTitleProducts`är en lokaliserad strängreferens `h2` som innehåller sidhuvudtexten. En lista över strängresurser, sidkontroller och ikoner som är tillgängliga för användning i utvecklarportalmallar finns i [API Management developer portal templates reference](api-management-developer-portal-templates-reference.md).

```html
<search-control></search-control>
<div class="row">
    <div class="col-md-9">
        <h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>
    </div>
</div>
<div class="row">
    <div class="col-md-12">
    {% if products.size > 0 %}
    <ul class="list-unstyled">
    {% for product in products %}
        <li>
            <h3><a href="/products/{{product.id}}">{{product.title}}</a></h3>
            {{product.description}}
        </li>
    {% endfor %}
    </ul>
    <paging-control></paging-control>
    {% else %}
    {% localized "CommonResources|NoItemsToDisplay" %}
    {% endif %}
    </div>
</div>
```

## <a name="to-save-a-template"></a>Så här sparar du en mall
Om du vill spara en mall klickar du på Spara i mallredigeraren.

![Spara mallen][api-management-save-template]

Sparade ändringar visas inte i utvecklarportalen förrän de har publicerats.

## <a name="to-publish-a-template"></a>Så här publicerar du en mall
Sparade mallar kan publiceras antingen individuellt eller tillsammans. Om du vill publicera en enskild mall klickar du på Publicera i mallredigeraren.

![Publicera mall][api-management-publish-template]

Klicka på **Ja** för att bekräfta och göra mallen live på utvecklarportalen.

![Bekräfta publicering][api-management-publish-template-confirm]

Om du vill publicera alla opublicerade mallversioner klickar du på **Publicera** i malllistan. Opublicerade mallar betecknas med en asterisk som följer mallnamnet. I det här exemplet publiceras **produktlistan** och **produktmallarna.**

![Publicera mallar][api-management-publish-templates]

Klicka på **Publicera anpassningar** för att bekräfta.

![Bekräfta publicering][api-management-publish-customizations]

Nyligen publicerade mallar träder omedelbart i utvecklarportalen.

## <a name="to-revert-a-template-to-the-previous-version"></a>Så här återställer du en mall till den tidigare versionen
Om du vill återställa en mall till den tidigare publicerade versionen klickar du på Återställ i mallredigeraren.

![Återställ mall][api-management-revert-template]

Bekräfta genom att klicka på **Ja**.

![Bekräfta][api-management-revert-template-confirm]

Den tidigare publicerade versionen av en mall finns i utvecklarportalen när återställningen är klar.

## <a name="to-restore-a-template-to-the-default-version"></a>Så här återställer du en mall till standardversionen
Att återställa mallar till standardversionen är en tvåstegsprocess. Först måste mallarna återställas och sedan måste de återställda versionerna publiceras.

Om du vill återställa en enda mall till standardversionen klickar du på återställ i mallredigeraren.

![Återställ mall][api-management-reset-template]

Bekräfta genom att klicka på **Ja**.

![Bekräfta][api-management-reset-template-confirm]

Om du vill återställa alla mallar till standardversionerna klickar du på **Återställ standardmallar** i malllistan.

![Återställa mallar][api-management-restore-templates]

De återställda mallarna måste sedan publiceras individuellt eller alla på en gång genom att följa stegen i [Publicera en mall](#to-publish-a-template).

## <a name="next-steps"></a>Nästa steg
Referensinformation för utvecklarportalmallar, strängresurser, ikoner och sidkontroller finns i [API Management developer portal templates reference](api-management-developer-portal-templates-reference.md).

[modify-content-layout]: api-management-modify-content-layout.md
[customize-styles]: api-management-customize-styles.md
[portal-templates]: api-management-developer-portal-templates.md

[api-management-customize-menu]: ./media/api-management-developer-portal-templates/api-management-customize-menu.png
[api-management-templates-menu]: ./media/api-management-developer-portal-templates/api-management-templates-menu.png
[api-management-developer-portal-templates-overview]: ./media/api-management-developer-portal-templates/api-management-developer-portal-templates-overview.png
[api-management-template]: ./media/api-management-developer-portal-templates/api-management-template.png
[api-management-template-data]: ./media/api-management-developer-portal-templates/api-management-template-data.png
[api-management-developer-portal-menu]: ./media/api-management-developer-portal-templates/api-management-developer-portal-menu.png
[api-management-management-console]: ./media/api-management-developer-portal-templates/api-management-management-console.png
[api-management-browse]: ./media/api-management-developer-portal-templates/api-management-browse.png
[api-management-user-profile-templates]: ./media/api-management-developer-portal-templates/api-management-user-profile-templates.png
[api-management-save-template]: ./media/api-management-developer-portal-templates/api-management-save-template.png
[api-management-publish-template]: ./media/api-management-developer-portal-templates/api-management-publish-template.png
[api-management-publish-template-confirm]: ./media/api-management-developer-portal-templates/api-management-publish-template-confirm.png
[api-management-publish-templates]: ./media/api-management-developer-portal-templates/api-management-publish-templates.png
[api-management-publish-customizations]: ./media/api-management-developer-portal-templates/api-management-publish-customizations.png
[api-management-revert-template]: ./media/api-management-developer-portal-templates/api-management-revert-template.png
[api-management-revert-template-confirm]: ./media/api-management-developer-portal-templates/api-management-revert-template-confirm.png
[api-management-reset-template]: ./media/api-management-developer-portal-templates/api-management-reset-template.png
[api-management-reset-template-confirm]: ./media/api-management-developer-portal-templates/api-management-reset-template-confirm.png
[api-management-restore-templates]: ./media/api-management-developer-portal-templates/api-management-restore-templates.png
