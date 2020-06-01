---
title: Azure Private Link service ARM-mall
description: Personlig länk service ARM-mall
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: article
ms.date: 05/29/2020
ms.author: allensu
ms.openlocfilehash: 93a66057ddb0034f7ac9ac62578292ca38f2d2fe
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/31/2020
ms.locfileid: "84237131"
---
# <a name="create-a-private-link-service---resource-manager-template"></a>Skapa en Private Link service – Resource Manager-mall

I den här snabb starten använder du en Resource Manager-mall för att skapa en privat länk-tjänst.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Du kan också slutföra den här snabb starten med [Azure Portal](create-private-link-service-portal.md), [Azure POWERSHELL](create-private-link-service-powershell.md)eller [Azure CLI](create-private-link-service-cli.md).

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-private-link-service"></a>Skapa en privat länk-tjänst

den här mallen skapar en privat länk-tjänst.

### <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabb starten är från [Azure snabb starts mallar](https://github.com/Azure/azure-quickstart-templates/blob/master/101-privatelink-service/azuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-privatelink-service/azuredeploy.json" range="001-432" highlight="263-289":::

Flera Azure-resurser definieras i mallen:

- [**Microsoft. Network/privateLinkServices**](/azure/templates/microsoft.network/privateLinkServices) : privat länk tjänst för att exponera tjänsten lokalt
- [**Microsoft. Network/privateendpoints**](/azure/templates/microsoft.network/privateendpoints) : privat slut punkt för att få åtkomst till privat tjänst
- [**Microsoft. Network/belastningsutjämnare**](/azure/templates/microsoft.network/loadBalancers) : Load Balancer som exponerar de virtuella datorer som är värdar för tjänsten
- [**Microsoft. Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines) : 2 virtuella datorer, en som är värd för tjänsten och en för att testa anslutningen till den privata slut punkten
- [**Microsoft. Compute/virtualMachines/tillägg**](/azure/templates/Microsoft.Compute/virtualMachines/extensions) : tillägg som installerar webb server
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks) : ett för varje virtuell dator
- [**Microsoft. Network/publicIpAddresses**](/azure/templates/microsoft.network/publicIpAddresses) : 2 offentlig IP-adress, en för varje virtuell dator
- [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) : 2 nätverks gränssnitt, ett för varje virtuell dator

### <a name="deploy-the-template"></a>Distribuera mallen

Distribuera Resource Manager-mall till Azure:

1. Välj **distribuera till Azure** för att logga in på Azure och öppna mallen. Mallen skapar en virtuell dator, standard belastningsutjämnare, privat länk tjänst, privat slut punkt, nätverk och en virtuell dator som ska verifieras.

   [![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-privatelink-service%2Fazuredeploy.json)

2. Välj eller skapa en resurs grupp,
3. Ange administratörs användar namn och lösen ord för den virtuella datorn.
4. Välj **Jag accepterar de villkor som anges ovan** och välj sedan **köp**.

## <a name="validate-the-deployment"></a>Verifiera distributionen

> [!NOTE]
> ARM-mallen genererar ett unikt namn för den virtuella datorns myConsumerVm<b>{UniqueID}</b> resurs. Ersätt <b>{UniqueID}</b> med det genererade värdet.

### <a name="connect-to-a-vm-from-the-internet"></a>Ansluta till en virtuell dator från Internet

Anslut till VM- _myConsumerVm {UniqueID}_ från Internet på följande sätt:

1.  I portalens Sök fält anger du _myConsumerVm {UniqueID}_.

2.  Välj knappen **Anslut**. När du har valt knappen **Anslut** öppnas **Anslut till den virtuella datorn**.

3.  Välj **Ladda ned RDP-fil**. Azure skapar en _.rdp_-fil (Remote Desktop Protocol) och laddar ned den till datorn.

4.  Öppna den nedladdade RDP- \* filen.

    a. Välj **Anslut** om du uppmanas att göra det.

    b. Ange det användar namn och lösen ord som du angav när du skapade den virtuella datorn.
    
    > [!NOTE]
    > Du kan behöva välja **fler alternativ**  >  **Använd ett annat konto**för att ange de autentiseringsuppgifter du angav när du skapade den virtuella datorn.

5.  Välj **OK**.

6.  Du kan få en certifikatvarning under inloggningen. Välj **Ja** eller **Fortsätt** om du får en certifikatvarning.

7.  När virtuella datorns skrivbord visas kan du minimera det att gå tillbaka till din lokala dator.

### <a name="access-the-http-service-privately-from-the-vm"></a>Få åtkomst till http-tjänsten privat från den virtuella datorn

I det här avsnittet ska du ansluta till http-tjänsten från den virtuella datorn med hjälp av den privata slut punkten.

1.  I fjärr skrivbordet för _myConsumerVm {UniqueID}_
2.  Öppna en webbläsare och ange adressen till den privata slut punktenhttp://10.0.0.5/
3.  Du ser den förvalda IIS-sidan

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resurs gruppen när du inte längre behöver de resurser som du skapade med den privata länk tjänsten. Detta tar bort den privata länk tjänsten och alla relaterade resurser.

Anropa cmdleten om du vill ta bort resurs gruppen `Remove-AzResourceGroup` :

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Azures privata länk](private-link-overview.md)
