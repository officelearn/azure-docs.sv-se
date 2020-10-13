---
title: Använd anpassad DNS-Server
titleSuffix: Azure Machine Learning
description: Konfigurera en anpassad DNS-server så att den fungerar med en Azure Machine Learning arbets yta och en privat slut punkt.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: jhirono
author: jhirono
ms.date: 10/05/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 1d215c9564d89e5bd410e68839807f5c2c752356
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91828636"
---
# <a name="how-to-use-your-workspace-with-a-custom-dns-server"></a>Så här använder du din arbets yta med en anpassad DNS-Server

När du använder Azure Machine Learning med ett virtuellt nätverk finns det [flera sätt att hantera DNS-namnmatchning](/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances). Som standard hanterar Azure automatiskt namn matchning för din arbets yta och privat slut punkt. Men __när du använder din egen anpassade DNS-Server__måste du skapa DNS-poster för arbets ytan manuellt.

> [!IMPORTANT]
> Den här artikeln beskriver bara hur du hittar det fullständigt kvalificerade domän namnet (FQDN) och IP-adresser för dessa poster. det ger inte information om hur du konfigurerar DNS-poster för dessa objekt. I dokumentationen för DNS-programvaran finns information om hur du lägger till poster.

## <a name="prerequisites"></a>Förutsättningar

- En Azure-Virtual Network som använder [din egen DNS-Server](/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server).

- En Azure Machine Learning-arbetsyta med en privat slut punkt. Mer information finns i [skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).

- Du är van vid att använda [nätverks isolering under utbildning &s störningar](how-to-enable-virtual-network.md).

- Valfritt, [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) eller [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="find-the-ip-addresses"></a>Hitta IP-adresserna

I följande lista visas de fullständigt kvalificerade domän namnen (FQDN) som används av din arbets yta och privat slut punkt:

* `<workspace-GUID>.workspace.<region>.api.azureml.ms`
* `<workspace-GUID>.studio.workspace.<region>.api.azureml.ms`
* `cert-<workspace-GUID>.workspace.<region>.api.azureml.ms`
* `<workspace-GUID>.workspace.<region>.experiments.azureml.net`
* `<workspace-GUID>.workspace.<region>.modelmanagement.azureml.net`
* `<workspace-GUID>.workspace.<region>.aether.ms`
* `ml-<workspace-name>-<region>-<workspace-guid>.notebooks.azure.ml`
* Om du skapar en beräknings instans måste du också lägga till en post för `<instance-name>.<region>.instances.azureml.ms` .

Använd någon av följande metoder för att hitta de interna IP-adresserna för FQDN i VNet:

> [!NOTE]
> De fullständigt kvalificerade domän namnen och IP-adresserna är olika beroende på din konfiguration. Till exempel är GUID-värdet i domän namnet unikt för din arbets yta.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az network private-endpoint show --endpoint-name <endpoint> --resource-group <resource-group> --query 'customDnsConfigs[*].{FQDN: fqdn, IPAddress: ipAddresses[0]}' --output table
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
$workspaceDns=Get-AzPrivateEndpoint -Name <endpoint> -resourcegroupname <resource-group>
$workspaceDns.CustomDnsConfigs | format-table
```

# <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

1. I [Azure Portal](https://portal.azure.com)väljer du Azure Machine Learning __arbets ytan__.
1. I avsnittet __Inställningar__ väljer du __anslutningar för privata slut punkter__.
1. Välj länken i kolumnen för den __privata slut punkten__ som visas.
1. En lista över de fullständigt kvalificerade domän namnen (FQDN) och IP-adresserna för den privata slut punkten för arbets ytan finns längst ned på sidan.

:::image type="content" source="./media/how-to-custom-dns/private-endpoint-custom-dns.png" alt-text="Lista över FQDN i portalen":::

---

Informationen som returneras från alla metoder är densamma. en lista över FQDN och privat IP-adress för resurserna.

| FQDN | IP-adress |
| ----- | ----- |
| `fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.api.azureml.ms` | `10.1.0.5` |
| `fb7e20a0-8891-458b-b969-55ddb3382f51.studio.workspace.eastus.api.azureml.ms` | `10.1.0.5` |
| `cert-fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.api.azureml.ms` | `10.1.0.5` |
| `ml-myworkspace-eastus-fb7e20a0-8891-458b-b969-55ddb3382f51.notebooks.azure.net` | `10.1.0.6` |

> [!IMPORTANT]
> Vissa FQDN visas inte i listan av den privata slut punkten, men krävs av arbets ytan. Dessa FQDN visas i följande tabell och måste också läggas till i DNS-servern:
>
> * `<workspace-GUID>.workspace.<region>.experiments.azureml.net`
> * `<workspace-GUID>.workspace.<region>.modelmanagement.azureml.net`
> * `<workspace-GUID>.workspace.<region>.aether.ms`
> * Om du har en beräknings instans använder du `<instance-name>.<region>.instances.azureml.ms` , där `<instance-name>` är namnet på din beräknings instans.
>
> För alla dessa IP-adresser använder du samma adress som de `*.api.azureml.ms` poster som returneras från föregående steg.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder Azure Machine Learning med ett virtuellt nätverk finns i [Översikt över virtuella nätverk](how-to-network-security-overview.md).