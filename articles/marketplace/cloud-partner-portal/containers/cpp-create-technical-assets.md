---
title: Skapa behållaravbildning för Azure teknisk tillgångar | Microsoft Docs
description: Skapa tekniska resurser för en Azure-behållare.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: pbutlerm
ms.openlocfilehash: 5a7531be73a872d9c088a0bf02a8686f947c220a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61472948"
---
# <a name="prepare-your-container-technical-assets"></a>Förbereda din behållare tekniska resurser

Den här artikeln beskriver stegen och krav för att konfigurera ett erbjudande med behållare på Azure Marketplace.

## <a name="before-you-begin"></a>Innan du börjar

Granska den [Azure Container Instances](https://docs.microsoft.com/azure/container-instances) dokumentationen som innehåller Snabbstarter, självstudier och exempel.

## <a name="fundamental-technical-knowledge"></a>Grundläggande teknisk kunskap

Utforma, bygga och testa dessa tillgångar ta tid och kräver teknisk kunskap av både Azure-plattformen och de tekniker som används för att skapa erbjudandet.
 
Förutom att din lösning domän, bör ingenjörsteamet ha kunskaper om följande Microsoft-tekniker:

-   Grundläggande förståelse för [Azure-tjänster](https://azure.microsoft.com/services/) 
-   Så här [utforma och skapa Azure-program](https://azure.microsoft.com/solutions/architecture/)
-   Kunskaper om [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage) och [Azure Networking](https://azure.microsoft.com/services/?filter=networking)
-   Kunskaper om [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
-   Kunskaper om [JSON](https://www.json.org/)

## <a name="suggested-tools"></a>Föreslaget verktyg

Välj en eller båda av följande skript miljöer för att hantera din behållaravbildning:

-   [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-   [Azure CLI](https://docs.microsoft.com/cli/azure)

Vi rekommenderar dessutom att lägga till följande verktyg i utvecklingsmiljön:

-   [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-   [Visual Studio Code](https://code.visualstudio.com/)
    *   Anknytning: [Verktyget Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *   Anknytning: [Tjärgropar](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *   Anknytning: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Vi rekommenderar också granska de tillgängliga verktyg i den [Azure-utvecklarverktyg](https://azure.microsoft.com/tools/) sidan och, om du använder Visual Studio i [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-the-container-image"></a>Skapa behållaravbildningen

- Skapa och konfigurera den virtuella hårddisken (VHD) för din behållare virtuella dator (VM). Den här virtuella Hårddisken innehåller operativsystemet (Windows, Linux och Ubuntu) för behållaren. Ytterligare datadiskar kan krävas.
- Konfigurera VM-OS, VM-storlek, portar öppna och eventuella anslutna datadiskar.
- Installera programmet och annan programvara som behövs för ditt erbjudande. Till exempel: databasprogram, programvara från tredje part eller ett anpassat program.

## <a name="next-steps"></a>Nästa steg

[Skapa ditt erbjudande för behållare](./cpp-create-offer.md)
