---
title: Begränsa Azure Automation Ändringsspårning och distributions omfång för inventering
description: Den här artikeln beskriver hur du arbetar med scope-konfigurationer för att begränsa omfånget för en Ändringsspårning och lager distribution.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 49655d11858086b16099a1864fd4d2dc5988f02a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84117434"
---
# <a name="limit-change-tracking-and-inventory-deployment-scope"></a>Begränsa Ändringsspårning och distributions omfång för inventering

Den här artikeln beskriver hur du arbetar med scope-konfigurationer när du använder funktionen [ändringsspårning och inventering](change-tracking.md) för att distribuera ändringar i dina virtuella datorer. Mer information finns i [mål övervakning av lösningar i Azure Monitor (för hands version)](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting). 

## <a name="about-scope-configurations"></a>Om scope-konfigurationer

En omfattnings konfiguration är en grupp med en eller flera sparade sökningar (frågor) som används för att begränsa omfånget för Ändringsspårning och inventering till vissa datorer. Omfattnings konfigurationen används inom Log Analytics arbets ytan för att rikta in de datorer som ska aktive ras. När du lägger till en dator i ändringarna från funktionen läggs datorn också till i en sparad sökning i arbets ytan.

## <a name="set-the-scope-limit"></a>Ange omfattnings gräns

Begränsa omfånget för din Ändringsspårning-och inventerings distribution:

1. I ditt Automation-konto väljer du **länkad arbets yta** under **relaterade resurser**.

2. Klicka på **gå till arbets yta**.

3. Välj **scope-konfigurationer (för hands version)** under **arbets ytans data källor**.

4. Välj ellipsen till höger om `MicrosoftDefaultScopeConfig-ChangeTracking` omfattnings konfigurationen och klicka på **Redigera**. 

5. I redigerings fönstret väljer du **Välj dator grupper**. I fönstret dator grupper visas sparade sökningar som används för att skapa omfattnings konfigurationen. Den sparade sökningen som används av Ändringsspårning och inventering är:

    |Name     |Kategori  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |

6. Välj den sparade sökningen för att visa och redigera frågan som används för att fylla i gruppen. Följande bild visar frågan och dess resultat:

    ![Sparade sökningar](media/automation-scope-configurations-change-tracking/logsearch.png)

## <a name="next-steps"></a>Nästa steg

* Information om hur du arbetar med Ändringsspårning och lager finns i [hantera ändringsspårning och inventering](change-tracking-file-contents.md).
* Information om hur du felsöker allmänna funktions problem finns i [felsöka ändringsspårning-och inventerings problem](troubleshoot/change-tracking.md).