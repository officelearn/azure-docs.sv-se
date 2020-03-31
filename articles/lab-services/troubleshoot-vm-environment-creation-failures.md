---
title: Felsöka vm- och miljöfel Azure DevTest Labs
description: Lär dig hur du felsöker fel på virtuella datorer (VM) och miljöskapande fel i Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 155a359608cf6d846578306545f5ce0b4003949c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76166336"
---
# <a name="troubleshoot-virtual-machine-vm-and-environment-creation-failures-in-azure-devtest-labs"></a>Felsöka fel på virtuella datorer (VM) och miljöskapande fel i Azure DevTest Labs
DevTest Labs ger dig varningar om ett datornamn är ogiltigt eller om du är på väg att bryta mot en labbpolicy. Ibland ser du `X` rött bredvid din labb-VM eller miljöstatus som informerar dig om att något gick fel.  Den här artikeln innehåller några knep som du kan använda för att hitta det underliggande problemet och förhoppningsvis undvika problemet i framtiden.

## <a name="portal-notifications"></a>Portal meddelanden
Om du använder Azure-portalen är det första stället att titta på **meddelandepanelen**.  Meddelandepanelen, som är tillgänglig i huvudkommandofältet genom att klicka på **klockikonen,** kommer att berätta om labbet VM eller miljöskapande lyckades eller inte.  Om det uppstod ett fel visas felmeddelandet som är associerat med skapandefelet. Informationen ger ofta ytterligare information som hjälper dig att lösa problemet. I följande exempel misslyckades skapandet av virtuella datorer på grund av på kärnor. Det detaljerade meddelandet visar hur du åtgärdar problemet och begär en kärnkvotsökning.

![Meddelande om Azure-portal](./media/troubleshoot-vm-environment-creation-failures/portal-notification.png)

### <a name="vm-in-corruption-state"></a>VM i korruptionstillstånd
Om du ser statusen för den virtuella datorn i labbet som **skadad**kan den underliggande virtuella datorn ha tagits bort från sidan **Virtuell dator** som användaren kan navigera till från sidan **Virtuella datorer** (inte från sidan DevTest Labs). Rensa ditt labb i DevTest Labs genom att ta bort den virtuella datorn från labbet. Återskapa sedan den virtuella datorn i labbet. 

![Vm i skadat tillstånd](./media/troubleshoot-vm-environment-creation-failures/vm-corrupted-state.png)



## <a name="activity-logs"></a>Aktivitetsloggar
Titta på aktivitetsloggar om du undersöker ett fel någon gång efter att ha försökt skapa din virtuella dator eller miljö. I det här avsnittet visas hur du hittar loggar för virtuella datorer och miljöer.

## <a name="activity-logs-for-virtual-machines"></a>Aktivitetsloggar för virtuella datorer

1. På startsidan för ditt labb väljer du den virtuella datorn för att starta sidan **Virtuell dator.**
2. På sidan **Virtuell dator,** i avsnittet **ÖVERVAKNING** på den vänstra menyn, väljer du **Aktivitetslogg** för att se alla loggar som är associerade med den virtuella datorn.
3. Markera den åtgärd som misslyckades i aktivitetsloggobjekten. Vanligtvis kallas `Write Virtualmachines`den misslyckade åtgärden .
4. Växla till fliken JSON i den högra rutan. Du ser detaljerna i JSON-vyn för loggen.

    ![Aktivitetslogg för en virtuell dator](./media/troubleshoot-vm-environment-creation-failures/vm-activity-log.png)
5. Titta igenom JSON-loggen `statusMessage` tills du hittar fastigheten. Det ger dig det viktigaste felmeddelandet och ytterligare detaljerad information, om tillämpligt. Följande JSON är ett exempel på det kärnadda överskred felet som tidigare sågs i den här artikeln.

    ```json
    "properties": {
        "statusCode": "Conflict",
        "statusMessage": "{\"status\":\"Failed\",\"error\":{\"code\":\"ResourceDeploymentFailure\",\"message\":\"The resource operation completed with terminal provisioning state 'Failed'.\",\"details\":[{\"code\":\"OperationNotAllowed\",\"message\":\"Operation results in exceeding quota limits of Core. Maximum allowed: 100, Current in use: 100, Additional requested: 8. Please read more about quota increase at https://aka.ms/corequotaincrease.\"}]}}",
    },
    ```

## <a name="activity-log-for-an-environment"></a>Aktivitetslogg för en miljö

Så här ser du aktivitetsloggen för att skapa en miljö:

1. På startsidan för ditt labb väljer du **Konfiguration och principer** på den vänstra menyn.
2. På sidan **Konfiguration och principer** väljer du **Aktivitetsloggar** på menyn.
3. Leta efter felet i aktivitetslistan i loggen och välj det.
4. I den högra rutan växlar du till fliken JSON och letar efter **statusMessage**.

    ![Aktivitetslogg för miljö](./media/troubleshoot-vm-environment-creation-failures/envirionment-activity-log.png)

## <a name="resource-manager-template-deployment-logs"></a>Distributionsloggar för Resurshanterarens mall
Om din miljö eller virtuella dator skapades genom automatisering finns det ett sista ställe där du kan titta på felinformation. Det är distributionsloggen för Azure Resource Manager-mallen. När en labbresurs skapas genom automatisering görs den ofta via en Azure Resource Manager-malldistribution. Se[https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates) för exempel på Azure Resource Manager-mallar som skapar DevTest Labs-resurser.

Så här ser du distributionsloggarna för labbmallen:

1. Starta sidan för resursgruppen där labbet finns.
2. Välj **Distributioner** på den vänstra menyn under **Inställningar**.
3. Leta efter distributioner med en misslyckad status och välj den.
4. På **sidan Distribution** väljer du länk **för åtgärdsinformation** för åtgärden som misslyckades.
5. Du ser information om åtgärden som misslyckades i fönstret **Driftinformation.**

## <a name="next-steps"></a>Nästa steg
Se [Felsöka artefaktfel](devtest-lab-troubleshoot-artifact-failure.md)
