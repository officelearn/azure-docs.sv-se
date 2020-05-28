---
title: Begränsa Azure Automation Uppdateringshantering distributions omfång
description: Den här artikeln beskriver hur du använder scope-konfigurationer för att begränsa omfånget för en Uppdateringshantering-distribution.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 72065b388f348da1d268f875a10d5b13d2f8cf3b
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84117403"
---
# <a name="limit-update-management-deployment-scope"></a>Begränsa Uppdateringshantering distributions omfång

Den här artikeln beskriver hur du arbetar med scope-konfigurationer när du använder funktionen [uppdateringshantering](automation-update-management.md) för att distribuera uppdateringar och korrigeringar till dina virtuella datorer. Mer information finns i [mål övervakning av lösningar i Azure Monitor (för hands version)](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting). 

## <a name="about-scope-configurations"></a>Om scope-konfigurationer

En omfattnings konfiguration är en grupp med en eller flera sparade sökningar (frågor) som används för att begränsa omfånget för Uppdateringshantering till vissa datorer. Omfattnings konfigurationen används inom Log Analytics arbets ytan för att rikta in de datorer som ska aktive ras. När du lägger till en dator för att ta emot uppdateringar från Uppdateringshantering läggs datorn också till i en sparad sökning i arbets ytan.

## <a name="set-the-scope-limit"></a>Ange omfattnings gräns

Begränsa omfånget för din Uppdateringshantering-distribution:

1. I ditt Automation-konto väljer du **länkad arbets yta** under **relaterade resurser**.

2. Klicka på **gå till arbets yta**.

3. Välj **scope-konfigurationer (för hands version)** under **arbets ytans data källor**.

4. Välj ellipsen till höger om `MicrosoftDefaultScopeConfig-Updates` omfattnings konfigurationen och klicka på **Redigera**. 

5. I redigerings fönstret expanderar du **Välj dator grupper**. I fönstret dator grupper visas sparade sökningar som används för att skapa omfattnings konfigurationen. Den sparade sökningen som används av Uppdateringshantering är:

    |Name     |Kategori  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     | Uppdateringar        | Updates__MicrosoftDefaultComputerGroup         |

6. Välj den sparade sökningen för att visa och redigera frågan som används för att fylla i gruppen. Följande bild visar frågan och dess resultat:

    ![Sparade sökningar](media/automation-scope-configurations-update-management/logsearch.png)

## <a name="next-steps"></a>Nästa steg

* Information om hur du arbetar med funktionen finns i [Hantera uppdateringar och korrigeringar för dina virtuella Azure-datorer](automation-tutorial-update-management.md).
* Information om hur du felsöker funktions fel finns i [felsöka uppdateringshantering problem](troubleshoot/update-management.md).
* Fel sökning av problem med Windows Update-agenten finns i [Felsöka problem med Windows Update-agenten](troubleshoot/update-agent-issues.md).
* Information om hur du felsöker problem med Linux-uppdaterings agent finns i [Felsöka problem med Linux Update Agent](troubleshoot/update-agent-issues-linux.md).