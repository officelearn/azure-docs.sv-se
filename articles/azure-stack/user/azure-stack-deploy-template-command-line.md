---
title: Distribuera mallar med kommandoraden i Azure Stack | Microsoft Docs
description: Lär dig hur du använder plattformsoberoende kommandoradsgränssnittet (CLI) för att distribuera mallar i Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 9584177f-4af3-4834-864d-930b09ae0995
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2019
ms.author: sethm
ms.reviewer: unknown
ms.openlocfilehash: 5d785549ff1cc73c7ff8a5013601276facf372ab
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2019
ms.locfileid: "54247698"
---
# <a name="deploy-templates-in-azure-stack-using-the-command-line"></a>Distribuera mallar i Azure Stack med hjälp av kommandoraden

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

Använda kommandoraden för att distribuera Azure Resource Manager-mallar i Azure Stack Development Kit-miljö. Azure Resource Manager-mallar distribuera och Tillhandahåll alla resurser för programmet i en enda, samordnad åtgärd.

## <a name="before-you-begin"></a>Innan du börjar

- [Installera och ansluta](azure-stack-version-profiles-azurecli2.md) till Azure Stack med Azure CLI.
- Ladda ned filerna *azuredeploy.json* och *azuredeploy.parameters.json* från den [skapa exempelmall för storage-konto](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-create-storage-account).

## <a name="deploy-template"></a>Distribuera mallen

Navigera till mappen dit filerna har hämtats och kör följande kommando för att distribuera mallen:

```azurecli
az group create "cliRG" "local" –f azuredeploy.json –d "testDeploy" –e azuredeploy.parameters.json
```

Det här kommandot distribuerar mallen till resursgruppen **cliRG** på standardplatsen för Azure Stack POC.

## <a name="validate-template-deployment"></a>Validera malldistributionen av

Om du vill se den här resurskontot för gruppen och lagring, Använd följande CLI-kommandon:

```azurecli
az group list

az storage account list
```

## <a name="next-steps"></a>Nästa steg

- Mer information om hur du distribuerar mallar finns:

[Distribuera mallar med PowerShell](azure-stack-deploy-template-powershell.md)
