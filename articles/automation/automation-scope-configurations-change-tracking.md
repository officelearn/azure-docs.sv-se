---
title: Arbeta med scope-konfigurationer för Azure Automation Ändringsspårning och inventering
description: Den här artikeln beskriver hur du arbetar med scope-konfigurationer när du använder Ändringsspårning och inventering.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: c30bd8a3bb4fa1085e56dd93c66c016c3612e352
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2020
ms.locfileid: "83749139"
---
# <a name="work-with-scope-configurations-for-change-tracking-and-inventory"></a>Arbeta med scope-konfigurationer för Ändringsspårning och inventering

Den här artikeln beskriver hur du kan arbeta med scope-konfigurationer när du aktiverar funktionen [uppdateringshantering](automation-update-management.md) på virtuella datorer. 

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="check-the-scope-configuration"></a><a name="scope-configuration"></a>Kontrol lera omfattnings konfigurationen

Uppdateringshantering använder en omfattnings konfiguration i arbets ytan Log Analytics för att rikta in de datorer som ska aktive ras för Uppdateringshantering. Omfattnings konfigurationen är en grupp med en eller flera sparade sökningar som används för att begränsa omfånget för funktionen till vissa datorer. För att få åtkomst till scope-konfigurationerna:

1. I Automation-kontot under **relaterade resurser**väljer du **arbets yta**. 

2. Välj arbets ytan under **data källor för arbets yta**och välj **omfång**.

3. Om den valda arbets ytan inte har Uppdateringshantering funktionen aktive rad, skapas `MicrosoftDefaultScopeConfig-ChangeTracking` omfattnings konfigurationen. 

4. Om den valda arbets ytan redan har funktionen aktive rad, distribueras den inte om, och omfattnings konfigurationen läggs inte till i den. 

5. Välj ellipsen i någon av konfigurationerna för omfattningar och klicka sedan på **Redigera**. 

6. I redigerings fönstret väljer du **Välj dator grupper**. I fönstret dator grupper visas sparade sökningar som används för att skapa omfattnings konfigurationen.

## <a name="view-a-saved-search"></a>Visa en sparad sökning

När en dator läggs till Ändringsspårning och inventeringen läggs den också till i en sparad sökning i din arbets yta. Den sparade sökningen är en fråga som innehåller mål datorerna.

1. Navigera till din Log Analytics arbets yta och välj **sparade sökningar** under **Allmänt**. Den sparade sökningen som används av Uppdateringshantering är:

    |Name     |Kategori  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |

2. Välj den sparade sökningen för att visa frågan som används för att fylla i gruppen. Följande bild visar frågan och dess resultat:

    ![Sparade sökningar](media/automation-scope-configurations-change-tracking/logsearch.png)

## <a name="next-steps"></a>Nästa steg

* Allmän information om funktionen finns i [ändringsspårning och inventerings översikt](change-tracking.md).
* Information om hur du arbetar med funktionen finns i [hantera ändringsspårning och inventering](change-tracking-file-contents.md).
* Om du vill använda ett Automation-konto för att aktivera funktionen, se [aktivera ändringsspårning och inventering från ett Automation-konto](automation-enable-changes-from-auto-acct.md).
* Om du vill använda Azure Portal för att aktivera funktionen, se [aktivera ändringsspårning och inventering från Azure Portal](automation-enable-changes-from-browse.md).
* Om du vill använda en Runbook för att aktivera funktionen, se [aktivera ändringsspårning och inventering från en Runbook](automation-enable-changes-from-runbook.md).
* Om du vill använda en virtuell Azure-dator för att aktivera funktionen, se [aktivera ändringsspårning och inventering från en virtuell Azure-dator](automation-enable-changes-from-vm.md).
* Information om hur du felsöker problem med ändrings spårning och inventering på en virtuell dator finns i [Felsöka ändringar på en virtuell Azure-dator](automation-tutorial-troubleshoot-changes.md).
* Information om hur du felsöker funktions fel finns i [felsöka ändringsspårning-och inventerings problem](troubleshoot/change-tracking.md).