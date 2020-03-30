---
title: 'Snabbstart: Direkt webbtrafik med hjälp av en Resource Manager-mall'
titleSuffix: Azure Application Gateway
description: Lär dig hur du använder en Resource Manager-mall för att skapa en Azure Application Gateway som dirigerar webbtrafik till virtuella datorer i en serverdelspool.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 03/23/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 0612371605f6b216eadeef49d9adb1497ba31591
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80135974"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---resource-manager-template"></a>Snabbstart: Direkt webbtrafik med Azure Application Gateway - Resource Manager-mall

I den här snabbstarten använder du en Resource Manager-mall för att skapa en Azure Application Gateway. Sedan kan du testa programgatewayen för att se till att den fungerar korrekt.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Du kan också slutföra den här snabbstarten med [Azure-portalen,](quick-create-portal.md) [Azure PowerShell](quick-create-powershell.md)eller [Azure CLI](quick-create-cli.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto gratis](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-an-application-gateway"></a>Skapa en programgateway

För enkelhetens skull skapar den här mallen en enkel installation med en offentlig klient-TV-adress, en grundläggande lyssnare som är värd för en enda plats i programgatewayen, en grundläggande routningsregel för begäran och två virtuella datorer i serverdelspoolen.

### <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure Quickstart-mallar](https://github.com/Azure/azure-quickstart-templates/blob/master/ag-docs-qs/azuredeploy.json)

:::code language="json" source="~/quickstart-templates/ag-docs-qs/azuredeploy.json" range="001-343" highlight="197-297":::

Flera Azure-resurser definieras i mallen:

- [**Microsoft.Network/applicationgateways**](/azure/templates/microsoft.network/applicationgateways)
- [**Microsoft.Network/publicIPAdresser:**](/azure/templates/microsoft.network/publicipaddresses) en för programgatewayen och två för de virtuella datorerna.
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines) : två virtuella datorer
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) : två för de virtuella datorerna
- [**Microsoft.Compute/virtualMachine/extensions**](/azure/templates/microsoft.compute/virtualmachines/extensions) : för att konfigurera IIS och webbsidorna


### <a name="deploy-the-template"></a>Distribuera mallen

Distribuera Resource Manager-mall till Azure:

1. Välj **Distribuera till Azure** för att logga in på Azure och öppna mallen. Mallen skapar en programgateway, nätverksinfrastrukturen och två virtuella datorer i serverdelspoolen som kör IIS.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-qs%2Fazuredeploy.json"><img src="./media/quick-create-template/deploy-to-azure.png" alt="deploy to azure"/></a>

2. Välj eller skapa resursgruppen, skriv användarnamn och lösenord för administratören för den virtuella datorn.
3. Välj **Jag godkänner de villkor som anges ovan** och välj sedan **Köp**. Distributionen kan ta 20 minuter eller längre att slutföra.

## <a name="validate-the-deployment"></a>Verifiera distributionen

Även om IIS inte krävs för att skapa programgatewayen, installeras den för att verifiera om Azure har skapat programgatewayen. Använd IIS för att testa programgatewayen:

1. Hitta den offentliga IP-adressen för programgatewayen på sidan **Översikt.** ![Registrera den offentliga](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png) IP-adressen för programgateway Eller så kan du välja **Alla resurser,** ange *myAGPublicIPAddress* i sökrutan och sedan markera den i sökresultaten. Azure visar den offentliga IP-adressen på sidan **Översikt**.
2. Kopiera den offentliga IP-adressen och klistra sedan in den i webbläsarens adressfält för att bläddra i den IP-adressen.
3. Kontrollera svaret. Ett giltigt svar verifierar att programgatewayen har skapats och kan ansluta till backend.

   ![Testa programgatewayen](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

   Uppdatera webbläsaren flera gånger och du bör se anslutningar till både myVM1 och myVM2.

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver de resurser som du skapade med programgatewayen tar du bort resursgruppen. Detta tar bort programgatewayen och alla relaterade resurser.

Om du vill ta `Remove-AzResourceGroup` bort resursgruppen anropar du cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hantera webbtrafik med en programgateway som använder Azure CLI](./tutorial-manage-web-traffic-cli.md)
