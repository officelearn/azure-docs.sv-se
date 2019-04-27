---
title: Anpassa utvecklarportalen API Management med hjälp av mallar – Azure | Microsoft Docs
description: Lär dig hur du anpassar utvecklarportalen Azure API Management med hjälp av mallar.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: a195675b-f7d0-4fc9-90bf-860e6f17ccf7
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: 00d5e3df78e85d19a519786dad1a1b176ad7fa08
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60837257"
---
# <a name="how-to-customize-the-azure-api-management-developer-portal-using-templates"></a>Hur du anpassar utvecklarportalen Azure API Management med hjälp av mallar

Det finns tre grundläggande metoder för att anpassa utvecklarportalen i Azure API Management:

* [Redigera innehållet på statiska sidor och sidlayoutelement][modify-content-layout]
* [Uppdatera formaten som används för sidelement i utvecklingsportalen][customize-styles]
* [Ändra mallarna som används för sidor som genereras av portalen] [ portal-templates] (förklaras i den här guiden)

Mallar för att anpassa innehållet på systemgenererade utvecklarportalens sidor (till exempel API-dokumentation, produkter, användarautentisering osv.). Med hjälp av [DotLiquid](http://dotliquidmarkup.org/) syntax och en angiven uppsättning Lokaliserade strängresurser, ikoner och kontroller, har bra möjlighet att konfigurera innehållet i sidorna som du vill.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="developer-portal-templates-overview"></a>Översikt över Developer portal mallar

Redigera mallar gör det från den **utvecklarportalen** när du är inloggad som administratör. Att ta dig dit först öppna Azure Portal och klicka på **utvecklarportalen** i API Management-verktygsfältet.

För att komma åt utvecklarportalsmallar, klickar du på ikonen anpassa vänster för att visa menyn för anpassning och klickar på **mallar**.

![Utvecklarportalsmallar][api-management-customize-menu]

Lista över mallar visas flera kategorier av mallar som täcker de olika sidorna på utvecklarportalen. Varje mall är olika, men stegen för att redigera dem och publicera ändringar är desamma. Om du vill redigera en mall klickar du på namnet på mallen.

![Utvecklarportalsmallar][api-management-templates-menu]

Klicka på en mall går du till developer-Portalsida som kan anpassas av mallen. I det här exemplet på **produktlista** mallen visas. Den **produktlista** mallen styr område på skärmen som anges med röd rektangel.

![Produkter listmall][api-management-developer-portal-templates-overview]

Vissa mallar, som den **användarprofil** mallar och anpassa olika delar av samma sida.

![Användaren profilmallar][api-management-user-profile-templates]

Redigeraren för varje mall för utvecklare har två avsnitt visas längst ned på sidan. Till vänster visar redigeringsfönstret för mallen och till höger visar datamodellen för mallen.

Redigera fönstret mallen innehåller koden som styr utseendet och beteendet för den motsvarande sidan i utvecklarportalen. Koden i mallen använder den [DotLiquid](http://dotliquidmarkup.org/) syntax. En populär Redigeraren för DotLiquid är [DotLiquid för Designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers). Ändringar som görs i mallen när du redigerar visas i realtid i webbläsaren, men är inte synlig för kunderna förrän du [spara](#to-save-a-template) och [publicera](#to-publish-a-template) mallen.

![Mallkod][api-management-template]

Den **malldata** fönstret innehåller en guide till datamodellen för de entiteter som är tillgängliga för användning i en viss mall. Det ger den här guiden genom att visa realtidsdata som för närvarande visas i developer-portalen. Du kan expandera fönstren mallen genom att klicka på rektangeln i det övre högra hörnet av den **malldata** fönstret.

![Mall-datamodell][api-management-template-data]

I föregående exempel finns två produkter som visas i developer-portalen och som har hämtats från data som visas i den **malldata** som visas i följande exempel:

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

Markeringen i den **produktlista** mall bearbetar data för att tillhandahålla önskade utdata genom att gå igenom samlingen med produkter att visa information och en länk för varje enskild produkt. Obs den `<search-control>` och `<page-control>` element i koden. Dessa styr visningen av att söka efter och växling kontrollerna på sidan. `ProductsStrings|PageTitleProducts` är en referens för lokaliserad sträng som innehåller den `h2` rubriktext för sidan. En lista över strängresurser, kontroller och ikoner som är tillgängliga för användning i utvecklarportalsmallar finns i [Mallreferens för API Management developer portal](api-management-developer-portal-templates-reference.md).

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

## <a name="to-save-a-template"></a>Spara en mall
Om du vill spara en mall klickar du på Spara i Redigeraren för mallen.

![Spara mallen][api-management-save-template]

Sparade ändringar är inte aktiva i developer-portalen förrän de har publicerats.

## <a name="to-publish-a-template"></a>Publicera en mall
Sparade mallar kan publiceras antingen individuellt eller allt på samma plats. Om du vill publicera en mall för enskilda, klicka på Publicera i Redigeraren för mallen.

![Publicera mall][api-management-publish-template]

Klicka på **Ja** att bekräfta och att mallen publicerats på developer-portalen.

![Bekräfta att publicera][api-management-publish-template-confirm]

Om du vill publicera alla för tillfället opublicerade mallversioner **publicera** i listan över mallar. Opublicerade mallar visas med en asterisk som följer mallnamnet. I det här exemplet på **produktlista** och **produkten** mallar kommer att publiceras.

![Publicera mallar][api-management-publish-templates]

Klicka på **publicera anpassningar** att bekräfta.

![Bekräfta att publicera][api-management-publish-customizations]

Nyligen publicerade mallarna är effektiva omedelbart i developer-portalen.

## <a name="to-revert-a-template-to-the-previous-version"></a>Återställa en mall till föregående version
Om du vill återställa en mall för att den tidigare publicerade versionen klickar du på Återställ i Redigeraren för mallen.

![Återställ mall][api-management-revert-template]

Bekräfta genom att klicka på **Ja**.

![Bekräfta][api-management-revert-template-confirm]

Den tidigare publicerade versionen av en mall är live i developer-portalen när återställningen är klar.

## <a name="to-restore-a-template-to-the-default-version"></a>Återställa en mall till standardversion
Återställa mallar till sina standardversionen är en tvåstegsprocess. Först mallarna måste återställas och sedan de återställda versionerna måste publiceras.

Om du vill återställa en enda mall till standardversionen klickar du på Återställ i Redigeraren för mallen.

![Återställ mall][api-management-reset-template]

Bekräfta genom att klicka på **Ja**.

![Bekräfta][api-management-reset-template-confirm]

Om du vill återställa alla mallar till standardversioner, klickar du på **återställa standardmallarna** på listan mall.

![Återställ mallar][api-management-restore-templates]

Återställda mallarna måste sedan publiceras individuellt eller på en gång genom att följa stegen i [att publicera en mall](#to-publish-a-template).

## <a name="next-steps"></a>Nästa steg
Referensinformation för utvecklarportalsmallar, strängresurser, ikoner och kontroller, se [Mallreferens för API Management developer portal](api-management-developer-portal-templates-reference.md).

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
