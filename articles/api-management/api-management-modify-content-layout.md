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
ms.openlocfilehash: bcf48ab8dd3b57ace70fa713074b13a992940002
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
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

Developer-portalen är tillgänglig på Azure Portal. 

1. Klicka på **utgivarportalen** i API Management-instansens verktygsfält.
2. Om du vill redigera innehållet i widgetar klickar du på ikonen som består av två penslar på menyn **Utvecklare** till vänster på portalen. 
3. Om du vill ändra rubrikens innehåll bläddrar du till avsnittet **Rubrik** på listan till vänster.
    
    Widgetarna går att redigera inifrån fälten.
4. När du är redo att publicera ändringarna klickar du på **Publicera** längst ned på sidan.

Nu visas det nya sidhuvudet på varje sida på utvecklarportalen.

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
