---
title: Snabbstart – Skapa en budget med en Azure Resource Manager-mall
description: Snabbstarten visar hur du skapar en budget med en Azure Resource Manager-mall.
author: bandersmsft
ms.author: banders
tags: azure-resource-manager
ms.service: cost-management-billing
ms.topic: quickstart
ms.date: 07/28/2020
ms.custom: subject-armqs
ms.openlocfilehash: 70408a3ed6638ec76af113c24cc3c8190a44f55c
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2020
ms.locfileid: "87445993"
---
# <a name="quickstart-create-a-budget-with-an-arm-template"></a>Snabbstart: Skapa en budget med en ARM-mall

Budgetar i Cost Management hjälper dig att planera och öka organisationsansvar. Med budgetar kan du ta hänsyn till de Azure-tjänster du förbrukar eller prenumererar på under en viss period. De hjälper dig att informera andra om sina utgifter för att proaktivt hantera kostnader och för att övervaka hur utgifter fortskrider över tid. När de budgettrösklar som du har skapat har överskridits utlöses meddelanden. Ingen av dina resurser påverkas och förbrukningen stoppas inte. Du kan använda budgetar för att jämföra och spåra utgifter när du analyserar kostnader. Den här snabbstarten visar hur du skapar en budget med hjälp av en Azure Resource Manager-mall (ARM-mall).

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fcreate-budget%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

Om du har en ny prenumeration kan du inte skapa en budget eller använda Cost Management-funktioner direkt. Det kan ta upp till 48 timmar innan du kan använda alla Cost Management-funktioner.

Budgetar stöds för följande typer av Azure-konton och omfång:

- Azure RBAC-omfång (rollbaserad åtkomstkontroll)
    - Hanteringsgrupper
    - Prenumeration
- Omfång för Enterprise-avtal
    - Faktureringskonto
    - Avdelning
    - Registreringskonto
- Enskilda avtal
    - Faktureringskonto
- Omfång för Microsoft-kundavtal
    - Faktureringskonto
    - Faktureringsprofil
    - Fakturaavsnitt
    - Kund
- AWS-omfång
    - Externt konto
    - Extern prenumeration

Om du vill visa budgetar behöver du minst läsbehörighet för ditt Azure-konto.

För Azure EA-prenumerationer måste du ha läsbehörighet för att visa budgetar. Du måste ha deltagarbehörighet för att skapa och hantera budgetar.

Följande Azure-behörigheter, eller -omfång, stöds per prenumeration för budgetar efter användare och grupp. Mer information om omfång finns i [Förstå och arbeta med omfång](understand-work-scopes.md).

- Ägare – Kan skapa, ändra och ta bort budgetar för en prenumeration.
- Deltagare och Cost Management-deltagare – Kan skapa, ändra och ta bort sina egna budgetar. Kan ändra budgetbeloppet för budgetar som skapats av andra.
- Läsare och Cost Management-läsare – Kan visa budgetar som de har behörighet till.

Mer information om hur du tilldelar åtkomst till Cost Management-data finns i [Tilldela åtkomst till Cost Management-data](assign-access-acm-data.md).

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/create-budget).

:::code language="json" source="~/quickstart-templates/create-budget/azuredeploy.json" :::

En Azure-resurs har definierats i mallen:

* [Microsoft.Consumption/budgets](/azure/templates/microsoft.consumption/budgets): Skapa en Azure-budget.

## <a name="deploy-the-template"></a>Distribuera mallen

1. Välj följande bild för att logga in på Azure och öppna en mall. Mallen skapar en budget.

   [![Distribuera till Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fcreate-budget%2Fazuredeploy.json)

2. Välj eller ange följande värden.

   :::image type="content" source="./media/quick-create-budget-template/create-budget-using-template-portal.png" alt-text="Resource Manager-mall, Skapa budget, distribuera portal]" lightbox="./media/quick-create-budget-template/create-budget-using-template-portal.png" :::
   
    * **Prenumeration**: välj en Azure-prenumeration.
    * **Resursgrupp**: om det behövs väljer du en befintlig resursgrupp eller **skapar en ny**.
    * **Region**: välj en Azure-region. Välj till exempel **USA, centrala**.
    * **Budgetnamn**: ange ett namn på budgeten. Det ska vara unikt inom sin resursgrupp. Endast alfanumeriska tecken, understreck och bindestreck får användas.
    * **Belopp**: ange den totala kostnaden att spåra med budgeten.
    * **Tidsenhet**: Ange tiden som omfattas av en budget. Tillåtna värden är månadsvis, kvartalsvis eller varje år. Budgeten återställs i slutet av tidsenheten.
    * **Startdatum**: Ange startdatumet med den första dagen i månaden i formatet ÅÅÅÅ-MM-DD. Ett startdatum får i framtiden inte ligga längre bort än tre månader från i dag. Du kan ange ett tidigare startdatum med tidsintervallperioden.
    * **Slutdatum**: ange slutdatumet för budgeten i formatet ÅÅÅÅ-MM-DD. 
    * **Första tröskel**: ange ett tröskelvärde för den första aviseringen. Ett meddelande skickas när kostnaden överskrider tröskeln. Det är alltid procent och måste vara mellan 0 och 1000.
    * **Andra tröskel**: ange ett tröskelvärde för den andra aviseringen. Ett meddelande skickas när kostnaden överskrider tröskeln. Det är alltid procent och måste vara mellan 0 och 1000.
    * **Kontaktroller** anger listan över kontaktroller för att skicka budgetmeddelandet när tröskelvärdet har överskridits. Standardvärden är ägare, deltagare och läsare. Förväntat format är `["Owner","Contributor","Reader"]`.
    * **Kontakt-e-post**: ange en lista med e-postadresser som budgetaviseringen ska skickas till när tröskelvärdet överskrids. Förväntat format är `["user1@domain.com","user2@domain.com"]`.
    * **Kontaktgrupper** anger en lista över resurs-ID:er för åtgärdsgrupper, som fullständiga resurs-URI:er, för att skicka budgetmeddelandet när tröskelvärdet har överskridits. Den accepterar matris med strängar. Förväntat format är `["action group resource ID1","action group resource ID2"]`. Om du inte vill använda åtgärdsgrupper anger du `[]`.
    * **Värden för resursgruppsfiler**: ange en lista med resursgruppsnamn som ska filtreras. Förväntat format är `["Resource Group Name1","Resource Group Name2"]`. Om du inte vill använda ett filter anger du `[]`. 
    * **Värden för mätarkategorifilter**: ange en lista med mätarkategorier i Azure-tjänsten. Förväntat format är `["Meter Category1","Meter Category2"]`. Om du inte vill använda ett filter anger du `[]`.
   
3. Beroende på typen av Azure-prenumeration gör du något av följande:
   - Välj **Granska + skapa**.
   - Granska villkoren, välj **Jag godkänner villkoren ovan** och sedan **Köp**.

4. Om du valde **Granska och skapa** verifieras mallen. Välj **Skapa**.  

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
