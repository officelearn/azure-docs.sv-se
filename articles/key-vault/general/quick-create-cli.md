---
title: Snabb start – skapa en Azure Key Vault med Azure CLI
description: Snabb start visar hur du skapar en Azure Key Vault med hjälp av Azure CLI
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: quickstart
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: f3b9758d52c4be8e4d85c398f5ef1d0b3fae7e86
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2020
ms.locfileid: "87541812"
---
# <a name="quickstart-create-a-key-vault-using-the-azure-cli"></a>Snabb start: skapa ett nyckel valv med Azure CLI

Azure Key Vault är en moln tjänst som tillhandahåller en säker lagring för [nycklar](../keys/index.yml), [hemligheter](../secrets/index.yml)och [certifikat](../certificates/index.yml). Mer information om Key Vault finns i [About Azure Key Vault](overview.md); Mer information om vad som kan lagras i ett nyckel valv finns i [om nycklar, hemligheter och certifikat](about-keys-secrets-certificates.md).

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

I den här snabb starten skapar du ett nyckel valv med [Azure CLI](/cli/azure/). Azure CLI används för att skapa och hantera Azure-resurser med hjälp av kommandon eller skript.  Om du väljer att installera och använda CLI:t lokalt måste du köra Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli).

Du kan logga in i Azure via CLI genom att skriva:

```azurecli
az login
```

Mer information om inloggningsalternativen via CLI finns i [Logga in med Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. I följande exempel skapas en resursgrupp med namnet *ContosoResourceGroup* på platsen *eastus*.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"
```

## <a name="create-a-key-vault"></a>Skapa ett nyckelvalv

Skapa en Key Vault i resurs gruppen från föregående steg. Du måste ange en del information:

- Nyckel valvets namn: en sträng på 3 till 24 tecken som bara får innehålla siffror (0-9), bokstäver (a-z, A-Z) och bindestreck (-)

  > [!Important]
  > Varje nyckel valv måste ha ett unikt namn. Ersätt <ditt-unika-nyckel-valv> med namnet på nyckel valvet i följande exempel.

- Resurs grupp namn: **myResourceGroup**.
- Platsen: **öster**.

```azurecli
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "EastUS"
```

Utdata från denna cmdlet visar egenskaper för nyckelvalvet du precis skapade. Anteckna de två egenskaperna som visas nedan:

- **Valv namn**: det namn du angav för parametern--name ovan.
- **Valv-URI**: i exemplet är detta https:// &lt; ditt-Unique-Vault.Azure.net/-valv &gt; .. Program som använder ditt valv via dess REST-API måste använda denna URI.

Nu är ditt Azure-konto det enda kontot med behörighet att utföra åtgärder i det nya valvet.

## <a name="clean-up-resources"></a>Rensa resurser

De andra snabbstarterna och självstudierna i den här samlingen bygger på den här snabbstarten. Om du planerar att fortsätta med efterföljande snabbstarter och självstudier kan du lämna kvar de här resurserna.

När det inte längre behövs kan du använda kommandot för Azure CLI- [AZ Group Delete](/cli/azure/group) för att ta bort resurs gruppen och alla relaterade resurser:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en Key Vault och tog bort den. Om du vill veta mer om Key Vault och hur du integrerar den med dina program, Fortsätt till artiklarna nedan.

- Läs en [Översikt över Azure Key Vault](overview.md)
- Se referensen för [kommandon för Azure CLI-AZ](/cli/azure/keyvault?view=azure-cli-latest)
- Granska [Azure Key Vault bästa praxis](best-practices.md)
