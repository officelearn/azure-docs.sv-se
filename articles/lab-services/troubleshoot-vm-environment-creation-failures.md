---
title: Felsök VM och miljö skapas Azure DevTest Labs | Microsoft Docs
description: Lär dig hur du felsöker virtuell dator (VM) och miljö skapas fel i Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2019
ms.author: spelluru
ms.openlocfilehash: a653a785e99619c3e256613d6a4d2c7592f54c8c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60848511"
---
# <a name="troubleshoot-virtual-machine-vm-and-environment-creation-failures-in-azure-devtest-labs"></a>Felsökning av virtuell dator (VM) och fel för miljön när skapas i Azure DevTest Labs
DevTest Labs ger varningar om ett namn för datorn är ogiltigt eller om du är på väg att bryta mot en princip för labb. Ibland kan du se red `X` bredvid ditt labb status för virtuell dator eller miljö som meddelar dig om att något gick fel.  Den här artikeln innehåller några tips som du kan använda för att hitta det underliggande problemet och förhoppningsvis kan undvika problemet i framtiden.

## <a name="portal-notifications"></a>Portalaviseringarna
Om du använder Azure-portalen är den första platsen för att titta på den **meddelandepanelen**.  Meddelanden panelen, tillgängliga i det huvudsakliga kommandofältet genom att klicka på den **klockikonen**, talar du om labbet skapa en virtuell dator eller miljö lyckades eller inte.  Om ett fel uppstod, visas ett felmeddelande som är associerade med fel vid skapande. Informationen ger ofta ytterligare information för att hjälpa dig att lösa problemet. I följande exempel, skapa en virtuell dator misslyckades på grund av slut på kärnor. Detaljerat meddelande visar hur du åtgärdar problemet och begära en kvot för kärnor.

![Azure portal-meddelande](./media/troubleshoot-vm-environment-creation-failures/portal-notification.png)


## <a name="activity-logs"></a>Aktivitetsloggar
Titta på aktivitetsloggar om ett fel undersöker en stund efter ett försökt skapandet av din VM eller miljö. Det här avsnittet visar hur du hittar loggar för virtuella datorer och miljöer.

## <a name="activity-logs-for-virtual-machines"></a>Aktivitetsloggar för virtuella datorer

1. På startsidan för labbet, väljer du den virtuella datorn att starta den **VM** sidan.
2. På den **VM** sidan den **övervakning** avsnitt i den vänstra menyn och välj **aktivitetsloggen** kan se alla loggar som är associerade med den virtuella datorn.
3. Välj den åtgärd som misslyckades i loggen aktivitetsobjekt. Normalt den misslyckade åtgärden kallas `Write Virtualmachines`.
4. Växla till JSON-fliken i den högra rutan. Du kan se information i JSON-vy av loggen.

    ![Aktivitetsloggen för en virtuell dator](./media/troubleshoot-vm-environment-creation-failures/vm-activity-log.png)
5. Titta igenom JSON-loggen tills du hittar den `statusMessage` egenskapen. Det ger dig de viktigaste felmeddelande och mer detaljerad information om det är tillämpligt. Följande JSON är ett exempel för core citattecken har överskridits-fel som visas tidigare i den här artikeln.

    ```json
    "properties": {
        "statusCode": "Conflict",
        "statusMessage": "{\"status\":\"Failed\",\"error\":{\"code\":\"ResourceDeploymentFailure\",\"message\":\"The resource operation completed with terminal provisioning state 'Failed'.\",\"details\":[{\"code\":\"OperationNotAllowed\",\"message\":\"Operation results in exceeding quota limits of Core. Maximum allowed: 100, Current in use: 100, Additional requested: 8. Please read more about quota increase at http://aka.ms/corequotaincrease.\"}]}}",
    },
    ```

## <a name="activity-log-for-an-environment"></a>Aktivitetsloggen för en miljö

Följ dessa steg om du vill se aktivitetsloggen för att skapa en miljö:

1. På startsidan för labbet, väljer **konfiguration och principer** på den vänstra menyn.
2. på den **konfiguration och principer** väljer **aktivitetsloggar** på menyn.
3. Leta efter fel i aktivitetslistan i loggen och markera den.
4. Växla till JSON-fliken i den högra rutan, och leta efter den **statusMessage**.

    ![Miljö aktivitetsloggen](./media/troubleshoot-vm-environment-creation-failures/envirionment-activity-log.png)

## <a name="resource-manager-template-deployment-logs"></a>Distributionsloggar för Resource Manager-mall
Om din miljö eller virtuella datorn har skapats via automation, är det en sista ställe med information om felet. Det är distributionsloggen för Azure Resource Manager-mall. När en labb-resurs skapas via automation, görs den ofta via en Azure Resource Manager för malldistribution. Se[ https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates ](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates) för exemplet Azure Resource Manager-mallar som skapar DevTest Labs-resurser.

Följ dessa steg om du vill visa lab mall distributionsloggarna:

1. Starta sidan för resursgruppen där labbet finns.
2. Välj **distributioner** på den vänstra menyn under **inställningar**.
3. Leta efter distributioner med en felstatus och markera den.
4. På den **distribution** väljer **åtgärdsinformation** länk för den åtgärd som misslyckades.
5. Du ser information om den åtgärd som misslyckades i den **åtgärdsinformation** fönster.

## <a name="next-steps"></a>Nästa steg
Se [felsökning av fel i artefakter](devtest-lab-troubleshoot-artifact-failure.md)