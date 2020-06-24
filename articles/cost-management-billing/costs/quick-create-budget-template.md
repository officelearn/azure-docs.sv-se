---
title: Snabbstart – Skapa en budget med en Azure Resource Manager-mall
description: Snabbstarten visar hur du skapar en budget med en Azure Resource Manager-mall.
author: bandersmsft
ms.author: banders
tags: azure-resource-manager
ms.service: cost-management-billing
ms.topic: quickstart
ms.date: 06/10/2020
ms.custom: subject-armqs
ms.openlocfilehash: dc37039d6777a77f9de247808329930f1621ee82
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2020
ms.locfileid: "84686417"
---
# <a name="quickstart-create-a-budget-with-an-azure-resource-manager-template"></a>Snabbstart: Skapa en budget med en Azure Resource Manager-mall

Budgetar i Cost Management hjälper dig att planera och öka organisationsansvar. Med budgetar kan du ta hänsyn till de Azure-tjänster du förbrukar eller prenumererar på under en viss period. De hjälper dig att informera andra om sina utgifter för att proaktivt hantera kostnader och för att övervaka hur utgifter fortskrider över tid. När de budgettrösklar som du har skapat har överskridits utlöses meddelanden. Ingen av dina resurser påverkas och förbrukningen stoppas inte. Du kan använda budgetar för att jämföra och spåra utgifter när du analyserar kostnader. I den här snabbstarten får du se hur du skapar en budget med en Resource Manager-mall.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Krav

Azure Resource Manager-mallen stöder endast Azure-prenumerationer för Enterprise-avtal (EA). Andra prenumerationstyper stöds inte av mallen.

Du måste ha deltagarbehörighet för att skapa och hantera budgetar. Du kan skapa enskilda budgetar för EA-prenumerationer och resursgrupper. Men du kan inte skapa budgetar för EA-faktureringskonton. För Azure EA-prenumerationer måste du ha läsbehörighet för att visa budgetar.

När du har skapat en budget behöver du minst läsbehörighet för ditt Azure-konto för att visa den.

Om du har en ny prenumeration kan du inte skapa en budget eller använda Cost Management-funktioner direkt. Det kan ta upp till 48 timmar innan du kan använda alla Cost Management-funktioner.

Följande Azure-behörigheter, eller -omfång, stöds per prenumeration för budgetar efter användare och grupp. Mer information om omfång finns i [Förstå och arbeta med omfång](understand-work-scopes.md).

- Ägare – Kan skapa, ändra och ta bort budgetar för en prenumeration.
- Deltagare och Cost Management-deltagare – Kan skapa, ändra och ta bort sina egna budgetar. Kan ändra budgetbeloppet för budgetar som skapats av andra.
- Läsare och Cost Management-läsare – Kan visa budgetar som de har behörighet till.

Mer information om hur du tilldelar åtkomst till Cost Management-data finns i [Tilldela åtkomst till Cost Management-data](assign-access-acm-data.md).

## <a name="create-a-budget"></a>Skapa en budget

### <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten är från [Azure snabbstartsmallar](https://azure.microsoft.com/resources/templates/create-budget).

:::code language="json" source="~/quickstart-templates/create-budget/azuredeploy.json" range="1-146" highlight="110-139":::

En Azure-resurs har definierats i mallen:

* [Microsoft.Consumption/budgets](/azure/templates/microsoft.consumption/budgets): Skapa en Azure-budget.

### <a name="deploy-the-template"></a>Distribuera mallen

1. Välj följande bild för att logga in på Azure och öppna en mall. Mallen skapar en budget.

   [![Distribuera till Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fcreate-budget%2Fazuredeploy.json)

2. Välj eller ange följande värden.

   [![Resource Manager-mall, skapa budget, distribuera portal](./media/quick-create-budget-template/create-budget-using-template-portal.png)](./media/quick-create-budget-template/create-budget-using-template-portal.png#lightbox)

    * **Prenumeration**: välj en Azure-prenumeration.
    * **Resursgrupp** – välj **Skapa ny** och ange ett unikt namn för resursgruppen och klicka på **OK** eller välj en befintlig resursgrupp.
    * **Plats**: välj en plats. Välj till exempel **USA, centrala**.
    * **Budgetnamn**: ange ett namn på budgeten. Det ska vara unikt inom sin resursgrupp. Endast alfanumeriska tecken, understreck och bindestreck får användas.
    * **Belopp**: Ange den totala kostnaden eller användningen för att spåra med budgeten.
    * **Budgetkategori**: välj kategori för budgeten, om budgeten spårar **kostnad** eller **användning**.
    * **Tidsenhet**: Ange tiden som omfattas av en budget. Tillåtna värden är månadsvis, kvartalsvis eller varje år. Budgeten återställs i slutet av tidsenheten.
    * **Startdatum**: Ange startdatumet med den första dagen i månaden i formatet ÅÅÅÅ-MM-DD. Ett startdatum får i framtiden inte ligga längre bort än tre månader från i dag. Du kan ange ett tidigare startdatum med tidsintervallperioden.
    * **Slutdatum**: ange slutdatumet för budgeten i formatet ÅÅÅÅ-MM-DD. Om detta inte anges är standardvärdet inställt på 10 år från startdatumet.
    * **Operator**: Välj en jämförelseoperator. Möjliga värden är EqualTo, GreaterThan eller GreaterThanOrEqualTo.
    * **Tröskel**: ange ett tröskelvärde för meddelandet. Ett meddelande skickas när kostnaden överskrider tröskeln. Det är alltid procent och måste vara mellan 0 och 1000.
    * **Kontakt-e-post** anger en lista över e-postadresser för att skicka budgetmeddelandet när tröskelvärdet har överskridits. Förväntat format är `["user1@domain.com","user2@domain.com"]`.
    * **Kontaktroller** anger listan över kontaktroller för att skicka budgetmeddelandet när tröskelvärdet har överskridits. Standardvärden är ägare, deltagare och läsare. Förväntat format är `["Owner","Contributor","Reader"]`.
    * **Kontaktgrupper** anger en lista över resurs-ID:er för åtgärdsgrupper, som fullständiga resurs-URI:er, för att skicka budgetmeddelandet när tröskelvärdet har överskridits. Den accepterar matris med strängar. Förväntat format är `["action group resource ID1","action group resource ID2"]`. Om du inte vill använda åtgärdsgrupper anger du `[]`.
    * **Resursfiltret** anger en lista med filter för resurser. Förväntat format är `["Resource Filter Name1","Resource Filter Name2"]`. Om du inte vill använda ett filter anger du `[]`. Om du anger ett resursfilter måste du också ange värden för **mätfilter**.
    * **Mätfilter** anger en lista med filter för mätare. De är obligatoriska för budgetar med kategorin **Användning**. Förväntat format är `["Meter Filter Name1","Meter Filter Name2"]`. Om du inte har angett ett **resursfilter** anger du `[]`.
    * **Jag godkänner villkoren ovan**: Välj.

3. Välj **Köp**. När du har distribuerat budgeten får du ett meddelande:

   ![Resource Manager-mall, budget, distribuera portalmeddelande](./media/quick-create-budget-template/resource-manager-template-portal-deployment-notification.png)

Azure-portalen används för att distribuera mallen. Utöver Azure-portalen kan du också använda Azure PowerShell, Azure CLI, and REST API. Mer information om andra mallar för distribution finns i [Distribuera mallar](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="validate-the-deployment"></a>Verifiera distributionen

Du kan använda Azure-portalen för att kontrollera att budgeten har skapats genom att gå till **Kostnadshantering + fakturering** > välja en omfattning > **Budgetar**. Du kan också använda följande Azure CLI- eller Azure PowerShell-skript för att visa budgeten.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
az consumption budget list
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
Get-AzConsumptionBudget
```

---

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver en budget tar du bort den med någon av följande metoder:

### <a name="azure-portal"></a>Azure Portal

Gå till **Kostnadshantering + fakturering** > välj ett faktureringsomfång > **Budgetar** > välj en budget > välj sedan **Ta bort budget**.

### <a name="command-line"></a>Kommandorad

Du kan ta bort budgeten med Azure CLI eller Azure PowerShell.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the budget name:" &&
read budgetName &&
az consumption budget delete --budget-name $budgetName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$budgetName = Read-Host -Prompt "Enter the budget name"
Remove-AzConsumptionBudget -Name $budgetName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en Azure-budget för distributionen. Om du vill veta mer om Azure Cost Management och fakturering och Azure Resource Manager fortsätter du till artiklarna nedan.

- Översikt över [Kostnadshantering och fakturering](../cost-management-billing-overview.md)
- [Skapa budgetar](tutorial-acm-create-budgets.md) i Azure-portalen
- Läs mer om [Azure Resource Manager](../../azure-resource-manager/management/overview.md)
