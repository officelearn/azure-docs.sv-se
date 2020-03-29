---
title: Språk understanding service kryptering av data i vila
titleSuffix: Azure Cognitive Services
description: Språk understanding service kryptering av data i vila.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 59e066974f690bda2384504cc27af5aa94b7b75b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372341"
---
# <a name="language-understanding-service-encryption-of-data-at-rest"></a>Språk understanding service kryptering av data i vila

Tjänsten Språk understanding krypterar automatiskt dina data när de sparas till molnet. Krypteringen för tjänsten Språk understanding skyddar dina data och hjälper dig att uppfylla dina organisatoriska säkerhets- och efterlevnadsåtaganden.

## <a name="about-cognitive-services-encryption"></a>Om Cognitive Services-kryptering

Data krypteras och dekrypteras med [FIPS 140-2-kompatibel](https://en.wikipedia.org/wiki/FIPS_140-2) [256-bitars AES-kryptering.](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) Kryptering och dekryptering är transparenta, vilket innebär att kryptering och åtkomst hanteras för dig. Dina data är säkra som standard och du behöver inte ändra din kod eller dina program för att dra nytta av kryptering.

## <a name="about-encryption-key-management"></a>Om hantering av krypteringsnyckel

Som standard använder din prenumeration Microsoft-hanterade krypteringsnycklar. Det finns också en möjlighet att hantera din prenumeration med dina egna nycklar. Customer-managed keys (CMK), erbjuder större flexibilitet att skapa, rotera, inaktivera och återkalla åtkomstkontroller. Du kan också granska krypteringsnycklarna som används för att skydda dina data.

## <a name="customer-managed-keys-with-azure-key-vault"></a>Kundhanterade nycklar med Azure Key Vault

Det finns också en möjlighet att hantera din prenumeration med dina egna nycklar. Customer-managed keys (CMK), även känd som Bring your own key (BYOK), erbjuder större flexibilitet att skapa, rotera, inaktivera och återkalla åtkomstkontroller. Du kan också granska krypteringsnycklarna som används för att skydda dina data.

Du måste använda Azure Key Vault för att lagra dina kundhanterade nycklar. Du kan antingen skapa egna nycklar och lagra dem i ett nyckelvalv, eller så kan du använda Azure Key Vault API:er för att generera nycklar. Cognitive Services-resursen och nyckelvalvet måste finnas i samma region och i samma Azure Active Directory-klientorganisation (Azure AD), men de kan finnas i olika prenumerationer. Mer information om Azure Key Vault finns i [Vad är Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview).

### <a name="customer-managed-keys-for-language-understanding"></a>Kundhanterade nycklar för språkförståelse

Om du vill begära möjligheten att använda kundhanterade nycklar fyller du i och skickar [LUIS Service-kundhanterade nyckelbegärandeformuläret](https://aka.ms/cogsvc-cmk). Det tar cirka 3-5 arbetsdagar att höra om statusen för din begäran. Beroende på efterfrågan kan du placeras i en kö och godkännas när utrymme blir tillgängligt. När du har godkänts för att använda CMK med LUIS måste du skapa en ny språk understanding-resurs från Azure-portalen och välja E0 som prisnivå. Den nya SKU:n fungerar på samma sätt som F0 SKU som redan är tillgänglig förutom CMK. Användare kommer inte att kunna uppgradera från F0 till den nya E0 SKU.

E0-resurser är endast tillgängliga för redigeringstjänsten och att E0-nivån initialt bara stöds i region Västra USA.

![Bild av LUIS-prenumeration](../media/cognitive-services-encryption/luis-subscription.png)

### <a name="regional-availability"></a>Regional tillgänglighet

Kundhanterade nycklar är för närvarande tillgängliga i regionen **västra USA.**

### <a name="limitations"></a>Begränsningar

Det finns vissa begränsningar när du använder E0-nivån med befintliga/tidigare skapade program:

* Migreringen till en E0-resurs kommer att blockeras. Användare kan bara migrera sina appar till F0-resurser. När du har migrerat en befintlig resurs till F0 kan du skapa en ny resurs på E0-nivån. Läs mer om [migrering här](https://docs.microsoft.com/azure/cognitive-services/luis/luis-migration-authoring).  
* Flytta program till eller från en E0-resurs kommer att blockeras. En lösning för den här begränsningen är att exportera ditt befintliga program och importera det som en E0-resurs.
* Funktionen Bing Spell check stöds inte.
* Loggning av slutanvändarens trafik inaktiveras om ditt program är E0.
* Talpriming-funktionen från Azure Bot-tjänsten stöds inte för program på E0-nivån. Den här funktionen är tillgänglig via Azure Bot Service, som inte stöder CMK.
* Talpriming-funktionen från portalen kräver Azure Blob Storage. Mer information finns i [Ta med eget lagringsutrymme](../Speech-Service/speech-encryption-of-data-at-rest.md#bring-your-own-storage-byos-for-customization-and-logging).

### <a name="enable-customer-managed-keys"></a>Aktivera kundhanterade nycklar

En ny Cognitive Services-resurs krypteras alltid med Microsoft-hanterade nycklar. Det går inte att aktivera kundhanterade nycklar när resursen skapas. Kundhanterade nycklar lagras i Azure Key Vault och nyckelvalvet måste etableras med åtkomstprinciper som ger nyckelbehörigheter till den hanterade identiteten som är associerad med Cognitive Services-resursen. Den hanterade identiteten är endast tillgänglig när resursen har skapats med hjälp av prisnivån för CMK.

Mer information om hur du använder kundhanterade nycklar med Azure Key Vault för Cognitive Services-kryptering finns i:

- [Konfigurera kundhanterade nycklar med Key Vault för Cognitive Services-kryptering från Azure-portalen](../Encryption/cognitive-services-encryption-keys-portal.md)

Om du aktiverar kundhanterade nycklar aktiveras också en systemtilldelad hanterad identitet, en funktion i Azure AD. När den tilldelade systemtilldelade hanterade identiteten är aktiverad registreras den här resursen med Azure Active Directory. När den hanterade identiteten har registrerats får den åtkomst till det nyckelvalv som valts under kundhanterade nyckelinställningar. Du kan läsa mer om [hanterade identiteter](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

> [!IMPORTANT]
> Om du inaktiverar systemtilldelade hanterade identiteter tas åtkomsten till nyckelvalvet bort och alla data som krypteras med kundnycklarna kommer inte längre att vara tillgängliga. Alla funktioner som är beroende av dessa data kommer att sluta fungera.

> [!IMPORTANT]
> Hanterade identiteter stöder för närvarande inte scenarier för korskataloger. När du konfigurerar kundhanterade nycklar i Azure-portalen tilldelas en hanterad identitet automatiskt under skalen. Om du sedan flyttar prenumerationen, resursgruppen eller resursen från en Azure AD-katalog till en annan överförs inte den hanterade identiteten som är associerad med resursen till den nya klienten, så kundhanterade nycklar kanske inte längre fungerar. Mer information finns i **Överföra en prenumeration mellan Azure AD-kataloger** i vanliga frågor och andra frågor och kända problem med hanterade [identiteter för Azure-resurser](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories).  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Lagra kundhanterade nycklar i Azure Key Vault

Om du vill aktivera kundhanterade nycklar måste du använda ett Azure Key Vault för att lagra dina nycklar. Du måste aktivera egenskaperna **Mjuk borttagning** **och Rensa inte** på nyckelvalvet.

Endast RSA-nycklar i storlek 2048 stöds med Cognitive Services-kryptering. Mer information om nycklar finns i **Key Vault-nycklar** i [Om Azure Key Vault-nycklar, hemligheter och certifikat](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys).

### <a name="rotate-customer-managed-keys"></a>Rotera kundhanterade nycklar

Du kan rotera en kundhanterad nyckel i Azure Key Vault enligt dina efterlevnadsprinciper. När nyckeln roteras måste du uppdatera Cognitive Services-resursen för att kunna använda den nya nyckeln URI. Mer information om hur du uppdaterar resursen för att använda en ny version av nyckeln i Azure-portalen finns i avsnittet **Uppdatera nyckelversionen** i [Konfigurera kundhanterade nycklar för Cognitive Services med hjälp av Azure-portalen](../Encryption/cognitive-services-encryption-keys-portal.md).

Om du roterar nyckeln kan du inte återskapa data i resursen. Användaren behöver inte vidta några ytterligare åtgärder.

### <a name="revoke-access-to-customer-managed-keys"></a>Återkalla åtkomst till kundhanterade nycklar

Om du vill återkalla åtkomsten till kundhanterade nycklar använder du PowerShell eller Azure CLI. Mer information finns i [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//) eller [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault). Om du återkallar åtkomst blockeras åtkomsten till alla data i Cognitive Services-resursen, eftersom krypteringsnyckeln inte är tillgänglig för Cognitive Services.

## <a name="next-steps"></a>Nästa steg

* [LUIS Service kundhanterad nyckelbegärandformulär](https://aka.ms/cogsvc-cmk)
* [Läs mer om Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
