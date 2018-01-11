---
title: "Använda Azure CLI för att skapa en principtilldelning som identifierar icke-kompatibla resurser i Azure-miljön | Microsoft Docs"
description: "Använda PowerShell för att skapa en Azure Policy-tilldelning som identifierar icke-kompatibla resurser."
services: azure-policy
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 12/06/2017
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 88ceb47d46b66e716c6c263098d5b9458e4aff22
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2017
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment-with-the-azure-cli"></a>Skapa en principtilldelning för att identifiera icke-kompatibla resurser i Azure-miljön med Azure CLI

Det första steget för att förstå efterlevnad i Azure är att veta hur du ligger till med dina befintliga resurser. Denna snabbstart vägleder dig genom processen för att skapa en principtilldelning som identifierar virtuella datorer som inte använder hanterade diskar.

I slutet av den här processen kommer du att ha lyckats identifiera virtuella datorer som inte använder hanterade diskar och därför är *icke-kompatibla*.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-policy-assignment"></a>Skapa en principtilldelning

I den här snabbstarten skapar vi en principtilldelning och tilldelar principdefinitionen Granska virtuella datorer utan Managed Disks. Den här principdefinitionen ska identifiera resurser som inte uppfyller villkoren i principdefinitionen.

Följ de här stegen om du vill skapa en ny principtilldelning.

Visa alla principdefinitioner och leta reda på principdefinitionen ”Granska virtuella datorer utan Managed Disks”:

```azurecli
az policy definition list
```

Azure Policy har inbyggda principdefinitioner som du kan använda. Du kan se inbyggda principdefinitioner som:

- Framtvinga tagg och dess värde
- Använd tagg och dess värde
- Kräv SQL Server Version 12.0

Ange följande information och kör följande kommando för att tilldela principdefinitionen:

- Visa ett **namn** för principtilldelningen. I det här fallet använder vi *Granska virtuella datorer utan Managed Disks*.
- **Policy** – detta är principdefinitionen som du använder som bas för att skapa tilldelningen. I det här fallet är principdefinitionen *Granska virtuella datorer utan Managed Disks*.
- **Scope** – en omfattning avgör vilka resurser eller grupper med resurser som principtilldelningen används på. Det kan vara allt från en prenumeration till resursgrupper.

  Använd den prenumeration (eller resursgrupp) som du registrerade tidigare. I det här exemplet använder vi detta prenumerations-ID – **bc75htn-a0fhsi-349b-56gh-4fghti-f84852** och detta resursgruppsnamn – **FabrikamOMS**. Kom ihåg att ändra dessa till ditt prenumerations-ID och namnet på den resursgrupp som du arbetar med.

Så här ska kommandot se ut:

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

   Om det finns några befintliga resurser som inte är kompatibla med denna nya tilldelning visas de på fliken **Icke-kompatibla resurser** (se ovan).

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
