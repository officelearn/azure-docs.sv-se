---
title: QnA Maker kryptering av data i vila
titleSuffix: Azure Cognitive Services
description: Microsoft erbjuder Microsoft-hanterade krypterings nycklar och du kan också hantera dina Cognitive Services prenumerationer med dina egna nycklar, som kallas Kundhanterade nycklar (CMK). Den här artikeln beskriver data kryptering i vila för QnA Maker och hur du aktiverar och hanterar CMK.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 07/10/2020
ms.author: egeaney
ms.openlocfilehash: 1ca0dda046329e95c649540fd42f96ca43838c85
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87086713"
---
# <a name="qna-maker-encryption-of-data-at-rest"></a>QnA Maker kryptering av data i vila

QnA Maker krypterar dina data automatiskt när de sparas i molnet, vilket hjälper till att uppfylla organisationens säkerhets-och efterlevnads mål.

## <a name="about-encryption-key-management"></a>Om hantering av krypterings nyckel

Som standard använder din prenumeration Microsoft-hanterade krypterings nycklar. Det finns också ett alternativ för att hantera din prenumeration med dina egna nycklar. Kundhanterade nycklar (CMK) ger större flexibilitet för att skapa, rotera, inaktivera och återkalla åtkomst kontroller. Du kan också granska de krypterings nycklar som används för att skydda dina data.

QnA Maker använder CMK-stöd från Azure Search. Du måste skapa [CMK i Azure Search med Azure Key Vault](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys). Den här Azure-instansen bör vara kopplad till QnA Maker tjänst för att göra den CMK aktive rad.

> [!IMPORTANT]
> Din Azure Search tjänst resurs måste ha skapats efter 2019 januari och får inte finnas på den kostnads fria nivån (delad). Det finns inget stöd för att konfigurera Kundhanterade nycklar i Azure Portal.

## <a name="enable-customer-managed-keys"></a>Aktivera Kundhanterade nycklar

Tjänsten QnA Maker använder CMK från tjänsten Azure Search. Följ de här stegen för att aktivera CMKs:

1. Skapa en ny Azure Search-instans och aktivera de krav som anges i de [Kundhanterade nyckel kraven för Azure kognitiv sökning](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys#prerequisites).

   ![Visa krypterings inställningar](../media/cognitive-services-encryption/qna-encryption-1.png)

2. När du skapar en QnA Maker resurs associeras den automatiskt med en Azure Search instans. Detta kan inte användas med CMK. Om du vill använda CMK måste du associera den nyligen skapade instansen av Azure Search som skapades i steg 1. Mer specifikt måste du uppdatera `AzureSearchAdminKey` och `AzureSearchName` i QNA Maker-resursen.

   ![Visa krypterings inställningar](../media/cognitive-services-encryption/qna-encryption-2.png)

3. Skapa sedan en ny program inställning:
   * **Namn**: ange detta till`CustomerManagedEncryptionKeyUrl`
   * **Värde**: det här är det värde som du fick i steg 1 när du skapade din Azure Search-instans.

   ![Visa krypterings inställningar](../media/cognitive-services-encryption/qna-encryption-3.png)

4. Starta om körningen när du är färdig. Nu är QnA Makers tjänsten CMK-aktiverad.

## <a name="regional-availability"></a>Regional tillgänglighet

Kundhanterade nycklar är tillgängliga i alla Azure Search regioner.

## <a name="encryption-of-data-in-transit"></a>Kryptering av data under överföring

QnA Maker portalen körs i användarens webbläsare. Varje åtgärd utlöser ett direkt anrop till respektive kognitiva tjänst-API. Därför är QnA Maker kompatibel för data under överföring.
Men eftersom QnA Maker Portal tjänsten ligger i västra USA är den fortfarande inte idealisk för kunder som inte är USA. 

## <a name="next-steps"></a>Nästa steg

* [Kryptering i Azure Search med CMKs i Azure Key Vault](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys)
* [Datakryptering i vila](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
* [Läs mer om Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)