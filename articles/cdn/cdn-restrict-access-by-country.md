---
title: Begränsa Azure CDN-innehåll per land/region | Microsoft-dokument
description: Lär dig hur du begränsar åtkomsten per land/region till ditt Azure CDN-innehåll med hjälp av geofiltreringsfunktionen.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: 12c17cc5-28ee-4b0b-ba22-2266be2e786a
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2018
ms.author: allensu
ms.openlocfilehash: 7ae7224efdaa281106dfbe2118ab0092c8284c6e
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260164"
---
# <a name="restrict-azure-cdn-content-by-countryregion"></a>Begränsa Azure CDN-innehåll efter land/region

## <a name="overview"></a>Översikt
När en användare begär ditt innehåll, som standard, visas innehållet oavsett var användaren gör begäran. I vissa fall kanske du vill begränsa åtkomsten till ditt innehåll efter land/region. Med *geofiltreringsfunktionen* kan du skapa regler för specifika sökvägar på CDN-slutpunkten för att tillåta eller blockera innehåll i valda länder/regioner.

> [!IMPORTANT]
> **Azure CDN Standard från Microsoft-profiler** stöder inte sökvägsbaserad geofiltrering.
> 

## <a name="standard-profiles"></a>Standardprofiler
Procedurerna i det här avsnittet är endast för **Azure CDN Standard från Akamai** och **Azure CDN Standard från** Verizon-profiler. 

För **Azure CDN Premium från Verizon-profiler** måste du använda **hantera** portalen för att aktivera geofiltrering. Mer information finns i [Azure CDN Premium från Verizon-profiler](#azure-cdn-premium-from-verizon-profiles).

### <a name="define-the-directory-path"></a>Definiera katalogsökvägen
Om du vill komma åt geofiltreringsfunktionen väljer du CDN-slutpunkten i portalen och väljer sedan **Geofiltrering** under INSTÄLLNINGAR på menyn till vänster. 

![Geofiltreringsstandard](./media/cdn-filtering/cdn-geo-filtering-standard.png)

I rutan **SÖKVÄG** anger du den relativa sökvägen till den plats där användare ska tillåtas eller nekas åtkomst till. 

Du kan använda geofiltrering för alla dina filer med ett snedstreck (/) eller välja specifika mappar genom att ange katalogsökvägar (till exempel */pictures/*). Du kan också använda geofiltrering på en enda fil (till exempel */pictures/city.png*). Flera regler är tillåtna. När du har angett en regel visas en tom rad där du kan ange nästa regel.

Alla följande katalogsökvägsfilter är till exempel giltiga:   
*/*                                 
*/Foton/*     
*/Bilder/Strasbourg/*     
*/Bilder/Strasbourg/city.png*

### <a name="define-the-type-of-action"></a>Definiera typ av åtgärd

Välj Tillåt eller Blockera i listan **ÅTGÄRD:** **Tillåt** eller **Blockera:** 

- **Tillåt**: Endast användare från angivna länder/regioner får åtkomst till tillgångar som begärs från den rekursiva sökvägen.

- **Blockera**: Användare från angivna länder/regioner nekas åtkomst till de resurser som begärs från den rekursiva sökvägen. Om inga andra land-/regionfiltreringsalternativ har konfigurerats för den platsen tillåts alla andra användare åtkomst.

En geofiltreringsregel för att blockera sökvägen */Bilder/Strasbourg/* filtrerar till exempel följande filer:     
*\//http:\<slutpunkt>.azureedge.net/Photos/Strasbourg/1000.jpg*
*http:\//\<slutpunkt>.azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg*

### <a name="define-the-countriesregions"></a>Definiera länder/regioner
Välj de länder/regioner som du vill blockera eller tillåta sökvägen i listan **LANDSKODER.** 

När du har valt länder/regioner väljer du **Spara** för att aktivera den nya geofiltreringsregeln. 

![Regler för geofiltrering](./media/cdn-filtering/cdn-geo-filtering-rules.png)

### <a name="clean-up-resources"></a>Rensa resurser
Om du vill ta bort en regel markerar du den i listan på sidan **Geofiltrering** och väljer sedan **Ta bort**.

## <a name="azure-cdn-premium-from-verizon-profiles"></a>Azure CDN Premium från Verizon-profiler
För **Azure CDN Premium från Verizon-profiler** är användargränssnittet för att skapa en geofiltreringsregel annorlunda:

1. Välj **Hantera**på den övre menyn i din Azure CDN-profil.

2. På Verizon-portalen väljer du **HTTP Large**och väljer sedan **Landsfiltrering**.

    ![Geofiltreringsstandard](./media/cdn-filtering/cdn-geo-filtering-premium.png)

3. Välj **Lägg till landsfilter**.

    Sidan **Steg ett:** visas.

4. Ange katalogsökvägen, välj **Blockera** eller **Lägg till**och välj sedan **Nästa**.

    Sidan **Steg två:** visas. 

5. Välj ett eller flera länder/regioner i listan och välj sedan **Slutför** för att aktivera regeln. 
    
    Den nya regeln visas i tabellen på sidan **Landsfiltrering.**

    ![Regler för geofiltrering](./media/cdn-filtering/cdn-geo-filtering-premium-rules.png)

### <a name="clean-up-resources"></a>Rensa resurser
I tabellen lands-/regionfiltreringsregler markerar du borttagningsikonen bredvid en regel för att ta bort den eller redigeringsikonen för att ändra den.

## <a name="considerations"></a>Överväganden
* Ändringar i geofiltreringskonfigurationen börjar inte gälla omedelbart:
   * För **Azure CDN Standard från Microsoft**-profiler slutförs spridningen vanligtvis inom 10 minuter. 
   * För **Azure CDN Standard från Akamai**-profiler slutförs spridningen vanligtvis inom en minut. 
   * För **Azure CDN Standard från Verizon**- och **Azure CDN Premium från Verizon**-profiler slutförs spridningen vanligtvis inom 10 minuter. 
 
* Den här funktionen stöder inte jokertecken (till exempel *).

* Den geofiltreringskonfiguration som är associerad med den relativa sökvägen tillämpas rekursivt på sökvägen.

* Endast en regel kan tillämpas på samma relativa sökväg. Det vill säga du kan inte skapa flera lands-/regionfilter som pekar på samma relativa sökväg. Men eftersom lands-/regionfilter är rekursiva kan en mapp ha flera lands-/regionfilter. Med andra ord kan en undermapp till en tidigare konfigurerad mapp tilldelas ett annat land/regionfilter.

* Geofiltreringsfunktionen använder landskoder för att definiera de länder/regioner från vilka en begäran tillåts eller blockeras för en säker katalog. Även om Akamai och Verizon profiler stöder de flesta av samma land koder, det finns några skillnader. Mer information finns i [Azure CDN landskoder](/previous-versions/azure/mt761717(v=azure.100)). 

