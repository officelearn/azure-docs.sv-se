---
title: Distribuera mallar med kommandoraden i Azure-stacken | Microsoft Docs
description: "Lär dig hur du använder plattformsoberoende kommandoradsgränssnittet (CLI) för att distribuera mallar till Azure-stacken."
services: azure-stack
documentationcenter: 
author: heathl17
manager: byronr
editor: 
ms.assetid: 9584177f-4af3-4834-864d-930b09ae0995
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: d58d80d89bb2544c4d4a34f608177a96760406ba
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-templates-in-azure-stack-using-the-command-line"></a>Distribuera mallar i Azure-stacken med hjälp av kommandoraden

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Använda kommandoraden för att distribuera Azure Resource Manager-mallar i Azure-stacken Development Kit. Azure Resource Manager-mallar distribuera och Tillhandahåll alla resurser i programmet i en enda, samordnad åtgärd.

## <a name="before-you-begin"></a>Innan du börjar
 - [Installera och ansluta](azure-stack-connect-cli.md) till Azure-stacken med Azure CLI
 - Hämta filerna *azuredeploy.json* och *azuredeploy.parameters.json* från den [skapa storage-konto exempelmall](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-create-storage-account).
 
## <a name="deploy-template"></a>Distribuera mallen
Navigera till mappen där filerna har hämtats och kör följande kommando för att distribuera mallen:

    azure group create "cliRG" "local" –f azuredeploy.json –d "testDeploy" –e azuredeploy.parameters.json

Det här kommandot distribuerar mallen till resursgruppen **cliRG** i Azure-stacken POC standardplatsen.

## <a name="validate-template-deployment"></a>Validera malldistribution
Kontots resurs grupp och lagring, Använd följande kommandon:

    azure group list

    azure storage account list




