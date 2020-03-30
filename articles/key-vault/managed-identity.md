---
title: Använda en systemtilldelad hanterad identitet för att komma åt Azure Key Vault
description: Lär dig hur du skapar en hanterad identitet för App Service-program och hur du använder den för att komma åt Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 36a4871339401629300eedd77b6441aed10aabf3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270958"
---
# <a name="provide-key-vault-authentication-with-a-managed-identity"></a>Tillhandahålla Key Vault-autentisering med en hanterad identitet

En hanterad identitet från Azure Active Directory gör att din app enkelt kan komma åt andra Azure AD-skyddade resurser. Identiteten hanteras av Azure-plattformen och kräver inte att du etablerar eller roterar några hemligheter. Mer information finns i [Hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md). 

Den här artikeln visar hur du skapar en hanterad identitet för ett App Service-program och använder den för att komma åt Azure Key Vault. Information om program som finns i virtuella Azure-datorer finns i [Använda en windows VM-systemtilldelade hanterad identitet för att komma åt Azure Key Vault](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md).


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Krav 

För att kunna slutföra den här guiden måste du ha följande resurser. 

- Ett nyckelvalv. Du kan använda ett befintligt nyckelvalv eller skapa ett nytt genom att följa stegen i någon av dessa snabbstarter:
   - [Skapa ett nyckelvalv med Azure CLI](quick-create-cli.md)
   - [Skapa ett nyckelvalv med Azure PowerShell](quick-create-powershell.md)
   - [Skapa ett nyckelvalv med Azure-portalen](quick-create-portal.md).
- Ett befintligt App Service-program som du vill bevilja nyckelvalvsåtkomst till. Du kan snabbt skapa en genom att följa stegen i [App Service-dokumentationen](../app-service/overview.md).
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) eller [Azure PowerShell](/powershell/azure/overview). Du kan också använda [Azure-portalen](https://portal.azure.com).


## <a name="adding-a-system-assigned-identity"></a>Lägga till en systemtilldelad identitet 

Först måste du lägga till en systemtilldelad identitet i ett program. 
 
### <a name="azure-portal"></a>Azure Portal 

För att konfigurera en hanterad identitet i portalen skapar du först ett program som vanligt och aktiverar sedan funktionen. 

1. Om du använder en funktionsapp navigerar du till **plattformsfunktioner**. För andra apptyper bläddrar du ned till gruppen **Inställningar** i den vänstra navigeringen. 

1. Välj **Hanterad identitet**. 

1. Växla **status** till **På**fliken System **som tilldelats** . Klicka på **Spara**. 

    ![](./media/managed-identity-system-assigned.png)

### <a name="azure-cli"></a>Azure CLI

Den här snabbstarten kräver Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta din nuvarande version. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest). 

Om du vill logga in med Azure CLI använder du kommandot [az login:](/cli/azure/reference-index?view=azure-cli-latest#az-login)

```azurecli-interactive
az login
```

Mer information om inloggningsalternativ med Azure CLI finns [i Logga in med Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest). 

Om du vill skapa identiteten för det här programmet använder du kommandot Azure CLI [az webapp identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) command eller az [functionapp identity assign](/cli/azure/functionapp/identity?view=azure-cli-latest#az-functionapp-identity-assign) command:


```azurecli-interactive
az webapp identity assign --name myApp --resource-group myResourceGroup
```

```azurecli-interactive
az functionapp identity assign --name myApp --resource-group myResourceGroup
```

Anteckna `PrincipalId`, som kommer att behövas i nästa avsnitt.

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```
## <a name="grant-your-app-access-to-key-vault"></a>Bevilja din app åtkomst till Key Vault 

### <a name="azure-portal"></a>Azure Portal

1.  Navigera till Key Vault-resursen. 

1.  Välj **Åtkomstprinciper** och klicka på **Lägg till åtkomstprincip**. 

1.  Välj **Hämta, Lista**i **Hemliga behörigheter**. 

1.  Välj **Välj Huvudnamn**och ange namnet på appen i sökfältet.  Markera appen i resultatlistan och klicka på **Välj**. 

1.  Klicka på **Lägg till** om du vill lägga till den nya åtkomstprincipen.

    ![](./media/managed-identity-access-policy.png)

### <a name="azure-cli"></a>Azure CLI

Om du vill ge ditt program åtkomst till nyckelvalvet använder du setprincipkommandot Azure CLI [az keyvault](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) och anger parametern **ObjectId** med **principalId** som du noterade ovan.

```azurecli-interactive
az keyvault set-policy --name myKeyVault --object-id <PrincipalId> --secret-permissions get list 
```

## <a name="next-steps"></a>Nästa steg

- [Azure Key Vault-säkerhet: Hantering av identitet och åtkomst](overview-security.md#identity-and-access-management)
- [Ange autentisering av Key Vault med en åtkomstkontrollprincip](key-vault-group-permissions-for-apps.md)
- [Om nycklar, hemligheter och certifikat](about-keys-secrets-and-certificates.md)
- [Säkra ditt nyckelvalv.](key-vault-secure-your-key-vault.md)
- [Utvecklarguide för Azure Key Vault](key-vault-developers-guide.md)
- Granska [metodtips för Azure Key Vault](key-vault-best-practices.md)