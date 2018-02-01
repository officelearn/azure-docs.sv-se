---
title: "Använda Azure CLI för att skapa en principtilldelning som identifierar icke-kompatibla resurser i Azure-miljön | Microsoft Docs"
description: "Använda PowerShell för att skapa en Azure Policy-tilldelning som identifierar icke-kompatibla resurser."
services: azure-policy
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 01/17/2018
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 76725f3ebeaf5af4f2ab8aadb303d862fa111ecb
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2018
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment-with-the-azure-cli"></a>Skapa en principtilldelning för att identifiera icke-kompatibla resurser i Azure-miljön med Azure CLI

Det första steget mot att förstå kompatibilitet i Azure är att identifiera dina resursers status. Denna snabbstart vägleder dig genom processen för att skapa en principtilldelning som identifierar virtuella datorer som inte använder hanterade diskar.

Efter den här genomgången kommer du att kunna identifiera virtuella datorer som inte använder hanterade diskar. De är *inkompatibla* med principtilldelningen.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

För den här snabbstarten måste du köra Azure CLI version 2.0.4 eller senare för att installera och använda CLI:t lokalt. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-policy-assignment"></a>Skapa en principtilldelning

I den här snabbstarten skapar vi en principtilldelning och tilldelar principdefinitionen Granska virtuella datorer utan Managed Disks. Den här principdefinitionen ska identifiera resurser som inte uppfyller villkoren i principdefinitionen.

Följ de här stegen om du vill skapa en ny principtilldelning:

1. Registrera resursprovidern Policy Insights för att säkerställa att din prenumeration fungerar med resursprovidern. När du ska registrera en resursleverantör måste du ha behörighet att utföra registeringsåtgärden för resursprovidern. Den här åtgärden ingår i rollerna Deltagare och Ägare.

    Registrera resursprovidern genom att köra följande kommando:

    ```azurecli
    az provider register --namespace Microsoft.PolicyInsights
    ```

    Kommandot returnerar ett meddelande om att registreringen pågår.

    Du kan inte avregistrera en resursprovider när du har resurstyper från resursprovidern i din prenumeration. Läs mer om att registrera och visa resursproviders i [Resource Providers and Types](../azure-resource-manager/resource-manager-supported-services.md) (Resursproviders och typer).

2. Visa alla principdefinitioner och leta reda på principdefinitionen *Granska virtuella datorer utan Managed Disks*:

    ```azurecli
az policy definition list
```

    Azure Policy har inbyggda principdefinitioner som du kan använda. Du kan se inbyggda principdefinitioner som:

    - Framtvinga tagg och dess värde
    - Använd tagg och dess värde
    - Kräv SQL Server Version 12.0

3. Ange följande information och kör följande kommando för att tilldela principdefinitionen:

    - Visa ett **namn** för principtilldelningen. I det här fallet använder vi *Granska virtuella datorer utan Managed Disks*.
    - **Policy** – detta är principdefinitionen som du använder som bas för att skapa tilldelningen. I det här fallet är principdefinitionen *Granska virtuella datorer utan Managed Disks*.
    - **Scope** – en omfattning avgör vilka resurser eller grupper med resurser som principtilldelningen används på. Det kan vara allt från en prenumeration till resursgrupper.

    Använd den prenumeration (eller resursgrupp) som du registrerade tidigare. I det här exemplet använder vi prenumerations-ID:t **bc75htn-a0fhsi-349b-56gh-4fghti-f84852** och resursgruppsnamnet **FabrikamOMS**. Kom ihåg att ändra dessa till ditt prenumerations-ID och namnet på den resursgrupp som du arbetar med.

    Kommandot bör se ut ungefär så här:

    ```azurecli
az policy assignment create --name Audit Virtual Machines without Managed Disks Assignment --policy Audit Virtual Machines without Managed Disks --scope /subscriptions/
bc75htn-a0fhsi-349b-56gh-4fghti-f84852/resourceGroups/FabrikamOMS
```

En principtilldelning är en princip som tilldelats ett specifikt område. Området kan även här vara allt från en hanteringsgrupp till en resursgrupp.

## <a name="identify-non-compliant-resources"></a>Identifiera icke-kompatibla resurser

Så här visar du icke-kompatibla resurser i samband med den nya tilldelningen:

1. Gå tillbaka till sidan för Azure Policy.
2. Välj **Efterlevnad** i det vänstra fönstret och sök efter den **principtilldelning** du skapade.

   ![Principefterlevnad](media/assign-policy-definition/policy-compliance.png)

   Eventuella befintliga resurser som inte är kompatibla med denna nya tilldelning visas på fliken **Icke-kompatibla resurser**. I föregående bild visas exempel på icke-kompatibla resurser.

## <a name="clean-up-resources"></a>Rensa resurser

De andra guiderna i den här samlingen bygger på den här snabbstarten. Om du planerar att fortsätta arbeta med efterföljande självstudier ska du inte rensa upp resurserna som du skapade i den här snabbstarten. Om du inte planerar att fortsätta tar du bort tilldelningen som du skapade genom att köra det här kommandot:

```azurecli
az policy assignment delete –name  Assignment --scope /subscriptions/ bc75htn-a0fhsi-349b-56gh-4fghti-f84852 resourceGroups/ FabrikamOMS
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du tilldelat en principdefinition för att identifiera icke-kompatibla resurser i Azure-miljön.

Om du vill lära dig mer om att tilldela principer för att se till att **framtida** resurser som skapas är kompatibla kan du gå vidare till självstudien för:

> [!div class="nextstepaction"]
> [Skapa och hantera principer](./create-manage-policy.md)
