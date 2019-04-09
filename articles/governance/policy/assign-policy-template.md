---
title: Skapa en principtilldelning med en Resource Manager-mall
description: Den här artikeln vägleder dig igenom stegen för att använda Resource Manager-mall för att skapa en principtilldelning som identifierar icke-kompatibla resurser.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/13/2019
ms.topic: quickstart
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 3b7ceb07fe874ebc012696f73c21d5c270cb3cee
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59269001"
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-by-using-a-resource-manager-template"></a>Skapa en principtilldelning som identifierar icke-kompatibla resurser med hjälp av Resource Manager-mall

Det första steget mot att förstå kompatibilitet i Azure är att identifiera dina resursers status.
Denna snabbstart vägleder dig genom processen för att skapa en principtilldelning som identifierar virtuella datorer som inte använder hanterade diskar.

Efter den här genomgången kommer du att kunna identifiera virtuella datorer som inte använder hanterade diskar. De är *inkompatibla* med principtilldelningen.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="create-a-policy-assignment"></a>Skapa en principtilldelning

I den här snabbstarten har du skapa en principtilldelning och tilldelar en inbyggd principdefinition som heter *granska virtuella datorer som inte använder hanterade diskar*. En ej fullständig lista över tillgängliga inbyggda principer finns i [Principexempel](./samples/index.md).

Det finns flera metoder för att skapa principtilldelningar. I den här snabbstarten använder du en [snabbstartsmall](https://azure.microsoft.com/resources/templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/).
Här är en kopia av mallen:

[!code-json[policy-assignment](~/quickstart-templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/azuredeploy.json)]

> [!NOTE]
> Azure Policy-tjänsten är kostnadsfri.  Mer information finns i [översikt över Azure Policy](./overview.md).

1. Välj följande bild för att logga in på Azure Portal och öppna mallen:

   [![Deploy Principmall till Azure](./media/assign-policy-template/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json)

1. Välj eller ange följande värden:

   | Namn | Värde |
   |------|-------|
   | Prenumeration | Välj din Azure-prenumeration. |
   | Resursgrupp | Välj **Skapa nytt**, ange ett namn och välj sedan **OK**. På skärmbilden, resursgruppens namn är *mypolicyquickstart<Date in MMDD>rg*. |
   | Plats | Välj en region. Välj till exempel **USA, centrala**. |
   | Principtilldelningsnamnet | Ange ett principnamn för tilldelning. Du kan använda visning för definition av principen om du vill. Till exempel **granska virtuella datorer som inte använder hanterade diskar**. |
   | Rg namn | Ange ett Resursgruppsnamn som du vill tilldela principen till. I den här snabbstarten använder du standardvärdet **[resourceGroup () .name]**. **[resourceGroup()](../../azure-resource-manager/resource-group-template-functions-resource.md#resourcegroup)**  är en mall-funktion som hämtar resursgruppen. |
   | Principdefinitions-ID: | Specify **/providers/Microsoft.Authorization/policyDefinitions/0a914e76-4921-4c19-b460-a2d36003525a**. |
   | Jag godkänner villkoren ovan | (Välj) |

1. Välj **Köp**.

Ytterligare resurser:

- Du hittar fler exempel mallar [Azure-snabbstartsmall](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular).
- Om du vill se mallreferensen, gå till [Azure mallreferensen](/azure/templates/microsoft.authorization/allversions).
- Läs hur du utvecklar Resource Manager-mallar i [dokumentation om Azure Resource Manager](/azure/azure-resource-manager/).
- Läs prenumerationsnivå distribution i [skapa resursgrupper och resurser på prenumerationsnivå](../../azure-resource-manager/deploy-to-subscription.md).

## <a name="identify-non-compliant-resources"></a>Identifiera icke-kompatibla resurser

Välj **Efterlevnad** till vänster på sidan. Leta sedan upp principtilldelningen **Granska virtuella datorer som inte använder hanterade diskar** som du skapade.

![Översiktssidan för princip för efterlevnad](./media/assign-policy-template/policy-compliance.png)

Om det finns befintliga resurser som inte är kompatibla med denna nya tilldelning visas de under **Icke-kompatibla resurser**.

Mer information finns i [hur kompatibilitet fungerar](./how-to/get-compliance-data.md#how-compliance-works).

## <a name="clean-up-resources"></a>Rensa resurser

Följ dessa steg för att ta bort tilldelningen som skapades:

1. Välj **Efterlevnad** (eller **Tilldelningar**) till vänster på sidan Azure Policy och leta upp principtilldelningen **Granska virtuella datorer som inte använder hanterade diskar** som du skapade.

1. Högerklicka på den **granska virtuella datorer som inte använder hanterade diskar** tilldelning av principer och välj **ta bort tilldelning**.

   ![Ta bort en tilldelning från översiktssidan efterlevnad](./media/assign-policy-template/delete-assignment.png)

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten tilldelade en inbyggd principdefinition till ett scope och utvärderas dess Kompatibilitetsrapport. Principdefinitionen kontrollerar att alla resurser i omfånget är kompatibla och identifierar vilka som inte är det.

Om du vill ha mer information om tilldelning av principer för att validera att de nya resurserna är kompatibla fortsätter du till självstudien för att:

> [!div class="nextstepaction"]
> [Skapa och hantera principer](./tutorials/create-and-manage.md)