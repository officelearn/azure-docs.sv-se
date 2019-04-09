---
title: Azure Resource Manager-mallexempel – Azure Container Instances
description: Azure Resource Manager-mallexempel för Azure Container Instances
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 03/07/2019
ms.author: danlep
ms.openlocfilehash: 3d73d05c64f4b4867c69a15089c19ab8c320b9a8
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/04/2019
ms.locfileid: "59006480"
---
# <a name="azure-resource-manager-templates-for-azure-container-instances"></a>Azure Resource Manager-mallar för Azure Container Instances

Följande exempelmallarna distribuera behållarinstanser i olika konfigurationer.

Distributionsalternativ finns i den [distribution](#deployment) avsnittet. Om du vill skapa egna mallar, Azure Container Instances [Resource Manager-mallreferensen] [ ref] innehåller mallformat och tillgängliga egenskaper.

## <a name="sample-templates"></a>Exempelmallar

| | |
|-|-|
| **Appar** ||
| [WordPress][app-wp] | Skapar en WordPress-webbplats och dess MySQL-databas i en behållargrupp. WordPress webbplatsens innehåll och MySQL-databas har sparats till en Azure Files dela. Skapar även en application gateway om du vill exponera offentliga nätverksåtkomst till WordPress. |
| [MS NAV med SQLServer och IIS][app-nav] | Distribuerar en enkel Windows-behållare med en komplett funktionalitet självständigt Dynamics NAV / Dynamics 365 Business Central miljö. |
| **Volymer** ||
| [emptyDir][vol-emptydir] | Distribuerar två Linux-behållare som delar en emptyDir-volym. |
| [gitRepo][vol-gitrepo] | Distribuerar en Linux-behållare som du klonar en GitHub-lagringsplatsen och monterar den som en volym. |
| [secret][vol-secret] | Distribuerar en Linux-behållare med ett PFX-certifikat som monterats som en hemlig volym. |
| **Nätverk** ||
| [UDP-exponerade behållare][net-udp] | Distribuerar en Windows- eller Linux-behållare som Exponerar en UDP-port. |
| [Linux-behållare med offentliga IP-adress][net-publicip] | Distribuerar en enkel Linux behållare som är tillgängliga via en offentlig IP-adress. |
| [Distribuera en behållargrupp med ett virtuellt nätverk (förhandsversion)][net-vnet] | Distribuerar ett nytt virtuellt nätverk, undernät, nätverksprofil och behållargruppen. |
| **Azure-resurser** ||
| [Skapa Azure Storage-konto och dela filer][az-files] | Använder Azure CLI i en behållarinstans för att skapa ett lagringskonto och en Azure Files-resurs.

## <a name="deployment"></a>Distribution

Har du flera alternativ för att distribuera resurser med Resource Manager-mallar:

[Azure CLI][deploy-cli]

[Azure PowerShell][deploy-powershell]

[Azure-portalen][deploy-portal]

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
[deploy-cli]: ../azure-resource-manager/resource-group-template-deploy-cli.md
[deploy-portal]: ../azure-resource-manager/resource-group-template-deploy-portal.md
[deploy-powershell]: ../azure-resource-manager/resource-group-template-deploy.md
[deploy-rest]: ../azure-resource-manager/resource-group-template-deploy-rest.md
[ref]: /azure/templates/microsoft.containerinstance/containergroups
