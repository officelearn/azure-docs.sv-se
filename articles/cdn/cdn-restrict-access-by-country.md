---
title: Begränsa Azure CDN innehåll efter land | Microsoft Docs
description: Lär dig mer om att begränsa åtkomsten efter land till ditt Azure CDN-innehåll med hjälp av funktionen geo-filtrering.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: 12c17cc5-28ee-4b0b-ba22-2266be2e786a
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2018
ms.author: v-deasim
ms.openlocfilehash: 661356aeb2369bc1bbddd6caee57b256dd9e1212
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36285023"
---
# <a name="restrict-azure-cdn-content-by-country"></a>Begränsa Azure CDN innehåll efter land

## <a name="overview"></a>Översikt
När en användare begär innehåll, som standard, hanteras innehållet oavsett var de användare som skickar begäran. Men i vissa fall kan vilja du begränsa åtkomsten till ditt innehåll efter land. Med den *geo-filtrering* -funktionen kan du skapa regler på specifika sökvägar på din CDN-slutpunkten för att tillåta eller blockera innehåll i valda länder.

> [!IMPORTANT]
> **Azure CDN Standard från Microsoft** profiler stöder inte sökväg-baserade geo-filtrering.
> 

## <a name="standard-profiles"></a>Standard-profiler
Procedurerna i det här avsnittet gäller **Azure CDN Standard från Akamai** och **Azure CDN Standard från Verizon** profiler endast. 

För **Azure CDN Premium från Verizon** profiler, måste du använda den **hantera** portalen för att aktivera geo-filtrering. Mer information finns i [Azure CDN Premium från Verizon profiler](#azure-cdn-premium-from-verizon-profiles).

### <a name="define-the-directory-path"></a>Ange sökvägen till katalogen
Markera CDN-slutpunkten i portalen för att komma åt funktionen geo-filtrering och sedan välja **Geo-filtrering** under inställningar i den vänstra menyn. 

![Standard GEO-filtrering](./media/cdn-filtering/cdn-geo-filtering-standard.png)

Från den **sökväg** anger den relativa sökvägen till den plats som användare beviljas eller nekas åtkomst. 

Du kan använda geo-filtrering för alla filer med en vanlig snedstreck (/) eller markera specifika mappar genom att ange katalogsökvägar (till exempel */pictures/*). Du kan också använda geo-filtrering för att en enda fil (till exempel */pictures/city.png*). Flera regler är tillåtna. När du anger en regel, visas en tom rad att ange nästa regel.

Till exempel är alla följande directory sökvägen filter giltiga:   
*/*                                 
*/Photos/*     
*/Photos/Strasbourg /*     
*/Photos/Strasbourg/City.PNG*

### <a name="define-the-type-of-action"></a>Definiera typ av åtgärd

Från den **åtgärd** väljer **Tillåt** eller **Block**: 

- **Tillåt**: endast användare från de angivna länderna tillåts åtkomst till tillgångar som begärs från den rekursiva sökvägen.

- **Blockera**: användare från de angivna länderna nekas åtkomst till tillgångar som begärs från den rekursiva sökvägen. Om inga andra land filtreringsalternativ har konfigurerats för den platsen, sedan får alla andra användare åtkomst.

Till exempel en geo-filtreringsregel för blockering av sökvägen */foton/Strasbourg/* filtrerar följande filer:     
*http://<endpoint>.azureedge.net/Photos/Strasbourg/1000.jpg*
*http://<endpoint>.azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg*

### <a name="define-the-countries"></a>Definiera länderna
Från den **LANDSKODER** väljer du land som du vill blockera eller tillåta för sökvägen. 

När du har valt länderna markerar **spara** att aktivera den nya regeln för filtrering av geo. 

![GEO-filtrering regler](./media/cdn-filtering/cdn-geo-filtering-rules.png)

### <a name="clean-up-resources"></a>Rensa resurser
Om du vill ta bort en regel väljer du det från listan på den **Geo-filtrering** sidan och väljer sedan **ta bort**.

## <a name="azure-cdn-premium-from-verizon-profiles"></a>Azure CDN Premium från Verizon profiler
För **för Azure CDN Premium från Verizon** användarprofiler, användargränssnittet för att skapa en regel för filtrering av geo skiljer sig:

1. På den översta menyn i Azure CDN-profilen väljer **hantera**.

2. Välj Verizon-portal **HTTP stora**och välj **land filtrering**.

    ![Standard GEO-filtrering](./media/cdn-filtering/cdn-geo-filtering-premium.png)

3. Välj **lägga till Filter för land**.

    Den **steg ett:** visas.

4. Ange sökvägen till katalogen, Välj **Block** eller **Lägg till**och välj **nästa**.

    Den **steg två:** visas. 

5. Välj ett eller flera länder i listan och sedan **Slutför** att aktivera regeln. 
    
    Den nya regeln visas i tabellen på den **land filtrering** sidan.

    ![GEO-filtrering regler](./media/cdn-filtering/cdn-geo-filtering-premium-rules.png)

### <a name="clean-up-resources"></a>Rensa resurser
I land filtrering tabellen, väljer du ikonen Ta bort bredvid en regel för att ta bort den eller redigeringsikonen om du vill ändra den.

## <a name="considerations"></a>Överväganden
* Ändringar i konfigurationen av geo-filtrering börjar inte gälla omedelbart:
   * För **Azure CDN Standard från Microsoft**-profiler slutförs spridningen vanligtvis inom tio minuter. 
   * För **Azure CDN Standard från Akamai**-profiler slutförs spridningen vanligtvis inom en minut. 
   * För **Azure CDN Standard från Verizon**- och **Azure CDN Premium från Verizon**-profiler slutförs spridningen vanligtvis inom 10 minuter. 
 
* Den här funktionen har inte stöd för jokertecken (t.ex. *).

* Geo-filtrering konfigurationen som är associerade med den relativa sökvägen är tillämpas rekursivt till denna sökväg.

* Endast en regel kan tillämpas på samma relativa sökväg. Du kan inte att skapa flera land filter som pekar på samma relativa sökväg. Eftersom land filter är rekursiv, kan en mapp ha flera land filter. Med andra ord en undermapp till en tidigare konfigurerade mapp som kan tilldelas ett annat land filter.

* Funktionen geo-filtrering använder landskoder för att definiera de länder där en begäran tillåts eller blockeras för en skyddad katalog. Men Akamai och Verizon profiler stöder de flesta av samma landskoder, finns det några skillnader. Mer information finns i [Azure CDN landskoder](https://msdn.microsoft.com/library/mt761717.aspx). 

