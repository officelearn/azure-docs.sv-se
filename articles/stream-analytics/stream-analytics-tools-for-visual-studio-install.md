---
title: Konfigurera Azure Stream Analytics verktyg för Visual Studio
description: Den här artikeln beskriver installations krav och hur du konfigurerar Azure Stream Analytics verktyg för Visual Studio.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/22/2018
ms.openlocfilehash: 447fa07953c15fe67b8a2e313fe9534164f47bbd
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2019
ms.locfileid: "71130501"
---
# <a name="install-azure-stream-analytics-tools-for-visual-studio"></a>Installera Azure Stream Analytics verktyg för Visual Studio

Visual Studio 2019 och Visual Studio 2017 stöder Azure Data Lake och Stream Analytics verktyg. Den här artikeln beskriver hur du installerar och avinstallerar verktygen.

Mer information om hur du använder verktygen finns i [snabb start: Skapa ett Azure Stream Analytics jobb med hjälp av Visual](stream-analytics-quick-create-vs.md)Studio.

## <a name="install"></a>Installera

Visual Studio Enterprise (Ultimate/Premium), Professional och community-utgåvor har stöd för verktygen. Express Edition och Visual Studio för Mac stöder dem inte.

Vi rekommenderar Visual Studio 2019.

### Installera för Visual Studio 2019 och 2017<a name="recommended-visual-studio-2019-and-2017"></a>

Azure Data Lake och Stream Analytics verktyg är en del av **Azure-utveckling** och **data lagring och bearbetning** av arbets belastningar. Aktivera antingen en av dessa två arbets belastningar under installationen. Om Visual Studio redan är installerat väljer du **verktyg** > **Hämta verktyg och funktioner** för att lägga till arbets belastningar.

Hämta [Visual studio 2019 (för hands version 2 eller senare) eller Visual Studio 2017 (15,3 eller senare)](https://www.visualstudio.com/) och följ anvisningarna för att installera.

Välj arbets belastningen **data lagring och bearbetning** som det visas:

![Arbets belastningen för data lagring och bearbetning är vald](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2019-install-01.png)

Välj arbets belastningen **Azure Development** som det visas:

![Arbets belastningen Azure Development är vald](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2019-install-02.png)

När du har lagt till arbets belastningen uppdaterar du verktygen. Den här proceduren refererar till Visual Studio 2019:

1. Välj **tillägg** > **Hantera tillägg**.

1. I **Hantera tillägg**väljer du **uppdateringar** och sedan **Azure Data Lake och Stream Analytics verktyg**.

1. Välj **Uppdatera** för att installera det senaste tillägget.

![Visual Studio-tillägg och uppdateringar](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-vs2019-extensions-updates.png)

### Installera för Visual Studio 2015 och 2013<a name="visual-studio-2015-2013"></a>

Visual Studio Enterprise (Ultimate/Premium), Professional och community-utgåvor har stöd för verktygen. Express-versionen stöder inte dem.

* Installera Visual Studio 2015 eller Visual Studio 2013 uppdatering 4.
* Installera Microsoft Azure SDK för .NET version 2.7.1 eller senare med hjälp av [installations programmet för webb plattformen](https://www.microsoft.com/web/downloads/platform.aspx).
* Installera [Microsoft Azure Data Lake och Stream Analytics verktyg för Visual Studio](https://www.microsoft.com/en-us/download/details.aspx?id=49504).

## Uppdatera<a name="visual-studio-2019-and-2017"></a><a name="visual-studio-2015-and-2013"></a>

För Visual Studio 2019 och Visual Studio 2017 visas en ny versions påminnelse som Visual Studio-meddelande.

För Visual Studio 2015 och Visual Studio 2013 söker verktygen automatiskt efter nya versioner. Följ anvisningarna för att installera den senaste versionen.

## <a name="uninstall"></a>Avinstallera

Du kan avinstallera Azure Data Lake-och Stream Analytics-verktyg. För Visual Studio 2019 eller Visual Studio 2017 väljer du **verktyg** > **Hämta verktyg och funktioner**. I **ändra**avmarkerar du **Azure Data Lake och Stream Analytics verktyg**. Den visas under arbets belastningen **data lagring och bearbetning** eller arbets belastningen **Azure Development** .

Om du vill avinstallera från Visual Studio 2015 eller Visual Studio 2013 går du till **kontroll panelens** > **program och funktioner**. Avinstallera **Microsoft Azure Data Lake-och Stream Analytics-verktyg för Visual Studio**.
