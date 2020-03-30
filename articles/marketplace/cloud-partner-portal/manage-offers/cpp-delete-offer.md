---
title: Ta bort marknadsplatserbjudanden | Azure Marketplace
description: Ta bort erbjudanden på Azure- och AppSource Marketplaces med cloud partnerportalen
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: dsindona
ms.openlocfilehash: fa8ee834bd6d9261c47c540e9517c200c894b6c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80286463"
---
# <a name="delete-azure-marketplace-and-appsource-offers-or-skus"></a>Ta bort Azure Marketplace- och AppSource-erbjudanden eller SKU:er

Av olika skäl kan du välja att dra tillbaka ditt erbjudande från microsoft-marknaden, som kan ta två former:

- *Borttagning av erbjudanden* säkerställer att nya kunder inte längre kan köpa eller distribuera ditt erbjudande, men har ingen inverkan på befintliga kunder, som du måste stödja enligt ditt licensavtal och relevanta lagar. 
- *Erbjudandet uppsägning* är processen att avsluta tjänsten och / eller licensavtal mellan dig och dina befintliga kunder. 

Vägledning och policyer för borttagning och uppsägning av erbjudanden regleras av [Microsoft Marketplace Publisher Agreement](https://go.microsoft.com/fwlink/?LinkID=699560) och [deltagandepolicyerna](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) (avsnittet [Erbjuder avstängning och borttagning).](https://docs.microsoft.com/legal/marketplace/participation-policy#offering-suspension-and-removal) 

I den här artikeln beskrivs de olika scenarierna för borttagning som stöds och de steg som krävs för att utföra var och en.  

> [!NOTE]
> Du kan ta bort ett erbjudande som inte har publicerats genom att helt enkelt välja knappen **Ta bort** i verktygsfältet på fliken **Redigerare.**


## <a name="delete-a-published-sku-from-the-azure-marketplace"></a>Ta bort en publicerad SKU från Azure Marketplace

Du kan ta bort en publicerad SKU från Azure Marketplace med följande steg:

1.  Logga in på [Molnpartnerportalen](https://cloudpartner.azure.com/).
2.  På sidan **Alla erbjudanden** väljer du ditt erbjudande.  Erbjudandet ska visas på fliken **Redigerare.**
3.  Välj fliken **SKU:er** i det vänstra verktygsfältet. 
4.  Markera den SKU som du vill ta bort och klicka på knappen **Ta bort.**
5.  [Publicera](./cpp-publish-offer.md) erbjudandet på Azure Marketplace.

När det ändrade erbjudandet har publicerats på Azure Marketplace visas inte längre den valda SKU:n i Azure Marketplace och Azure-portalen.


## <a name="roll-back-to-a-previous-sku-version"></a>Återställ till en tidigare SKU-version

Du kan ta bort den aktuella versionen av en publicerad SKU från Azure Marketplace med hjälp av stegen här. När processen är klar återställs SKU:n till den tidigare versionen.

1. Logga in på [Molnpartnerportalen](https://cloudpartner.azure.com/).
2. På sidan **Alla erbjudanden** väljer du ditt erbjudande.  Erbjudandet ska visas på fliken **Redigerare.**
3. Välj fliken **SKU:er** i det vänstra verktygsfältet. 
4. Ta bort den senaste versionen av den associerade lösningstillgången från listan över diskversioner.  Beroende på erbjudandetypen kan det här fältet vara **Diskversion,** **Paketversioner**eller liknande tillgång. 
5. [Publicera](./cpp-publish-offer.md) erbjudandet på Azure Marketplace.

När det ändrade erbjudandet har publicerats påAzure Marketplace visas inte längre den aktuella versionen av den angivna SKU:n. i Azure Marketplace och Azure-portalen.  SKU:n återställs till den tidigare versionen.


## <a name="delete-a-live-offer"></a>Ta bort ett live-erbjudande

Det finns olika procedurmässiga, affärsmässiga och juridiska aspekter för att ta bort ett levande erbjudande. Följ följande steg för att få vägledning från supportteamet för att ta bort ett live-erbjudande från Azure Marketplace:

1.  Höj en supportbiljett med hjälp av sidan [Skapa en incident](https://go.microsoft.com/fwlink/?linkid=844975) eller genom att klicka på **Support** i det övre högra hörnet av Cloud [Partner Portal](https://cloudpartner.azure.com/).

2.  Välj din specifika erbjudandetyp i listan **Problemtyp** och välj **Ta bort ett publicerat erbjudande** i listan **Kategori.**

3.  Skicka begäran.

Supportteamet guidar dig genom borttagningsprocessen för erbjudandet.

> [!NOTE]
> Om du tar bort ett erbjudande (eller SKU) påverkas inte aktuella köp av erbjudandet (eller SKU). Dessa inköp kommer att fortsätta att fungera som tidigare. Borttagna erbjudanden eller SKU:er är dock inte tillgängliga för framtida köp.


## <a name="next-steps"></a>Nästa steg

När du är bekant med de grundläggande åtgärder som används för att hantera erbjudanden är du redo att skapa en instans av ett [Microsoft marketplace-erbjudande](../cpp-marketplace-offers.md).
