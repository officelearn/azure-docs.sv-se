---
title: Självstudie – minska kostnaderna i Azure med rekommendationer
description: Den här kursen hjälper dig att minska kostnaderna för Azure när du vidta åtgärder för optimering av rekommendationer.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/24/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: 6c3fc60ca623649a175fb1078efc6903ebde2811
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75444593"
---
# <a name="tutorial-optimize-costs-from-recommendations"></a>Självstudie: Optimera kostnaderna från rekommendationer

Azure Cost Management tillhandahåller tillsammans med Azure Advisor rekommendationer för kostnadsoptimering. Azure Advisor hjälper dig att optimera och förbättra effektiviteten genom att identifiera inaktiva och underutnyttjade resurser. Den här självstudien visar ett exempel där du identifiera underutnyttjade Azure-resurser och sedan du vidta åtgärder för att minska kostnaderna.

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Visa optimering rekommendationer att visa potentiella ineffektivitet
> * Agera på en rekommendation att ändra storlek på en virtuell dator till ett mer kostnadseffektivt alternativ
> * Bekräfta åtgärden för att säkerställa att den virtuella datorn har har ändrats

## <a name="prerequisites"></a>Krav
Rekommendationerna är tillgängliga för olika omfattningar och Azure-konto typer. Om du vill visa en fullständig lista över typer av stöds kan du läsa [Förstå Cost Management-data](understand-cost-mgt-data.md). Du måste minst ha skrivskyddad åtkomst till ett eller flera av följande omfång för att visa kostnadsdata. Mer information om omfattningar finns i [förstå och arbeta med omfattningar](understand-work-scopes.md).

- Prenumeration
- Resursgrupp

Du måste ha aktiva virtuella datorer med minst 14 dagars aktivitet.

## <a name="sign-in-to-azure"></a>Logga in på Azure
Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com/).

## <a name="view-cost-optimization-recommendations"></a>Visa rekommendationer för optimering

Om du vill visa rekommendationer för kostnads optimering för en prenumeration öppnar du önskad omfattning i Azure Portal och väljer **Advisor-rekommendationer**.

Om du vill visa rekommendationer för en hanterings grupp öppnar du önskad omfattning i Azure Portal och väljer **kostnads analys** i menyn. Använd **omfånget** Pill för att växla till ett annat omfång, till exempel en hanterings grupp. Välj **Advisor-rekommendationer** på menyn. Mer information om omfattningar finns i [förstå och arbeta med omfattningar](understand-work-scopes.md).

![Cost Management Advisor-rekommendationer visas i Azure portal](./media/tutorial-acm-opt-recommendations/advisor-recommendations.png)

Lista över rekommendationer identifierar ineffektivitet eller visar rekommendationer för köp som kan hjälpa dig att spara ytterligare pengar. Den sammanräknad **potentiella årliga besparingar** visar det totala beloppet som du kan spara om du stänger ner eller frigör alla dina virtuella datorer som uppfyller reglerna för rekommendation. Om du inte vill att stänga av dem, bör du överväga att ändra storlek på dem till en mindre VM-SKU.

Den **inverkan** kategori, tillsammans med den **potentiella årliga besparingar**, är utformade för att identifiera rekommendationer som kan spara så mycket som möjligt.

Rekommendationer för hög påverkan är:
- [Köp reserverade instanser av virtuella datorer för att spara pengar jämfört med betala per användning-kostnad](../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs)
- [Optimera den virtuella datorn genom att ändra storlek på eller stänga av underutnyttjade instanser](../advisor/advisor-cost-recommendations.md#optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances)
- [Använd standard lagring för att lagra Managed Disks ögonblicks bilder](../advisor/advisor-cost-recommendations.md#use-standard-snapshots-for-managed-disks)

Rekommendationer för medelhög påverkan är:
- [Ta bort Azure Data Factory pipelines som inte fungerar](../advisor/advisor-cost-recommendations.md#delete-azure-data-factory-pipelines-that-are-failing)
- [Minska kostnaderna genom att ta bort icke-etablerade ExpressRoute-kretsar](../advisor/advisor-cost-recommendations.md#reduce-costs-by-eliminating-unprovisioned-expressroute-circuits)
- [Minska kostnaderna genom att ta bort eller konfigurera om inaktiva virtuella nätverks-gatewayer](../advisor/advisor-cost-recommendations.md#reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways)

## <a name="act-on-a-recommendation"></a>Agera på en rekommendation

Azure Advisor övervakar användningen av den virtuella datorn i sju dagar och identifierar sedan underutnyttjade virtuella datorer. Virtuella datorer vars CPU-belastningen är fem procent eller mindre och nätverkets användning är sju MB eller mindre för fyra eller flera dagar betraktas som låg användning virtuella datorer.

5% eller mindre CPU användning är standardinställningen, men du kan justera inställningarna. Mer information om hur du justerar inställningen finns i [Konfigurera den genomsnittliga CPU-användningen eller rekommendationen för den virtuella datorn med lägre användning](../advisor/advisor-get-started.md#configure-low-usage-vm-recommendation).

Även om vissa scenarier kan resultera i låg användning av design, kan du ofta spara pengar genom att ändra storleken på dina virtuella datorer till billigare storlekar. Dina faktiska besparingar kan variera om du väljer en åtgärd för storleksändring. Låt oss gå igenom ett exempel på att ändra storlek på en virtuell dator.

I listan över rekommendationer, klickar du på den **rätt storlek för eller Stäng av underutnyttjade virtuella datorer** rekommendation. I listan över kandidater för virtuell dator, väljer du en virtuell dator för att ändra storlek på och klicka sedan på den virtuella datorn. Den virtuella datorns information visas så att du kan kontrollera mätvärden för resursutnyttjande. Den **potentiella årliga besparingar** värdet är vad du kan spara om du stänger ner eller ta bort den virtuella datorn. Ändra storlek på en virtuell dator kommer förmodligen att spara pengar, men du kommer inte att spara med potentiella årliga besparingar.

![Exempel på information om rekommendation](./media/tutorial-acm-opt-recommendations/recommendation-details.png)

Kontrollera användningen av den virtuella datorn för att bekräfta att det är en lämplig storleksändring kandidat i VM-information.

![Exempel VM information som visar historisk användning](./media/tutorial-acm-opt-recommendations/vm-details.png)

Observera den aktuella virtuella datorns storlek. När du har kontrollerat att den virtuella datorn ska ändras, Stäng VM-information så att du ser listan över virtuella datorer.

I listan över kandidater att stänga av eller ändra storlek väljer du * * ändra storlek *&lt;FromVirtualMachineSKU&gt;* till *&lt;ToVirtualMachineSKU&gt;* * *.
![Exempel rekommendation möjlighet att ändra storlek på den virtuella datorn](./media/tutorial-acm-opt-recommendations/resize-vm.png)

Därefter visas en lista med tillgängliga storleksändring alternativ. Välj det som ger bästa prestanda och kostnadseffektivitet för ditt scenario. I följande exempel ändrar det valda alternativet storlek från **Standard_D8s_v3** till **Standard_D2s_v3**.

![Exempellistan över tillgängliga storlekar för Virtuella datorer där du kan välja en storlek](./media/tutorial-acm-opt-recommendations/choose-size.png)

När du har valt lämplig storlek klickar du på **ändra storlek** för att starta åtgärden ändra storlek.

Ändra storlek på kräver ett aktivt aktiv virtuell dator startas om. Om den virtuella datorn är i en produktionsmiljö, rekommenderar vi att du kör storleksändringen efter kontorstid. Schemaläggning av omstarten kan minska avbrott som orsakas av tillfälligt otillgängliga.

## <a name="verify-the-action"></a>Bekräfta åtgärden

När VM storleksändringen är klar visas ett meddelande om Azure.

![Lyckad ändrade VM-meddelande](./media/tutorial-acm-opt-recommendations/resized-notification.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Visa optimering rekommendationer att visa potentiella ineffektivitet
> * Agera på en rekommendation att ändra storlek på en virtuell dator till ett mer kostnadseffektivt alternativ
> * Bekräfta åtgärden för att säkerställa att den virtuella datorn har har ändrats

Om du inte har redan läst artikel kostnadshantering bästa praxis, ger det övergripande vägledning och principer för att tänka på för att hantera kostnader.

> [!div class="nextstepaction"]
> [Kostnad rekommenderade metoder](cost-mgt-best-practices.md)
