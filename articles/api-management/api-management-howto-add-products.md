---
title: Hur du skapar och publicera en produkt i Azure API Management
description: "Lär dig hur du skapar och publicerar produkter i Azure API Management."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 31de55cb-9384-490b-a2f2-6dfcf83da764
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 2cf905967f26de97613ff7d5fc495c9223e11390
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2017
---
# <a name="how-to-create-and-publish-a-product-in-azure-api-management"></a>Hur du skapar och publicera en produkt i Azure API Management
En produkt innehåller en eller flera API: er som en kvot för användning och villkor för användning i Azure API Management. När en produkt har publicerats kan utvecklare prenumererar på produkten och börjar använda produktens API: er. I avsnittet beskrivs hur du skapar en produkt, lägga till en API och publicera för utvecklare.

## <a name="create-product"></a>Skapar en produkt
Åtgärder läggs till och konfigurerats för att en API i portalen utgivare. För att komma åt publisher-portalen klickar du på **Publisher portal** i Azure Portal för API Management-tjänsten.

![Utgivarportalen][api-management-management-console]

> Om du inte har skapat en API Management-tjänstinstans än läser du [Skapa en API Management-tjänstinstans][Create an API Management service instance] i självstudiekursen [Komma igång med Azure API Management][Get started with Azure API Management].
> 
> 

Klicka på **produkter** på menyn till vänster för att visa den **produkter** och klicka på **lägga till produkten**.

![Produkter][api-management-products]

![Ny produkt][api-management-add-new-product]

Ange ett beskrivande namn för produkten i den **namn** fältet och en beskrivning av produkten i den **beskrivning** fältet.

Produkter i API-hantering kan vara **öppna** eller **skyddade**. Utvecklare måste prenumerera på skyddade produkter innan de kan användas. Öppna produkter kan användas utan en prenumeration. Kontrollera **kräver prenumeration** att skapa en skyddad produkt som kräver en prenumeration. Det här är standardinställningen.

Kontrollera **kräver godkännande för prenumerationen** om du vill att en administratör för att granska och godkänna eller avvisa prenumeration försöker den här produkten. Om rutan är avmarkerad att prenumeration försök automatiskt godkänd. Mer information om prenumerationer finns [visa prenumeranter för en produkt][View subscribers to a product].

För att tillåta developer konton att abonnera produkten flera gånger, kontrollera den **tillåter flera prenumerationer** kryssrutan. Om inte den här kryssrutan är markerad, kan varje utvecklarkonto prenumerera på bara en gång till produkten.

![Obegränsade flera prenumerationer][api-management-unlimited-multiple-subscriptions]

Om du vill begränsa antalet flera samtidiga prenumerationer, kontrollera den **begränsa antalet samtidiga prenumerationer** kryssrutan och ange gränsen. I följande exempel är samtidiga prenumerationer begränsade till fyra per utvecklarkonto.

![Fyra flera prenumerationer][api-management-four-multiple-subscriptions]

När alla nya produktalternativ för har konfigurerats, klickar du på **spara** att skapa den nya produkten.

![Produkter][api-management-products-page]

> Som standard nya produkter är opublicerad och visas bara för den **administratörer** grupp.
> 
> 

Om du vill konfigurera en produkt, klicka på Produktnamn i den **produkter** fliken.

## <a name="add-apis"></a>Lägg till API: er för en produkt
Den **produkter** sidan innehåller fyra länkar för konfiguration: **sammanfattning**, **inställningar**, **synlighet**, och **prenumeranter**. Den **sammanfattning** är där du kan lägga till API: er och publicera eller avpublicera en produkt.

![Sammanfattning][api-management-new-product-summary]

Du måste lägga till en eller flera API: er innan du publicerar produkten. Gör detta genom att klicka på **lägga till API för att produkten**.

![Lägg till API: er][api-management-add-apis-to-product]

Välj önskad API: er och klicka på **spara**.

## <a name="add-description"></a>Lägg till beskrivande information för en produkt
Den **inställningar** fliken kan du ange detaljerad information om produkten som sitt syfte, API: er som den ger åtkomst till och annan användbar information. Innehållet är riktad mot utvecklare som kommer att anropa API: et och kan skrivas i oformaterad text eller HTML-kod.

![Produktinställningar för][api-management-product-settings]

Kontrollera **kräver prenumeration** att skapa en skyddad produkt som kräver en prenumeration ska användas eller avmarkera kryssrutan om du vill skapa en öppen produkt som kan anropas utan en prenumeration.

Välj **kräver godkännande för prenumerationen** om du vill godkänna alla begäranden om produkten prenumeration manuellt. Som standard beviljas alla produktprenumeration automatiskt.

För att tillåta developer konton att abonnera produkten flera gånger, kontrollera den **tillåter flera prenumerationer** kryssrutan och om du vill ange en gräns. Om inte den här kryssrutan är markerad, kan varje utvecklarkonto prenumerera på bara en gång till produkten.

Om du vill fylla i den **användningsvillkoren** fält som beskriver villkor för användning av produkten som prenumeranter måste acceptera för att kunna använda produkten.

## <a name="publish-product"></a>Publicera en produkt
Produkten måste publiceras innan API: er i en produkt kan anropas. På den **sammanfattning** för produkten klickar du på **publicera**, och klicka sedan på **Ja, publicera den** att bekräfta. Klicka på att en tidigare publicerade produkt privata **Upphäv publicering**.

![Publicera produkten][api-management-publish-product]

## <a name="make-visible"></a>Att en produkt ska visas för utvecklare
Den **synlighet** fliken kan du välja vilka roller som kan se produkten på developer-portalen och prenumerera på produkten.

![Produkten synlighet][api-management-product-visiblity]

Om du vill aktivera eller inaktivera synligheten för en produkt för utvecklare i en grupp, markera eller avmarkera kryssrutan bredvid gruppen och klicka sedan på **spara**.

> Mer information finns i [hur du skapar och använda grupper för att hantera developer konton i Azure API Management][How to create and use groups to manage developer accounts in Azure API Management].
> 
> 

## <a name="view-subscribers"></a>Visa prenumeranter för en produkt
Den **prenumeranter** fliken Listar de utvecklare som prenumererar på produkten. Information och inställningar för varje utvecklare kan visas genom att klicka på utvecklarens namn. I det här exemplet har ännu inte utvecklare prenumererat på produkten.

![Utvecklare][api-management-developer-list]

## <a name="next-steps"> </a>Nästa steg
När önskade API: er och produkten publicerade utvecklare kan prenumerera på produkten och börja att anropa API: erna. En genomgång som visar dessa objekt samt avancerade produktkonfigurationen finns [hur skapar och konfigurerar produktinställningar för avancerad i Azure API Management][How create and configure advanced product settings in Azure API Management].

Mer information om hur du arbetar med produkter finns i följande videoklipp.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Using-Products/player]
> 
> 

[Create a product]: #create-product
[Add APIs to a product]: #add-apis
[Add descriptive information to a product]: #add-description
[Publish a product]: #publish-product
[Make a product visible to developers]: #make-visible
[View subscribers to a product]: #view-subscribers
[Next steps]: #next-steps

[api-management-management-console]: ./media/api-management-howto-add-products/api-management-management-console.png
[api-management-add-product]: ./media/api-management-howto-add-products/api-management-add-product.png
[api-management-add-new-product]: ./media/api-management-howto-add-products/api-management-add-new-product.png
[api-management-unlimited-multiple-subscriptions]: ./media/api-management-howto-add-products/api-management-unlimited-multiple-subscriptions.png
[api-management-four-multiple-subscriptions]: ./media/api-management-howto-add-products/api-management-four-multiple-subscriptions.png
[api-management-products-page]: ./media/api-management-howto-add-products/api-management-products-page.png
[api-management-new-product-summary]: ./media/api-management-howto-add-products/api-management-new-product-summary.png
[api-management-add-apis-to-product]: ./media/api-management-howto-add-products/api-management-add-apis-to-product.png
[api-management-product-settings]: ./media/api-management-howto-add-products/api-management-product-settings.png
[api-management-publish-product]: ./media/api-management-howto-add-products/api-management-publish-product.png
[api-management-product-visiblity]: ./media/api-management-howto-add-products/api-management-product-visibility.png
[api-management-developer-list]: ./media/api-management-howto-add-products/api-management-developer-list.png



[api-management-products]: ./media/api-management-howto-add-products/api-management-products.png
[api-management-]: ./media/api-management-howto-add-products/
[api-management-]: ./media/api-management-howto-add-products/


[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md
[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[Next steps]: #next-steps
[How to create and use groups to manage developer accounts in Azure API Management]: api-management-howto-create-groups.md
[How create and configure advanced product settings in Azure API Management]: api-management-howto-product-with-rules.md 
