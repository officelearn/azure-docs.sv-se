---
title: Interaktiva funktionen verifiering testning i Azure Stack-validering som en tjänst | Microsoft Docs
description: Lär dig hur du skapar interaktiva funktionen verifieringstest för Azure Stack med verifiering som en tjänst.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: d3db8ea8639f73f3522ddaa358195e7c9ef2f9a9
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57766010"
---
# <a name="interactive-feature-verification-testing"></a>Interaktiva funktionen verifiering testning  

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Du kan använda interaktiva funktionen verifiering testning framework för att begära tester för ditt system. Microsoft använder ramverket för att förbereda tester som kräver manuell interaktiva åtgärder när du begär ett test. Microsoft kan använda ramverket för att länka samman flera fristående automatiserade tester.

Den här artikeln beskriver ett enkelt scenario för manuell. Testet kontrollerar ersätta en disk i Azure Stack. Ramverket samlar diagnostikloggar i varje steg. Du kan felsöka problem som du hittar dem. Ramverket också tillåter delning av loggar som producerats av andra verktyg eller processer och gör att du kan ge feedback på scenariot.

> [!Important]  
> Den här artikeln refererar till stegen för att utföra identifiering av Disk. Detta är bara en demonstration eftersom inga resultat som samlats in från testet arbetsflödet inte kan användas för den nya lösningen verifiering.

## <a name="overview-of-interactive-testing"></a>Översikt över interaktiva testning

Ett test för diskbyte är ett vanligt scenario. Testet har fem stegen i det här exemplet:

1. Skapa en ny **testet** arbetsflöde.
2. Välj den **Disk identifiering Test**.
3. Slutför den manuella åtgärden när du tillfrågas.
4. Kontrollera resultatet av scenariot.
5. Skicka testresultatet till Microsoft.

## <a name="create-a-new-test-pass"></a>Skapa en ny testet

Om du inte har en befintlig testversion av skicka tillgängliga följer du anvisningarna för [schemaläggning av ett test](azure-stack-vaas-schedule-test-pass.md).

## <a name="schedule-the-test"></a>Schemalägga testet

1. Välj **Disk identifiering Test**.

    > [!Note]  
    > Versionen av testet kommer räkna upp när förbättringar av test-säkerheter görs. Den senaste versionen ska alltid användas om inte Microsoft anger annars.

    ![Alternativ text](media/azure-stack-vaas-interactive-feature-verification/image4.png)

1. Ange domänen administratörens användarnamn och lösenord genom att välja **redigera**.

1. Välj den annan lämplig testning körning agent/DVM starta testet i.

    ![Alternativ text](media/azure-stack-vaas-interactive-feature-verification/image5.png)

1. Välj **skicka** att starta testet.

    ![Alternativ text](media/azure-stack-vaas-interactive-feature-verification/image6.png)

1. Komma åt Användargränssnittet för interaktiva testet från agenten valde i föregående steg.

    ![Alternativ text](media/azure-stack-vaas-interactive-feature-verification/image8.png)

1. Följ den **dokumentation** och **verifiering** länkar till instruktioner från Microsoft om hur du utför det här scenariot.

    ![Alternativ text](media/azure-stack-vaas-interactive-feature-verification/image9.png)

1. Välj **Nästa**.

    ![Alternativ text](media/azure-stack-vaas-interactive-feature-verification/image10.png)

1. Följ anvisningarna för att köra skriptet precheck.

    ![Alternativ text](media/azure-stack-vaas-interactive-feature-verification/image11.png)

1. När skriptet precheck har slutförts, köra manuell scenariot (diskbyte) enligt den **dokumentation** och **verifiering** länkar från den **Information**fliken.

    ![Alternativ text](media/azure-stack-vaas-interactive-feature-verification/image12.png)

    > [!Important]  
    > Stäng inte dialogrutan medan du utför manuella scenariot.

1. När du är klar med att utföra manuell scenariot, följ instruktionerna för att köra skriptet efter kontrollen.

    ![Alternativ text](media/azure-stack-vaas-interactive-feature-verification/image13.png)

1. När åtgärden har slutförts av manuell scenariot (diskbyte), Välj **nästa**.

    ![Alternativ text](media/azure-stack-vaas-interactive-feature-verification/image14.png)

    > [!Important]  
    > Om du stänger fönstret avbryts testet innan det är klart.

1. Ge feedback för test-upplevelse. Dessa frågor hjälper Microsoft att bedöma lyckades pris- och jobbpubliceringsaktiviteter kvaliteten på scenariot.

    ![Alternativ text](media/azure-stack-vaas-interactive-feature-verification/image15.png)

1. Bifoga alla loggfiler som du vill skicka till Microsoft.

    ![Alternativ text](media/azure-stack-vaas-interactive-feature-verification/image16.png)

1. Acceptera överföringen EULA feedback.

1. Välj **skicka** att skicka resultaten till Microsoft.

## <a name="next-steps"></a>Nästa steg

- [Övervaka och hantera tester i VaaS-portalen](azure-stack-vaas-monitor-test.md)
