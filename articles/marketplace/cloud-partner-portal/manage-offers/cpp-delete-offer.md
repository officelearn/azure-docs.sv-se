---
title: Ta bort Marketplace-erbjudanden | Azure Marketplace
description: Ta bort erbjudanden på Azure-och AppSource-Marketplace med hjälp av Cloud Partner Portal
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: pabutler
ms.openlocfilehash: 6a75fbb0c4b9c364342a406a8076128346943101
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826770"
---
# <a name="delete-azure-marketplace-and-appsource-offers-or-skus"></a>Ta bort Azure Marketplace-och AppSource-erbjudanden eller SKU: er

Av olika anledningar kan du välja att dra tillbaka ditt erbjudande från Microsofts marknads plats, som kan ta två formulär:

- Med alternativet för att *ta bort* kan nya kunder inte längre köpa eller distribuera ditt erbjudande, utan påverkar befintliga kunder, som du måste ha stöd för enligt ditt licens avtal och relevanta lagar. 
- *Avslutning av erbjudandet* är processen att avsluta tjänsten och/eller licens avtalet mellan dig och dina befintliga kunder. 

Vägledning och principer som rör borttagning och uppsägning av erbjudanden regleras av [Microsoft Marketplace utgivar avtal](https://go.microsoft.com/fwlink/?LinkID=699560) och [deltagande principer](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) (avsnitt som [erbjuder SUS pension och borttagning](https://docs.microsoft.com/legal/marketplace/participation-policy#offering-suspension-and-removal)). 

Den här artikeln beskriver de olika scenarier som stöds och de steg som krävs för att utföra var och en.  

> [!NOTE]
> Du kan ta bort ett erbjudande som inte har publicerats genom att bara välja knappen **ta bort** i verktygsfältet på fliken **redigerare** .


## <a name="delete-a-published-sku-from-the-azure-marketplace"></a>Ta bort en publicerad SKU från Azure Marketplace

Du kan ta bort en publicerad SKU från Azure Marketplace på följande sätt:

1.  Logga in på [Cloud Partner Portal](https://cloudpartner.azure.com/).
2.  På sidan **alla erbjudanden** väljer du ditt erbjudande.  Ditt erbjudande bör visas på fliken **redigerare** .
3.  Välj fliken **SKU: er** i det vänstra verktygsfältet. 
4.  Välj den SKU som du vill ta bort och klicka på knappen **ta bort** .
5.  [Publicera](./cpp-publish-offer.md) om erbjudandet på Azure Marketplace.

När det ändrade erbjudandet har publicerats på Azure Marketplace visas inte längre den valda SKU: n i Azure Marketplace och Azure Portal.


## <a name="roll-back-to-a-previous-sku-version"></a>Återställa till en tidigare version av SKU

Du kan ta bort den aktuella versionen av en publicerad SKU från Azure Marketplace genom att följa stegen här. När processen är klar återställs SKU: n till den tidigare versionen.

1. Logga in på [Cloud Partner Portal](https://cloudpartner.azure.com/).
2. På sidan **alla erbjudanden** väljer du ditt erbjudande.  Ditt erbjudande bör visas på fliken **redigerare** .
3. Välj fliken **SKU: er** i det vänstra verktygsfältet. 
4. Ta bort den senaste versionen av den associerade lösnings till gången från listan över disk versioner.  Beroende på typen av erbjudande kan det här fältet vara **disk version**, **paket versioner**eller liknande till gång. 
5. [Publicera](./cpp-publish-offer.md) om erbjudandet på Azure Marketplace.

När det ändrade erbjudandet har publicerats på theAzure Marketplace visas inte längre den aktuella versionen av den angivna SKU: n. på Azure Marketplace och Azure Portal.  SKU: n återställs till den tidigare versionen.


## <a name="delete-a-live-offer"></a>Ta bort ett Live-erbjudande

Det finns olika procedurer, verksamheter och juridiska aspekter för att ta bort ett Live-erbjudande. Följ stegen nedan för att få vägledning från support teamet för att ta bort ett Live-erbjudande från Azure Marketplace:

1.  Skapa ett support ärende på sidan [skapa en incident](https://go.microsoft.com/fwlink/?linkid=844975) eller klicka på **stöd** i det övre högra hörnet av [Cloud Partner Portal](https://cloudpartner.azure.com/).

2.  Välj din önskade erbjudande typ i listan **problem typ** och välj **ta bort ett publicerat erbjudande** i listan **kategori** .

3.  Skicka begäran.

Support teamet vägleder dig genom borttagnings processen för erbjudandet.

> [!NOTE]
> Om du tar bort ett erbjudande (eller SKU) påverkas inte det aktuella köpet av erbjudandet (eller SKU). Dessa inköp kommer att fortsätta att fungera som tidigare. Men borttagna erbjudanden eller SKU: er är inte tillgängliga för framtida köp.


## <a name="next-steps"></a>Nästa steg

När du är bekant med de grundläggande åtgärderna som används för att hantera erbjudanden är du redo att skapa en instans av ett Microsoft [Marketplace-erbjudande](../cpp-marketplace-offers.md).
