---
title: Azure Resource Manager-mallexempel
description: Hitta mallexempel för Azure Resource Manager för att distribuera Azure Container Instances i olika konfigurationer
ms.topic: article
ms.date: 03/07/2019
ms.openlocfilehash: a8f3c81c539562a3c56e4822cf6e4df77d04928f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981655"
---
# <a name="azure-resource-manager-templates-for-azure-container-instances"></a>Azure Resource Manager-mallar för Azure Container-instanser

Följande exempelmallar distribuerar behållarinstanser i olika konfigurationer.

Distributionsalternativ finns i avsnittet [Distribution.](#deployment) Om du vill skapa egna mallar, azure container instances [Resource Manager mall referensinformation][ref] mallformat och tillgängliga egenskaper.

## <a name="sample-templates"></a>Exempelmallar

| | |
|-|-|
| **Program** ||
| [WordPress][app-wp] | Skapar en WordPress-webbplats och dess MySQL-databas i en containergrupp. WordPress-webbplatsinnehållet och MySQL-databasen sparas i en Azure Files-resurs. Skapar också en programgateway för att exponera allmänhetens nätverksåtkomst till WordPress. |
| [MS NAV med SQL Server och IIS][app-nav] | Distribuerar en enda Windows-behållare med en komplett fristående Dynamics NAV / Dynamics 365 Business Central-miljö. |
| **Volymer** ||
| [emptyDir][vol-emptydir] | Distribuerar två Linux-behållare som delar en emptyDir-volym. |
| [gitRepo][vol-gitrepo] | Distribuerar en Linux-behållare som klonar en GitHub-repo och monterar den som en volym. |
| [Hemliga][vol-secret] | Distribuerar en Linux-behållare med ett PFX-certifikat monterat som en hemlig volym. |
| **Nätverk** ||
| [UDP-exponerad behållare][net-udp] | Distribuerar en Windows- eller Linux-behållare som exponerar en UDP-port. |
| [Linux-behållare med offentlig IP][net-publicip] | Distribuerar en enda Linux-behållare som är tillgänglig via en offentlig IP. |
| [Distribuera en behållargrupp med ett virtuellt nätverk (förhandsgranskning)][net-vnet] | Distribuerar ett nytt virtuellt nätverk, undernät, nätverksprofil och behållargrupp. |
| **Azure-resurser** ||
| [Skapa Azure Storage-konto och filresurs][az-files] | Använder Azure CLI i en behållarinstans för att skapa ett lagringskonto och en Azure Files-resurs.

## <a name="deployment"></a>Distribution

Du har flera alternativ för att distribuera resurser med Resource Manager-mallar:

[Azure CLI][deploy-cli]

[Azure PowerShell][deploy-powershell]

[Azure-portal][deploy-portal]

[REST API][deploy-rest]

<!-- LINKS - External -->
[app-nav]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-dynamicsnav
[app-wp]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress
[az-files]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-storage-file-share
[net-publicip]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-linuxcontainer-public-ip
[net-udp]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-udp
[net-vnet]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
[repo]: https://github.com/Azure/azure-quickstart-templates
[vol-emptydir]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-emptydir
[vol-gitrepo]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-gitrepo
[vol-secret]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-secret

<!-- LINKS - Internal -->
[deploy-cli]: ../azure-resource-manager/templates/deploy-cli.md
[deploy-portal]: ../azure-resource-manager/templates/deploy-portal.md
[deploy-powershell]: ../azure-resource-manager/templates/deploy-powershell.md
[deploy-rest]: ../azure-resource-manager/templates/deploy-rest.md
[ref]: /azure/templates/microsoft.containerinstance/containergroups
