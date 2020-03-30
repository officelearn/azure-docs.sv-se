---
title: Konfigurera hanterade identiteter med Azure App-konfiguration
description: Lär dig hur hanterade identiteter fungerar i Azure App-konfiguration och hur du konfigurerar en hanterad identitet
author: jpconnock
ms.topic: article
ms.date: 02/25/2020
ms.author: jeconnoc
ms.reviewer: lcozzens
ms.service: azure-app-configuration
ms.openlocfilehash: fe66466395a100221e6a3cdebdef870bdf195afc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623032"
---
# <a name="how-to-use-managed-identities-for-azure-app-configuration"></a>Så här använder du hanterade identiteter för Azure App-konfiguration

Det här avsnittet visar hur du skapar en hanterad identitet för Azure App-konfiguration. En hanterad identitet från Azure Active Directory (AAD) gör att Azure App-konfiguration enkelt kan komma åt andra AAD-skyddade resurser, till exempel Azure Key Vault. Identiteten hanteras av Azure-plattformen. Det kräver inte att du etablerar eller roterar några hemligheter. Mer information om hanterade identiteter i AAD finns i [Hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md).

Din ansökan kan beviljas två typer av identiteter:

- En **systemtilldelad identitet** är knuten till konfigurationsarkivet. Den tas bort om konfigurationsarkivet tas bort. Ett konfigurationsarkiv kan bara ha en systemtilldelad identitet.
- En **användartilldelad identitet** är en fristående Azure-resurs som kan tilldelas till ditt konfigurationsarkiv. Ett konfigurationsarkiv kan ha flera användartilldelade identiteter.

## <a name="adding-a-system-assigned-identity"></a>Lägga till en systemtilldelad identitet

Att skapa ett App Configuration Store med en systemtilldelad identitet kräver att ytterligare en egenskap anges i arkivet.

### <a name="using-the-azure-cli"></a>Använda Azure CLI

Om du vill konfigurera en hanterad identitet med Azure CLI använder du kommandot [az appconfig identity assign] mot ett befintligt konfigurationsarkiv. Du har tre alternativ för att köra exemplen i det här avsnittet:

- Använd [Azure Cloud Shell](../cloud-shell/overview.md) från Azure-portalen.
- Använd det inbäddade Azure Cloud Shell via knappen "Prova", som finns i det övre högra hörnet av varje kodblock nedan.
- [Installera den senaste versionen av Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.1 eller senare) om du föredrar att använda en lokal CLI-konsol.

I följande steg kan du skapa en appkonfigurationsbutik och tilldela den en identitet med CLI:

1. Om du använder Azure CLI i en lokal konsol börjar du med att logga in i Azure med [az login]. Använd ett konto som är kopplat till din Azure-prenumeration:

    ```azurecli-interactive
    az login
    ```

1. Skapa ett appkonfigurationsarkiv med CLI. Fler exempel på hur du använder CLI med Azure App-konfiguration finns i [CLI-exempel på appkonfiguration:](scripts/cli-create-service.md)

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. Kör kommandot [az appconfig identity assign] för att skapa den systemtilldelade identiteten för det här konfigurationsarkivet:

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup
    ```

## <a name="adding-a-user-assigned-identity"></a>Lägga till en användartilldelad identitet

Att skapa ett appkonfigurationsarkiv med en användartilldelad identitet kräver att du skapar identiteten och sedan tilldelar dess resursidentifierare till ditt arkiv.

### <a name="using-the-azure-cli"></a>Använda Azure CLI

Om du vill konfigurera en hanterad identitet med Azure CLI använder du kommandot [az appconfig identity assign] mot ett befintligt konfigurationsarkiv. Du har tre alternativ för att köra exemplen i det här avsnittet:

- Använd [Azure Cloud Shell](../cloud-shell/overview.md) från Azure-portalen.
- Använd det inbäddade Azure Cloud Shell via knappen "Prova", som finns i det övre högra hörnet av varje kodblock nedan.
- [Installera den senaste versionen av Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.31 eller senare) om du föredrar att använda en lokal CLI-konsol.

I följande steg kan du skapa en användartilldelad identitet och ett App Configuration-arkiv och sedan tilldela identiteten till arkivet med CLI:

1. Om du använder Azure CLI i en lokal konsol börjar du med att logga in i Azure med [az login]. Använd ett konto som är kopplat till din Azure-prenumeration:

    ```azurecli-interactive
    az login
    ```

1. Skapa ett appkonfigurationsarkiv med CLI. Fler exempel på hur du använder CLI med Azure App-konfiguration finns i [CLI-exempel på appkonfiguration:](scripts/cli-create-service.md)

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. Skapa en användartilldelad `myUserAssignedIdentity` identitet som anropas med CLI.

    ```azurecli-interactive
    az identity create -resource-group myResourceGroup --name myUserAssignedIdentity
    ```

    I utdata för det här kommandot `id` noterar du egenskapens värde.

1. Kör kommandot [az appconfig identity assign] för att tilldela den nya användartilldelade identiteten till det här konfigurationsarkivet. Använd värdet för `id` egenskapen som du noterade i föregående steg.

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup --identities /subscriptions/[subscription id]/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity
    ```

## <a name="removing-an-identity"></a>Ta bort en identitet

En systemtilldelad identitet kan tas bort genom att inaktivera funktionen med kommandot [az appconfig identity remove](/cli/azure/appconfig/identity?view=azure-cli-latest#az-appconfig-identity-remove) i Azure CLI. Användartilldelade identiteter kan tas bort individuellt. Om du tar bort en systemtilldelad identitet på det här sättet tas den också bort från AAD. Systemtilldelade identiteter tas också bort automatiskt från AAD när appresursen tas bort.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en ASP.NET Core-app med Azure App Configuration](quickstart-aspnet-core-app.md)

[az appconfig identitet tilldela]: /cli/azure/appconfig/identity?view=azure-cli-latest#az-appconfig-identity-assign
[az login]: /cli/azure/reference-index#az-login
