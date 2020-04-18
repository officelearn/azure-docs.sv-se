---
title: 'Snabbstart: Ny principtilldelning med mallar'
description: I den här snabbstarten använder du en Resource Manager-mall för att skapa en principtilldelning för att identifiera icke-kompatibla resurser.
ms.date: 03/16/2020
ms.topic: quickstart
ms.custom: subject-armqs
ms.openlocfilehash: ae6b2d151baaa904215639276c7fb74766810c6a
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606607"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-by-using-a-resource-manager-template"></a>Snabbstart: Skapa en principtilldelning för att identifiera icke-kompatibla resurser med hjälp av en Resource Manager-mall

Det första steget mot att förstå kompatibilitet i Azure är att identifiera dina resursers status.
Denna snabbstart vägleder dig genom processen för att skapa en principtilldelning som identifierar virtuella datorer som inte använder hanterade diskar. Efter den här genomgången kommer du att kunna identifiera virtuella datorer som inte använder hanterade diskar. De är _inkompatibla_ med principtilldelningen.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="create-a-policy-assignment"></a>Skapa en principtilldelning

I den här snabbstarten skapar du en principtilldelning och tilldelar en inbyggd principdefinition som kallas _Granska virtuella datorer som inte använder hanterade diskar_. En ofullständig lista över tillgängliga inbyggda principer finns i [Azure Policy-exempel](./samples/index.md).

### <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure Quickstart-mallar](https://azure.microsoft.com/resources/templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/).

:::code language="json" source="~/quickstart-templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/azuredeploy.json" range="1-36" highlight="26-34":::

Resursen som definieras i mallen är:

- [Microsoft.Authorization/policyTilldelningar](/azure/templates/microsoft.authorization/policyassignments)

### <a name="deploy-the-template"></a>Distribuera mallen

> [!NOTE]
> Azure Policy-tjänsten är kostnadsfri. Mer information finns i [Översikt över Azure Policy](./overview.md).

1. Logga in på Azure-portalen och öppna mallen genom att välja följande bild:

   [![Distribuera principmallen till Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json)

1. Markera eller ange följande värden:

   | Namn | Värde |
   |------|-------|
   | Prenumeration | Välj din Azure-prenumeration. |
   | Resursgrupp | Välj **Skapa nytt**, ange ett namn och välj sedan **OK**. I skärmbilden är resursgruppnamnet _mypolicyquickstart\<Datum\>i MMDD rg_. |
   | Location | Välj en region. Välj till exempel **USA, centrala**. |
   | Namn på principtilldelning | Ange ett principtilldelningsnamn. Du kan använda principdefinitionsvisningen om du vill. Granska till exempel **virtuella datorer som inte använder hanterade diskar**. |
   | Rg-namn | Ange ett resursgruppnamn som du vill tilldela principen till. I den här snabbstarten använder du standardvärdet **[resourceGroup().name]**. **[resourceGroup()](../../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)** är en mallfunktion som hämtar resursgruppen. |
   | Principdefinitions-ID | Ange **/providers/Microsoft.Authorization/policyDefinitions/0a914e76-4921-4c19-b460-a2d36003525a**. |
   | Jag godkänner de villkor som anges ovan | (Välj) |

1. Välj **Köp**.

Några ytterligare resurser:

- Mer information om hur du hittar fler exempelmallar finns i [Azure Quickstart-mall](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular).
- Om du vill se mallreferensen går du till [Azure-mallreferens](/azure/templates/microsoft.authorization/allversions).
- Mer information om hur du utvecklar Resource Manager-mallar finns i [Azure Resource Manager-dokumentationen](../../azure-resource-manager/management/overview.md).
- Information om hur du lär dig distribution på prenumerationsnivå finns i [Skapa resursgrupper och resurser på prenumerationsnivå](../../azure-resource-manager/templates/deploy-to-subscription.md).

## <a name="validate-the-deployment"></a>Verifiera distributionen

Välj **Efterlevnad** till vänster på sidan. Leta sedan upp principtilldelningen **Granska virtuella datorer som inte använder hanterade diskar** som du skapade.

![Översiktssida för policyefterlevnad](./media/assign-policy-template/policy-compliance.png)

Om det finns befintliga resurser som inte är kompatibla med denna nya tilldelning visas de under **Icke-kompatibla resurser**.

Mer information finns i [Så här fungerar efterlevnad](./how-to/get-compliance-data.md#how-compliance-works).

## <a name="clean-up-resources"></a>Rensa resurser

Följ dessa steg för att ta bort tilldelningen som skapades:

1. Välj **Efterlevnad** (eller **Tilldelningar**) till vänster på sidan Azure Policy och leta upp principtilldelningen **Granska virtuella datorer som inte använder hanterade diskar** som du skapade.

1. Högerklicka på de **virtuella gransknings-datorerna som inte använder tilldelning av hanterade diskar** och välj **Ta bort tilldelning**.

   ![Ta bort en tilldelning från översiktssidan för efterlevnad](./media/assign-policy-template/delete-assignment.png)

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du tilldelat en inbyggd principdefinition till ett scope och utvärderat dess efterlevnadsrapport. Principdefinitionen kontrollerar att alla resurser i omfånget är kompatibla och identifierar vilka som inte är det.

Om du vill ha mer information om tilldelning av principer för att validera att de nya resurserna är kompatibla fortsätter du till självstudien för att:

> [!div class="nextstepaction"]
> [Skapa och hantera principer](./tutorials/create-and-manage.md)