---
title: "Ändra sidinnehåll på utvecklarportalen i Azure API Management | Microsoft Docs"
description: "Lär dig hur du redigerar sidinnehåll på utvecklarportalen i Azure API Management."
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
ms.openlocfilehash: 708c803c36c182ed90e04731b12d4ade00ae7ffb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="modify-the-content-and-layout-of-pages-on-the-developer-portal-in-azure-api-management"></a>Ändra innehåll och layout för sidorna på utvecklarportalen i Azure API Managment
Det finns tre grundläggande metoder för att anpassa utvecklarportalen i Azure API Management:

* [Redigera innehållet på statiska sidor och sidlayoutelement][modify-content-layout] (beskrivs i den här guiden)
* [Uppdatera formaten som används för sidelement i utvecklingsportalen][customize-styles]
* [Ändra mallarna som används för sidor som genereras av portalen][portal-templates] (t.ex. API-dokumentation, produkter, användarautentisering osv.)

## <a name="page-structure"> </a>Sidstruktur för utvecklarportalen

Utvecklarportalen baseras på ett innehållshanteringssystem. Sidlayouten bygger på en uppsättning mindre sidelement som kallas widgetar:

![Sidstruktur för utvecklarportalen][api-management-customization-widget-structure]

Alla widgetar kan redigeras. 
* Huvudinnehållet för varje specifik sida finns i innehållswidgeten. När du redigerar en sida redigerar du alltså innehållet i widgeten.
* Alla övriga layoutelement på sidan finns även de som widgetar. Om du gör en ändring i en sådan widget slår detta igenom på samtliga sidor. De kallas därför layoutwidgetar.

Under vardagligt redigeringsarbete gör man normalt sett bara ändringar i innehållswidgeten, som har olika innehåll för varje enskild sida.

## <a name="modify-layout-widget"> </a>Ändra innehållet i en layoutwidget

Du ändrar innehållet på utvecklarportalen från utgivarportalen, som du kommer åt via Azure Portal. För att nå den, klickar du på **utgivarportalen** i API Management-verktygsfältet.

![Utgivarportalen][api-management-management-console]

Om du vill redigera innehållet i widgeten klickar du på **Widgetar** i menyn **Utvecklarportal** till vänster. I det här exemplet ska vi ändra innehållet i en widget för sidhuvud. Välj widgeten **Sidhuvud** i listan.

![Widgeten Sidhuvud][api-management-widgets-header]

Du kan redigera innehållet i sidhuvudet i fältet **Brödtext**. Ändra till önskad text och klicka sedan på **Spara** längst ned på sidan.

Nu visas det nya sidhuvudet på varje sida på utvecklarportalen.

> Om du vill öppna utvecklarportalen från utgivarportalen klickar du på **Utvecklarportal** på den översta raden.
> 
> 

## <a name="edit-page-contents"> </a>Redigera innehållet på en sida

Om du vill visa en lista över alla befintliga innehållssidor klickar du på **Innehåll** på menyn **Utvecklarportal** på utgivarportalen.

![Hantera innehåll][api-management-customization-manage-content]

Klicka på **välkomstsidan** om du vill ändra vad som visas på utvecklarportalens startsida. Gör önskade ändringar, förhandsgranska dem om det behövs och klicka sedan på **Publicera nu** så att de visas för alla.

> Startsidan har en särskild layout och har plats för en banderoll längst upp. Det går inte att ändra banderollen från avsnittet **Innehåll**. Om du vill redigera banderollen klickar du på **Widgetar** på menyn **Utvecklarportal**, väljer **Startsida** i listrutan **Aktuellt skikt** och öppnar sedan objektet **Banderoll** under avsnittet **Aktuellt**. Du kan redigera innehållet i den här widgeten på samma sätt som andra sidor.
> 
> 

## <a name="next-steps"> </a>Nästa steg
* [Uppdatera formaten som används för sidelement i utvecklingsportalen][customize-styles]
* [Ändra mallarna som används för sidor som genereras av portalen][portal-templates] (t.ex. API-dokumentation, produkter, användarautentisering osv.)

[Structure of developer portal pages]: #page-structure
[Modifying the contents of a layout widget]: #modify-layout-widget
[Edit the contents of a page]: #edit-page-contents
[Next steps]: #next-steps

[modify-content-layout]: api-management-modify-content-layout.md
[customize-styles]: api-management-customize-styles.md
[portal-templates]: api-management-developer-portal-templates.md

[api-management-customization-widget-structure]: ./media/api-management-modify-content-layout/portal-widget-structure.png
[api-management-management-console]: ./media/api-management-modify-content-layout/api-management-management-console.png
[api-management-widgets-header]: ./media/api-management-modify-content-layout/api-management-widgets-header.png
[api-management-customization-manage-content]: ./media/api-management-modify-content-layout/api-management-customization-manage-content.png
