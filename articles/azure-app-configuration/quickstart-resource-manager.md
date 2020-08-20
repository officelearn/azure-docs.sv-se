---
title: Automatisk distribution av virtuella datorer med Azure App konfigurations snabb start
description: Den här snabb starten visar hur du använder Azure PowerShell-modulen och Azure Resource Manager mallar för att distribuera ett Azure App konfigurations lager. Använd sedan värdena i butiken för att distribuera en virtuell dator.
author: lisaguthrie
ms.author: lcozzens
ms.date: 08/11/2020
ms.topic: quickstart
ms.service: azure-app-configuration
ms.custom:
- mvc
- subject-armqs
ms.openlocfilehash: 7b7dd00d3495c24733ecdc213e0e25f8bc9640eb
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2020
ms.locfileid: "88661477"
---
# <a name="quickstart-automated-vm-deployment-with-app-configuration-and-resource-manager-template-arm-template"></a>Snabb start: automatisk distribution av virtuella datorer med app-konfiguration och Resource Manager-mall (ARM-mall)

Lär dig hur du använder Azure Resource Manager mallar och Azure PowerShell för att distribuera ett Azure App konfigurations lager, hur du lägger till nyckel värden i butiken och hur du använder nyckel värden i butiken för att distribuera en Azure-resurs, t. ex. en virtuell Azure-dator i det här exemplet.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="review-the-templates"></a>Granska mallarna

Mallarna som används i den här snabb starten är från [Azure snabb starts mallar](https://azure.microsoft.com/resources/templates/). Den [första mallen](https://azure.microsoft.com/resources/templates/101-app-configuration-store/) skapar ett konfigurations Arkiv för appar:

:::code language="json" source="~/quickstart-templates/101-app-configuration-store/azuredeploy.json" range="1-37" highlight="27-35":::

En Azure-resurs har definierats i mallen:

- [Microsoft. AppConfiguration/configurationStores](/azure/templates/microsoft.appconfiguration/2019-10-01/configurationstores): skapa ett konfigurations lager för appar.

Den [andra mallen](https://azure.microsoft.com/resources/templates/101-app-configuration/) skapar en virtuell dator med hjälp av nyckel värden i butiken. Innan det här steget måste du lägga till nyckel värden med hjälp av portalen eller Azure CLI.

:::code language="json" source="~/quickstart-templates/101-app-configuration/azuredeploy.json" range="1-217" highlight="77, 181,189":::

## <a name="deploy-the-templates"></a>Distribuera mallarna

### <a name="create-an-app-configuration-store"></a>Skapa ett konfigurations Arkiv för appen

1. Välj följande bild för att logga in på Azure och öppna en mall. Mallen skapar ett konfigurations lager för appar.

    [![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store%2Fazuredeploy.json)

1. Välj eller ange följande värden.

    - **prenumeration**: Välj den Azure-prenumeration som används för att skapa appens konfigurations arkiv.
    - **Resurs grupp**: Välj **Skapa ny** för att skapa en ny resurs grupp, om du inte vill använda en befintlig resurs grupp.
    - **Region**: Välj en plats för resurs gruppen.  Till exempel **USA, östra**.
    - **Namn på konfigurations Arkiv**: Ange ett nytt namn på appens konfigurations arkiv.
    - **Plats**: Ange platsen för appens konfigurations arkiv.  Använd standardvärdet.
    - **SKU-namn**: Ange SKU-namnet för appens konfigurations arkiv. Använd standardvärdet.

1. Välj **Granska + skapa**.
1. Verifiera att sidan visar att **verifieringen är klar**och välj sedan **skapa**.

Anteckna resurs gruppens namn och namnet på appens konfigurations arkiv.  Du behöver dessa värden när du distribuerar den virtuella datorn
### <a name="add-vm-configuration-key-values"></a>Lägg till konfigurations nyckel för virtuell dator-värden

När du har skapat ett konfigurations Arkiv för appar kan du använda Azure Portal eller Azure CLI för att lägga till nyckel värden i butiken.

1. Logga in på [Azure Portal](https://portal.azure.com)och navigera sedan till det nyligen skapade konfigurations arkivet för appen.
1. Välj **Configuration Explorer** på den vänstra menyn.
1. Välj **skapa** för att lägga till följande nyckel/värde-par:

   |Tangent|Värde|Etikett|
   |-|-|-|
   |windowsOsVersion|2019 – Data Center|mall|
   |diskSizeGB|1023|mall|

   Behåll **innehålls typen** tom.

Information om hur du använder Azure CLI finns [i arbeta med nyckel värden i en Azure App konfigurations lager](./scripts/cli-work-with-keys.md).

### <a name="deploy-vm-using-stored-key-values"></a>Distribuera virtuell dator med hjälp av lagrade nyckel värden

Nu när du har lagt till nyckel värden i butiken är du redo att distribuera en virtuell dator med hjälp av en Azure Resource Manager-mall. Mallen refererar till de **windowsOsVersion** -och **diskSizeGB** -nycklar som du har skapat.

> [!WARNING]
> ARM-mallar kan inte referera till nycklar i ett program konfigurations lager som har privat länk aktiverat.

1. Välj följande bild för att logga in på Azure och öppna en mall. Mallen skapar en virtuell dator med hjälp av lagrade nyckel värden i appens konfigurations arkiv.

    [![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration%2Fazuredeploy.json)

1. Välj eller ange följande värden.

    - **prenumeration**: Välj den Azure-prenumeration som används för att skapa den virtuella datorn.
    - **Resurs grupp**: Ange antingen samma resurs grupp som appens konfigurations arkiv eller skapa en ny resurs grupp genom att välja **Skapa ny** .
    - **Region**: Välj en plats för resurs gruppen.  Till exempel **USA, östra**.
    - **Plats**: Ange platsen för den virtuella datorn. Använd standardvärdet.
    - **Admin-användar namn**: Ange ett administratörs användar namn för den virtuella datorn.
    - **Administratörs lösen ord**: Ange ett administratörs lösen ord för den virtuella datorn.
    - **Domän namns etikett**: Ange ett unikt domän namn.
    - **Lagrings konto namn**: Ange ett unikt namn för ett lagrings konto som är kopplat till den virtuella datorn.
    - **Resurs grupp för app config Store**: Ange den resurs grupp som innehåller appens konfigurations lager.
    - **Namn på App config Store**: Ange namnet på Azure App konfigurations lagringen.
    - **VM SKU-nyckel**: ange **windowsOsVersion**.  Detta är nyckel värdes namnet som du lade till i arkivet.
    - **Disk storleks nyckel**: ange **diskSizeGB**. Det här är nyckel värdes namnet som du har lagt till i butiken.

1. Välj **Granska + skapa**.
1. Verifiera att sidan visar att **verifieringen är klar**och välj sedan **skapa**.

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

1. Logga in på [Azure Portal](https://portal.azure.com)och navigera sedan till den nya virtuella datorn.
1. Välj **Översikt** på den vänstra menyn och kontrol lera att **SKU: n** är **2019-datacenter**.
1. Välj **diskar** på den vänstra menyn och kontrol lera att storleken på data disken är **2013**.

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs tar du bort resurs gruppen, konfigurations arkivet för appen, den virtuella datorn och alla relaterade resurser. Om du planerar att använda konfigurations arkivet för appen eller den virtuella datorn i framtiden kan du hoppa över borttagningen. Om du inte planerar att fortsätta använda det här jobbet tar du bort alla resurser som skapades i snabbstarten genom att köra följande cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup `
  -Name $resourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du distribuerat en virtuell dator med hjälp av en Azure Resource Manager-mall och nyckel värden från Azure App konfiguration.

Om du vill veta mer om hur du skapar andra program med Azure App konfiguration fortsätter du till följande artikel:

> [!div class="nextstepaction"]
> [Snabb start: skapa en ASP.NET Core-app med Azure App konfiguration](quickstart-aspnet-core-app.md)
