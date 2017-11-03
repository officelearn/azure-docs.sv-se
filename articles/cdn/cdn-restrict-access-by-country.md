---
title: "Begränsa Azure CDN innehåll efter land | Microsoft Docs"
description: "Lär dig hur du begränsar åtkomsten till ditt Azure CDN-innehåll med hjälp av funktionen Geo-filtrering."
services: cdn
documentationcenter: 
author: lichard
manager: akucer
editor: 
ms.assetid: 12c17cc5-28ee-4b0b-ba22-2266be2e786a
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: rli
ms.openlocfilehash: 30160088d9c770400f342e67527e1cf1cabc4f6b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="restrict-azure-cdn-content-by-country"></a>Begränsa Azure CDN innehåll efter land

## <a name="overview"></a>Översikt
När en användare begär innehåll, som standard, hanteras innehållet oavsett om användaren har gjort denna begäran från. I vissa fall kanske du vill begränsa åtkomsten till ditt innehåll efter land. Det här avsnittet beskriver hur du använder den **Geo-filtrering** funktionen för att konfigurera tjänsten om du vill tillåta eller blockera åtkomst efter land.

> [!IMPORTANT]
> Verizon och Akamai produkter ge samma funktioner för filtrering av geo men har en liten skillnad i te landskoder som de stöder. En länk till skillnaderna finns i steg3.


Information om saker som gäller för att konfigurera den här typen av begränsning finns på [överväganden](cdn-restrict-access-by-country.md#considerations) avsnittet i slutet av ämnet.  

![Landsfiltrering](./media/cdn-filtering/cdn-country-filtering-akamai.png)

## <a name="step-1-define-the-directory-path"></a>Steg 1: Definiera katalogsökvägen
Välj din slutpunkt i portalen och hitta fliken Geo-filtrering på det vänstra navigeringsfönstret att hitta den här funktionen.

När du konfigurerar land-filtret, måste du ange den relativa sökvägen till den plats som användare beviljas eller nekas åtkomst. Du kan använda geo-filtrering för alla filer med ”/” eller valda mappar genom att ange katalogsökvägar ”/ bilder /”. Du kan också använda filtrering av geo-replikering till en enda fil genom att ange filen och lämnar ut avslutande snedstreck ”/ pictures/city.png”.

Exempel directory sökvägen filter:

    /                                 
    /Photos/
    /Photos/Strasbourg/
      /Photos/Strasbourg/city.png

## <a name="step-2-define-the-action-block-or-allow"></a>Steg 2: Definiera åtgärden: blockera eller tillåta
**Blockering:** användare från de angivna länderna nekas åtkomst till tillgångar som begärs från den rekursiva sökvägen. Om inga andra land filtreringsalternativ har konfigurerats för den platsen, sedan får alla andra användare åtkomst.

**Tillåt:** endast användare från de angivna länderna får åtkomst till tillgångar som begärs från den rekursiva sökvägen.

## <a name="step-3-define-the-countries"></a>Steg 3: Definiera länderna
Välj land som du vill blockera eller tillåta för sökvägen. 

Regeln för blockeringen /Photos/Strasbourg/kommer till exempel filtrera filer, inklusive:

    http://<endpoint>.azureedge.net/Photos/Strasbourg/1000.jpg
    http://<endpoint>.azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg


### <a name="country-codes"></a>Landskoder
Den **Geo-filtrering** funktionen använder landskoder för att definiera de länder där en begäran ska tillåtas eller blockeras för en skyddad katalog. Du hittar landskoder i [Azure CDN landskoder](https://msdn.microsoft.com/library/mt761717.aspx). 

## <a id="considerations"></a>Överväganden
* Det kan ta upp till 90 minuter för Verizon eller ett par minuter med Akamai, ändringar i ditt land filtrering konfigurationen ska börja gälla.
* Den här funktionen har inte stöd för jokertecken (till exempel ' *').
* Geo-filtrering konfigurationen som är associerade med den relativa sökvägen ska tillämpas rekursivt till denna sökväg.
* Endast en regel kan tillämpas på samma relativa sökväg (du kan skapa flera land filter som pekar på samma relativa sökväg. En mapp kan dock ha flera land filter. Detta beror på den rekursiva natur land filter. Med andra ord en undermapp till en tidigare konfigurerade mapp som kan tilldelas ett annat land filter.

