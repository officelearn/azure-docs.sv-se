---
title: Självstudie – minska kostnaderna i Azure med optimerings rekommendationer | Microsoft Docs
description: Den här självstudien hjälper dig att minska kostnaderna för Azure när du arbetar med optimerings rekommendationer.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/24/2019
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: 603de4d9bed936ecb91f130b0e30f6d1383a9092
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72935840"
---
# <a name="tutorial-optimize-costs-from-recommendations"></a>Självstudie: Optimera kostnader från rekommendationer

Azure Cost Management tillhandahåller tillsammans med Azure Advisor rekommendationer för kostnadsoptimering. Azure Advisor hjälper dig att optimera och förbättra effektiviteten genom att identifiera inaktiva och underutnyttjade resurser. Den här självstudien vägleder dig genom ett exempel där du kan identifiera underutnyttjade Azure-resurser och sedan vidta åtgärder för att minska kostnaderna.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Visa rekommendationer för kostnads optimering för att se potentiell användnings effektivitet
> * Vidta rekommendationer för att ändra storlek på en virtuell dator till ett mer kostnads effektivt alternativ
> * Verifiera åtgärden för att säkerställa att storleken på den virtuella datorn har ändrats

## <a name="prerequisites"></a>Krav
Rekommendationerna är tillgängliga för olika omfattningar och Azure-konto typer. Om du vill visa en fullständig lista över typer av stöds kan du läsa [Förstå Cost Management-data](understand-cost-mgt-data.md). Du måste minst ha skrivskyddad åtkomst till ett eller flera av följande omfång för att visa kostnadsdata. Mer information om omfattningar finns i [förstå och arbeta med omfattningar](understand-work-scopes.md).

- Prenumeration
- Resursgrupp

Du måste ha aktiva virtuella datorer med minst 14 dagars aktivitet.

## <a name="sign-in-to-azure"></a>Logga in på Azure
Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com/).

## <a name="view-cost-optimization-recommendations"></a>Visa rekommendationer för kostnads optimering

Om du vill visa rekommendationer för kostnads optimering för en prenumeration öppnar du önskad omfattning i Azure Portal och väljer **Advisor-rekommendationer**.

Om du vill visa rekommendationer för en hanterings grupp öppnar du önskad omfattning i Azure Portal och väljer **kostnads analys** i menyn. Använd **omfånget** Pill för att växla till ett annat omfång, till exempel en hanterings grupp. Välj **Advisor-rekommendationer** på menyn. Mer information om omfattningar finns i [förstå och arbeta med omfattningar](understand-work-scopes.md).

![Rekommendationer för Cost Management Advisor som visas i Azure Portal](./media/tutorial-acm-opt-recommendations/advisor-recommendations.png)

Listan över rekommendationer identifierar användnings ineffektivitet eller visar köp rekommendationer som kan hjälpa dig att spara ytterligare pengar. De totala **möjliga årliga besparingarna** visar det totala beloppet som du kan spara om du stänger av eller frigör alla virtuella datorer som uppfyller rekommendations reglerna. Om du inte vill stänga av dem bör du överväga att ändra storlek på dem till en billigare VM-SKU.

Kategorin **påverkan** , tillsammans med **potentiella årliga besparingar**, är utformad för att hjälpa till att identifiera rekommendationer som har möjlighet att spara så mycket som möjligt.

Rekommendationer för hög påverkan är:
- [Köp reserverade instanser av virtuella datorer för att spara pengar jämfört med betala per användning-kostnad](../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs)
- [Optimera den virtuella datorn genom att ändra storlek på eller stänga av underutnyttjade instanser](../advisor/advisor-cost-recommendations.md#optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances)
- [Använd standard lagring för att lagra Managed Disks ögonblicks bilder](../advisor/advisor-cost-recommendations.md#use-standard-snapshots-for-managed-disks)

Rekommendationer för medelhög påverkan är:
- [Ta bort Azure Data Factory pipelines som inte fungerar](../advisor/advisor-cost-recommendations.md#delete-azure-data-factory-pipelines-that-are-failing)
- [Minska kostnaderna genom att ta bort icke-etablerade ExpressRoute-kretsar](../advisor/advisor-cost-recommendations.md#reduce-costs-by-eliminating-unprovisioned-expressroute-circuits)
- [Minska kostnaderna genom att ta bort eller konfigurera om inaktiva virtuella nätverks-gatewayer](../advisor/advisor-cost-recommendations.md#reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways)

## <a name="act-on-a-recommendation"></a>Agera enligt rekommendation

Azure Advisor övervakar användningen av den virtuella datorn i sju dagar och identifierar sedan underutnyttjade virtuella datorer. Virtuella datorer vars processor användning är fem procent eller mindre och nätverks användningen är sju MB eller mindre under fyra eller flera dagar betraktas virtuella datorer med låg belastning.

Inställningen 5% eller mindre processor användning är standard, men du kan justera inställningarna. Mer information om hur du justerar inställningen finns i [Konfigurera den genomsnittliga CPU-användningen eller rekommendationen för den virtuella datorn med lägre användning](../advisor/advisor-get-started.md#configure-low-usage-vm-recommendation).

Även om vissa scenarier kan resultera i låg användning genom design, kan du ofta Spara pengar genom att ändra storleken på dina virtuella datorer till mindre dyra storlekar. Dina faktiska besparingar kan variera om du väljer en åtgärd för att ändra storlek. Låt oss gå igenom ett exempel på att ändra storlek på en virtuell dator.

I listan över rekommendationer klickar du på rekommendationen **rätt storlek eller avstängning underutnyttjade virtuella datorer** . I listan över kandidater för virtuella datorer väljer du en virtuell dator som du vill ändra storlek på och klickar sedan på den virtuella datorn. Den virtuella datorns information visas så att du kan kontrol lera användnings måtten. Det **potentiella årliga sparande** värdet är vad du kan spara om du stänger av eller tar bort den virtuella datorn. Att ändra storlek på en virtuell dator kommer förmodligen att spara pengar, men du kommer inte att spara hela mängden potentiella årliga besparingar.

![Exempel på rekommendations information](./media/tutorial-acm-opt-recommendations/recommendation-details.png)

I den virtuella dator informationen kontrollerar du användningen av den virtuella datorn för att bekräfta att det är en lämplig storleks ändrings kandidat.

![Exempel på information om virtuella datorer som visar historisk användning](./media/tutorial-acm-opt-recommendations/vm-details.png)

Anteckna storleken på den aktuella virtuella datorn. När du har kontrollerat att storleken på den virtuella datorn ska ändras stänger du den virtuella dator informationen så att du ser listan över virtuella datorer.

I listan över kandidater att stänga av eller ändra storlek väljer du * * ändra storlek *&lt;FromVirtualMachineSKU&gt;* till *&lt;ToVirtualMachineSKU&gt;* * *.
![exempel rekommendation med alternativet att ändra storlek på den virtuella datorn](./media/tutorial-acm-opt-recommendations/resize-vm.png)

Sedan visas en lista över tillgängliga alternativ för storleks ändring. Välj den som ska ge bästa möjliga prestanda och kostnads effektivitet för ditt scenario. I följande exempel ändrar det valda alternativet storlek från **Standard_D8s_v3** till **Standard_D2s_v3**.

![Exempel lista med tillgängliga VM-storlekar där du kan välja en storlek](./media/tutorial-acm-opt-recommendations/choose-size.png)

När du har valt lämplig storlek klickar du på **ändra storlek** för att starta åtgärden ändra storlek.

För storleks ändring krävs en aktiv virtuell dator som körs aktivt för omstart. Om den virtuella datorn finns i en produktions miljö rekommenderar vi att du kör åtgärden ändra storlek efter kontors tid. Schemaläggning av omstarten kan minska avbrott som orsakas av tillfälligt otillgänglighet.

## <a name="verify-the-action"></a>Verifiera åtgärden

När storleks ändringen av den virtuella datorn har slutförts visas ett Azure-meddelande.

![Aviseringen om storleks ändring av virtuell dator har ändrats](./media/tutorial-acm-opt-recommendations/resized-notification.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Visa rekommendationer för kostnads optimering för att se potentiell användnings effektivitet
> * Vidta rekommendationer för att ändra storlek på en virtuell dator till ett mer kostnads effektivt alternativ
> * Verifiera åtgärden för att säkerställa att storleken på den virtuella datorn har ändrats

Om du inte redan har läst den Cost Management Best Practices-artikeln får du vägledning och principer på hög nivå som du kan använda för att hantera kostnader.

> [!div class="nextstepaction"]
> [Metod tips för Cost Management](cost-mgt-best-practices.md)
