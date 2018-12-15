---
title: Skapa Azure-program tekniska resurser | Microsoft Docs
description: Skapa tekniska resurser för ett erbjudande för Azure-program.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 12/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 6050ad98c87dbe38516a6ee3c4862495ad868031
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2018
ms.locfileid: "53414388"
---
# <a name="prepare-your-azure-application-technical-assets"></a>Förbereda dina tekniska resurser för Azure-program

Den här artikeln beskriver resurserna för att förbereda tekniska resurser för ditt erbjudande i Azure-program.

## <a name="before-you-begin"></a>Innan du börjar

Granska följande dokumentation från Azure-program, vilket ger Snabbstarter, självstudier och exempel.

- [Förstå Azure Resource Manager-mallar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)
- Snabbstarter:

  - [Azure Quickstart-mallar](https://azure.microsoft.com/documentation/templates/)
  - [GitHub Azure Quickstart-mallar](https://github.com/azure/azure-quickstart-templates)
  - [Publicera programdefinition](https://docs.microsoft.com/azure/managed-applications/publish-managed-app-definition-quickstart)
  - [Distribuera service catalog-app](https://docs.microsoft.com/azure/managed-applications/deploy-service-catalog-quickstart)

  
- Självstudier:

  - [Skapa definitionsfiler](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
  - [Publicera marknadsplatsprogram](https://docs.microsoft.com/azure/managed-applications/publish-marketplace-app)

 - Exempel:

   - [Azure CLI](https://docs.microsoft.com/azure/managed-applications/cli-samples)
   - [Azure PowerShell](https://docs.microsoft.com/azure/managed-applications/powershell-samples)
   - [Hanterade programlösningar](https://docs.microsoft.com/azure/managed-applications/sample-projects)

## <a name="fundamental-technical-knowledge"></a>Grundläggande teknisk kunskap

Utforma, bygga och testa dessa tillgångar ta tid och kräver teknisk kunskap av både Azure-plattformen och de tekniker som används för att skapa erbjudandet.

Ingenjörsteamet bör ha kunskaper om följande Microsoft-tekniker:

- Grundläggande förståelse för [Azure-tjänster](https://azure.microsoft.com/services/)
- Så här [utforma och skapa Azure-program](https://azure.microsoft.com/solutions/architecture/)
- Kunskaper om [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage), och [Azure Networking](https://azure.microsoft.com/services/?filter=networking)
- Kunskaper om [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
- Kunskaper om [JSON](https://www.json.org/)

## <a name="suggested-tools"></a>Föreslaget verktyg

Välj en eller båda av följande skript miljöer för att hantera dina Azure-program:

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
- [Azure CLI](https://docs.microsoft.com/cli/azure)

Vi rekommenderar att lägga till följande verktyg i utvecklingsmiljön:

- [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
- [Visual Studio Code](https://code.visualstudio.com/) med följande filtillägg:

  - Anknytning: [Verktyget Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - Anknytning: [Tjärgropar](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - Anknytning: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Vi rekommenderar också granska de tillgängliga verktyg i den [Azure-utvecklarverktyg](https://azure.microsoft.com/tools/) sidan och, om du använder Visual Studio i [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="next-steps"></a>Nästa steg

[Skapa ett erbjudande för Azure-program](./cpp-create-offer.md)

