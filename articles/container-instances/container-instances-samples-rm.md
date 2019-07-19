---
title: Exempel på Azure Resource Manager mallar – Azure Container Instances
description: Azure Resource Manager mal len exempel för Azure Container Instances
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 03/07/2019
ms.author: danlep
ms.openlocfilehash: 2089f024e1de2e92f6e401549c5876e26db17ebb
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325701"
---
# <a name="azure-resource-manager-templates-for-azure-container-instances"></a>Azure Resource Manager mallar för Azure Container Instances

Följande exempel mallar distribuerar behållar instanser i olika konfigurationer.

Information om distributions alternativ finns i avsnittet [distribution](#deployment) . Om du vill skapa egna mallar kan du använda mallarna för referens information för mallen i Azure Container Instances [Resource Manager][ref] och tillgängliga egenskaper.

## <a name="sample-templates"></a>Exempelmallar

| | |
|-|-|
| **Program** ||
| [WordPress][app-wp] | Skapar en WordPress-webbplats och dess MySQL-databas i en behållar grupp. WordPress-webbplatsens innehåll och MySQL-databasen är bestående av en Azure Files-resurs. Skapar också en Programgateway för att exponera offentlig nätverks åtkomst till WordPress. |
| [MS NAV med SQL Server och IIS][app-nav] | Distribuerar en enda Windows-behållare med en helt aktuell fristående Dynamics NAV/Dynamics 365 Business central-miljö. |
| **Enheter** ||
| [emptyDir][vol-emptydir] | Distribuerar två Linux-behållare som delar en emptyDir-volym. |
| [gitRepo][vol-gitrepo] | Distribuerar en Linux-behållare som klonar en GitHub-lagrings platsen och monterar den som en volym. |
| [secret][vol-secret] | Distribuerar en Linux-behållare med ett PFX-certifikat monterat som en hemlig volym. |
| **Nätverk** ||
| [UDP-exponerad behållare][net-udp] | Distribuerar en Windows-eller Linux-behållare som exponerar en UDP-port. |
| [Linux-behållare med offentlig IP][net-publicip] | Distribuerar en enda Linux-behållare som kan nås via en offentlig IP-adress. |
| [Distribuera en behållar grupp med ett virtuellt nätverk (för hands version)][net-vnet] | Distribuerar ett nytt virtuellt nätverk, undernät, nätverks profil och behållar grupp. |
| **Azure-resurser** ||
| [Skapa Azure Storage konto-och fil resurs][az-files] | Använder Azure CLI i en behållar instans för att skapa ett lagrings konto och en Azure Files-resurs.

## <a name="deployment"></a>Distribution

Du har flera alternativ för att distribuera resurser med Resource Manager-mallar:

[Azure CLI][deploy-cli]

[Azure PowerShell][deploy-powershell]

[Azure Portal][deploy-portal]

[REST-API][deploy-rest]

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
