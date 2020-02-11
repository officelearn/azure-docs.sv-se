---
title: Granska din Azure Enterprise-avtalsfaktura
description: Lär dig att läsa och förstå din användning och fakturering för Azure Enterprise-avtal.
author: banders
manager: dougeby
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/17/2020
ms.author: banders
ms.openlocfilehash: 75b6dd1b2d76d12087270e155551402d1bcd4965
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2020
ms.locfileid: "76274034"
---
# <a name="understand-your-azure-enterprise-agreement-bill"></a>Förstå fakturan för ditt Azure Enterprise-avtal

Azure-kunder med ett Enterprise-avtal får en faktura när de överskrider organisationens kredit eller använder tjänster som inte omfattas av krediten.

Din organisations kredit inkluderar ditt ekonomiska åtagande. Det ekonomiska åtagandet är det belopp som din organisation förskottsbetalade för användningen av Azure-tjänster. Du kan lägga till belopp för ekonomiskt åtagande till ditt Enterprise-avtal genom att kontakta din Microsoft-kontoansvarige eller återförsäljare.

Den här självstudien gäller endast för Azure-kunder som har ett Azure Enterprise-avtal.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Granska fakturerade avgifter
> * Granska avgifter för överförbrukning av tjänst
> * Granska Marketplace-faktura

## <a name="prerequisites"></a>Krav

För att kunna granska och verifiera avgifterna på din faktura måste du vara företagsadministratör. Mer information finns i [Förstå administrativa roller för Azure Enterprise-avtal i Azure](../manage/understand-ea-roles.md). Om du inte vet vem som är företagsadministratör för din organisation bör du [kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="review-invoiced-charges-for-most-customers"></a>Granska fakturerade avgifter för de flesta kunder

Det här avsnittet gäller inte för Azure-kunder i Australien, Japan eller Singapore.

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

Din faktura visar all din Azure-användning, följt av eventuella Marketplace-avgifter. Om du har ett kreditsaldo tillämpas det på Azure-användning.

Jämför det sammanlagda belopp som visas i Enterprise Portal i **Rapporter** > **Användningssammanfattning** med din Azure-faktura. De belopp som anges i **Användningssammanfattning** inkluderar inte skatt.

Logga in på [Azure EA-portalen](https://ea.azure.com). Välj sedan **Rapporter**. I flikens övre högra hörn växlar du vyn från **M** till **C** och matchar perioden på fakturan.  

![Skärmbild som visar alternativet M + C i Användningssammanfattning.](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)

Den sammanlagda summan för **Total användning** och **Azure Marketplace** bör överensstämma med **Totalt utökat belopp** på din faktura. Om du vill ha mer information om dina avgifter kan du gå till **Ladda ned användning**.  

![Skärmbild som visar fliken Ladda ned användning](./media/review-enterprise-agreement-bill/ea-portal-download-usage.png)

## <a name="review-invoiced-charges-for-other-customers"></a>Granska fakturerade avgifter för andra kunder

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

## <a name="review-service-overage-charges-for-other-customers"></a>Granska avgifter överförbrukning av tjänst för andra kunder

Det här avsnittet gäller endast om du är i Australien, Japan eller Singapore.

Jämför ditt totala användningsbelopp i Enterprise Portal i **Rapporter** > **Användningssammanfattning** med din faktura för överförbrukning av tjänst. Fakturan för överförbrukning av tjänst omfattar användning som överskrider organisationens kredit och/eller tjänster som inte omfattas av krediten. Belopp i **Användningssammanfattning** inkluderar inte skatt.

Logga in på [Azure EA-portalen](https://ea.azure.com) och välj sedan **Rapporter**. I flikens övre högra hörn växlar du vyn från **M** till **C** och matchar perioden på fakturan.  

![Skärmbild som visar alternativet M + C i Användningssammanfattning.](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)

Beloppet för **Total användning** ska överensstämma med **Totalt utökat belopp** på fakturan för överförbrukning av tjänst. Du kan få mer information om dina avgifter genom att gå till **Ladda ned användning** > **Nedladdning av avancerad rapport**. Rapporten innehåller inte skatter eller avgifter för reservationer eller Marketplace-debiteringar.  

![Skärmbild som visar Nedladdning av avancerad rapport på fliken Ladda ned användning.](./media/review-enterprise-agreement-bill/ea-portal-download-usage-advanced.png)

I följande tabell visas de termer och beskrivningar som finns på fakturan och i **Användningssammanfattning** i Enterprise Portal:

|Fakturaterm|Termen Användningssammanfattning|Beskrivning|
|---|---|---|
|Totalt utökat belopp|Total användning|Den totala användningsavgiften före skatt för den specifika perioden innan krediten tillämpas.|
|Åtagandeanvändning|Åtagandeanvändning|Den kredit som tillämpas under den specifika perioden.|
|Total försäljning|Total överförbrukning|Den totala överförbrukningsavgiften som överstiger ditt kreditbelopp. Detta belopp inkluderar inte skatt.|
|Skattebelopp|Inte tillämpligt|Skatt som gäller för det totala försäljningsbeloppet för den specifika perioden.|
|Totalt belopp|Inte tillämpligt|Det belopp som ska betalas för fakturan efter det att krediten har tillämpats och skatten lagts till.|

### <a name="review-marketplace-invoice"></a>Granska Marketplace-faktura

Det här avsnittet gäller endast om du är i Australien, Japan eller Singapore.

Jämför din totala Azure Marketplace-summa i **Rapporter** > **Användningssammanfattning** i Enterprise-portalen med din Marketplace-faktura. Marketplace-fakturan avser enbart Azure Marketplace-köp och -användning. Belopp i **Användningssammanfattning** inkluderar inte skatt.

Logga in på [Enterprise-portalen](https://ea.azure.com) och välj sedan **Rapporter**. I flikens övre högra hörn växlar du vyn från **M** till **C** och matchar perioden på fakturan.  

![Skärmbild som visar alternativet M + C i Användningssammanfattning.](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)  

Det totala **Azure Marketplace**-beloppet ska överensstämma med **Total försäljning** på din Marketplace-faktura. Du kan få mer information om dina användningsbaserade avgifter genom att gå till **Ladda ned användning**. Under **Marketplace-avgifter** väljer du **Ladda ned**. Marketplace-priset innehåller en skatt som fastställs av utgivaren. Kunder får ingen separat faktura från utgivaren för att samla in skatt på transaktionen.

![Skärmbild som visar nedladdningsalternativet under Marketplace-avgifter.](./media/review-enterprise-agreement-bill/ea-portal-download-usage-marketplace.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Granska fakturerade avgifter
> * Granska avgifter för överförbrukning av tjänst
> * Granska Marketplace-faktura

Fortsätt till nästa artikel för att lära dig mer om hur du använder Azure EA-portalen.

> [!div class="nextstepaction"]
> [Komma igång med Azure EA-portalen](../manage/ea-portal-get-started.md)
