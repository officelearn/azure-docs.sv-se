---
title: Översättare kryptering av data i vila
titleSuffix: Azure Cognitive Services
description: Översättare kryptering av data i vila.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 44bb223dbc944be1b8769aa2572f1b88b916528b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372242"
---
# <a name="translator-encryption-of-data-at-rest"></a>Översättare kryptering av data i vila

Översättaren krypterar automatiskt dina data, som du laddar upp för att skapa anpassade översättningsmodeller, när de sparas i molnet, vilket hjälper till att uppfylla dina organisatoriska säkerhets- och efterlevnadsmål.

## <a name="about-cognitive-services-encryption"></a>Om Cognitive Services-kryptering

Data krypteras och dekrypteras med [FIPS 140-2-kompatibel](https://en.wikipedia.org/wiki/FIPS_140-2) [256-bitars AES-kryptering.](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) Kryptering och dekryptering är transparenta, vilket innebär att kryptering och åtkomst hanteras för dig. Dina data är säkra som standard och du behöver inte ändra din kod eller dina program för att dra nytta av kryptering.

## <a name="about-encryption-key-management"></a>Om hantering av krypteringsnyckel

Som standard använder din prenumeration Microsoft-hanterade krypteringsnycklar. Om du använder en prisnivå som stöder kundhanterade nycklar kan du se krypteringsinställningarna för din resurs i avsnittet **Kryptering** i [Azure-portalen](https://portal.azure.com), som visas i följande avbildning.

![Visa krypteringsinställningar](../media/cognitive-services-encryption/encryptionblade.png)

För prenumerationer som bara stöder Microsoft-hanterade krypteringsnycklar har du inget **krypteringsavsnitt.**

## <a name="customer-managed-keys-with-azure-key-vault"></a>Kundhanterade nycklar med Azure Key Vault

Det finns också en möjlighet att hantera din prenumeration med dina egna nycklar. Customer-managed keys (CMK), även känd som Bring your own key (BYOK), erbjuder större flexibilitet att skapa, rotera, inaktivera och återkalla åtkomstkontroller. Du kan också granska krypteringsnycklarna som används för att skydda dina data.

> [!IMPORTANT]
> Kundhanterade nycklar är tillgängliga för alla prisnivåer för översätta-tjänsten. Om du vill begära möjligheten att använda kundhanterade nycklar fyller du i och skickar formuläret [För kundhanterad nyckelbegäran för översättare](https://aka.ms/cogsvc-cmk) det tar ungefär 3-5 arbetsdagar att höra tillbaka om statusen för din begäran. Beroende på efterfrågan kan du placeras i en kö och godkännas när utrymme blir tillgängligt. När du har godkänts för att använda CMK med translator-tjänsten måste du skapa en ny Translator-resurs. När din Translator-resurs har skapats kan du använda Azure Key Vault för att konfigurera din hanterade identitet.

Så här aktiverar du kundhanterade nycklar för Översättare:

1. Skapa din nya regionala Translator Text eller regionala Cognitive Services-resurs. Detta fungerar inte med en global resurs.
2. Aktiverad hanterad identitet i Azure-portalen och lägga till kundhanterad nyckelinformation.
3. Skapa en ny arbetsyta i Anpassad översättare och associera den här prenumerationsinformationen.

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

### <a name="enable-customer-managed-keys"></a>Aktivera kundhanterade nycklar

Du måste använda Azure Key Vault för att lagra dina kundhanterade nycklar. Du kan antingen skapa egna nycklar och lagra dem i ett nyckelvalv, eller så kan du använda Azure Key Vault API:er för att generera nycklar. Cognitive Services-resursen och nyckelvalvet måste finnas i samma region och i samma Azure Active Directory-klientorganisation (Azure AD), men de kan finnas i olika prenumerationer. Mer information om Azure Key Vault finns i [Vad är Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview).

En ny Cognitive Services-resurs krypteras alltid med Microsoft-hanterade nycklar. Det går inte att aktivera kundhanterade nycklar när resursen skapas. Kundhanterade nycklar lagras i Azure Key Vault och nyckelvalvet måste etableras med åtkomstprinciper som ger nyckelbehörigheter till den hanterade identiteten som är associerad med Cognitive Services-resursen. Den hanterade identiteten är tillgänglig så snart resursen har skapats.

Mer information om hur du använder kundhanterade nycklar med Azure Key Vault för Cognitive Services-kryptering finns i:

- [Konfigurera kundhanterade nycklar med Key Vault för Cognitive Services-kryptering från Azure-portalen](../Encryption/cognitive-services-encryption-keys-portal.md)

Om du aktiverar kundhanterade nycklar aktiveras också en systemtilldelad hanterad identitet, en funktion i Azure AD. När den tilldelade systemtilldelade hanterade identiteten är aktiverad registreras den här resursen med Azure Active Directory. När den hanterade identiteten har registrerats får den åtkomst till det nyckelvalv som valts under kundhanterade nyckelinställningar. Du kan läsa mer om [hanterade identiteter](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

> [!IMPORTANT]
> Om du inaktiverar systemtilldelade hanterade identiteter tas åtkomsten till nyckelvalvet bort och alla data som krypteras med kundnycklarna kommer inte längre att vara tillgängliga. Alla funktioner som är beroende av dessa data kommer att sluta fungera. Alla modeller som du har distribuerat kommer också att vara odeployed. Alla uppladdade data tas bort från Custom Translator. Om de hanterade identiteterna återaktiverats kommer vi inte automatiskt att distribuera om modellen åt dig.

> [!IMPORTANT]
> Hanterade identiteter stöder för närvarande inte scenarier för korskataloger. När du konfigurerar kundhanterade nycklar i Azure-portalen tilldelas en hanterad identitet automatiskt under skalen. Om du sedan flyttar prenumerationen, resursgruppen eller resursen från en Azure AD-katalog till en annan överförs inte den hanterade identiteten som är associerad med resursen till den nya klienten, så kundhanterade nycklar kanske inte längre fungerar. Mer information finns i **Överföra en prenumeration mellan Azure AD-kataloger** i vanliga frågor och andra frågor och kända problem med hanterade [identiteter för Azure-resurser](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories).  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Lagra kundhanterade nycklar i Azure Key Vault

Om du vill aktivera kundhanterade nycklar måste du använda ett Azure Key Vault för att lagra dina nycklar. Du måste aktivera egenskaperna **Mjuk borttagning** **och Rensa inte** på nyckelvalvet.

Endast RSA-nycklar i storlek 2048 stöds med Cognitive Services-kryptering. Mer information om nycklar finns i **Key Vault-nycklar** i [Om Azure Key Vault-nycklar, hemligheter och certifikat](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys).

> [!NOTE]
> Om hela nyckelvalvet tas bort visas inte längre dina data och alla dina modeller kommer att distribueras. Alla uppladdade data tas bort från Custom Translator. 

### <a name="revoke-access-to-customer-managed-keys"></a>Återkalla åtkomst till kundhanterade nycklar

Om du vill återkalla åtkomsten till kundhanterade nycklar använder du PowerShell eller Azure CLI. Mer information finns i [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//) eller [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault). Om du återkallar åtkomst blockeras åtkomsten till alla data i Cognitive Services-resursen och dina modeller inte distribueras, eftersom krypteringsnyckeln inte är tillgänglig för Cognitive Services. Alla uppladdade data kommer också att raderas från Custom Translator.


## <a name="next-steps"></a>Nästa steg

* [Läs mer om Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
