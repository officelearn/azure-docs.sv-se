---
title: Granska Azure Enterprise-avtal Bill
description: Lär dig att läsa och förstå din användning och fakturering för Azure Enterprise-avtal.
author: banders
manager: dougeby
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/02/2020
ms.author: banders
ms.openlocfilehash: 2beb1d3e4468ff96f629dd6ec7015376a7f3a904
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "75987266"
---
# <a name="understand-your-azure-enterprise-agreement-bill"></a>Förstå fakturan för ditt Azure Enterprise-avtal

Azure-kunder med ett Enterprise-avtal får en faktura när de överskrider organisationens kredit eller använder tjänster som inte omfattas av krediten.

Din organisations kredit inkluderar ditt ekonomiska åtagande. Det ekonomiska åtagandet är det belopp som din organisation förskottsbetalade för användningen av Azure-tjänster. Du kan lägga till belopp för ekonomiskt åtagande till ditt Enterprise-avtal genom att kontakta din Microsoft-kontoansvarige eller återförsäljare.

Den här självstudien gäller endast för Azure-kunder med en Azure-Enterprise-avtal.

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Granska fakturerade avgifter
> * Granska service avgifter
> * Granska marknads plats faktura

## <a name="prerequisites"></a>Krav

För att kunna granska och verifiera avgifterna på din faktura måste du vara företagsadministratör. Mer information finns i [Förstå administrativa roller för Azure Enterprise-avtal i Azure](../manage/understand-ea-roles.md). Om du inte vet vem som är företagsadministratör för din organisation bör du [kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="review-invoiced-charges-for-most-customers"></a>Granska fakturerade kostnader för de flesta kunder

Det här avsnittet gäller inte för Azure-kunder i Australien, Japan eller Singapore.

Du får en Azure-faktura när något av följande inträffar under din faktureringsperiod:

- Överförbrukning av **tjänst**: din organisations användnings avgifter överstiger ditt kredit belopp.
- Debiterade **avgifter separat**: de tjänster som organisationen använder omfattas inte av krediten. Du faktureras för följande tjänster oavsett ditt kreditsaldo:
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
- **Marketplace-avgifter**: Azure Marketplace-köp och användning omfattas inte av din organisations kredit. Du faktureras därmed för Marketplace-avgifter oavsett ditt kreditsaldo. I Enterprise Portal kan en företagsadministratör aktivera och inaktivera Marketplace-köp.

Din faktura visar all din Azure-användning, följt av eventuella Marketplace-avgifter. Om du har ett kreditsaldo tillämpas det på Azure-användning.

Jämför det sammanlagda belopp som visas i Enterprise Portal i **Rapporter** > **Användningssammanfattning** med din Azure-faktura. De belopp som anges i **Användningssammanfattning** inkluderar inte skatt.

Logga in på [Azure-EA-portalen](https://ea.azure.com). Välj sedan **rapporter**. I flikens övre högra hörn växlar du vyn från **M** till **C** och matchar perioden på fakturan.  

![Skärm bild som visar alternativet M + C i användnings översikt.](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)

Den sammanlagda summan för **Total användning** och **Azure Marketplace** bör överensstämma med **Totalt utökat belopp** på din faktura. Om du vill ha mer information om dina avgifter kan du gå till **Ladda ned användning**.  

![Skärm bild som visar fliken hämtnings användning](./media/review-enterprise-agreement-bill/ea-portal-download-usage.png)

## <a name="review-invoiced-charges-for-other-customers"></a>Granska fakturerade kostnader för andra kunder

Det här avsnittet gäller endast för Azure-kunder i Australien, Japan eller Singapore.

Du får en eller flera Azure-fakturor när något av följande inträffar:

- Överförbrukning av **tjänst**: din organisations användnings avgifter överstiger ditt kredit belopp.
- Debiterade **avgifter separat**: de tjänster som organisationen använder omfattas inte av krediten. Du faktureras för följande tjänster:
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
- **Marketplace-avgifter**: Azure Marketplace-köp och användning täcks inte av din organisations kredit och faktureras separat. I Enterprise Portal kan en företagsadministratör aktivera och inaktivera Marketplace-köp.

När du har avgifter som har förfallit för överförbrukning av tjänst och avgifter som fakturas separat under faktureringsperioden får du en faktura. Den innehåller båda typerna av avgifter. Marketplace-avgifter faktureras alltid separat.

## <a name="review-service-overage-charges-for-other-customers"></a>Granska kostnader för tjänsten för andra kunder

Det här avsnittet gäller endast om du är i Australien, Japan eller Singapore.

Jämför ditt totala användningsbelopp i Enterprise Portal i **Rapporter** > **Användningssammanfattning** med din faktura för överförbrukning av tjänst. Fakturan för överförbrukning av tjänst omfattar användning som överskrider organisationens kredit och/eller tjänster som inte omfattas av krediten. Belopp i **Användningssammanfattning** inkluderar inte skatt.

Logga in på [Azure-EA-portalen](https://ea.azure.com) och välj sedan **rapporter**. I flikens övre högra hörn växlar du vyn från **M** till **C** och matchar perioden på fakturan.  

![Skärm bild som visar alternativet M + C i användnings översikt.](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)

Beloppet för **Total användning** ska överensstämma med **Totalt utökat belopp** på fakturan för överförbrukning av tjänst. Du kan få mer information om dina avgifter genom att gå till **Ladda ned användning** > **Nedladdning av avancerad rapport**. Rapporten innehåller inte skatter eller avgifter för reservationer eller Marketplace-debiteringar.  

![Skärm bild som visar avancerad rapport hämtning på fliken hämtnings användning.](./media/review-enterprise-agreement-bill/ea-portal-download-usage-advanced.png)

I följande tabell visas de termer och beskrivningar som finns på fakturan och i **Användningssammanfattning** i Enterprise Portal:

|Fakturaterm|Termen Användningssammanfattning|Beskrivning|
|---|---|---|
|Totalt utökat belopp|Total användning|Den totala användningsavgiften före skatt för den specifika perioden innan krediten tillämpas.|
|Åtagandeanvändning|Åtagandeanvändning|Den kredit som tillämpas under den specifika perioden.|
|Total försäljning|Total överförbrukning|Den totala överförbrukningsavgiften som överstiger ditt kreditbelopp. Detta belopp inkluderar inte skatt.|
|Skattebelopp|Inte aktuellt|Skatt som gäller för det totala försäljningsbeloppet för den specifika perioden.|
|Totalt belopp|Inte aktuellt|Det belopp som ska betalas för fakturan efter det att krediten har tillämpats och skatten lagts till.|

### <a name="review-marketplace-invoice"></a>Granska marknads plats faktura

Det här avsnittet gäller endast om du är i Australien, Japan eller Singapore.

Jämför din totala Azure Marketplace-summa i **Rapporter** > **Användningssammanfattning** i Enterprise-portalen med din Marketplace-faktura. Marketplace-fakturan avser enbart Azure Marketplace-köp och -användning. Belopp i **Användningssammanfattning** inkluderar inte skatt.

Logga in på [Enterprise Portal](https://ea.azure.com) och välj sedan **rapporter**. I flikens övre högra hörn växlar du vyn från **M** till **C** och matchar perioden på fakturan.  

![Skärm bild som visar alternativen M + C i användnings översikt.](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)  

Det totala **Azure Marketplace**-beloppet ska överensstämma med **Total försäljning** på din Marketplace-faktura. Du kan få mer information om dina användningsbaserade avgifter genom att gå till **Ladda ned användning**. Under **Marketplace-avgifter** väljer du **Ladda ned**. Den här rapporten inkluderar inga skatter och visar inte enstaka köp.  

![Skärm bild som visar hämtnings alternativet under Marketplace-avgifter.](./media/review-enterprise-agreement-bill/ea-portal-download-usage-marketplace.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Granska fakturerade avgifter
> * Granska service avgifter
> * Granska marknads plats faktura

Fortsätt till nästa artikel om du vill lära dig mer om hur du använder Azure EA-portalen.

> [!div class="nextstepaction"]
> [Komma igång med Azure EA-portalen](../manage/ea-portal-get-started.md)
