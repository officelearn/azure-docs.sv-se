---
title: Begränsa Azure CDN innehåll efter land/region | Microsoft Docs
description: Lär dig hur du begränsar åtkomst efter land/region till Azure CDN innehåll med hjälp av funktionen för geo-filtrering.
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
ms.topic: how-to
ms.date: 06/19/2018
ms.author: allensu
ms.openlocfilehash: ed82adcc1432bde27042d5775c454bfabcdb96ca
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91358142"
---
# <a name="restrict-azure-cdn-content-by-countryregion"></a>Begränsa Azure CDN innehåll efter land/region

## <a name="overview"></a>Översikt
När en användare begär ditt innehåll, hanteras innehållet som standard oavsett var användaren befinner sig i begäran. Men i vissa fall kanske du vill begränsa åtkomsten till ditt innehåll efter land/region. Med funktionen *geo-filtrering* kan du skapa regler för vissa sökvägar på CDN-slutpunkten för att tillåta eller blockera innehåll i valda länder/regioner.

> [!IMPORTANT]
> **Azure CDN Standard från Microsoft** -profiler stöder inte Path-baserad geo-filtrering.
> 

## <a name="standard-profiles"></a>Standard profiler
Procedurerna i det här avsnittet gäller **Azure CDN Standard från Akamai** och **Azure CDN Standard från Verizon** -profiler. 

För **Azure CDN Premium från Verizon** -profiler måste du använda **hanterings** portalen för att aktivera geo-filtrering. Mer information finns i [Azure CDN Premium från Verizon-profiler](#azure-cdn-premium-from-verizon-profiles).

### <a name="define-the-directory-path"></a>Definiera sökvägen till katalogen
Om du vill komma åt geo-filtrerings funktionen väljer du din CDN-slutpunkt i portalen och väljer **geo-filtrering** under Inställningar i den vänstra menyn. 

![Skärm bild som visar geo-filtrering valt på menyn för en slut punkt.](./media/cdn-filtering/cdn-geo-filtering-standard.png)

I rutan **sökväg** anger du den relativa sökvägen till den plats som användarna ska beviljas eller nekas åtkomst till. 

Du kan använda geo-filtrering för alla filer med ett snedstreck (/) eller välja vissa mappar genom att ange katalog Sök vägar (till exempel */Pictures/*). Du kan också använda geo-filtrering på en enskild fil (till exempel */pictures/city.png*). Flera regler är tillåtna. När du har angett en regel visas en tom rad där du kan ange nästa regel.

Till exempel är alla följande katalog Sök vägs filter giltiga:   
*/*                                 
*Photo*     
*/Photos/Strasbourg/*     
*/Photos/Strasbourg/city.png*

### <a name="define-the-type-of-action"></a>Definiera typ av åtgärd

I listan **åtgärd** väljer du **Tillåt** eller **blockera**: 

- **Tillåt**: endast användare från de angivna länderna/regionerna får åtkomst till till gångar som begärs från den rekursiva sökvägen.

- **Blockera**: användare från de angivna länderna/regionerna nekas åtkomst till de till gångar som begärs från den rekursiva sökvägen. Om inga andra länder/regions filtrerings alternativ har kon figurer ATS för den platsen kommer alla andra användare att få åtkomst.

En geo-filtrerings regel för att blockera sökvägen */photos/Strasbourg/* filtrerar till exempel följande filer:     
*http: \/ / \<endpoint> . azureedge.net/photos/Strasbourg/1000.jpg* 
 *http: \/ / \<endpoint> . azureedge.net/photos/Strasbourg/Cathedral/1000.jpg*

### <a name="define-the-countriesregions"></a>Definiera länder/regioner
I listan **lands koder** väljer du de länder/regioner som du vill blockera eller tillåta för sökvägen. 

När du är färdig med att välja länder/regioner väljer du **Spara** för att aktivera den nya geo-filtrerings regeln. 

![Skärm bild som visar lands koder som används för att blockera eller tillåta länder eller regioner.](./media/cdn-filtering/cdn-geo-filtering-rules.png)

### <a name="clean-up-resources"></a>Rensa resurser
Om du vill ta bort en regel markerar du den i listan på sidan **geo-filtrering** och väljer sedan **ta bort**.

## <a name="azure-cdn-premium-from-verizon-profiles"></a>Azure CDN Premium från Verizon-profiler
För **Azure CDN Premium från Verizon** -profiler, skiljer sig användar gränssnittet för att skapa en geo-filtrerings regel:

1. Välj **Hantera**på den översta menyn i Azure CDN profilen.

2. Välj **http stor**på Verizon-portalen och välj sedan **land filtrering**.

    ![Skärm bild som visar hur du väljer land filtrering i Azure C D N.](./media/cdn-filtering/cdn-geo-filtering-premium.png)

3. Välj **Lägg till land filter**.

    Sidan **steg ett:** visas.

4. Ange sökvägen till katalogen, Välj **block** eller **Lägg till**och välj sedan **Nästa**.

    Sidan **steg två:** visas. 

5. Välj ett eller flera länder/regioner i listan och välj sedan **Slutför** för att aktivera regeln. 
    
    Den nya regeln visas i tabellen på sidan **land filtrering** .

    ![Skärm bild som visar var regeln visas i lands filtrering.](./media/cdn-filtering/cdn-geo-filtering-premium-rules.png)

### <a name="clean-up-resources"></a>Rensa resurser
I tabellen land/region filtrerings regler väljer du ikonen Ta bort bredvid en regel för att ta bort den eller redigera-ikonen för att ändra den.

## <a name="considerations"></a>Överväganden
* Ändringar av konfigurationen för geo-filtrering börjar inte gälla direkt:
   * För **Azure CDN Standard från Microsoft**-profiler slutförs spridningen vanligtvis inom 10 minuter. 
   * För **Azure CDN Standard från Akamai**-profiler slutförs spridningen vanligtvis inom en minut. 
   * För **Azure CDN Standard från Verizon**- och **Azure CDN Premium från Verizon**-profiler slutförs spridningen vanligtvis inom 10 minuter. 
 
* Den här funktionen stöder inte jokertecken (till exempel *).

* Den geo-filtrerings konfiguration som är associerad med den relativa sökvägen tillämpas rekursivt på den sökvägen.

* Endast en regel kan tillämpas på samma relativa sökväg. Det innebär att du inte kan skapa flera länder/region-filter som pekar på samma relativa sökväg. Men eftersom land/region-filter är rekursiva kan en mapp ha flera länder/region-filter. En undermapp till en tidigare konfigurerad mapp kan med andra ord tilldelas ett annat land/region-filter.

* Funktionen för geo-filtrering använder lands koder för att definiera de länder/regioner från vilka en begäran tillåts eller blockeras för en säker katalog. Även om Akamai och Verizon-profiler stöder de flesta av samma lands koder, finns det några skillnader. Mer information finns i [Azure CDN lands koder](/previous-versions/azure/mt761717(v=azure.100)). 

