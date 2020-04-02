---
title: Hantera språktillägg i Azure Data Explorer-klustret.
description: Använd språktillägg för att integrera andra språk i dina KQL-frågor i Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: orhasban
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 88c3047afebf3d31b50145c6df47776fdc1ddcd4
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80522477"
---
# <a name="manage-language-extensions-in-your-azure-data-explorer-cluster-preview"></a>Hantera språktillägg i Azure Data Explorer-klustret (förhandsversion)

Med funktionen språktillägg kan du använda plugin-program för språktillägg för att integrera andra språk i dina Azure Data Explorer KQL-frågor. När du kör en användardefinierad funktion (UDF) med ett relevant skript får skriptet tabelldata som indata och förväntas producera tabellutdata. Plugin-programmets körning finns i en [sandlåda](/azure/kusto/concepts/sandboxes), en isolerad och säker miljö som körs på klustrets noder. I den här artikeln hanterar du plugin-programmet för språktillägg i Azure Data Explorer-klustret i Azure-portalen.

> [!NOTE]
> Azure Data Explorer-språktillägg som för närvarande stöds är Python och R.

## <a name="prerequisites"></a>Krav

* Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* Skapa [ett Azure Data Explorer-kluster och -databas](create-cluster-database-portal.md).

## <a name="enable-language-extensions-on-your-cluster"></a>Aktivera språktillägg i klustret

> [!WARNING]
> Läs [begränsningarna](#limitations) innan du aktiverar ett språktillägg.

Gör så här för att aktivera språktillägg i klustret:

1. Gå till Azure Data Explorer-klustret i Azure-portalen. 
1. Välj **Konfigurationer**i **Inställningar**. 
1. Välj **På** i fönstret **Konfigurationer** för att aktivera ett språktillägg.
1. Välj **Spara**.
 
    ![språktillägg på](media/language-extensions/configurations-enable-extension.png)

> [!NOTE]
> Det kan ta några minuter att aktivera språktilläggsprocessen. Under den tiden pausas klustret.
 
## <a name="run-language-extension-integrated-queries"></a>Köra språktilläggsintegrerade frågor

* Lär dig hur du [kör Python-integrerade KQL-frågor](/azure/kusto/query/pythonplugin).
* Lär dig hur du [kör R-integrerade KQL-frågor](/azure/kusto/query/rplugin). 

## <a name="disable-language-extensions-on-your-cluster"></a>Inaktivera språktillägg i klustret

> [!NOTE]
> Det kan ta några minuter att inaktivera språktillägg.

Gör så här för att inaktivera språktillägg i klustret:

1. Gå till Azure Data Explorer-klustret i Azure-portalen. 
1. Välj **Konfigurationer**i **Inställningar**. 
1. I fönstret **Konfigurationer** väljer du **Av** för att inaktivera ett språktillägg.
1. Välj **Spara**.

    ![Språktillägget är inaktiverat](media/language-extensions/configurations-disable-extension.png)

## <a name="limitations"></a>Begränsningar

* Funktionen för språktillägg stöder inte [diskkryptering](manage-cluster-security.md). 
* I begränsat till språktillägget för körning allokeras diskutrymme även om ingen fråga körs i det relevanta språkets omfattning.

