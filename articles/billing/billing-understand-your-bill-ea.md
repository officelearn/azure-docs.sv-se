---
title: Förstå fakturan för Azure Enterprise | Microsoft Docs
description: Lär dig att läsa och förstå din användningen och fakturan för Azure-kunder med ett Enterprise-avtal
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
ms.date: 11/02/2018
ms.author: banders
ms.openlocfilehash: 36ce4d96e02bac1eae1791acf811da468726b4a6
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2019
ms.locfileid: "54902711"
---
# <a name="understand-your-bill-for-azure-customers-with-an-enterprise-agreement"></a>Förstå fakturan för Azure-kunder med ett Enterprise-avtal

Azure-kunder med Enterprise Agreement-kund får en faktura när de överskrider organisationens kredit eller använda tjänster som inte omfattas av kredit. 

Din organisations kredit innehåller ditt betalningsåtagande. Summa i förskott är den organisationen förskott betalas för användning av Azure-tjänster. Du kan lägga till några prenumerationskrediter till ditt Enterprise Agreement genom att kontakta din Kontoansvariga på Microsoft eller din återförsäljare.  

## <a name="when-credit-exceeded-or-doesnt-apply"></a>När kredit har överskridits eller kan inte användas

Du får en eller flera fakturor när inträffar följande:

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
- **Marketplace-avgifter**: Azure Marketplace-köp och användning omfattas inte av din organisations kredit och faktureras separat. Enterprise-administratören har möjlighet att aktivera och inaktivera Marketplace-köp för sin organisation i Enterprise Portal. 

När du har avgifter på grund av för både överförbrukning för tjänsten och avgifter som debiteras separat under faktureringsperioden måste du hämta en faktura som innehåller båda typerna av avgifter. Marknadsplatser kostnader faktureras alltid separat.

## <a name="review-charges"></a>Granska avgifter

Om du vill granska och kontrollera avgifter på fakturan, måste du vara en företagsadministratör. Mer information finns i [förstå Azure Enterprise-avtal administrativa roller i Azure](billing-understand-ea-roles.md). Om du inte vet vem företagsadministratören är för din organisation, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="review-service-overage-invoice"></a>Granska-överförbrukning fakturan

Jämför din totala användning från Enterprise-portalen i **rapporter** > **Användningsöversikt** med servicefakturan överförbrukning. Den extra fakturan inkluderar användning som överstiger krediten för din organisation och/eller tjänster som inte omfattas av kredit. De mängder som anges på den **Användningsöversikt** inkluderar inte skatt. 

1. Logga in på den [Enterprise portal](https://ea.azure.com).
1. Välj **rapporter**.
1. Växla från vyn på det övre högra hörnet av fliken **M** till **C** och matcha perioden på fakturan.
 
   ![Skärmbild som visar M + C alternativet på Användningsöversikt.](./media/billing-understand-your-bill-ea/ea-portal-usage-sumary-cm-option.png)

1. Den **Total användning** beloppet måste matcha den **utökade Totalmängd** för din överförbrukning faktura. I följande tabell visas de termer och beskrivningar som visas på fakturan och på den **Användningsöversikt** i Enterprise portal:

   |Faktura termen|Sammanfattning av villkor för användning|Beskrivning|
   |---|---|---|
   |Totalt antal utökade belopp|Total användning|Total användning för före skatt avgiften för den specifika perioden innan krediten tillämpas.|
   |Åtagande användning|Åtagande användning|Kredit som gällde under den specifika perioden.|
   |Total försäljning|Total överförbrukning|Avgiften för total användning som överstiger din kredit. Den här mängden omfattar inte skatt.|
   |Skattebelopp|Inte tillämpligt|Skatt som gäller för det totala försäljningsbeloppet för den specifika perioden.|
   |Totalmängd|Inte tillämpligt|Hur mycket på grund av för fakturan när krediten har tillämpats och skatt läggs till.|
1. Om du vill ha mer information om dina debiteringar kan du gå till **ladda ned användning** > **avancerade ladda ned rapporten**. Den här rapporten innehåller inte skatter eller avgifter för reservationer eller marketplace-avgifter.

      ![Skärmbild som visar avancerade rapporten ladda ned på fliken hämta användning.](./media/billing-understand-your-bill-ea/ea-portal-download-usage-advanced.png)

### <a name="review-marketplace-invoice"></a>Granska marketplace-faktura

Jämför din totala på Azure Marketplace **rapporter** > **Användningsöversikt** i Enterprise portal med din marketplace-faktura. Marketplace-faktura är bara för Azure Marketplace-köp och användning. De mängder som anges på den **Användningsöversikt** inkluderar inte skatt. 

1. Logga in på den [Enterprise portal](https://ea.azure.com).
1. Välj **rapporter**.
1. Växla från vyn på det övre högra hörnet av fliken **M** till **C** och matcha perioden på fakturan.

     ![Skärmbild som visar M + C alternativet på Användningsöversikt.](./media/billing-understand-your-bill-ea/ea-portal-usage-sumary-cm-option.png)

1. Den **Azure Marketplace** totalt ska matcha den **Total försäljning** på din marketplace-faktura.
1. Mer information om dina användningsbaserade avgifter, gå till **ladda ned användning**. Under **Marketplace-avgifter**väljer **hämta**. Den här rapporten inte inkludera skatter eller visa enstaka inköp.

     ![Skärmbild som visar Hämta alternativ under Marketplace-avgifter.](./media/billing-understand-your-bill-ea/ea-portal-download-usage-marketplace.png)

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
