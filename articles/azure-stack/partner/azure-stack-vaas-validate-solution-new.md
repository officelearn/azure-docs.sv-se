---
title: Verifiera en ny Azure Stack-lösning | Microsoft Docs
description: Lär dig hur du validerar en ny Azure Stack-lösning med verifiering som en tjänst.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: fb7b98a5307606f7335e76ada42d583b975730f5
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57759671"
---
# <a name="validate-a-new-azure-stack-solution"></a>Verifiera en ny Azure Stack-lösning

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Lär dig hur du använder den **lösning verifiering** arbetsflöde för att certifiera nya Azure Stack-lösningar.

En lösning med Azure Stack är en maskinvara struktur (BoM) som har gemensamt kommit överens om mellan Microsoft och partnern när du har uppfyllt kraven på logotypcertifiering i Windows Server. En lösning måste vara recertified när det har skett en ändring av maskinvara BoM. För ytterligare frågor om hur du certifiera om lösningar, kontakta teamet på [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com).

Kör lösningen valideringsarbetsflödet två gånger för att certifiera din lösning. Kör den en gång för den *minimalt* konfiguration som stöds. Kör den en gång för den *högst* konfiguration som stöds. Microsoft certifierar lösningen om båda konfigurationerna klara alla tester.

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

## <a name="create-a-solution-validation-workflow"></a>Skapa en lösning valideringsarbetsflödet

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-solution](includes/azure-stack-vaas-workflow-step_select-solution.md)]

3. Välj **starta** på den **lösning verifieringar** panelen.

    ![Lösningen verifieringar arbetsflöde panel](media/tile_validation-solution.png)

4. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]

5. Välj den **lösningskonfigurationen**.
    - **Minsta**: lösningen har konfigurerats med det minsta tillåtna antalet noder.
    - **Maximal**: lösningen har konfigurerats med det högsta tillåtna antalet noder.
6. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]

    ![Lösningen verifieringsinformation](media/workflow_validation-solution_info.png)

7. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]

    > [!NOTE]
    > Miljö-parametrar kan inte ändras när du har skapat ett arbetsflöde.

8. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]
9. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]
    Du omdirigeras till sammanfattningssidan tester.

## <a name="run-solution-validation-tests"></a>Köra verifieringstester för lösningen

I den **lösning verifiering testar sammanfattning** sidan visas en lista över de tester som krävs för att slutföra verifieringen.

I arbetsflöden verifiering **schemaläggning** ett test använder de gemensamma parametrarna för arbetsflödet på servernivå som du angav under arbetsflödet skapats (se [arbetsflödets gemensamma parametrar för Azure Stack-verifiering som en tjänst](azure-stack-vaas-parameters.md)). Om någon av test parametervärden blir ogiltiga måste du resupply dem som finns beskrivet i [ändra arbetsflödesparametrar](azure-stack-vaas-monitor-test.md#change-workflow-parameters).

> [!NOTE]
> Schemalägga ett verifieringstest över en befintlig instans skapas en ny instans i stället för den gamla instansen i portalen. Loggar för den gamla instansen kommer att hållas kvar, men är inte tillgängliga från portalen.  
När ett test har slutförts, den **schema** åtgärden inaktiveras.

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](includes/azure-stack-vaas-workflow-step_select-agent.md)]

2. Välj följande test:
    - Cloud Simulation Engine
    - Compute SDK operativa Suite
    - Disk identifiering Test
    - KeyVault Extension SDK Operational Suite
    - KeyVault SDK Operational Suite
    - Network SDK Operational Suite
    - Storage-konto SDK operativa Suite

3. Välj **schema** från snabbmenyn för att öppna en kommandotolk för att schemalägga test-instans.

4. Granska de test-parametrarna och välj sedan **skicka** att schemalägga testet för körning.

![Schema för lösningen verifieringstest](media/workflow_validation-solution_schedule-test.png)

## <a name="next-steps"></a>Nästa steg

- [Övervaka och hantera tester i VaaS-portalen](azure-stack-vaas-monitor-test.md)