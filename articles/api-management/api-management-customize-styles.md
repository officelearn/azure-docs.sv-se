---
title: Anpassa utvecklarportalens format i Azure API Management | Microsoft Docs
description: "Lär dig hur du ändrar de format som används för en sida i utvecklarportalen i Azure API Management."
services: api-management
documentationcenter: 
author: antonba
manager: vlvinogr
editor: 
ms.assetid: 186128fe-41c0-4efb-9efe-2478ad4d103f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/09/2017
ms.author: antonba
translationtype: Human Translation
ms.sourcegitcommit: 336d4f80f0357796fb29eb9314c11edfce831a69
ms.openlocfilehash: bd08eb476a4bd7298c5650977b88ba0b24deddec
ms.lasthandoff: 02/23/2017


---
# <a name="customize-the-styling-of-the-developer-portal-in-azure-api-management"></a>Anpassa format för utvecklarportalen i Azure API Management
Det finns tre grundläggande metoder för att anpassa utvecklarportalen i Azure API Management:

* [Redigera innehållet på statiska sidor och sidlayoutelement][modify-content-layout]
* [Uppdatera formaten som används för sidelement i utvecklingsportalen][customize-styles] (förklaras i den här guiden)
* [Ändra mallarna som används för sidor som genereras av portalen][portal-templates] (t.ex. API-dokumentation, produkter, användarautentisering osv.)

## <a name="change-headers-styling"> </a>Ändra format för sidelement

Färger, teckensnitt, storlekar, avstånd och andra formatrelaterade element på en sida på portalen definieras av formatregler. 

Du redigerar formatreglerna från **utvecklarportalen** när du är inloggad som administratör. För att nå dit öppnar du först Azure Portal och klickar på **Utgivarportal** i API Management-verktygsfältet.

![Utgivarportalen][api-management-management-console]

Klicka sedan på **Utgivarportal** överst till höger. 

![Utvecklarportallänken på utgivarportalen][api-management-pp-dp-link]

Om du vill öppna verktygsfältet för anpassning drar du musen över anpassningsikonen (eller markerar den) och klickar på "format" på verktygsfältet.

![Knapp i verktygsfältet för anpassning][api-management-customization-toolbar-button]

Det finns två huvudsakliga sätt att redigera formatregler. Du kan leta i listan över alla formatreglerna som används någonstans som visas som standard och där ändra format efter behov. Du kan även välja **Select an element on the page** (Markera ett element på sidan) och klicka var som helst på sidan för att endast visa formatet för just det elementet.

![Verktygsfält för anpassning][api-management-customization-toolbar]

Klicka på alternativet **Select an element on the page** (Markera ett element på sidan) för det här exemplet.  Nu markeras elementen när du hovrar över dem med musen så att du ser vilket elements format som du kommer att redigera om du klickar. Dra musen över texten i rubriken (vanligtvis står företaget namn här) och klicka på den. En uppsättning namngivna och kategoriserade formatregler visas i formatredigeraren. Varje regel representerar en formategenskap för det valda elementet. Exempelvis har den markerade rubriktexten ovan storleken @font-size-h1, och namnet på teckensnittet med varianter är @headings-font-family.

> Om du är bekant med [bootstrap][bootstrap] (starttjänsten) så är dessa regler i själva verket [LESS-variabler][LESS variables] i starttemat som används på utvecklarportalen.
> 
> 

Nu ska vi ändra färg på rubriktexten. Välj posten i fältet **@headings-color** och skriv **#000000**. Det här är den hexadecimala koden för färgen svart. En rektangulär färgindikator bör visas i slutet av textrutan. Om du klickar på indikatorn visas en färgväljare och du kan välja en färg.

![Färgväljare][api-management-customization-toolbar-color-picker]

Ändringar förhandsvisas i realtid under tiden du gör dem men visas endast för administratörer. Om du vill att alla ska se ändringarna klickar du på knappen **Publicera** i formatredigeraren och bekräftar ändringarna.

![Publicera-menyn][api-management-customization-toolbar-publish-form]

> Om du vill ändra formatreglerna som tillämpas på andra objekt på sidan följer du samma steg som för sidhuvudet. Klicka på **Select an element on the page** (Markera ett element på sidan) i formatredigeraren, välj önskat element och börja redigera värdena för formatreglerna som visas på skärmen.
> 
> 


## <a name="next-steps"> </a>Nästa steg
* Läs om hur du kan anpassa innehållet på utvecklarportalens sidor med [utvecklarportalsmallar](api-management-developer-portal-templates.md).

[Change the styling of the headers]: #change-headers-styling
[Next steps]: #next-steps

[Azure Classic Portal]: https://manage.windowsazure.com/

[api-management-management-console]: ./media/api-management-customize-styles/api-management-management-console.png
[api-management-pp-dp-link]: ./media/api-management-customize-styles/api-management-pp-dp-link.png
[api-management-customization-toolbar-button]: ./media/api-management-customize-styles/api-management-customization-toolbar-button.png
[api-management-customization-toolbar]: ./media/api-management-customize-styles/api-management-customization-toolbar.png
[api-management-customization-toolbar-color-picker]: ./media/api-management-customize-styles/api-management-customization-toolbar-color-picker.png
[api-management-customization-toolbar-publish-form]: ./media/api-management-customize-styles/api-management-customization-toolbar-publish-form.png

[modify-content-layout]: api-management-modify-content-layout.md
[customize-styles]: api-management-customize-styles.md
[portal-templates]: api-management-developer-portal-templates.md

[bootstrap]: http://getbootstrap.com/
[LESS variables]: http://getbootstrap.com/css/

