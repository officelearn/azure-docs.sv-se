---
title: Förstå din Azure Enterprise-faktura
description: Lär dig att läsa och förstå din användning och fakturering för Azure Enterprise-avtal.
author: adpick
manager: dougeby
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 70d46d0d212f339fae9c99ef2ae13d13d1433709
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225687"
---
# <a name="understand-your-azure-enterprise-agreement-bill"></a>Förstå fakturan för ditt Azure Enterprise-avtal

Azure-kunder med ett Enterprise-avtal får en faktura när de överskrider organisationens kredit eller använder tjänster som inte omfattas av krediten.

Din organisations kredit inkluderar ditt ekonomiska åtagande. Det ekonomiska åtagandet är det belopp som din organisation förskottsbetalade för användningen av Azure-tjänster. Du kan lägga till belopp för ekonomiskt åtagande till ditt Enterprise-avtal genom att kontakta din Microsoft-kontoansvarige eller återförsäljare.  

## <a name="invoices-for-most-customers"></a>Fakturor för de flesta kunder

Det här avsnittet gäller inte för Azure-kunder i Australien, Japan eller Singapore. Om du är i något av dessa länder/regioner kan du läsa [Fakturor för andra kunder](#invoices-for-other-customers).

Du får en Azure-faktura när något av följande inträffar under din faktureringsperiod:

- **Överförbrukning av tjänst**: Din organisations användningsavgifter överstiger ditt kreditsaldo.
- **Avgifter som debiteras separat**: De tjänster som din organisation använder omfattas inte av krediten. Du faktureras för följande tjänster oavsett ditt kreditsaldo:
    - Canonical
    - Citrix XenApp Essentials
    - Citrix XenDesktop
    - Registrerad användare
    - Openlogic
    - Registrerad användare för Remote Access Rights XenApp Essentials
    - Ubuntu Advantage
    - Visual Studio Enterprise (månatligen)
    - Visual Studio Enterprise (årsvis)
    - Visual Studio Professional (månatligen)
    - Visual Studio Professional (årsvis)
- **Marketplace-avgifter**: Azure Marketplace-köp och -användning omfattas inte av din organisations kredit. Du faktureras därmed för Marketplace-avgifter oavsett ditt kreditsaldo. I Enterprise Portal kan en företagsadministratör aktivera och inaktivera Marketplace-köp.

## <a name="review-charges-for-most-customers"></a>Granska avgifter för de flesta kunder
Det här avsnittet gäller inte för Azure-kunder i Australien, Japan eller Singapore. Om du är i något av dessa länder/regioner kan du läsa [Granska avgifter för andra kunder](#review-charges-for-other-customers).

För att kunna granska och verifiera avgifterna på din faktura måste du vara företagsadministratör. Mer information finns i [Förstå administrativa roller för Azure Enterprise-avtal i Azure](billing-understand-ea-roles.md). Om du inte vet vem som är företagsadministratör för din organisation bör du [kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

Din faktura visar all din Azure-användning, följt av eventuella Marketplace-avgifter. Om du har ett kreditsaldo tillämpas det på Azure-användning.

Jämför det sammanlagda belopp som visas i Enterprise Portal i **Rapporter** > **Användningssammanfattning** med din Azure-faktura. De belopp som anges i **Användningssammanfattning** inkluderar inte skatt.

1. Logga in på [Enterprise Portal](https://ea.azure.com).
1. Välj **Rapporter**.
1. I flikens övre högra hörn växlar du vyn från **M** till **C** och matchar perioden på fakturan.  
    ![Skärmbild som visar alternativet M + C i Användningssammanfattning.](./media/billing-understand-your-bill-ea/ea-portal-usage-sumary-cm-option.png)
1. Den sammanlagda summan för **Total användning** och **Azure Marketplace** bör överensstämma med **Totalt utökat belopp** på din faktura.
1. Om du vill ha mer information om dina avgifter kan du gå till **Ladda ned användning**.  
    ![Skärmbild som visar fliken Ladda ned användning](./media/billing-understand-your-bill-ea/ea-portal-download-usage.png)

## <a name="invoices-for-other-customers"></a>Fakturor för andra kunder

Det här avsnittet gäller endast för Azure-kunder i Australien, Japan eller Singapore.

Du får en eller flera Azure-fakturor när något av följande inträffar:

- **Överförbrukning av tjänst**: Din organisations användningsavgifter överstiger ditt kreditsaldo.
- **Avgifter som debiteras separat**: De tjänster som din organisation använder omfattas inte av krediten. Du faktureras för följande tjänster:
    - Canonical
    - Citrix XenApp Essentials
    - Citrix XenDesktop
    - Registrerad användare
    - Openlogic
    - Registrerad användare för Remote Access Rights XenApp Essentials
    - Ubuntu Advantage
    - Visual Studio Enterprise (månatligen)
    - Visual Studio Enterprise (årsvis)
    - Visual Studio Professional (månatligen)
    - Visual Studio Professional (årsvis)
- **Marketplace-avgifter**: Azure Marketplace-köp och -användning omfattas inte av din organisations kredit, och faktureras separat. I Enterprise Portal kan en företagsadministratör aktivera och inaktivera Marketplace-köp.

När du har avgifter som har förfallit för överförbrukning av tjänst och avgifter som fakturas separat under faktureringsperioden får du en faktura. Den innehåller båda typerna av avgifter. Marketplace-avgifter faktureras alltid separat.

## <a name="review-charges-for-other-customers"></a>Granska avgifter för andra kunder

Det här avsnittet gäller endast om du är i Australien, Japan eller Singapore.

För att kunna granska och verifiera avgifterna på din faktura måste du vara företagsadministratör. Mer information finns i [Förstå administrativa roller för Azure Enterprise-avtal i Azure](billing-understand-ea-roles.md). Om du inte vet vem som är företagsadministratör för din organisation bör du [kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="review-service-overage-invoice"></a>Granska fakturan för överförbrukning av tjänst

Jämför ditt totala användningsbelopp i Enterprise Portal i **Rapporter** > **Användningssammanfattning** med din faktura för överförbrukning av tjänst. Fakturan för överförbrukning av tjänst omfattar användning som överskrider organisationens kredit och/eller tjänster som inte omfattas av krediten. Belopp i **Användningssammanfattning** inkluderar inte skatt.

1. Logga in på [Enterprise Portal](https://ea.azure.com).
1. Välj **Rapporter**.
1. I flikens övre högra hörn växlar du vyn från **M** till **C** och matchar perioden på fakturan.  
    ![Skärmbild som visar alternativet M + C i Användningssammanfattning.](./media/billing-understand-your-bill-ea/ea-portal-usage-sumary-cm-option.png)
1. Beloppet för **Total användning** ska överensstämma med **Totalt utökat belopp** på fakturan för överförbrukning av tjänst.  
1. Du kan få mer information om dina avgifter genom att gå till **Ladda ned användning** > **Nedladdning av avancerad rapport**. Rapporten innehåller inte skatter eller avgifter för reservationer eller Marketplace-debiteringar.  
      ![Skärmbild som visar Nedladdning av avancerad rapport på fliken Ladda ned användning.](./media/billing-understand-your-bill-ea/ea-portal-download-usage-advanced.png)

I följande tabell visas de termer och beskrivningar som finns på fakturan och i **Användningssammanfattning** i Enterprise Portal:

|Fakturaterm|Termen Användningssammanfattning|Beskrivning|
|---|---|---|
|Totalt utökat belopp|Total användning|Den totala användningsavgiften före skatt för den specifika perioden innan krediten tillämpas.|
|Åtagandeanvändning|Åtagandeanvändning|Den kredit som tillämpas under den specifika perioden.|
|Total försäljning|Total överförbrukning|Den totala överförbrukningsavgiften som överstiger ditt kreditbelopp. Detta belopp inkluderar inte skatt.|
|Skattebelopp|Inte tillämpligt|Skatt som gäller för det totala försäljningsbeloppet för den specifika perioden.|
|Totalt belopp|Inte tillämpligt|Det belopp som ska betalas för fakturan efter det att krediten har tillämpats och skatten lagts till.|

### <a name="marketplace-invoice"></a>Marketplace-faktura

Det här avsnittet gäller endast om du är i Australien, Japan eller Singapore.

Jämför din totala Azure Marketplace-summa i **Rapporter** > **Användningssammanfattning** i Enterprise-portalen med din Marketplace-faktura. Marketplace-fakturan avser enbart Azure Marketplace-köp och -användning. Belopp i **Användningssammanfattning** inkluderar inte skatt.

1. Logga in på [Enterprise Portal](https://ea.azure.com).
1. Välj **Rapporter**.
1. I flikens övre högra hörn växlar du vyn från **M** till **C** och matchar perioden på fakturan.  
     ![Skärmbild som visar alternativet M + C i Användningssammanfattning.](./media/billing-understand-your-bill-ea/ea-portal-usage-sumary-cm-option.png)  
1. Det totala **Azure Marketplace**-beloppet ska överensstämma med **Total försäljning** på din Marketplace-faktura.
1. Du kan få mer information om dina användningsbaserade avgifter genom att gå till **Ladda ned användning**. Under **Marketplace-avgifter** väljer du **Ladda ned**. Den här rapporten inkluderar inga skatter och visar inte enstaka köp.  
     ![Skärmbild som visar nedladdningsalternativet under Marketplace-avgifter.](./media/billing-understand-your-bill-ea/ea-portal-download-usage-marketplace.png)

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg
- [Visa och ladda ned information om din användning och dina avgifter för Azure](billing-download-azure-daily-usage.md)
