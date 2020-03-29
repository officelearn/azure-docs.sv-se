---
title: QnA Maker kryptering av data i vila
titleSuffix: Azure Cognitive Services
description: QnA Maker-kryptering av data i vila .
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: bd64321a6a7afbac61a63365c77a75120f837e83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372099"
---
# <a name="qna-maker-encryption-of-data-at-rest"></a>QnA Maker kryptering av data i vila

QnA Maker krypterar automatiskt dina data när de sparas i molnet, vilket hjälper till att uppfylla dina organisatoriska säkerhets- och efterlevnadsmål.

## <a name="about-encryption-key-management"></a>Om hantering av krypteringsnyckel

Som standard använder din prenumeration Microsoft-hanterade krypteringsnycklar. Det finns också en möjlighet att hantera din prenumeration med dina egna nycklar. Cmk (Customer-managed keys) erbjuder större flexibilitet att skapa, rotera, inaktivera och återkalla åtkomstkontroller. Du kan också granska krypteringsnycklarna som används för att skydda dina data.

QnA Maker använder CMK-stöd från Azure-sökning. Du måste skapa [CMK i Azure Search med Azure Key Vault](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys). Den här Azure-instansen bör associeras med QnA Maker-tjänsten för att göra den CMK aktiverad.

> [!IMPORTANT]
> Din Azure Search-tjänstresurs måste ha skapats efter januari 2019 och kan inte vara på den kostnadsfria (delade) nivån. Det finns ingen support för att konfigurera kundhanterade nycklar i Azure-portalen.

## <a name="enable-customer-managed-keys"></a>Aktivera kundhanterade nycklar

Tjänsten QnA Maker använder CMK från Azure Search-tjänsten. Så här aktiverar du CMK:er:

1. Skapa en ny Azure Search-instans och aktivera de förutsättningar som nämns i de [kundhanterade nyckelförutsättningarna för Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys#prerequisites).

   ![Visa krypteringsinställningar](../media/cognitive-services-encryption/qna-encryption-1.png)

2. När du skapar en QnA Maker-resurs associeras den automatiskt med en Azure Search-instans. Detta kan inte användas med CMK. Om du vill använda CMK måste du associera din nyskapade instans av Azure Search som skapades i steg 1. Specifikt måste du uppdatera `AzureSearchAdminKey` och `AzureSearchName` i din QnA Maker-resurs.

   ![Visa krypteringsinställningar](../media/cognitive-services-encryption/qna-encryption-2.png)

3. Skapa sedan en ny programinställning:
   * **Namn**: Ställ in detta på`CustomerManagedEncryptionKeyUrl`
   * **Värde:** Det här är värdet som du fick i steg 1 när du skapade din Azure Search-instans.

   ![Visa krypteringsinställningar](../media/cognitive-services-encryption/qna-encryption-3.png)

4. Starta om körningen när du är klar. Nu är din QnA Maker-tjänst CMK-aktiverad.

## <a name="regional-availability"></a>Regional tillgänglighet

Kundhanterade nycklar är tillgängliga i alla Azure Search-regioner.

## <a name="next-steps"></a>Nästa steg

* [Kryptering i Azure Search med CMK:er i Azure Key Vault](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys)
* [Datakryptering i vila](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
* [Läs mer om Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
