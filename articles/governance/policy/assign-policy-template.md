---
title: Skapa tilldelning med Resource Manager-mall
description: Den här artikeln beskriver steg för steg hur du använder en Resource Manager-mall för att skapa en princip tilldelning som identifierar icke-kompatibla resurser.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/13/2019
ms.topic: quickstart
ms.service: azure-policy
ms.openlocfilehash: 842b29160e3abaea5edc4bfb4f2e1c345e3ffc85
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2019
ms.locfileid: "72254814"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-by-using-a-resource-manager-template"></a>Snabb start: skapa en princip tilldelning för att identifiera icke-kompatibla resurser med hjälp av en Resource Manager-mall

Det första steget mot att förstå kompatibilitet i Azure är att identifiera dina resursers status.
Denna snabbstart vägleder dig genom processen för att skapa en principtilldelning som identifierar virtuella datorer som inte använder hanterade diskar.

Efter den här genomgången kommer du att kunna identifiera virtuella datorer som inte använder hanterade diskar. De är *inkompatibla* med principtilldelningen.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="create-a-policy-assignment"></a>Skapa en principtilldelning

I den här snabb starten skapar du en princip tilldelning och tilldelar en inbyggd princip definition som kallas *Granska virtuella datorer som inte använder hanterade diskar*. En lista över tillgängliga inbyggda principer finns i [Azure policy exempel](./samples/index.md).

Det finns flera metoder för att skapa princip tilldelningar. I den här snabb starten använder du en [snabb starts mall](https://azure.microsoft.com/resources/templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/).
Här är en kopia av mallen:

[!code-json[policy-assignment](~/quickstart-templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/azuredeploy.json)]

> [!NOTE]
> Azure Policys tjänsten är kostnads fri.  Mer information finns i [Översikt över Azure policy](./overview.md).

1. Välj följande bild för att logga in på Azure Portal och öppna mallen:

   [![Deploy till Azure](./media/assign-policy-template/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json)

1. Välj eller ange följande värden:

   | Namn | Värde |
   |------|-------|
   | Prenumeration | Välj din Azure-prenumeration. |
   | Resursgrupp | Välj **Skapa ny**, ange ett namn och välj sedan **OK**. I skärm bilden är resurs gruppens namn *mypolicyquickstart @ no__t-1Date i MMDD > RG*. |
   | Plats | Välj en region. Välj till exempel **USA, centrala**. |
   | Princip tilldelnings namn | Ange ett princip tilldelnings namn. Du kan använda princip definitions visningen om du vill. Granska till exempel **virtuella datorer som inte använder hanterade diskar**. |
   | RG namn | Ange ett resurs grupps namn som du vill tilldela principen till. I den här snabb starten använder du standardvärdet **[resourceGroup (). name]** . **[resourceGroup ()](../../azure-resource-manager/resource-group-template-functions-resource.md#resourcegroup)** är en mall-funktion som hämtar resurs gruppen. |
   | ID för princip definition | Ange **/providers/Microsoft.Authorization/policyDefinitions/0a914e76-4921-4c19-B460-a2d36003525a**. |
   | Jag samtycker till de villkor som anges ovan | Select |

1. Välj **Köp**.

Vissa ytterligare resurser:

- Du hittar fler exempel på mallar i [Azure snabb starts mall](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular).
- Om du vill se mal linne Reference går du till [referens för Azure-mallar](/azure/templates/microsoft.authorization/allversions).
- Information om hur du utvecklar Resource Manager-mallar finns i [Azure Resource Manager-dokumentation](/azure/azure-resource-manager/).
- Information om distribution på prenumerations nivå finns i [skapa resurs grupper och resurser på prenumerations nivå](../../azure-resource-manager/deploy-to-subscription.md).

## <a name="identify-non-compliant-resources"></a>Identifiera icke-kompatibla resurser

Välj **Efterlevnad** till vänster på sidan. Leta sedan upp principtilldelningen **Granska virtuella datorer som inte använder hanterade diskar** som du skapade.

![Översikts sida för policy efterlevnad](./media/assign-policy-template/policy-compliance.png)

Om det finns befintliga resurser som inte är kompatibla med denna nya tilldelning visas de under **Icke-kompatibla resurser**.

Mer information finns i [så här fungerar efterlevnad](./how-to/get-compliance-data.md#how-compliance-works).

## <a name="clean-up-resources"></a>Rensa resurser

Följ dessa steg för att ta bort tilldelningen som skapades:

1. Välj **Efterlevnad** (eller **Tilldelningar**) till vänster på sidan Azure Policy och leta upp principtilldelningen **Granska virtuella datorer som inte använder hanterade diskar** som du skapade.

1. Högerklicka på tjänsten **Granska virtuella datorer som inte använder princip tilldelning för hanterade diskar** och välj **ta bort tilldelning**.

   ![Ta bort en tilldelning från sidan Översikt över efterlevnad](./media/assign-policy-template/delete-assignment.png)

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du tilldelat en inbyggd princip definition till ett definitions område och utvärderat dess Kompatibilitetsrapport. Principdefinitionen kontrollerar att alla resurser i omfånget är kompatibla och identifierar vilka som inte är det.

Om du vill ha mer information om tilldelning av principer för att validera att de nya resurserna är kompatibla fortsätter du till självstudien för att:

> [!div class="nextstepaction"]
> [Skapa och hantera principer](./tutorials/create-and-manage.md)