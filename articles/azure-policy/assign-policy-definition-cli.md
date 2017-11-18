---
title: "Använda Azure CLI för att skapa en tilldelning av principer för att identifiera icke-kompatibla resurser i Azure-miljön | Microsoft Docs"
description: "Använd PowerShell för att skapa en Azure principtilldelning för att identifiera resurser som icke-kompatibla."
services: azure-policy
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 11/02/2017
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 6ea39618a24249d92b77afdf5cb0ea284b180223
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2017
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment-with-the-azure-cli"></a>Skapa en tilldelning av principer för att identifiera icke-kompatibla resurser i Azure-miljön med Azure CLI

Det första steget i att förstå efterlevnad i Azure är viktigt att veta där du stå med aktuella resurserna. Denna Snabbstart vägleder dig genom processen att skapa en tilldelning av principer för att identifiera virtuella datorer som inte använder hanterade diskar.

I slutet av den här processen har identifieras vilka virtuella datorer inte använder hanterade diskar och är därför *icke-kompatibla*.
.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="opt-in-to-azure-policy"></a>Välja att Azure-princip

Azure princip är nu tillgänglig som förhandsversion och du behöver registrera att begära åtkomst.

1. Gå till Azure-princip på https://aka.ms/getpolicy och välj **registrera dig** i den vänstra rutan.

   ![Sök efter princip](media/assign-policy-definition/sign-up.png)

2. Välja att Azure-principen genom att välja prenumerationer i den **prenumeration** listan som du vill arbeta med. Välj sedan **registrera**.

   ![Om du vill använda Azure-princip](media/assign-policy-definition/preview-opt-in.png)

   Din förfrågan godkänns automatiskt för förhandsgranskning. Vänta i upp till 30 minuter för systemet för att bearbeta din registrering.

## <a name="create-a-policy-assignment"></a>Skapa en principtilldelning

I den här snabbstarten vi skapa en principtilldelning och tilldela Audit virtuella datorer utan diskar hanterade definition. Den här principdefinitionen identifierar resurser som inte uppfyller villkoren i principdefinitionen.

Följ dessa steg om du vill skapa en ny tilldelning av principer.

Visa alla principdefinitioner och hitta principdefinitionen ”Audit virtuella datorer utan hanterade diskar”:

```azurecli
az policy definition list
```

Princip för Azure medföljer redan inbyggd i principdefinitioner som du kan använda. Visas inbyggda principdefinitioner som:

- Framtvinga taggen och dess värde
- Tillämpa taggen och dess värde
- Kräv SQL Server-Version 12.0

Därefter ange följande information och kör följande kommando för att tilldela principdefinitionen:

- Visa **namn** för tilldelning av principer. I det här fallet ska vi använda *Audit virtuella datorer utan diskar hanteras*.
- **Principen** – detta är principdefinitionen, baserade av som du använder för att skapa tilldelningen. I det här fallet är det principdefinitionen – *Audit virtuella datorer utan hanterade diskar*
- En **omfång** – ett omfång avgör vilka resurser eller gruppering av resurser hämtar principtilldelningen tillämpas på. Det kan röra sig om en prenumeration till resursgrupper.

  Använda prenumerationen (eller resursgrupp) du tidigare har registrerat när du har valt att Azure-principen i det här exemplet använder vi den här prenumerations-ID - **bc75htn-a0fhsi-349b-56gh-4fghti-f84852** och resursgruppens namn - **FabrikamOMS**. Kom ihåg att ändra dessa ID för prenumerationen och namnet på resursgruppen som du arbetar med.

Detta är hur kommandot ska se ut:

```azurecli
az policy assignment create --name Audit Virtual Machines without Managed Disks Assignment --policy Audit Virtual Machines without Managed Disks --scope /subscriptions/
bc75htn-a0fhsi-349b-56gh-4fghti-f84852/resourceGroups/FabrikamOMS
```

En principtilldelning är en princip som har tilldelats äga rum inom ett specifikt omfång. Det här området kan också röra sig om en hanteringsgrupp till en resursgrupp.

## <a name="identify-non-compliant-resources"></a>Identifiera icke-kompatibla resurser

Visa de resurser som inte är kompatibla under den här nya tilldelning:

1. Gå tillbaka till sidan för principer för Azure.
2. Välj **kompatibilitet** på den vänstra rutan och söka efter den **principtilldelning** du skapade.

   ![För principefterlevnad](media/assign-policy-definition/policy-compliance.png)

   Om det finns några befintliga resurser som inte är kompatibla med den nya tilldelningen, de visas den **icke-kompatibel resurser** fliken som ovan.

## <a name="clean-up-resources"></a>Rensa resurser

Andra guider i den här samlingen bygger på denna Snabbstart. Om du vill fortsätta att arbeta med efterföljande självstudiekurser inte rensa upp de resurser som skapats i denna Snabbstart. Om du inte planerar att fortsätta, ta bort tilldelningen som du skapade genom att köra det här kommandot:

```azurecli
az policy assignment delete –name  Assignment --scope /subscriptions/ bc75htn-a0fhsi-349b-56gh-4fghti-f84852 resourceGroups/ FabrikamOMS
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten tilldelas en principdefinition att identifiera icke-kompatibla resurser i Azure-miljön.

Mer information om tilldelning av principer för att säkerställa att resurser som du skapar i den **framtida** är kompatibla, även i fortsättningen självstudierna för:

> [!div class="nextstepaction"]
> [Skapa och hantera principer](./create-manage-policy.md)
