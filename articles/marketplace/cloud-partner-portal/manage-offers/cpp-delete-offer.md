---
title: Ta bort Marketplace-erbjudanden – Azure Marketplace | Microsoft Docs
description: Ta bort erbjudanden på Azure och AppSource marknadsplatser med partnerportalen i molnet
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: pbutlerm
ms.openlocfilehash: 7b4ee33d3e231dc59ce1d7b4ae6337efb11e96bc
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2019
ms.locfileid: "54355855"
---
# <a name="delete-azure-marketplace-and-appsource-offers-or-skus"></a>Ta bort Azure Marketplace och AppSource erbjudanden och SKU: er

Av olika skäl, kan du välja att återkalla ditt erbjudande från dess Microsoft marketplace, vilket kan ske på två sätt:

- *Erbjuder borttagning av* säkerställer att nya kunder inte längre kan köpa eller distribuera ditt erbjudande, men påverkar inte befintliga kunder som du måste ha stöd för enligt ditt licensavtal (EULA) och relevanta lagar. 
- *Erbjuder avslutning* är processen att säga upp tjänsten och/eller licensavtalet mellan dig och dina befintliga kunder. Vägledning och principer som relaterar till borttagning av erbjudande och uppsägning regleras av [Publiceringsavtalet för Microsoft Marketplace](https://go.microsoft.com/fwlink/?LinkID=699560) (avsnitt 7) och [Deltagandepolicyer](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) (avsnittet 6.2). 

Den här artikeln berättar om de olika stöd för borttagning av scenarier och de steg som krävs för att utföra var och en.  

> [!NOTE]
> Du kan ta bort ett erbjudande som inte har publicerats genom att helt enkelt välja det **ta bort** i verktygsfältet för den **redigeraren** fliken.


## <a name="delete-a-published-sku-from-the-azure-marketplace"></a>Ta bort en publicerade SKU från Azure Marketplace

Du kan ta bort en publicerade SKU från Azure Marketplace med följande steg:

1.  Logga in på den [Cloud Partner Portal](https://cloudpartner.azure.com/).
2.  I den **alla erbjudanden** väljer du ditt erbjudande.  Ditt erbjudande ska visas i den **redigeraren** fliken.
3.  I det vänstra verktygsfältet så väljer du den **SKU: er** fliken. 
4.  Välj den SKU som du vill ta bort och klicka på den **ta bort** knappen.
5.  [Publicera](./cpp-publish-offer.md) erbjudandet på Azure Marketplace.

När det ändrade erbjudandet har publicerats på Azure Marketplace, visas inte längre den valda SKU: N i Azure Marketplace och Azure-portalen.


## <a name="roll-back-to-a-previous-sku-version"></a>Återställa en tidigare version av SKU

Du kan ta bort den aktuella versionen av en publicerade SKU från Azure Marketplace med hjälp av stegen här. När processen är klar, återställs SKU: N till en tidigare version.

1. Logga in på den [Cloud Partner Portal](https://cloudpartner.azure.com/).
2. I den **alla erbjudanden** väljer du ditt erbjudande.  Ditt erbjudande ska visas i den **redigeraren** fliken.
3. I det vänstra verktygsfältet så väljer du den **SKU: er** fliken. 
4. Ta bort den senaste versionen av den associera lösningen tillgången i listan över versioner.  Beroende på typ av erbjudande, det här fältet kan vara **Disk Version**, **paketversioner**, eller liknande tillgången. 
5. [Publicera](./cpp-publish-offer.md) erbjudandet på Azure Marketplace.

När det ändrade erbjudandet har publicerats på theAzure Marketplace, visas inte längre den aktuella versionen av den angivna SKU: N. i Azure Marketplace och Azure-portalen.  SKU: N återställs till en tidigare version.


## <a name="delete-a-live-offer"></a>Ta bort ett erbjudande med live

Det finns olika procedur, företag och juridiska aspekter att ta bort ett erbjudande med live. Följ stegen nedan för att få vägledning från support-teamet att ta bort ett erbjudande med live från Azure Marketplace:

1.  Generera en stöd biljett med den [skapar en incident](https://go.microsoft.com/fwlink/?linkid=844975) sidan eller genom att klicka på **stöder** i det övre högra hörnet av den [Cloud Partner Portal](https://cloudpartner.azure.com/).

2.  Välj specifika erbjudandetypen i den **problemtyp** listan och välj **ta bort ett erbjudande för publicerade** i den **kategori** lista.

3.  Skicka din begäran.

Supportteamet vägleder dig genom borttagningsprocessen erbjudandet.

> [!NOTE]
> Tar bort ett erbjudande (eller SKU) påverkas inte aktuella inköp som erbjudandet (eller SKU). Dessa inköp fortsätter att fungera som tidigare. Dock har tagits bort erbjudanden och SKU: er inte tillgänglig för alla framtida köp.


## <a name="next-steps"></a>Nästa steg

När du är bekant med de grundläggande åtgärderna som används för att hantera erbjudanden, är du redo att skapa en instans av en Microsoft [marketplace-erbjudandet](../cpp-marketplace-offers.md).
