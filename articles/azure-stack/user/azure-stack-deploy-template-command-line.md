---
title: Distribuera mallar med kommandoraden i Azure-stacken | Microsoft Docs
description: Lär dig hur du använder plattformsoberoende kommandoradsgränssnittet (CLI) för att distribuera mallar till Azure-stacken.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 9584177f-4af3-4834-864d-930b09ae0995
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 761e09889a230642c42697b6bc4f96dc32fe03a0
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
ms.locfileid: "30316203"
---
# <a name="deploy-templates-in-azure-stack-using-the-command-line"></a>Distribuera mallar i Azure-stacken med hjälp av kommandoraden

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Använda kommandoraden för att distribuera Azure Resource Manager-mallar i Azure-stacken Development Kit. Azure Resource Manager-mallar distribuera och Tillhandahåll alla resurser i programmet i en enda, samordnad åtgärd.

## <a name="before-you-begin"></a>Innan du börjar
 - [Installera och ansluta](azure-stack-version-profiles-azurecli2.md) till Azure-stacken med Azure CLI
 - Hämta filerna *azuredeploy.json* och *azuredeploy.parameters.json* från den [skapa storage-konto exempelmall](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-create-storage-account).
 
## <a name="deploy-template"></a>Distribuera mallen
Navigera till mappen där filerna har hämtats och kör följande kommando för att distribuera mallen:

    azure group create "cliRG" "local" –f azuredeploy.json –d "testDeploy" –e azuredeploy.parameters.json

Det här kommandot distribuerar mallen till resursgruppen **cliRG** i Azure-stacken POC standardplatsen.

## <a name="validate-template-deployment"></a>Validera malldistribution
Kontots resurs grupp och lagring, Använd följande kommandon:

    azure group list

    azure storage account list




