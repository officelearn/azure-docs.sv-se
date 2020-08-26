---
title: Självstudie – Minska kostnaderna i Azure med rekommendationer
description: Den här självstudien hjälper dig att minska kostnaderna för Azure med hjälp av optimeringsrekommendationer.
author: bandersmsft
ms.author: banders
ms.date: 03/24/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.custom: seodec18
ms.openlocfilehash: 28f9369d044cf9b2713cb2f2ad42f744e517918b
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2020
ms.locfileid: "88683291"
---
# <a name="tutorial-optimize-costs-from-recommendations"></a>Självstudier: Optimera kostnader utifrån rekommendationer

Azure Cost Management tillhandahåller tillsammans med Azure Advisor rekommendationer för kostnadsoptimering. Azure Advisor hjälper dig att optimera och förbättra effektiviteten genom att identifiera inaktiva och underutnyttjade resurser. Självstudien vägleder dig genom ett exempel där du identifierar underutnyttjade Azure-resurser och sedan vidtar åtgärder för att minska kostnaderna.

Titta på videon om [hur du optimerar molninvesteringar i Azure Cost Management](https://www.youtube.com/watch?v=cSNPoAb-TNc) och lär dig mer om hur du använder Advisor för att optimera kostnaderna. Om du vill titta på andra videor går du till [YouTube-kanalen för Cost Management](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/cSNPoAb-TNc]

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Titta på rekommendationerna för kostnadsoptimering för att hitta potentiell ineffektivitet i användningen
> * Följ rekommendationerna om att ändra storlek på en virtuell dator till ett mer kostnadseffektivt alternativ
> * Kontrollera åtgärden för att säkerställa att storleken på den virtuella datorn ändrades

## <a name="prerequisites"></a>Krav
Rekommendationerna är tillgängliga för olika omfång och Azure-kontotyper. Om du vill se hela listan med kontotyper som stöds kan du läsa [Förstå Cost Management-data](understand-cost-mgt-data.md). Du måste minst ha skrivskyddad åtkomst till ett eller flera av följande omfång för att visa kostnadsdata. Mer information om omfång finns i [Förstå och arbeta med omfång](understand-work-scopes.md).

- Prenumeration
- Resursgrupp

Om du har en ny prenumeration kan du inte använda Cost Management-funktioner direkt. Det kan ta upp till 48 timmar innan du kan använda alla Cost Management-funktioner. Du måste också ha virtuella datorer som har varit aktiva i minst 14 dagar.

## <a name="sign-in-to-azure"></a>Logga in på Azure
Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com/).

## <a name="view-cost-optimization-recommendations"></a>Visa rekommendationer för kostnadsoptimering

Om du vill se rekommendationer för kostnadsoptimering för en prenumeration, öppnar du önskat omfång i Azure-portalen och väljer **Advisor-rekommendationer**.

Om du vill se rekommendationer för en hanteringsgrupp, öppnar du önskat omfång i Azure-portalen och väljer **Kostnadsanalys** i menyn. Använd **Omfång** om du vill byta till ett annat omfång, exempelvis en hanteringsgrupp. Välj **Advisor-rekommendationer** i menyn. Mer information om omfång finns i [Förstå och arbeta med omfång](understand-work-scopes.md).

![Advisor-rekommendationer för Cost Management visas i Azure-portalen](./media/tutorial-acm-opt-recommendations/advisor-recommendations.png)

I listan med rekommendationer visas ineffektivitet i användningen eller köprekommendationer som kan hjälpa dig att spara pengar. Summan i **Potentiella årliga besparingar** visar hur mycket du kan spara om du stänger av eller frigör alla virtuella datorer som uppfyller rekommendationsreglerna. Om du inte vill stänga av dem bör du överväga att ändra storlek på dem till en billigare VM-SKU.

Kategorin **Påverkan** och **Potentiella årliga besparingar** är utformade för att hjälpa dig att hitta rekommendationer för hur du sparar så mycket pengar som möjligt.

Bland rekommendationer med hög påverkan finns:
- [Köp reserverade instanser för virtuella datorer och spara pengar jämfört med om du betalar per användning](../../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs)
- [Optimera utgifterna för den virtuella datorn genom att ändra storlek på eller stänga av underutnyttjade instanser](../../advisor/advisor-cost-recommendations.md#optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances)
- [Använd Standard Storage för att lagra Managed Disks-ögonblicksbilder](../../advisor/advisor-cost-recommendations.md#use-standard-snapshots-for-managed-disks)

Bland rekommendationer med medelhög påverkan finns:
- [Ta bort Azure Data Factory-pipelines som inte fungerar](../../advisor/advisor-cost-recommendations.md#delete-azure-data-factory-pipelines-that-are-failing)
- [Minska kostnaderna genom att ta bort ExpressRoute-kretsar som inte har etablerats](../../advisor/advisor-cost-recommendations.md#reduce-costs-by-eliminating-unprovisioned-expressroute-circuits)
- [Minska kostnaderna genom att ta bort eller konfigurera om inaktiva virtuella nätverksgatewayer](../../advisor/advisor-cost-recommendations.md#reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways)

## <a name="act-on-a-recommendation"></a>Följa en rekommendation

Azure Advisor övervakar användningen av den virtuella datorn i sju dagar och identifierar sedan underutnyttjade virtuella datorer. Virtuella datorer vars processoranvändning är fem procent eller mindre och nätverksanvändningen är 7 MB eller mindre under minst fyra dagar, anses vara virtuella datorer med lågt utnyttjande.

Inställningen av en processoranvändning på maximalt 5 % är standard, men du kan ändra inställningarna. Mer information om hur du ändrar inställningen finns i [Konfigurera regeln för genomsnittlig processoranvändning eller rekommendationen för virtuella datorer med låg användning](../../advisor/advisor-get-started.md#configure-low-usage-vm-recommendation).

Även om vissa scenarier kan resultera i låg användning, kan du ofta spara pengar genom att ändra storleken på dina virtuella datorer till mindre kostsamma storlekar. Dina faktiska besparingar kan variera om du väljer att ändra storlek. Låt oss se ett exempel på att ändra storlek på en virtuell dator.

I listan med rekommendationer klickar du på rekommendationen **Ange rätt storlek för eller stäng av underutnyttjade virtuella datorer**. I listan med kandidater för virtuella datorer väljer du en virtuell dator som du vill ändra storlek på och klickar sedan på den virtuella datorn. Den virtuella datorns information visas så att du kan kontrollera användningsmåtten. Värdet i **Potentiella årliga besparingar** är vad du kan spara om du stänger av eller tar bort den virtuella datorn. Att ändra storlek på en virtuell dator kommer förmodligen att spara pengar, men du kommer inte att spara hela beloppet i potentiella årliga besparingar.

![Exempel på rekommendationsinformation](./media/tutorial-acm-opt-recommendations/recommendation-details.png)

I den virtuella datorinformationen kontrollerar du användningen av den virtuella datorn för att se om det är en lämplig kandidat för storleksändring.

![Exempel på information om virtuella datorer som visar historisk användning](./media/tutorial-acm-opt-recommendations/vm-details.png)

Anteckna storleken på den aktuella virtuella datorn. När du är säker på att storleken på den virtuella datorn ska ändras, stänger du den virtuella datorinformationen så att du ser listan med virtuella datorer.

I listan med kandidater som du kan stänga av eller ändra storlek på, väljer du **Ändra storlek på _&lt;FromVirtualMachineSKU&gt;_ till _&lt;ToVirtualMachineSKU&gt;_** .
![Exempel på rekommendation med alternativet att ändra storlek på den virtuella datorn](./media/tutorial-acm-opt-recommendations/resize-vm.png)

Därefter visas en lista över tillgängliga alternativ för storleksändring. Välj den som ger bästa möjliga prestanda och kostnadseffektivitet för ditt scenario. I följande exempel kommer det valda alternativet att ändra storlek från **Standard_D8s_v3** till **Standard_D2s_v3**.

![Exempel på lista med tillgängliga VM-storlekar där du kan välja en storlek](./media/tutorial-acm-opt-recommendations/choose-size.png)

När du har valt en lämplig storlek klickar du på **Ändra storlek** för att starta åtgärden.

Vid storleksändring krävs det att en aktiv virtuell dator som körs kan startas om. Om den virtuella datorn finns i en produktionsmiljö, rekommenderar vi att du kör åtgärden för att ändra storlek efter kontorstid. Att schemalägga omstarten kan minska de avbrott som orsakas av tillfällig otillgänglighet.

## <a name="verify-the-action"></a>Kontrollera åtgärden

När storleksändringen av den virtuella datorn har slutförts, visas ett Azure-meddelande.

![Meddelande om att storleken på den virtuella datorn har ändrats](./media/tutorial-acm-opt-recommendations/resized-notification.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Titta på rekommendationerna för kostnadsoptimering för att hitta potentiell ineffektivitet i användningen
> * Följ rekommendationerna om att ändra storlek på en virtuell dator till ett mer kostnadseffektivt alternativ
> * Kontrollera åtgärden för att säkerställa att storleken på den virtuella datorn ändrades

Om du inte redan har läst artikeln om metodtips för Cost Management, finns det där vägledning och principer som du kan använda när du hanterar kostnaderna.

> [!div class="nextstepaction"]
> [Metodtips för Cost Management](cost-mgt-best-practices.md)
