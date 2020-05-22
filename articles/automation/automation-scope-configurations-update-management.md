---
title: Arbeta med scope-konfigurationer för Azure Automation Uppdateringshantering
description: Den här artikeln beskriver hur du arbetar med scope-konfigurationer när du använder Uppdateringshantering.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 95563e816fa4f0931e547b6bd163dce23c338a44
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2020
ms.locfileid: "83749125"
---
# <a name="work-with-scope-configurations-for-update-management"></a>Arbeta med scope-konfigurationer för Uppdateringshantering

Den här artikeln beskriver hur du kan arbeta med scope-konfigurationer när du använder funktionen [uppdateringshantering](automation-update-management.md) på virtuella datorer. 

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="check-the-scope-configuration"></a><a name="scope-configuration"></a>Kontrol lera omfattnings konfigurationen

Uppdateringshantering använder en omfattnings konfiguration i arbets ytan Log Analytics för att rikta in de datorer som ska aktive ras för funktionen. Omfattnings konfigurationen är en grupp med en eller flera sparade sökningar som används för att begränsa omfånget för funktionen till vissa datorer. För att få åtkomst till scope-konfigurationerna:

1. I Automation-kontot under **relaterade resurser**väljer du **arbets yta**. 

2. Välj arbets ytan under **data källor för arbets yta**och välj **omfång**.

3. Om den valda arbets ytan inte har Uppdateringshantering funktionen aktive rad, skapas `MicrosoftDefaultScopeConfig-Updates` omfattnings konfigurationen. 

4. Om den valda arbets ytan redan har funktionen aktive rad, distribueras den inte om, och omfattnings konfigurationen läggs inte till i den. 

5. Välj ellipsen i någon av konfigurationerna för omfattningar och klicka sedan på **Redigera**. 

6. I redigerings fönstret väljer du **Välj dator grupper**. I fönstret dator grupper visas sparade sökningar som används för att skapa omfattnings konfigurationen.

## <a name="view-a-saved-search"></a>Visa en sparad sökning

När en dator läggs till Uppdateringshantering läggs den också till i en sparad sökning i din arbets yta. Den sparade sökningen är en fråga som innehåller mål datorerna.

1. Navigera till din Log Analytics arbets yta och välj **sparade sökningar** under **Allmänt**. Den sparade sökningen som används av Uppdateringshantering är:

|Name     |Kategori  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     | Uppdateringar        | Updates__MicrosoftDefaultComputerGroup         |

2. Välj den sparade sökningen för att visa frågan som används för att fylla i gruppen. Följande bild visar frågan och dess resultat:

    ![Sparade sökningar](media/automation-scope-configurations-update-management/logsearch.png)

## <a name="next-steps"></a>Nästa steg

* Allmän information om funktionen finns i [uppdateringshantering översikt](automation-update-management.md).
* Information om hur du arbetar med funktionen finns i [Hantera uppdateringar och korrigeringar för dina virtuella Azure-datorer](automation-tutorial-update-management.md).
* Om du vill använda ett Automation-konto för att aktivera funktionen, se [aktivera uppdateringshantering från ett Automation-konto](automation-onboard-solutions-from-automation-account.md).
* Om du vill bläddra i Azure Portal för att aktivera funktionen, se [aktivera uppdateringshantering från Azure Portal](automation-onboard-solutions-from-browse.md).
* Om du vill använda en Runbook för att aktivera funktionen, se [aktivera uppdateringshantering från en Runbook](automation-onboard-solutions.md).
* Om du vill använda en virtuell Azure-dator för att aktivera funktionen, se [aktivera uppdateringshantering från en virtuell Azure-dator](automation-onboard-solutions-from-vm.md).
* Information om hur du felsöker funktions fel finns i [felsöka uppdateringshantering problem](troubleshoot/update-management.md).
* Fel sökning av problem med Windows Update-agenten finns i [Felsöka problem med Windows Update-agenten](troubleshoot/update-agent-issues.md).
* Information om hur du felsöker problem med Linux-uppdaterings agent finns i [Felsöka problem med Linux Update Agent](troubleshoot/update-agent-issues-linux.md).