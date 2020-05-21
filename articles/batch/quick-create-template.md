---
title: Azure snabb start – skapa ett batch-konto – Azure Resource Manager mall
description: Lär dig snabbt att köra ett Batch-jobb med Azure CLI. Skapa och hantera Azure-resurser från kommando raden eller i skript.
ms.topic: quickstart
ms.date: 05/19/2020
ms.custom: subject-armqs
ms.openlocfilehash: 535c8c34ea7af8e6bc56c3ecfe564de4c1b2bc54
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2020
ms.locfileid: "83694231"
---
# <a name="quickstart-create-a-batch-account-by-using-azure-resource-manager-template"></a>Snabb start: skapa ett batch-konto med hjälp av Azure Resource Manager mall

Du behöver ett batch-konto för att skapa beräknings resurser (pooler för Compute-noder) och batch-jobb. Du kan länka ett Azure Storage konto med batch-kontot, vilket är användbart för att distribuera program och lagra indata och utdata för de flesta verkliga arbets belastningar.

Den här snabb starten visar hur du använder en Azure Resource Manager mall för att skapa ett batch-konto, inklusive lagring. När du har slutfört den här snabbstarten kommer du att förstå huvudbegreppen för Batch-tjänsten och vara redo att testa Batch med mer realistiska arbetsbelastningar i större skala.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Krav

Du måste ha en aktiv Azure-prenumeration.

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-storage-account"></a>skapar ett lagringskonto

### <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabb starten är från [Azure snabb starts-mallar](https://github.com/Azure/azure-quickstart-templates/tree/master/101-batchaccount-with-storage).

:::code language="json" source="~/quickstart-templates/101-batchaccount-with-storage/azuredeploy.json":::

Två Azure-resurser definieras i mallen:

- [Microsoft. Storage/storageAccounts](https://docs.microsoft.com/azure/templates/microsoft.storage/storageaccounts): skapar ett lagrings konto.
- [Microsoft. batch/batchAccounts](https://docs.microsoft.com/azure/templates/microsoft.batch/batchaccounts): skapar ett batch-konto.

### <a name="deploy-the-template"></a>Distribuera mallen

1. Välj följande bild för att logga in på Azure och öppna en mall. Mallen skapar ett Azure Batch konto och ett lagrings konto.

   [![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-batchaccount-with-storage%2Fazuredeploy.json)

1. Välj eller ange följande värden.

   ![Resource Manager-mall, skapa batch-konto, distribuera Portal](media/quick-create-template/batch-template.png)

   - **Prenumeration**: Välj en Azure-prenumeration.
   - **Resurs grupp**: Välj **Skapa ny**, ange ett unikt namn för resurs gruppen och klicka sedan på **OK**.
   - **Plats**: välj en plats. Välj till exempel **USA, centrala**.
   - **Batch-konto namn**: lämna standardvärdet.
   - **Lagrings Accountsku**: Välj en lagrings konto typ. Till exempel **Standard_LRS**.
   - **Plats**: lämna standardvärdet så att resurserna kommer att finnas på samma plats som din resurs grupp.
   - Jag samtycker till villkors tillståndet ovan: **Välj**.

1. Välj **Köp**.

Efter några minuter bör du se ett meddelande om att batch-kontot har skapats.

I det här exemplet används Azure Portal för att distribuera mallen. Förutom Azure Portal kan du också använda Azure PowerShell, Azure CLI och REST API. Mer information om andra distributions metoder finns i [distribuera mallar](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="validate-the-deployment"></a>Verifiera distributionen

Du kan verifiera distributionen i Azure Portal genom att gå till resurs gruppen som du skapade. I **översikts** skärmen bekräftar du att batch-kontot och lagrings kontot finns.

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta att arbeta med efterföljande [självstudier](./tutorial-parallel-dotnet.md)kanske du vill lämna resurserna på plats. Eller, om du inte längre behöver dem, kan du [ta bort resurs gruppen](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group), som också tar bort batch-kontot och det lagrings konto som du har skapat.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du ett batch-konto och ett lagrings konto. Om du vill veta mer om Azure Batch ska du fortsätta med Azure Batch-självstudierna.

> [!div class="nextstepaction"]
> [Azure Batch-självstudier](./tutorial-parallel-dotnet.md)
