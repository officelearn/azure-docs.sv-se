---
title: Kryptera säkra tillgångar i Azure Automation
description: Azure Automation skyddar säkra tillgångar med hjälp av flera krypteringsnivåer. Som standard görs krypteringen med Microsoft-hanterade nycklar. Kunder kan konfigurera sina automatiseringskonton så att de använder kundhanterade nycklar för kryptering. I den här artikeln beskrivs information om båda krypteringslägena och hur du kan växla mellan de två.
services: automation
ms.service: automation
ms.subservice: process-automation
author: snehithm
ms.author: snmuvva
ms.date: 01/11/2020
ms.topic: conceptual
manager: kmadnani
ms.openlocfilehash: 0b1d194209122fa71272243c80a2d4c57f6834a1
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547760"
---
# <a name="encrypt-secure-assets-in-azure-automation"></a>Kryptera säkra tillgångar i Azure Automation

Säkra resurser i Azure Automation innehåller autentiseringsuppgifter, certifikat, anslutningar och krypterade variabler. Dessa tillgångar skyddas i Azure Automation med hjälp av flera krypteringsnivåer. Baserat på nyckeln på den översta nivån som används för krypteringen finns det två modeller för kryptering:
-    Använda Microsoft-hanterade nycklar
-    Använda kundhanterade nycklar

## <a name="microsoft-managed-keys"></a>Microsoft-hanterade nycklar

Som standard använder ditt Azure Automation-konto Microsoft-hanterade nycklar.

Varje säker tillgång krypteras och lagras i Azure Automation med en unik nyckel (datakrypteringsnyckel) som genereras för varje automationskonto. Dessa nycklar själva krypteras och lagras i Azure Automation med ännu en unik nyckel som genereras för varje konto som kallas en kontokrypteringsnyckel (AEK). Dessa kontokrypteringsnycklar krypterade och lagras i Azure Automation med Hjälp av Microsoft-hanterade nycklar. 

## <a name="customer-managed-keys-with-key-vault-preview"></a>Kundhanterade nycklar med Key Vault (förhandsgranskning)

Du kan hantera kryptering av säkra tillgångar för ditt Automation-konto med dina egna nycklar. När du anger en kundhanterad nyckel på automationskontots nivå används den nyckeln för att skydda och kontrollera åtkomsten till kontokrypteringsnyckeln för Automation-kontot. Detta används i sin tur för att kryptera och dekryptera alla säkra tillgångar. Kundhanterade nycklar ger större flexibilitet att skapa, rotera, inaktivera och återkalla åtkomstkontroller. Du kan också granska krypteringsnycklarna som används för att skydda dina säkra tillgångar.

Använd Azure Key Vault för att lagra kundhanterade nycklar. Du kan antingen skapa egna nycklar och lagra dem i ett nyckelvalv, eller så kan du använda Azure Key Vault API:er för att generera nycklar.  Mer information om Azure Key Vault finns i [Vad är Azure Key Vault?](../key-vault/key-vault-overview.md)

## <a name="enable-customer-managed-keys-for-an-automation-account"></a>Aktivera kundhanterade nycklar för ett Automation-konto

När du aktiverar kryptering med kundhanterade nycklar för ett Automation-konto radbryts kontokrypteringsnyckeln med den kundhanterade nyckeln i det associerade nyckelvalvet. Aktivera kundhanterade nycklar påverkar inte prestanda och kontot krypteras med den nya nyckeln omedelbart, utan dröjsmål.

Ett nytt Automation-konto krypteras alltid med Microsoft-hanterade nycklar. Det går inte att aktivera kundhanterade nycklar när kontot skapas. Kundhanterade nycklar lagras i Azure Key Vault och nyckelvalvet måste etableras med åtkomstprinciper som ger nyckelbehörigheter till den hanterade identitet som är associerad med Automation-kontot. Den hanterade identiteten är endast tillgänglig när lagringskontot har skapats.

När du ändrar nyckeln som används för azure automation säker kryptering av tillgångar, genom att aktivera eller inaktivera kundhanterade nycklar, uppdatera nyckelversionen eller ange en annan nyckel, ändras kryptering av kontokrypteringsnyckeln, men de säkra tillgångarna i ditt Azure Automation-konto behöver inte krypteras på nytt.

I följande tre avsnitt beskrivs mekaniken för att aktivera kundhanterade nycklar för ett Automation-konto. 

> [!NOTE] 
> Om du vill aktivera kundhanterade nycklar måste du ringa Azure Automation REST API-anrop med api version 2020-01-13-preview

### <a name="pre-requisites-for-using-customer-managed-keys-in-azure-automation"></a>Förutsättningar för att använda kundhanterade nycklar i Azure Automation

Innan du aktiverar kundhanterade nycklar för ett Automation-konto måste du se till att följande förutsättningar uppfylls:

 - Nyckeln för kundmanövrerad lagras i ett Azure Key Vault. 
 - Aktivera egenskaperna **Mjuk borttagning** **och Rensa inte** på nyckelvalvet. Dessa funktioner krävs för att möjliggöra återställning av nycklar i händelse av oavsiktlig radering.
 - Endast RSA-nycklar stöds med Azure Automation-kryptering. Mer information om nycklar finns i [Om Azure Key Vault-nycklar, hemligheter och certifikat](../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).
- Automation-kontot och nyckelvalvet kan finnas i olika prenumerationer, men måste finnas i samma Azure Active Directory-klientorganisation.

### <a name="assign-an-identity-to-the-automation-account"></a>Tilldela en identitet till automationskontot

Om du vill använda kundhanterade nycklar med ett Automation-konto måste ditt Automation-konto autentiseras mot nyckelvalvet som lagrar kundhanterade nycklar. Azure Automation använder systemtilldelade hanterade identiteter för att autentisera kontot med Azure Key Vault. Mer information om hanterade identiteter finns i [Vad är hanterade identiteter för Azure-resurser?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

Konfigurera en systemtilldelad hanterad identitet till automationskontot med hjälp av följande REST API-anrop:

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

Begärandetext:

```json
{ 
 "identity": 
 { 
  "type": "SystemAssigned" 
  } 
}
```

Systemtilldelade identitet för Automation-kontot returneras i ett svar som liknar följande:

```json
{
 "name": "automation-account-name",
 "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
 ..
 "identity": {
    "type": "SystemAssigned",
    "principalId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
    "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb"
 },
..
}
```

### <a name="configure-the-key-vault-access-policy"></a>Konfigurera åtkomstprincipen för Key Vault

När en hanterad identitet har tilldelats Automation-kontot konfigurerar du åtkomsten till nyckelvalvet som lagrar kundhanterade nycklar. Azure Automation kräver **att du hämtar**, **återställer, wrapKey**, **UnwrapKey** på de kundhanterade nycklarna. **recover**

En sådan åtkomstprincip kan ställas in med hjälp av följande REST API-anrop:

```http
PUT https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sample-group/providers/Microsoft.KeyVault/vaults/sample-vault/accessPolicies/add?api-version=2018-02-14
```

Begärandetext:

```json
{
  "properties": {
    "accessPolicies": [
      {
        "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb",
        "objectId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
        "permissions": {
          "keys": [
            "get",
            "recover",
            "wrapKey",
            "unwrapKey"
          ],
          "secrets": [],
          "certificates": []
        }
      }
    ]
  }
}
```

> [!NOTE]
> Fälten **tenantId** och **objectId** måste anges med värden för **identity.tenantId** respektive **identity.principalId** från svaret för hanterad identitet för Automation-kontot.

### <a name="change-the-configuration-of-automation-account-to-use-customer-managed-key"></a>Ändra konfigurationen för Automation-konto för att använda kundhanterad nyckel

Slutligen kan du växla ditt Automation-konto från Microsft-hanterade nycklar till kundhanterade nycklar med hjälp av följande REST API-anrop:

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

Begärandetext:

```json
 {
    "properties": {
      "encryption": {
        "keySource": "Microsoft.Keyvault",
        "keyvaultProperties": {
          "keyName": "sample-vault-key",
          "keyvaultUri": "https://sample-vault-key12.vault.azure.net",
          "keyVersion": "7c73556c521340209371eaf623cc099d"
        }
      }
    }
  }
```

Exempelsvar

```json
{
  "name": "automation-account-name",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
  ..
  "properties": {
    ..
    "encryption": {
      "keyvaultProperties": {
         "keyName": "sample-vault-key",
          "keyvaultUri": "https://sample-vault-key12.vault.azure.net",
          "keyVersion": "7c73556c521340209371eaf623cc099d"
      },
      "keySource": "Microsoft.Keyvault"
    },
    ..
  }
}
```

## <a name="manage-customer-managed-keys-lifecycle"></a>Hantera kundhanterad nycklars livscykel

### <a name="rotate-customer-managed-keys"></a>Rotera kundhanterade nycklar

Du kan rotera en kundhanterad nyckel i Azure Key Vault enligt dina efterlevnadsprinciper. När nyckeln roteras måste du uppdatera Automation-kontot för att kunna använda den nya nyckeln URI.

Om du roterar nyckeln kan du inte återskapa säker tillgång i Automation-kontot. Det krävs inga ytterligare åtgärder.

### <a name="revoke-access-to-customer-managed-keys"></a>Återkalla åtkomst till kundhanterade nycklar

Om du vill återkalla åtkomsten till kundhanterade nycklar använder du PowerShell eller Azure CLI. Mer information finns i [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault/) eller [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault). Om du återkallar åtkomst blockeras åtkomsten till alla säkra tillgångar i Automation-kontot, eftersom krypteringsnyckeln inte är tillgänglig för Azure Automation.

## <a name="next-steps"></a>Nästa steg

- [Vad är Azure Key Vault?](../key-vault/key-vault-overview.md)

- [Certifikattillgångar i Azure Automation](shared-resources/certificates.md)

- [Autentiseringsuppgiftstillgångar i Azure Automation](shared-resources/credentials.md)

- [Variabeltillgångar i Azure Automation](shared-resources/variables.md)
