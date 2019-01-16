---
title: Använda verifieringen som en tjänst för Azure Stack-portalen för att schemalägga första testet | Microsoft Docs
description: Använda verifieringen som en tjänst för Azure Stack-portalen för att schemalägga första testet.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: How to
ms.date: 11/26/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 0e9ad89e504ce1cb86daad48bec9ffe9423e2cf1
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2019
ms.locfileid: "54319553"
---
# <a name="scheduling-a-test"></a>Schemalägga ett test

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Schemalägga ett test i validering som en (VaaS)-tjänstportalen för din Azure Stack-lösning. En lösning för VaaS representerar en Azure Stack-lösning med en viss maskinvara struktur (BoM). Du kan schemalägga ett test för att kontrollera att maskinvaran kan köra Azure Stack.

Skapa arbetsflöde för ett test för att kontrollera din lösning. Ett arbetsflöde för VaaS fungerar inom kontexten för en VaaS-lösning. Representerar en uppsättning testserier som utnyttja funktionerna i Azure Stack-distributioner på din maskinvara. Lägg till miljön parametrar för din lösning och välj ett eller flera test ska köras på din lösning.

Testet arbetsflödet kan användas för att köra alla test som tillhandahålls av VaaS, inklusive test från verifiering-arbetsflöden beaktas inte resultat från testet arbetsflödet *officiella*. Information om hur du officiellt verifiering arbetsflöden finns i [arbetsflöden](azure-stack-vaas-key-concepts.md#workflows).

## <a name="prerequisites"></a>Förutsättningar

Innan du följer den här snabbstarten ska du slutföra följande objekt:

- [Ställ in din validering som en tjänstresurser](azure-stack-vaas-set-up-resources.md)
- [Distribuera lokal agent](azure-stack-vaas-local-agent.md) (krävs)
- [Verifiering som en tjänst viktiga begrepp](azure-stack-vaas-key-concepts.md) (krävs)

## <a name="start-a-workflow"></a>Starta ett arbetsflöde

![Logga in på VaaS-portal](media/vaas_portalsignin.png)

Logga in på portalen, Välj eller skapa en lösning och välj sedan lösningen.

1. Logga in på den [VaaS portal](https://azurestackvalidation.com).
2. Ange namnet på en befintlig lösning eller välj **ny lösning** att skapa en ny lösning. Anvisningar finns i [skapar en lösning i portalen VaaS](azure-stack-vaas-key-concepts.md#create-a-solution-in-the-vaas-portal).
3. Välj **starta** på den **tester** panelen.

## <a name="specify-parameters"></a>Ange parametrar

![Alternativ Text](media/vaas_test_pass_parameters.png)

Definiera arbetsflödet för din lösning. Arbetsflödet har de steg i processen som används för att testa din lösning.

1. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]
2. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]
3. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]
4. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]
5. Välj **nästa** att välja tester för att schemalägga.

## <a name="select-tests-to-run"></a>Välj tester för att köra

Välj de tester som du vill köra i arbetsflödet.

1. Välj de tester som du vill köra i arbetsflödet.

    Om du vill åsidosätta de gemensamma parametrarna för (det vill säga de parametrar som ges i föregående avsnitt) för alla test väljer på det **redigera** länken nästa för att ange nya värden.

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](includes/azure-stack-vaas-workflow-step_select-agent.md)]
1. Välj **nästa** att granska arbetsflödet.

## <a name="review-and-submit"></a>Granska och skicka

Granska, skapa och sedan schemalägger du arbetsflödet.

1. Granska informationen som visas.

    Tjänsterna skapar ditt arbetsflöde med den angivna informationen och kommer att schemaläggas med valda testerna.

    Om något visas felaktigt, kan du använda den **föregående** knappar för att gå till ett tidigare avsnitt.

1. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]

## <a name="next-steps"></a>Nästa steg

- [Övervaka och hantera tester i VaaS-portalen](azure-stack-vaas-monitor-test.md)
