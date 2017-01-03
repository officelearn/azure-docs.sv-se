---
title: Anpassa utvecklarportalen i Azure API Management | Microsoft Docs
description: "Lär dig hur du anpassar utvecklarportalen i Azure API Management."
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 186128fe-41c0-4efb-9efe-2478ad4d103f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/15/2016
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: 30ec6f45da114b6c7bc081f8a2df46f037de61fd
ms.openlocfilehash: cbd2c3e915b93340c1a1478c09b23480c4565a98


---
# <a name="customize-the-developer-portal-in-azure-api-management"></a>Anpassa utvecklarportalen i Azure API Management
I den här guiden lär du dig hur du anpassar utvecklarportalen i Azure API Management till ditt varumärke.

## <a name="change-page-headers"> </a>Ändra text eller logotyp i sidhuvudet
En av de viktigaste aspekterna när du anpassar portalen är att ersätta texten längst upp på alla sidor med ditt företagsnamn eller en logotyp.

Du ändrar innehållet på utvecklarportalen från utgivarportalen, som du kommer åt via Azure Portal. För att nå den, klickar du på **utgivarportalen** i API Management-verktygsfältet.

![Utgivarportalen][api-management-management-console]

Utvecklarportalen baseras på ett innehållshanteringssystem eller CMS. Sidhuvudet som visas på varje sida är en särskild typ av innehåll kallat en widget. Om du vill redigera innehållet i widgeten klickar du på **Widgetar** från menyn **Utvecklarportal** till vänster och väljer sedan widgeten **Sidhuvud** i listan.

![Widgeten Sidhuvud][api-management-widgets-header]

Du kan redigera innehållet i sidhuvudet i fältet **Brödtext**. Ändra texten till ”Fabrikam Developer Portal” och klicka sedan på **Spara** längst ned på sidan.

Nu visas det nya sidhuvudet på varje sida på utvecklarportalen.

> Om du vill öppna utvecklarportalen från utgivarportalen klickar du på **Utvecklarportal** på den översta raden.
> 
> 

## <a name="change-headers-styling"> </a>Ändra formateringen i sidhuvudena
Färger, teckensnitt, storlekar, avstånd och andra formatrelaterade element på en sida på portalen definieras av formatregler. Om du vill redigera formaten klickar du på **Utseende** på menyn **Utvecklarportal** på utgivarportalen och aktiverar sedan formatredigeraren genom att klicka på **Börja anpassning**.

Webbläsaren växlar till en dold sida på utvecklarportalen som innehåller prover på innehåll, med exempel på alla formatregler som används på webbplatsen. Du öppnar formatredigeraren genom att placera pekaren över den tunna grå lodräta linjen längst till vänster på sidan. Verktygsfältet för redigeraren bör visas.

![Verktygsfält för anpassning][api-management-customization-toolbar]

Du kan välja mellan två huvudlägen när du redigerar formatregler. Med **Redigera alla regler** kan du visa en lista över alla formatregler som används och med **Välj ett element** kan du välja ett element på den sida som du befinner dig på, så visas formaten för endast det elementet.

I det här avsnittet vill vi bara ändra formatet på rubrikerna. Klicka på alternativet **Välj ett element** i verktygsfältet i formatredigeraren och klicka sedan på **Markera ett element som du vill anpassa**. Nu markeras elementen när du hovrar över dem med musen så att du ser vilket elements format som du kommer att redigera om du klickar. Flytta muspekaren över texten som representerar företagets namn i sidhuvudet (”Fabrikam Developer Portal” om du följde anvisningarna i föregående avsnitt) och klicka på den. En uppsättning namngivna och kategoriserade formatregler visas i formatredigeraren.

Varje regel representerar en formategenskap för det valda elementet. Exempelvis har den markerade rubriktexten ovan storleken @font-size-h1, och namnet på teckensnittet med varianter är @headings-font-family.

> Om du är bekant med [bootstrap][bootstrap] (starttjänsten) så är dessa regler i själva verket [LESS-variabler][LESS variables] i starttemat som används på utvecklarportalen.
> 
> 

Nu ska vi ändra färg på rubriktexten. Välj posten i fältet **@headings-color** och skriv **#000000**. Det här är den hexadecimala koden för färgen svart. En rektangulär färgindikator bör visas i slutet av textrutan. Om du klickar på indikatorn visas en färgväljare och du kan välja en färg.

![Färgväljare][api-management-customization-toolbar-color-picker]

När du har gjort önskade formatändringar för det valda elementet klickar du på **Förhandsgranska ändringar** så visas resultaten på skärmen. Än så länge visas ändringarna bara för administratörer. Om du vill att alla ska se ändringarna klickar du på knappen **Publicera** i formatredigeraren och bekräftar ändringarna.

![Publicera-menyn][api-management-customization-toolbar-publish-form]

> Om du vill ändra formatreglerna som tillämpas på andra objekt på sidan följer du samma steg som för sidhuvudet. Klicka på **Välj ett element** i formatredigeraren, välj önskat element och börja redigera värdena för formatreglerna som visas på skärmen.
> 
> 

## <a name="edit-page-contents"> </a>Redigera innehållet på en sida
Utvecklarportalen består av automatiskt genererade sidor som API:er, Produkter, Program, Problem och manuellt skrivet innehåll. Eftersom den baseras på ett innehållshanteringssystem kan du skapa innehåll efter behov.

Om du vill visa en lista över alla befintliga innehållssidor klickar du på **Innehåll** på menyn **Utvecklarportal** på utgivarportalen.

![Hantera innehåll][api-management-customization-manage-content]

Klicka på **välkomstsidan** om du vill ändra vad som visas på utvecklarportalens startsida. Gör önskade ändringar, förhandsgranska dem om det behövs och klicka sedan på **Publicera nu** så att de visas för alla.

> Startsidan har en särskild layout och har plats för en banderoll längst upp. Det går inte att ändra banderollen från avsnittet **Innehåll**. Om du vill redigera banderollen klickar du på **Widgetar** på menyn **Utvecklarportal**, väljer **Startsida** i listrutan **Aktuellt skikt** och öppnar sedan objektet **Banderoll** under avsnittet **Aktuellt**. Du kan redigera innehållet i den här widgeten på samma sätt som andra sidor.
> 
> 

## <a name="next-steps"> </a>Nästa steg
* Läs om hur du kan anpassa innehållet på utvecklarportalens sidor med [utvecklarportalsmallar](api-management-developer-portal-templates.md).

[Change the text/logo in the page headers]: #change-page-headers
[Change the styling of the headers]: #change-headers-styling
[Edit the contents of a page]: #edit-page-contents
[Next steps]: #next-steps

[Azure Classic Portal]: https://manage.windowsazure.com/

[api-management-management-console]: ./media/api-management-customize-portal/api-management-management-console.png
[api-management-widgets-header]: ./media/api-management-customize-portal/api-management-widgets-header.png
[api-management-customization-toolbar]: ./media/api-management-customize-portal/api-management-customization-toolbar.png
[api-management-customization-toolbar-color-picker]: ./media/api-management-customize-portal/api-management-customization-toolbar-color-picker.png
[api-management-customization-toolbar-publish-form]: ./media/api-management-customize-portal/api-management-customization-toolbar-publish-form.png
[api-management-customization-manage-content]: ./media/api-management-customize-portal/api-management-customization-manage-content.png


[bootstrap]: http://getbootstrap.com/
[LESS variables]: http://getbootstrap.com/css/



<!--HONumber=Jan17_HO1-->


