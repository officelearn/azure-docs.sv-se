---
title: Tilldela en Azure Key Vault åtkomst princip (CLI)
description: Hur du använder Azure CLI för att tilldela en Key Vault åtkomst princip till tjänstens huvud namn eller program identitet.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: 59ba81944ecdf4f2b6322f4298e61df33f5b1da8
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289191"
---
# <a name="assign-a-key-vault-access-policy"></a>Tilldela en princip för Key Vault åtkomst

En Key Vault åtkomst princip bestämmer om ett specifikt tjänstens huvud namn, nämligen ett program eller en användar grupp, kan utföra olika åtgärder på Key Vault [hemligheter](../secrets/index.yml), [nycklar](../keys/index.yml)och [certifikat](../certificates/index.yml). Du kan tilldela åtkomst principer med hjälp av [Azure Portal](assign-access-policy-portal.md), Azure CLI (den här artikeln) eller [Azure PowerShell](assign-access-policy-powershell.md).

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

Mer information om hur du skapar grupper i Azure Active Directory med Azure CLI finns i [AZ AD Group Create](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-create) och [AZ AD Group member Add](/cli/azure/ad/group/member?view=azure-cli-latest#az-ad-group-member-add).

## <a name="configure-the-azure-cli-and-sign-in"></a>Konfigurera Azure CLI och logga in

1. Installera [Azure](/cli/azure/install-azure-cli?view=azure-cli-latest)CLI för att köra Azure CLI-kommandon lokalt.
 
    Använd [Azure Cloud Shell](../../cloud-shell/overview.md)för att köra kommandon direkt i molnet.

1. Endast lokalt CLI: Logga in på Azure med `az login` :

    ```bash
    az login
    ```

    `az login`Kommandot öppnar ett webbläsarfönster för att samla in autentiseringsuppgifter om det behövs.

## <a name="acquire-the-object-id"></a>Hämta objekt-ID

Bestäm objekt-ID för det program, den grupp eller användare som du vill tilldela åtkomst principen till:

- Program och andra tjänst huvud namn: Använd kommandot [AZ AD SP List](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-list) för att hämta tjänstens huvud namn. Undersök kommandots utdata för att fastställa objekt-ID: t för det säkerhets objekt som du vill tilldela åtkomst principen till.

    ```azurecli-interactive
    az ad sp list --show-mine
    ```

- Grupper: Använd kommandot [AZ AD Group List](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-list) för att filtrera resultaten med `--display-name` parametern:

     ```azurecli-interactive
    az ad group list --display-name <search-string>
    ```

- Användare: Använd kommandot [AZ AD User show](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-show) för att skicka användarens e-postadress i `--id` parametern:

    ```azurecli-interactive
    az ad user show --id <email-address-of-user>
    ```

## <a name="assign-the-access-policy"></a>Tilldela åtkomst principen
    
Använd kommandot [AZ-nyckel valv set-princip](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) för att tilldela önskade behörigheter:

```azurecli-interactive
az keyvault set-policy --name myKeyVault --object-id <object-id> --secret-permissions <secret-permissions> --key-permissions <key-permissions> --certificate-permissions <certificate-permissions>
```

Ersätt `<object-id>` med objekt-ID: t för tjänstens huvud namn.

Du behöver bara inkludera `--secret-permissions` , `--key-permissions` och `--certificate-permissions` när du tilldelar behörigheter till dessa specifika typer. De tillåtna värdena för `<secret-permissions>` , `<key-permissions>` och `<certificate-permissions>` anges i dokumentationen för [AZ-nyckel valv uppsättnings principer](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) .

## <a name="next-steps"></a>Nästa steg

- [Azure Key Vault säkerhet: identitets-och åtkomst hantering](overview-security.md#identity-and-access-management)
- [Skydda nyckel valvet](secure-your-key-vault.md).
- [Guide för Azure Key Vault utvecklare](developers-guide.md)
- [Metod tips för Azure Key Vault](best-practices.md)