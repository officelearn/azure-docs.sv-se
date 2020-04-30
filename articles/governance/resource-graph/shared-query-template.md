---
title: 'Snabb start: skapa en delad fråga med mallar'
description: I den här snabb starten använder du en Resource Manager-mall för att skapa en delad resurs diagrams fråga som räknar virtuella datorer av OS.
ms.date: 04/28/2020
ms.topic: quickstart
ms.custom: subject-armqs
ms.openlocfilehash: 050cf26da2054883fceaa08b11f94c6af4c85a16
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82234334"
---
# <a name="quickstart-create-a-shared-query-by-using-a-resource-manager-template"></a>Snabb start: skapa en delad fråga med hjälp av en Resource Manager-mall

Resurs diagram frågor kan sparas som en _privat fråga_ eller en _delad fråga_. En privat fråga sparas i användarens Portal profil och är inte synlig för andra. En delad fråga är ett Resource Manager-objekt som kan delas med andra via behörigheter och rollbaserad åtkomst. En delad fråga ger gemensam och konsekvent körning av resurs identifiering. Den här snabb starten använder en Resource Manager-mall för att skapa en delad fråga.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="create-a-shared-query"></a>Skapa en delad fråga

I den här snabb starten skapar du en delad fråga som heter _antal virtuella datorer av OS_. Om du vill testa den här frågan i SDK eller i portalen med resurs diagram Utforskaren, se [exempel-Count Virtual Machines by OS Type](./samples/starter.md#count-os).

### <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabb starten är från [Azure snabb starts-mallar](https://azure.microsoft.com/resources/templates/resourcegraph-sharedquery-countos/).

:::code language="json" source="~/quickstart-templates/resourcegraph-sharedquery-countos/azuredeploy.json" highlight="28-37":::

Den resurs som definierats i mallen är:

- [Microsoft. ResourceGraph/frågor](/azure/templates/microsoft.resourcegraph/queries)

### <a name="deploy-the-template"></a>Distribuera mallen

> [!NOTE]
> Azure Resource Graph-tjänsten är kostnads fri. Mer information finns i [Översikt över Azure Resource Graph](./overview.md).

1. Logga in på Azure-portalen och öppna mallen genom att välja följande bild:

   [![Distribuera princip mal len till Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fresourcegraph-sharedquery-countos%2Fazuredeploy.json)

1. Välj eller ange följande värden:

   | Name | Värde |
   |------|-------|
   | Prenumeration | Välj din Azure-prenumeration. |
   | Resursgrupp | Välj **Skapa ny**, ange ett namn och välj sedan **OK**. |
   | Plats | Välj en region. Välj till exempel **USA, centrala**. |
   | Frågenamn | Lämna standardvärdet **antal virtuella datorer per operativ system**. |
   | Fråga kod | Låt standardvärdet vara kvar`Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)` |
   | Beskrivning av fråga | Lämna standardvärdet **den här delade frågan räknar alla virtuella dator resurser och sammanfattar av OS-typen.** |
   | Jag samtycker till de villkor som anges ovan | Select |

1. Välj **Köp**.

Vissa ytterligare resurser:

- Du hittar fler exempel på mallar i [Azure snabb starts mall](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular).
- Om du vill se mal linne Reference går du till [referens för Azure-mallar](/azure/templates/microsoft.resourcegraph/allversions).
- Information om hur du utvecklar Resource Manager-mallar finns i [Azure Resource Manager-dokumentation](../../azure-resource-manager/management/overview.md).
- Information om distribution på prenumerations nivå finns i [skapa resurs grupper och resurser på prenumerations nivå](../../azure-resource-manager/templates/deploy-to-subscription.md).

## <a name="validate-the-deployment"></a>Verifiera distributionen

Följ dessa steg om du vill köra den nya delade frågan:

1. I Portal Sök fältet söker du efter **resurs diagram frågor** och markerar den.

1. Välj den delade frågan **antal virtuella datorer per operativ system**och välj sedan fliken **resultat** på sidan **Översikt** .

Du kan också öppna den delade frågan från resurs diagram Utforskaren:

1. I Portal Sök fältet söker du efter **resurs diagram Utforskaren** och markerar den.

1. Välj knappen **öppna en fråga** .

1. Ändra **typ** till _delade frågor_. Om du inte ser **antalet virtuella datorer per operativ system** i listan använder du rutan filter för att begränsa resultaten. När **antalet virtuella datorer efter delad operativ Systems** fråga visas väljer du dess namn.

1. När frågan har lästs in väljer du knappen **Kör fråga** . Resultaten visas på fliken **resultat** nedan.

## <a name="clean-up-resources"></a>Rensa resurser

Följ dessa steg om du vill ta bort den delade frågan som skapats:

1. I Portal Sök fältet söker du efter **resurs diagram frågor** och markerar den.

1. Markera kryss rutan bredvid den delade frågan **antal virtuella datorer per operativ system**.

1. Välj knappen **ta bort** längst upp på sidan.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du skapat en delad fråga för resurs diagram.

Om du vill veta mer om delade frågor fortsätter du till självstudien för:

> [!div class="nextstepaction"]
> [Hantera frågor i Azure Portal](./tutorials/create-share-query.md)