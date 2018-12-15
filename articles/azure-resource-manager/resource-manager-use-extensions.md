---
title: Tillhandahåll konfigurationer efter distributionen med hjälp av tillägg – Azure | Microsoft Docs
description: Lär dig hur du använder malltillägg för Azure Resource Manager-för att ange konfigurationer efter distributionen.
services: azure-resource-manager
documentationcenter: na
author: mumian
editor: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/14/2018
ms.author: jgao
ms.openlocfilehash: eb46966c3a28b3fa4c2b23668109b7c5d23a609b
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2018
ms.locfileid: "53414381"
---
# <a name="provide-post-deployment-configurations-by-using-extensions"></a>Tillhandahåll konfigurationer efter distributionen med hjälp av tillägg

Malltillägg är litet program som ger konfigurationen efter distribution och automatiserade uppgifter på Azure-resurser. Den mest populära är tillägg till virtuella datorer. Se [VM-tillägg och funktioner i Windows](../virtual-machines/extensions/features-windows.md), och [virtuella datorer, tillägg och funktioner i Linux](../virtual-machines/extensions/features-linux.md).

## <a name="extensions"></a>Tillägg

Befintliga tilläggen är:

- [Microsoft.Compute/virtualMachines/extensions](https://docs.microsoft.com/azure/templates/microsoft.compute/2018-10-01/virtualmachines/extensions)
- [Microsoft.Compute-virtualMachineScaleSets /-tillägg](https://docs.microsoft.com/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions)
- [Microsoft.HDInsight-kluster /-tillägg](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/2018-06-01-preview/clusters/extensions)
- [Microsoft.Sql servrar/databaser/tillägg](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions) 
- [Microsoft.Web/sites/siteextensions](https://docs.microsoft.com/azure/templates/microsoft.web/2016-08-01/sites/siteextensions)

Om du vill ta reda på tillgängliga tillägg kan du bläddra till den [mallreferensen](https://docs.microsoft.com/azure/templates/). I **filtrera efter rubrik**, ange **tillägget**.

Lär dig hur du använder de här tilläggen, se:

- [Självstudiekurs: Distribuera virtual machine-tillägg med Azure Resource Manager-mallar](./resource-manager-tutorial-deploy-vm-extensions.md).
- [Självstudiekurs: Importera SQL BACPAC-filer med Azure Resource Manager-mallar](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudiekurs: Distribuera virtual machine-tillägg med Azure Resource Manager-mallar](./resource-manager-tutorial-deploy-vm-extensions.md)
