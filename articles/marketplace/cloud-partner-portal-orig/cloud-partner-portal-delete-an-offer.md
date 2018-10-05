---
title: 'Ta bort ett erbjudande eller SKU: N från Azure Marketplace | Microsoft Docs'
description: Steg för att ta bort ett erbjudande eller SKU.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: cc172e35e8964fad3b1a1410d1f1f3240c423ab3
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2018
ms.locfileid: "48811449"
---
<a name="delete-an-offer-or-sku-from-azure-marketplace"></a>Ta bort ett erbjudande eller SKU: N från Azure Marketplace
==========================================

Av olika anledningar kan du bestämma dig för att ta bort erbjudandet från Marketplace. Om du tar bort erbjudandet garanteras att nya kunder inte längre kan köpa eller distribuera ditt erbjudande, men det påverkar inte befintliga kunder.
Uppsägning av erbjudandet handlar om att säga upp tjänsten och/eller licensavtalet mellan dig och dina befintliga kunder. Vägledning och principer som relaterar till borttagning av erbjudande och uppsägning regleras av [Publiceringsavtalet för Microsoft Marketplace](http://go.microsoft.com/fwlink/?LinkID=699560) (se avsnittet
7) och [Deltagandepolicyer](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) (se avsnittet 6.2). Scenarier och de steg du kan vidta för dem tas bort den här artikeln berättar om de olika stöds.

<a name="delete-a-live-sku-from-azure-marketplace"></a>Ta bort en live-SKU från Azure Marketplace
----------------------------------------

Du kan ta bort en live-SKU från Azure Marketplace med följande steg:

1.  Logga in på den [Cloud Partner Portal](https://cloudpartner.azure.com/).

2.  Välj ditt erbjudande från den **alla erbjudanden** fliken.

3.  I fönstret till vänster på skärmen, väljer den **SKU: er** fliken.

4.  Välj den SKU som du vill ta bort och klicka på knappen Ta bort för SKU: N.

5.  [Publicera](./cloud-partner-portal-make-offer-live-on-Azure-Marketplace.md) erbjudandet på Azure Marketplace.

När erbjudandet är live på Azure Marketplace, tas SKU: N bort från Azure Marketplace och Azure-portalen.

<a name="roll-back-to-a-previous-sku-version"></a>Återställa en tidigare version av SKU
----------------------------------

Du kan ta bort den aktuella versionen av en live-SKU från Azure Marketplace genom att följa de här stegen. När processen är klar, kommer SKU: N att återställas till en tidigare version.

1.  Logga in på den [Cloud Partner Portal](https://cloudpartner.azure.com/).

2.  Välj ditt erbjudande från den **alla erbjudanden** fliken.

3.  I fönstret till vänster på skärmen, väljer den **SKU: er** fliken.

4.  Ta bort senast **Disk Version** från listan över publicerade versioner.

5.  [Publicera](./cloud-partner-portal-make-offer-live-on-Azure-Marketplace.md) erbjudandet på Azure Marketplace.

När erbjudandet är live i Azure Marketplace, tas den aktuella versionen av den angivna SKU: N bort från Azure Marketplace och Azure-portalen.
SKU: N kommer att återställas till en tidigare version.

<a name="delete-a-live-offer"></a>Ta bort ett erbjudande med live
-------------------

Det finns olika aspekter som måste vara åtgärdat i händelse av en begäran om att ta bort ett erbjudande med live. Följ stegen nedan för att få vägledning från support-teamet att ta bort ett erbjudande med live från Azure Marketplace:

1.  Generera ett supportärende som använder det här [länk](https://go.microsoft.com/fwlink/?linkid=844975), eller genom att klicka på stöd i det övre högra hörnet av partnerportalen i molnet.

2.  Välj specifika erbjudandetypen i den **problemtyp** lista och **ta bort ett erbjudande för publicerade** i den **kategori** lista.

3.  Skicka din begäran.

Supportteamet vägleder dig genom borttagningsprocessen erbjudandet.

>[!NOTE]
>Tar bort en SKU och erbjudanden påverkar inte aktuella inköp av SKU och erbjudanden. Dessa SKU: er/erbjudanden fortsätter att fungera som tidigare. Men de inte tillgängliga för alla nya inköp framöver.
