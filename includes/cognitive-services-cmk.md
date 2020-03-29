---
title: ta med fil
description: ta med fil
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.custom: include
ms.openlocfilehash: e5e1755e6351d308c041de5cefe943e9874c8ebd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372198"
---
### <a name="enable-customer-managed-keys"></a>Aktivera kundhanterade nycklar

Du måste använda Azure Key Vault för att lagra dina kundhanterade nycklar. Du kan antingen skapa egna nycklar och lagra dem i ett nyckelvalv, eller så kan du använda Azure Key Vault API:er för att generera nycklar. Cognitive Services-resursen och nyckelvalvet måste finnas i samma region och i samma Azure Active Directory-klientorganisation (Azure AD), men de kan finnas i olika prenumerationer. Mer information om Azure Key Vault finns i [Vad är Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview).

En ny Cognitive Services-resurs krypteras alltid med Microsoft-hanterade nycklar. Det går inte att aktivera kundhanterade nycklar när resursen skapas. Kundhanterade nycklar lagras i Azure Key Vault och nyckelvalvet måste etableras med åtkomstprinciper som ger nyckelbehörigheter till den hanterade identiteten som är associerad med Cognitive Services-resursen. Den hanterade identiteten är endast tillgänglig när resursen har skapats med hjälp av prisnivån för CMK.

Mer information om hur du använder kundhanterade nycklar med Azure Key Vault för Cognitive Services-kryptering finns i:

- [Konfigurera kundhanterade nycklar med Key Vault för Cognitive Services-kryptering från Azure-portalen](..\articles\cognitive-services\Encryption\cognitive-services-encryption-keys-portal.md)

Om du aktiverar kundhanterade nycklar aktiveras också en systemtilldelad hanterad identitet, en funktion i Azure AD. När den tilldelade systemtilldelade hanterade identiteten är aktiverad registreras den här resursen med Azure Active Directory. När den hanterade identiteten har registrerats får den åtkomst till det nyckelvalv som valts under kundhanterade nyckelinställningar. Du kan läsa mer om [hanterade identiteter](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

> [!IMPORTANT]
> Om du inaktiverar systemtilldelade hanterade identiteter tas åtkomsten till nyckelvalvet bort och alla data som krypteras med kundnycklarna kommer inte längre att vara tillgängliga. Alla funktioner som är beroende av dessa data kommer att sluta fungera.

> [!IMPORTANT]
> Hanterade identiteter stöder för närvarande inte scenarier för korskataloger. När du konfigurerar kundhanterade nycklar i Azure-portalen tilldelas en hanterad identitet automatiskt under skalen. Om du sedan flyttar prenumerationen, resursgruppen eller resursen från en Azure AD-katalog till en annan överförs inte den hanterade identiteten som är associerad med resursen till den nya klienten, så kundhanterade nycklar kanske inte längre fungerar. Mer information finns i **Överföra en prenumeration mellan Azure AD-kataloger** i vanliga frågor och andra frågor och kända problem med hanterade [identiteter för Azure-resurser](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories).  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Lagra kundhanterade nycklar i Azure Key Vault

Om du vill aktivera kundhanterade nycklar måste du använda ett Azure Key Vault för att lagra dina nycklar. Du måste aktivera egenskaperna **Mjuk borttagning** **och Rensa inte** på nyckelvalvet.

Endast RSA-nycklar i storlek 2048 stöds med Cognitive Services-kryptering. Mer information om nycklar finns i **Key Vault-nycklar** i [Om Azure Key Vault-nycklar, hemligheter och certifikat](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys).

### <a name="rotate-customer-managed-keys"></a>Rotera kundhanterade nycklar

Du kan rotera en kundhanterad nyckel i Azure Key Vault enligt dina efterlevnadsprinciper. När nyckeln roteras måste du uppdatera Cognitive Services-resursen för att kunna använda den nya nyckeln URI. Mer information om hur du uppdaterar resursen för att använda en ny version av nyckeln i Azure-portalen finns i avsnittet **Uppdatera nyckelversionen** i [Konfigurera kundhanterade nycklar för Cognitive Services med hjälp av Azure-portalen](..\articles\cognitive-services\Encryption\cognitive-services-encryption-keys-portal.md).

Om du roterar nyckeln kan du inte återskapa data i resursen. Användaren behöver inte vidta några ytterligare åtgärder.

### <a name="revoke-access-to-customer-managed-keys"></a>Återkalla åtkomst till kundhanterade nycklar

Om du vill återkalla åtkomsten till kundhanterade nycklar använder du PowerShell eller Azure CLI. Mer information finns i [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//) eller [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault). Om du återkallar åtkomst blockeras åtkomsten till alla data i Cognitive Services-resursen, eftersom krypteringsnyckeln inte är tillgänglig för Cognitive Services.


