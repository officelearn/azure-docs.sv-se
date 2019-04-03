---
title: Förstå fakturan Azure enterprise | Microsoft Docs
description: Lär dig att läsa och förstå din användningen och fakturan för Azure Enterprise-avtal
services: ''
documentationcenter: ''
author: adpick
manager: dougeby
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/01/2019
ms.author: banders
ms.openlocfilehash: 810d0795bd005cc313ec3567811ad55ba1d9d522
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2019
ms.locfileid: "58848836"
---
# <a name="understand-your-azure-enterprise-agreement-bill"></a>Förstå fakturan Azure Enterprise-avtal

Azure-kunder med Enterprise Agreement-kund får en faktura när de överskrider organisationens kredit eller använda tjänster som inte omfattas av kredit.

Din organisations kredit innehåller ditt betalningsåtagande. Summa i förskott är den organisationen förskott betalas för användning av Azure-tjänster. Du kan lägga till några prenumerationskrediter till ditt Enterprise Agreement genom att kontakta din Kontoansvariga på Microsoft eller din återförsäljare.  

## <a name="invoices-for-most-customers"></a>Fakturor för de flesta kunder

Det här avsnittet gäller inte för Azure-kunder i Australien, Japan eller Singapore. Om du använder en av dessa länder kan se [fakturor för andra kunder](#invoices-for-other-customers).

Du får en Azure-faktura när något av följande inträffar under faktureringsperioden:

- **Överförbrukning för tjänsten**: Avgifter för användning av din organisations överskrider din kreditsaldo.
- **Avgifter debiteras separat**: Tjänster som din organisation använde inte omfattas av kredit. Du är fakturerat för följande tjänster oavsett dina kreditsaldo:
    - Canonical
    - Citrix XenApp Essentials
    - Citrix XenDesktop
    - Registrerad användare
    - Openlogic
    - Fjärråtkomst Rights XenApp Essentials registrerad användare
    - Ubuntu Advantage
    - Visual Studio Enterprise (per månad)
    - Visual Studio Enterprise (årligt)
    - Visual Studio Professional (per månad)
    - Visual Studio Professional (årligt)
- **Marketplace-avgifter**: Azure Marketplace-köp och användning omfattas inte av din organisations kredit. Därför är du faktureras för Marketplace-avgifter oavsett dina kreditsaldo. I Enterprise-portalen kan en företagsadministratör aktivera och inaktivera Marketplace-köp.

## <a name="review-charges-for-most-customers"></a>Granska avgifter för de flesta kunder
Det här avsnittet gäller inte för Azure-kunder i Australien, Japan eller Singapore. Om du använder en av dessa länder kan se [granska kostnader för andra kunder](#review-charges-for-other-customers).

Om du vill granska och kontrollera avgifter på fakturan, måste du vara en företagsadministratör. Mer information finns i [förstå Azure Enterprise-avtal administrativa roller i Azure](billing-understand-ea-roles.md). Om du inte vet vem företagsadministratören är för din organisation, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

Din faktura visas alla dina Azure-användning, följt av alla Marketplace-avgifter. Om du har ett saldo, tillämpas den på Azure-användning.

Jämför dina kombinerade totala belopp som visas i Enterprise portal i **rapporter** > **Användningsöversikt** med din Azure-faktura. De mängder som anges i den **Användningsöversikt** inkluderar inte skatt.

1. Logga in på den [Enterprise portal](https://ea.azure.com).
1. Välj **rapporter**.
1. Växla från vyn på det övre högra hörnet av fliken **M** till **C** och matcha perioden på fakturan.  
    ![Skärmbild som visar M + C-alternativet i Användningsöversikt.](./media/billing-understand-your-bill-ea/ea-portal-usage-sumary-cm-option.png)
1. Den sammanlagda mängden **Total användning** och **Azure Marketplace** måste matcha den **utökade Totalmängd** på din faktura.
1. Mer information om dina debiteringar, gå till **ladda ned användning**.  
    ![Skärmbild som visar fliken hämta användning](./media/billing-understand-your-bill-ea/ea-portal-download-usage.png)

## <a name="invoices-for-other-customers"></a>Fakturor för andra kunder

Det här avsnittet gäller bara för Azure-kunder i Australien, Japan eller Singapore.

Du får en eller flera Azure-fakturor när inträffar följande:

- **Överförbrukning för tjänsten**: Avgifter för användning av din organisations överskrider din kreditsaldo.
- **Avgifter debiteras separat**: Tjänster som din organisation använde inte omfattas av kredit. Du är fakturerat för följande tjänster oavsett dina kreditsaldo:
    - Canonical
    - Citrix XenApp Essentials
    - Citrix XenDesktop
    - Registrerad användare
    - Openlogic
    - Fjärråtkomst Rights XenApp Essentials registrerad användare
    - Ubuntu Advantage
    - Visual Studio Enterprise (per månad)
    - Visual Studio Enterprise (årligt)
    - Visual Studio Professional (per månad)
    - Visual Studio Professional (årligt)
- **Marketplace-avgifter**: Azure Marketplace-köp och användning omfattas inte av din organisations kredit och faktureras separat. I Enterprise-portalen kan en företagsadministratör aktivera och inaktivera Marketplace-köp.

När du har avgifter på grund av för avgifter som debiteras separat under fakturaperioden och överförbrukning för tjänsten som du får en faktura. Den innehåller båda typerna av avgifter. Marknadsplatser kostnader faktureras alltid separat.

## <a name="review-charges-for-other-customers"></a>Granska kostnader för andra kunder

Det här avsnittet gäller endast om du är i Australien, Japan eller Singapore.

Om du vill granska och kontrollera avgifter på fakturan, måste du vara en företagsadministratör. Mer information finns i [förstå Azure Enterprise-avtal administrativa roller i Azure](billing-understand-ea-roles.md). Om du inte vet vem företagsadministratören är för din organisation, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="review-service-overage-invoice"></a>Granska-överförbrukning fakturan

Jämför din totala användning i Enterprise portal i **rapporter** > **Användningsöversikt** med servicefakturan överförbrukning. Den extra fakturan inkluderar användning som överstiger krediten för din organisation och/eller tjänster som inte omfattas av kredit. De mängder som anges på den **Användningsöversikt** inkluderar inte skatt.

1. Logga in på den [Enterprise portal](https://ea.azure.com).
1. Välj **rapporter**.
1. Växla från vyn på det övre högra hörnet av fliken **M** till **C** och matcha perioden på fakturan.  
    ![Skärmbild som visar M + C-alternativet i Användningsöversikt.](./media/billing-understand-your-bill-ea/ea-portal-usage-sumary-cm-option.png)
1. Den **Total användning** beloppet måste matcha den **utökade Totalmängd** för din överförbrukning faktura.  
1. Om du vill ha mer information om dina debiteringar kan du gå till **ladda ned användning** > **avancerade ladda ned rapporten**. Rapporten innehåller inte skatter eller avgifter för reservationer eller marketplace-avgifter.  
      ![Skärmbild som visar avancerade rapporten ladda ned på fliken hämta användning.](./media/billing-understand-your-bill-ea/ea-portal-download-usage-advanced.png)

I följande tabell visas de termer och beskrivningar som visas på fakturan och på den **Användningsöversikt** i Enterprise portal:

|Faktura termen|Sammanfattning av villkor för användning|Beskrivning|
|---|---|---|
|Totalt antal utökade belopp|Total användning|Total användning för före skatt avgiften för den specifika perioden innan krediten tillämpas.|
|Åtagande användning|Åtagande användning|Kredit som gällde under den specifika perioden.|
|Total försäljning|Total överförbrukning|Avgiften för total användning som överstiger din kredit. Den här mängden omfattar inte skatt.|
|Skattebelopp|Inte tillämpligt|Skatt som gäller för det totala försäljningsbeloppet för den specifika perioden.|
|Totalmängd|Inte tillämpligt|Hur mycket på grund av för fakturan när krediten har tillämpats och skatt läggs till.|

### <a name="marketplace-invoice"></a>Marketplace-faktura

Det här avsnittet gäller endast om du är i Australien, Japan eller Singapore.

Jämför din totala på Azure Marketplace **rapporter** > **Användningsöversikt** i Enterprise portal med din marketplace-faktura. Marketplace-fakturan avser enbart Azure Marketplace-köp och -användning. De mängder som anges på den **Användningsöversikt** inkluderar inte skatt.

1. Logga in på den [Enterprise portal](https://ea.azure.com).
1. Välj **rapporter**.
1. Växla från vyn på det övre högra hörnet av fliken **M** till **C** och matcha perioden på fakturan.  
     ![Skärmbild som visar M + C alternativet på Användningsöversikt.](./media/billing-understand-your-bill-ea/ea-portal-usage-sumary-cm-option.png)  
1. Den **Azure Marketplace** totalt ska matcha den **Total försäljning** på din marketplace-faktura.
1. Mer information om dina användningsbaserade avgifter, gå till **ladda ned användning**. Under **Marketplace-avgifter**väljer **hämta**. Den här rapporten inkluderar inga skatter och visar inte enstaka köp.  
     ![Skärmbild som visar Hämta alternativ under Marketplace-avgifter.](./media/billing-understand-your-bill-ea/ea-portal-download-usage-marketplace.png)

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg
- [Visa och hämta din Azure-användning och kostnader](billing-download-azure-daily-usage.md)
