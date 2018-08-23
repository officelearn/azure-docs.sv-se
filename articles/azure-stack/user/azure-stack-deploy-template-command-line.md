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
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 7814552256f17c5265bbeb4ce8c069dd8dca1bb2
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2018
ms.locfileid: "42059692"
---
# <a name="deploy-templates-in-azure-stack-using-the-command-line"></a>Distribuera mallar i Azure Stack med hjälp av kommandoraden

*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*

Använda kommandoraden för att distribuera Azure Resource Manager-mallar till Azure Stack Development Kit. Azure Resource Manager-mallar distribuera och Tillhandahåll alla resurser för programmet i en enda, samordnad åtgärd.

## <a name="before-you-begin"></a>Innan du börjar
 - [Installera och ansluta](azure-stack-version-profiles-azurecli2.md) till Azure Stack med Azure CLI
 - Ladda ned filerna *azuredeploy.json* och *azuredeploy.parameters.json* från den [skapa exempelmall för storage-konto](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-create-storage-account).
 
## <a name="deploy-template"></a>Distribuera mallen
Gå till den mapp där filerna har hämtats och kör följande kommando för att distribuera mallen:

    azure group create "cliRG" "local" –f azuredeploy.json –d "testDeploy" –e azuredeploy.parameters.json

Det här kommandot distribuerar mallen till resursgruppen **cliRG** i Azure Stack POC-standardplatsen.

## <a name="validate-template-deployment"></a>Validera malldistributionen av
Om du vill se den här resurskontot för gruppen och lagring, använder du följande kommandon:

    azure group list

    azure storage account list




