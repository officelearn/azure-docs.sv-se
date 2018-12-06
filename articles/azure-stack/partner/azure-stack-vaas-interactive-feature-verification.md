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
ms.date: 11/19/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: dc0c6a37e11574cef857a7af773a9d90bea57357
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52972073"
---
# <a name="interactive-feature-verification-testing"></a>Interaktiva funktionen verifiering testning  

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Du kan använda interaktiva funktionen verifiering testning framework för att begära tester för ditt system. Microsoft använder ramverket för att förbereda tester som kräver manuell interaktiva åtgärder när du begär ett test. Microsoft kan använda ramverket för att länka samman flera fristående automatiserade tester.

Den här artikeln beskriver ett enkelt scenario för manuell. Testet kontrollerar ersätta en disk i Azure Stack. Ramverket samlar diagnostikloggar i varje steg. Du kan felsöka problem som du hittar dem. Ramverket också tillåter delning av loggar som producerats av andra verktyg eller processer och gör att du kan ge feedback på scenariot.

## <a name="overview-of-a-test-pass"></a>Översikt över en testet

Ett test för diskbyte är ett vanligt scenario. I det här exemplet har testet sju steg:

1.  Skapa en ny **testet** arbetsflöde.
2.  Välj den **Disk identifiering Test**.
3.  Starta testet.
4.  Välj åtgärderna i interaktiva verifiering scenario.
5.  Kontrollera resultatet av scenariot.
6.  Skicka testresultatet till Microsoft.
7.  Kontrollera status på portalen VaaS.

## <a name="create-a-new-test-pass"></a>Skapa en ny testet

1.  Navigera till den [verifiering för Azure Stack portal](https://www.azurestackvalidation.com) och logga in.

2.  Skapa en ny lösning eller välj en befintlig.

3.  Välj **starta** på den **testet** panelen.

    ![Alternativ text](media/azure-stack-vaas-interactive-feature-verification/image1.png)

4.  Ange ett namn för den **testet** arbetsflöde.

5.  Ange miljö och gemensamma testparametrar genom att följa anvisningarna i den [arbetsflödets gemensamma parametrar för Azure Stack-verifiering som en tjänst](azure-stack-vaas-parameters.md) artikeln.

6.  Diagnostik-anslutningssträngen måste följa det format som anges i den [parametrarna för Webbtestet](azure-stack-vaas-parameters.md#test-parameters) i avsnittet den [gemensamma arbetsflödesparametrar](azure-stack-vaas-parameters.md) artikeln.

7.  Ange de obligatoriska parametrarna för testet.

8.  Välj agent att köra den interaktiva test-körningen.

> [!Note]  
> Domain admin-användare och lösenord måste anges för disk identifiering interaktiva funktionen verifieringstest.

![Alternativ text](media/azure-stack-vaas-interactive-feature-verification/image2.png)

## <a name="select-the-test"></a>Välj testet

1.  Välj **Disk identifiering Test\<version >**.

    > [!Note]  
    > Versionen av testet kommer räkna upp när förbättringar av test-säkerheter görs. Den senaste versionen ska alltid användas om inte Microsoft anger annars.

    ![Alternativ text](media/azure-stack-vaas-interactive-feature-verification/image4.png)

2.  Ange domain admin-användare och lösenord genom att välja **redigera**.

3.  Välj den annan lämplig testning körning agent/DVM starta testet i.

    ![Alternativ text](media/azure-stack-vaas-interactive-feature-verification/image5.png)

4.  Välj **skicka** att starta testet.

![Alternativ text](media/azure-stack-vaas-interactive-feature-verification/image6.png)

## <a name="start-the-test"></a>Starta testet

Disk identifiering Test anvisningarna visar på den dator som kör agenten VaaS. Detta är vanligtvis DVM eller Jumpbox för Azure Stack-instansen.

![Alternativ text](media/azure-stack-vaas-interactive-feature-verification/image8.png)

## <a name="choose-the-actions"></a>Välj åtgärderna

1.  Följ den **dokumentation** och **verifiering** länkar till instruktioner från Microsoft om hur du utför det här scenariot.

    ![Alternativ text](media/azure-stack-vaas-interactive-feature-verification/image9.png)

2.  Välj **Nästa**.

    ![Alternativ text](media/azure-stack-vaas-interactive-feature-verification/image10.png)

3.  Följ anvisningarna för att köra skriptet precheck.

    ![Alternativ text](media/azure-stack-vaas-interactive-feature-verification/image11.png)

4.  När skriptet precheck har slutförts, köra manuell scenariot (diskbyte) enligt den **dokumentation** och **verifiering** länkar från den **Information**fliken.

    ![Alternativ text](media/azure-stack-vaas-interactive-feature-verification/image12.png)

5.  Stäng inte dialogrutan medan du utför manuella scenariot.

6.  När du är klar med att utföra manuell scenariot, följ instruktionerna för att köra skriptet efter kontrollen.

    ![Alternativ text](media/azure-stack-vaas-interactive-feature-verification/image13.png)

7.  När åtgärden har slutförts av manuell scenariot (diskbyte), Välj **nästa**.

    ![Alternativ text](media/azure-stack-vaas-interactive-feature-verification/image14.png)

> [!Important]  
> Om du stänger fönstret avbryts testet innan det är klart.

## <a name="check-the-status"></a>Kontrollera status

1.  När testet är klart, blir du ombedd att lämna feedback.

    ![Alternativ text](media/azure-stack-vaas-interactive-feature-verification/image15.png)

2.  Dessa frågor hjälper Microsoft att bedöma lyckades pris- och jobbpubliceringsaktiviteter kvaliteten på scenariot.

## <a name="send-the-test-result"></a>Skicka testresultat

1.  Bifoga alla loggfiler som du vill skicka till Microsoft.

    ![Alternativ text](media/azure-stack-vaas-interactive-feature-verification/image16.png)

2.  Acceptera överföringen EULA feedback.

3.  Välj **skicka** att skicka resultaten till Microsoft.

## <a name="next-steps"></a>Nästa steg

- [Övervaka och hantera tester i VaaS-portalen](azure-stack-vaas-monitor-test.md)
