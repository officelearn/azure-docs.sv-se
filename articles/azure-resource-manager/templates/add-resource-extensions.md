---
title: Konfiguration efter distribution med hjälp av tillägg
description: Lär dig hur du använder Azure Resource Manager-malltillägg för att tillhandahålla konfigurationer efter distributionen.
author: mumian
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: jgao
ms.openlocfilehash: b3c4110c8761b3e8daf324d65ac7fa1dcbcdf61f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023505"
---
# <a name="provide-post-deployment-configurations-by-using-extensions"></a>Tillhandahålla konfigurationer efter distribution med hjälp av tillägg

Malltillägg är små program som tillhandahåller konfigurations- och automatiseringsuppgifter efter distributionen på Azure-resurser. Den mest populära är virtuella maskintillägg. Se [Tillägg och funktioner för virtuella](../../virtual-machines/extensions/features-windows.md)datorer för Windows och Tillägg och funktioner för virtuella datorer för [Linux](../../virtual-machines/extensions/features-linux.md).

## <a name="extensions"></a>Tillägg

De befintliga tilläggen är:

- [Microsoft.Compute/virtualMachines/extensions](https://docs.microsoft.com/azure/templates/microsoft.compute/2018-10-01/virtualmachines/extensions)
- [Microsoft.Beräkna virtuellamaskinskalor/tillägg](https://docs.microsoft.com/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions)
- [Microsoft.HDInsight-kluster/tillägg](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/2018-06-01-preview/clusters)
- [Microsoft.Sql-servrar/databaser/tillägg](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions) 
- [Microsoft.Webbplatser/webbplatsextensions](https://docs.microsoft.com/azure/templates/microsoft.web/2016-08-01/sites/siteextensions)

Om du vill ta reda på tillgängliga tillägg bläddrar du till [mallreferensen](https://docs.microsoft.com/azure/templates/). I **Filter efter rubrik**anger du **tillägg**.

Mer information om hur du använder dessa tillägg finns i:

- [Självstudiekurs: Distribuera tillägg till virtuella datorer med Azure Resource Manager-mallar](template-tutorial-deploy-vm-extensions.md).
- [Självstudie: Importera SQL BACPAC-filer med Azure Resource Manager-mallar](template-tutorial-deploy-sql-extensions-bacpac.md)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: Distribuera tillägg för virtuell dator med Azure Resource Manager-mallar](template-tutorial-deploy-vm-extensions.md)
