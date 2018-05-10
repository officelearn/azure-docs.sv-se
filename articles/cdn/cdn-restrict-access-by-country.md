---
title: Begränsa Azure CDN innehåll efter land | Microsoft Docs
description: Lär dig hur du begränsar åtkomsten till ditt Azure CDN-innehåll med hjälp av funktionen geo-filtrering.
services: cdn
documentationcenter: ''
author: lichard
manager: akucer
editor: ''
ms.assetid: 12c17cc5-28ee-4b0b-ba22-2266be2e786a
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: rli
ms.openlocfilehash: bb757ab115d03ab04dac4468d23f446696a971a9
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="restrict-azure-cdn-content-by-country"></a>Begränsa Azure CDN innehåll efter land

## <a name="overview"></a>Översikt
När en användare begär innehåll, som standard, hanteras innehållet oavsett om användaren har gjort denna begäran från. I vissa fall kanske du vill begränsa åtkomsten till ditt innehåll efter land. Den här artikeln förklarar hur du använder den *geo-filtrering* funktionen för att konfigurera tjänsten om du vill tillåta eller blockera åtkomst efter land.

> [!IMPORTANT]
> Azure CDN-produkter alla ge samma funktioner för filtrering av geo men har en liten skillnad i te landskoder som de stöder. En länk till skillnaderna finns i steg3.


Information om saker som gäller för att konfigurera den här typen av begränsning finns [överväganden](cdn-restrict-access-by-country.md#considerations).  

![Landsfiltrering](./media/cdn-filtering/cdn-country-filtering-akamai.png)

## <a name="step-1-define-the-directory-path"></a>Steg 1: Definiera katalogsökvägen
> [!IMPORTANT]
> **Azure CDN Standard från Microsoft** profiler stöder inte sökväg-baserade geo-filtrering.
>


Välj din slutpunkt i portalen och hitta fliken Geo-filtrering på det vänstra navigeringsfönstret att hitta den här funktionen.

När du konfigurerar land-filtret, måste du ange den relativa sökvägen till den plats som användare beviljas eller nekas åtkomst. Du kan använda geo-filtrering för alla filer med ett snedstreck (/) eller valda mappar genom att ange katalogsökvägar */pictures/*. Du kan också använda filtrering av geo-replikering till en enda fil genom att ange filen och lämnar ut det avslutande snedstrecket */pictures/city.png*.

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
Funktionen geo-filtrering använder landskoder för att definiera de länder där en begäran ska tillåtas eller blockeras för en skyddad katalog. Alla Azure CDN produkter ge samma funktioner för filtrering av geo, finns det en liten skillnad i landskoder som de stöder. Mer information finns i [Azure CDN landskoder](https://msdn.microsoft.com/library/mt761717.aspx). 

## <a name="considerations"></a>Överväganden
* Ändringar i konfigurationen av filtrering land börjar inte gälla omedelbart:
   * För **Azure CDN Standard från Microsoft** profiler, spridningen vanligtvis är klar på tio minuter. 
   * För **Azure CDN Standard från Akamai** profiler, spridningen vanligtvis slutförs inom en minut. 
   * För **Azure CDN Standard från Verizon** och **Azure CDN Premium från Verizon** profiler, spridningen vanligtvis har slutförts inom 90 minuter.  
* Den här funktionen har inte stöd för jokertecken (till exempel ' *').
* Geo-filtrering konfigurationen som är associerade med den relativa sökvägen ska tillämpas rekursivt till denna sökväg.
* Endast en regel kan tillämpas på samma relativa sökväg (du kan skapa flera land filter som pekar på samma relativa sökväg. En mapp kan dock ha flera land filter. Detta beror på den rekursiva natur land filter. Med andra ord en undermapp till en tidigare konfigurerade mapp som kan tilldelas ett annat land filter.

