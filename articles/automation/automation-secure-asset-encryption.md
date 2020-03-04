---
title: Kryptering av säkra till gångar i Automation
description: Azure Automation skyddar säkra till gångar med hjälp av flera krypterings nivåer. Som standard görs krypteringen med hjälp av Microsoft-hanterade nycklar. Kunder kan konfigurera sina Automation-konton så att de använder Kundhanterade nycklar för kryptering. Den här artikeln beskriver information om båda krypterings sätten och hur du kan växla mellan de två.
services: automation
ms.service: automation
ms.subservice: process-automation
author: snehithm
ms.author: snmuvva
ms.date: 01/11/2020
ms.topic: conceptual
manager: kmadnani
ms.openlocfilehash: 3c21e2fcdde9bffac91af56d49dfa0bf336e8c0c
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2020
ms.locfileid: "78246238"
---
# <a name="secure-assets-in-azure-automation"></a>Skydda till gångar i Azure Automation

Säkra till gångar i Azure Automation inkluderar autentiseringsuppgifter, certifikat, anslutningar och krypterade variabler. Dessa till gångar skyddas i Azure Automation att använda flera krypterings nivåer. Baserat på den översta nyckeln som används för krypteringen finns det två modeller för kryptering:
-   Använda Microsoft-hanterade nycklar
-   Använda Kundhanterade nycklar

## <a name="microsoft-managed-keys"></a>Microsoft-hanterade nycklar

Som standard använder ditt Azure Automation-konto Microsoft-hanterade nycklar.

Varje säker till gång krypteras och lagras i Azure Automation med hjälp av en unik nyckel (data krypterings nyckel) som genereras för varje Automation-konto. Dessa nycklar krypteras och lagras i Azure Automation att använda ännu en unik nyckel som genereras för varje konto som kallas för en konto krypterings nyckel (AEK). Krypterings nycklarna för kontot krypteras och lagras i Azure Automation med hjälp av Microsoft-hanterade nycklar. 

## <a name="customer-managed-keys-with-key-vault-preview"></a>Kundhanterade nycklar med Key Vault (för hands version)

Du kan hantera kryptering av säkra till gångar för ditt Automation-konto med dina egna nycklar. När du anger en kundhanterad nyckel på nivån för Automation-kontot används nyckeln för att skydda och kontrol lera åtkomsten till konto krypterings nyckeln för Automation-kontot. Detta används i sin tur för att kryptera och dekryptera alla säkra till gångar. Kundhanterade nycklar ger större flexibilitet för att skapa, rotera, inaktivera och återkalla åtkomst kontroller. Du kan också granska de krypterings nycklar som används för att skydda dina säkra till gångar.

Använd Azure Key Vault för att lagra Kundhanterade nycklar. Du kan antingen skapa egna nycklar och lagra dem i ett nyckel valv, eller så kan du använda Azure Key Vault API: er för att generera nycklar.  Mer information om Azure Key Vault finns i [Vad är Azure Key Vault?](../key-vault/key-vault-overview.md)

## <a name="enable-customer-managed-keys-for-an-automation-account"></a>Aktivera Kundhanterade nycklar för ett Automation-konto

När du aktiverar kryptering med Kundhanterade nycklar för ett Automation-konto omsluter Azure Automation konto krypterings nyckeln med den Kundhanterade nyckeln i det associerade nyckel valvet. Att aktivera Kundhanterade nycklar påverkar inte prestanda och kontot krypteras med den nya nyckeln omedelbart utan fördröjning.

Ett nytt Automation-konto krypteras alltid med Microsoft-hanterade nycklar. Det går inte att aktivera Kundhanterade nycklar vid den tidpunkt då kontot skapas. Kundhanterade nycklar lagras i Azure Key Vault och nyckel valvet måste tillhandahållas med åtkomst principer som ger nyckel behörigheter till den hanterade identitet som är associerad med Automation-kontot. Den hanterade identiteten är bara tillgänglig när lagrings kontot har skapats.

När du ändrar nyckeln som används för Azure Automation säker till gångs kryptering, genom att aktivera eller inaktivera Kundhanterade nycklar, uppdatera nyckel versionen eller ange en annan nyckel, ändras krypteringen av kontots krypterings nyckel, men skyddade till gångar i ditt Azure Automation-konto behöver inte krypteras igen.

I följande tre avsnitt beskrivs Mechanics för att aktivera Kundhanterade nycklar för ett Automation-konto. 

> [!NOTE] 
> Om du vill aktivera Kundhanterade nycklar måste du göra Azure Automation REST API samtal med API-version 2020-01-13-för hands version

### <a name="pre-requisites-for-using-customer-managed-keys-in-azure-automation"></a>Krav för att använda Kundhanterade nycklar i Azure Automation

Innan du aktiverar Kundhanterade nycklar för ett Automation-konto måste du se till att följande krav uppfylls:

 - Kund-hanterade-nyckeln lagras i en Azure Key Vault. 
 - Aktivera både den **mjuka borttagningen** och **Rensa inte** egenskaperna i nyckel valvet. Dessa funktioner krävs för att kunna återställa nycklar i händelse av oavsiktlig borttagning.
 - Endast RSA-nycklar stöds med Azure Automation kryptering. Mer information om nycklar finns i [om Azure Key Vault nycklar, hemligheter och certifikat](../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).
- Automation-kontot och nyckel valvet kan finnas i olika prenumerationer, men måste vara i samma Azure Active Directory-klient.

### <a name="assign-an-identity-to-the-automation-account"></a>Tilldela ett Automation-konto en identitet

Om du vill använda Kundhanterade nycklar med ett Automation-konto måste ditt Automation-konto autentisera mot nyckel valvet som lagrar Kundhanterade nycklar. Azure Automation använder systemtilldelade hanterade identiteter för att autentisera kontot med Azure Key Vault. Mer information om hanterade identiteter finns i [Vad är hanterade identiteter för Azure-resurser?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

Konfigurera en systemtilldelad hanterad identitet till Automation-kontot med följande REST API anrop:

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

Tilldelad identitet för Automation-kontot returneras i ett svar som liknar följande:

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

### <a name="configure-the-key-vault-access-policy"></a>Konfigurera Key Vault åtkomst princip

När en hanterad identitet har tilldelats Automation-kontot konfigurerar du åtkomst till nyckel valvet som lagrar Kundhanterade nycklar. Azure Automation kräver **Get**, **Recover**, **wrapKey**, **UnwrapKey** på kundens hanterade nycklar.

En sådan åtkomst princip kan anges med följande REST API anrop:

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
> **TenantId** -och **ObjectID** -fälten måste anges med värdena **Identity. tenantId** och **Identity. principalId** från svar på hanterad identitet för Automation-kontot.

### <a name="change-the-configuration-of-automation-account-to-use-customer-managed-key"></a>Ändra konfigurationen för Automation-kontot så att den använder kundhanterad nyckel

Slutligen kan du växla ditt Automation-konto från Microsft-hanterade nycklar till Kundhanterade nycklar med hjälp av följande REST API anrop:

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

## <a name="manage-customer-managed-keys-lifecycle"></a>Hantera livs cykel för Kundhanterade nycklar

### <a name="rotate-customer-managed-keys"></a>Rotera Kundhanterade nycklar

Du kan rotera en kundhanterad nyckel i Azure Key Vault enligt efterlevnadsprinciper. När nyckeln roteras måste du uppdatera Automation-kontot för att använda den nya nyckel-URI: n.

Rotation av nyckeln utlöser inte Omkryptering av skyddade till gångar i Automation-kontot. Ingen ytterligare åtgärd krävs.

### <a name="revoke-access-to-customer-managed-keys"></a>Återkalla åtkomst till Kundhanterade nycklar

Om du vill återkalla åtkomsten till Kundhanterade nycklar använder du PowerShell eller Azure CLI. Mer information finns i [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault/) eller [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault). Att återkalla åtkomsten på ett effektivt sätt blockerar åtkomst till alla skyddade till gångar i Automation-kontot, eftersom krypterings nyckeln inte är tillgänglig via Azure Automation.

## <a name="next-steps"></a>Nästa steg

- [Vad är Azure Key Vault?](../key-vault/key-vault-overview.md)

- [Certifikattillgångar i Azure Automation](shared-resources/certificates.md)

- [Autentiseringsuppgiftstillgångar i Azure Automation](shared-resources/credentials.md)

- [Variabeltillgångar i Azure Automation](shared-resources/variables.md)
