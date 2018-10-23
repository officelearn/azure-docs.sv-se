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
ms.date: 10/19/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 777609b89bc08cd61489d2c3a3669ec07ccbc372
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2018
ms.locfileid: "49647025"
---
# <a name="validate-a-new-azure-stack-solution"></a>Verifiera en ny Azure Stack-lösning

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Lär dig hur du använder den **lösning verifiering** arbetsflöde för att certifiera nya Azure Stack-lösningar.

En lösning med Azure Stack är en maskinvara struktur (BoM) som har gemensamt kommit överens om mellan Microsoft och partnern när du har uppfyllt kraven på logotypcertifiering i Windows Server. En lösning måste vara recertified när det har skett en ändring av maskinvara BoM. För ytterligare frågor om hur du certifiera om lösningar, kontakta teamet på [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com).

Kör lösningen valideringsarbetsflödet två gånger för att certifiera din lösning. Kör den en gång för den *minimalt* konfiguration som stöds. Kör den en gång för den *högst* konfiguration som stöds. Microsoft certifierar lösningen om båda konfigurationerna klara alla tester.

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

## <a name="create-a-solution-validation-workflow"></a>Skapa en lösning valideringsarbetsflödet

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-solution](includes/azure-stack-vaas-workflow-step_select-solution.md)]
2. Välj **starta** på den **lösning verifieringar** panelen.

    ![Lösningen verifieringar arbetsflöde panel](media/tile_validation-solution.png)

3. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]
4. Välj den **lösningskonfigurationen**.
    - **Minsta**: lösningen har konfigurerats med det minsta tillåtna antalet noder.
    - **Maximal**: lösningen har konfigurerats med det högsta tillåtna antalet noder.
5. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]

    ![Lösningen verifieringsinformation](media/workflow_validation-solution_info.png)

6. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]

    > [!NOTE]
    > Miljö-parametrar kan inte ändras när du har skapat ett arbetsflöde.

7. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]
8. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]
    Du omdirigeras till sammanfattningssidan tester.

## <a name="execute-solution-validation-tests"></a>Köra verifieringstester för lösningen

I den **lösning tester verifieringssammanfattning** sidan visas en lista över de tester som krävs för att slutföra verifieringen.

[!INCLUDE [azure-stack-vaas-workflow-validation-section_schedule](includes/azure-stack-vaas-workflow-validation-section_schedule.md)]

![Schema för lösningen verifieringstest](media/workflow_validation-solution_schedule-test.png)

## <a name="next-steps"></a>Nästa steg

- [Övervaka och hantera tester i VaaS-portalen](azure-stack-vaas-monitor-test.md)