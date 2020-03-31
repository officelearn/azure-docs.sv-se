---
title: Kom igång med Azure Advisor
description: Kom igång med Azure Advisor.
ms.topic: article
ms.date: 02/01/2019
ms.openlocfilehash: 8c2699030b1a6d428ddc2a4db40a66003824cf10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259635"
---
# <a name="get-started-with-azure-advisor"></a>Kom igång med Azure Advisor

Lär dig hur du får åtkomst till Advisor via Azure-portalen, får rekommendationer och implementerar rekommendationer.

> [!NOTE]
> Azure Advisor körs automatiskt i bakgrunden för att hitta nyskapade resurser. Det kan ta upp till 24 timmar att ge rekommendationer om dessa resurser.

## <a name="get-recommendations"></a>Få rekommendationer

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Klicka på **Rådgivare**i den vänstra rutan .  Om du inte ser Advisor i den vänstra rutan klickar du på **Alla tjänster**.  Klicka på **Rådgivare**under **Övervakning och hantering**i tjänstmenyfönstret. Instrumentpanelen Advisor visas.

   ![Få tillgång till Azure Advisor med Azure-portalen](./media/advisor-get-started/advisor-portal-menu.png) 

1. Advisor-instrumentpanelen visar en sammanfattning av dina rekommendationer för alla valda prenumerationer.  Du kan välja de prenumerationer som du vill att rekommendationerna ska visas för i listrutan prenumerationsfilter.

1. Om du vill få rekommendationer för en viss kategori klickar du på en av flikarna: **Hög tillgänglighet**, **Säkerhet**, **Prestanda**eller **Kostnad**. 

   ![Instrumentpanelen Azure Advisor](./media/advisor-overview/advisor-dashboard.png)

## <a name="get-recommendation-details-and-implement-a-solution"></a>Få rekommendationsinformation och implementera en lösning

Du kan välja en rekommendation i Advisor för att visa ytterligare information – till exempel rekommendationsåtgärder och påverkade resurser – och implementera lösningen på rekommendationen.  

1. Logga in på [Azure-portalen](https://portal.azure.com)och öppna sedan [Advisor](https://aka.ms/azureadvisordashboard).

1. Välj en rekommendationskategori om du vill visa listan med rekommendationer inom den kategorin eller välj fliken **Alla** om du vill visa alla dina rekommendationer.

1. Klicka på en rekommendation som du vill granska i detalj.

1. Granska informationen om rekommendationen och de resurser som rekommendationen gäller för.

1. Klicka på den **rekommenderade åtgärden** för att implementera rekommendationen.

## <a name="filter-recommendations"></a>Filtrera rekommendationer

Du kan filtrera rekommendationer för att öka detaljnivån till det som är viktigast för dig.  Du kan filtrera efter prenumerations-, resurstyp- eller rekommendationsstatus.  

1. Logga in på [Azure-portalen](https://portal.azure.com)och öppna sedan [Advisor](https://aka.ms/azureadvisordashboard).

1. Använd listrutan på advisor-instrumentpanelen för att filtrera efter prenumerations-, resurstyp- eller rekommendationsstatus.

    ![Villkor för sökfilter för rådgivare](./media/advisor-get-started/advisor-filters.png)

## <a name="postpone-or-dismiss-recommendations"></a>Skjuta upp eller avvisa rekommendationer

1. Logga in på [Azure-portalen](https://portal.azure.com)och öppna sedan [Advisor](https://aka.ms/azureadvisordashboard).

1. Navigera till den rekommendation som du vill skjuta upp eller avvisa.

1. Klicka på rekommendationen.

1. Klicka på **Skjut upp**. 

1. Ange en uppskjuten tidsperiod eller välj **Aldrig** att avvisa rekommendationen.

## <a name="exclude-subscriptions-or-resource-groups"></a>Exkludera prenumerationer eller resursgrupper

Du kan ha resursgrupper eller prenumerationer som du inte vill få Advisor-rekommendationer för– till exempel testresurser.  Du kan konfigurera Advisor så att rekommendationer för specifika prenumerationer och resursgrupper endast genereras.

> [!NOTE]
> Om du vill inkludera eller utesluta en prenumeration eller resursgrupp från Advisor måste du vara en prenumerationsägare.  Om du inte har de behörigheter som krävs för en prenumeration eller resursgrupp inaktiveras alternativet att inkludera eller utesluta den i användargränssnittet.

1. Logga in på [Azure-portalen](https://portal.azure.com)och öppna sedan [Advisor](https://aka.ms/azureadvisordashboard).

1. Klicka på **Konfigurera** i åtgärdsfältet.

1. Avmarkera prenumerationer eller resursgrupper som du inte vill få Advisor-rekommendationer för.

    ![Exempel på konfigurera resurser för rådgivare](./media/advisor-get-started/advisor-configure-resources.png)

1. Klicka på knappen **Använd**.

## <a name="configure-low-usage-vm-recommendation"></a>Konfigurera rekommendation om virtuell dator med låg användning

Den här proceduren konfigurerar den genomsnittliga cpu-användningsregeln för rekommendationen för den virtuella datorn med låg användning.

Advisor övervakar användningen av virtuella datorer i 7 dagar och identifierar sedan virtuella datorer med lågt utnyttjande. Virtuella datorer anses vara lågt utnyttjande om deras CPU-användning är 5% eller mindre och deras nätverksanvändning är mindre än 2% eller om den aktuella arbetsbelastningen kan rymmas av en mindre virtuell datorstorlek.

Om du vill vara mer aggressiv på att identifiera virtuella datorer med låg användning kan du justera den genomsnittliga cpu-användningsregeln per prenumeration.  Cpu-användningsregeln kan ställas in på 5%, 10%, 15% eller 20%.

> [!NOTE]
> Om du vill justera den genomsnittliga cpu-användningsregeln för att identifiera virtuella datorer med låg användning måste du vara en *prenumerationsägare*.  Om du inte har de behörigheter som krävs för en prenumeration eller resursgrupp inaktiveras alternativet att inkludera eller utesluta den i användargränssnittet. 

1. Logga in på [Azure-portalen](https://portal.azure.com)och öppna sedan [Advisor](https://aka.ms/azureadvisordashboard).

1. Klicka på **Konfigurera** i åtgärdsfältet.

1. Klicka på fliken **Regler.**

1. Välj de prenumerationer som du vill justera den genomsnittliga cpu-användningsregeln för och klicka sedan på **Redigera**.

1. Välj önskat genomsnittligt cpu-utnyttjandevärde och klicka på **Använd**.

1. Klicka på **Uppdatera rekommendationer** om du vill uppdatera dina befintliga rekommendationer om du vill använda den nya genomsnittliga regeln om processoranvändning. 

   ![Exempel på rekommendationsregler för rådgivare konfigurerar rekommendationsregler](./media/advisor-get-started/advisor-configure-rules.png)

## <a name="download-recommendations"></a>Ladda ner rekommendationer

Med Advisor kan du ladda ned en sammanfattning av dina rekommendationer.  Du kan hämta dina rekommendationer som en PDF-fil eller en CSV-fil.  Genom att ladda ned dina rekommendationer kan du enkelt dela med dina kollegor eller utföra din egen analys utöver rekommendationsdata.

1. Logga in på [Azure-portalen](https://portal.azure.com)och öppna sedan [Advisor](https://aka.ms/azureadvisordashboard).

1. Klicka på **Hämta som CSV** eller **Ladda ned som PDF** i åtgärdsfältet.

Hämtningsalternativet respekterar alla filter som du har använt på advisor-instrumentpanelen.  Om du väljer hämtningsalternativet när du visar en viss rekommendationskategori eller rekommendation innehåller den nedladdade sammanfattningen endast information om den kategorin eller rekommendationen. 

## <a name="next-steps"></a>Nästa steg

Mer information om Advisor finns i:

- [Introduktion till Azure Advisor](advisor-overview.md)
- [Advisor rekommendationer för hög tillgänglighet](advisor-high-availability-recommendations.md)
- [Säkerhetsrekommendationer för rådgivare](advisor-security-recommendations.md)
- [Advisors prestandarekommendationer](advisor-performance-recommendations.md)
- [Kostnadsrekommendationer för rådgivare](advisor-performance-recommendations.md)
