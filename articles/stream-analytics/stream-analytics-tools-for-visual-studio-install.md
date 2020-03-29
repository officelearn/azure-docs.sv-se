---
title: Konfigurera Azure Stream Analytics-verktyg för Visual Studio
description: I den här artikeln beskrivs installationskrav och hur du konfigurerar Azure Stream Analytics-verktygen för Visual Studio.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/22/2018
ms.openlocfilehash: fa3990d2239da71fb27f4c9a06699f758fcfe7a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75354370"
---
# <a name="install-azure-stream-analytics-tools-for-visual-studio"></a>Installera Azure Stream Analytics-verktyg för Visual Studio

Visual Studio 2019 och Visual Studio 2017 stöder Azure Data Lake och Stream Analytics Tools. I den här artikeln beskrivs hur du installerar och avinstallerar verktygen.

Mer information om hur du använder verktygen finns i [Snabbstart: Skapa ett Azure Stream Analytics-jobb med hjälp av Visual Studio](stream-analytics-quick-create-vs.md).

## <a name="install"></a>Installera

Visual Studio Enterprise (Ultimate/Premium), Professional och Community-utgåvor stöder verktygen. Express edition och Visual Studio för Mac stöder dem inte.

Vi rekommenderar Visual Studio 2019.

### <a name="install-for-visual-studio-2019-and-2017"></a>Installera för Visual Studio 2019 och 2017<a name="recommended-visual-studio-2019-and-2017"></a>

Azure Data Lake and Stream Analytics Tools är en del av **Azure-utvecklings-** och **datalagrings- och** bearbetningsarbetsbelastningarna. Aktivera någon av dessa två arbetsbelastningar under installationen. Om Visual Studio redan är installerat väljer du **Verktyg** > **hämta verktyg och funktioner** för att lägga till arbetsbelastningar.

Ladda ned [Visual Studio 2019 (Preview 2 eller högre) eller Visual Studio 2017 (15.3 eller högre)](https://www.visualstudio.com/) och följ instruktionerna för att installera.

Välj arbetsbelastningen **för datalagring och bearbetning** som visas:

![Datalagrings- och bearbetningsarbetsbelastning har valts](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2019-install-01.png)

Välj **Azure-utvecklingsarbetsbelastningen** som visas:

![Azure-utvecklingsarbetsbelastning har valts](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2019-install-02.png)

När du har lagt till arbetsbelastningen uppdaterar du verktygen. Den här proceduren refererar till Visual Studio 2019:

1. Välj **Tillägg** > **Hantera tillägg**.

1. I **Hantera tillägg**väljer du **Uppdateringar** och väljer Azure Data Lake och Stream **Analytics Tools**.

1. Välj **Uppdatera** om du vill installera det senaste tillägget.

![Visual Studio-tillägg och uppdateringar](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-vs2019-extensions-updates.png)

### <a name="install-for-visual-studio-2015-and-2013"></a>Installera för Visual Studio 2015 och 2013<a name="visual-studio-2015-2013"></a>

Visual Studio Enterprise (Ultimate/Premium), Professional och Community-utgåvor stöder verktygen. Express edition stöder dem inte.

* Installera Visual Studio 2015 eller Visual Studio 2013 Update 4.
* Installera Microsoft Azure SDK för .NET version 2.7.1 eller senare med hjälp av [installationsprogrammet för webbplattform](https://www.microsoft.com/web/downloads/platform.aspx).
* Installera [Microsoft Azure Data Lake och Stream Analytics Tools för Visual Studio](https://www.microsoft.com/en-us/download/details.aspx?id=49504).

## <a name="update"></a>Uppdatering<a name="visual-studio-2019-and-2017"></a><a name="visual-studio-2015-and-2013"></a>

För Visual Studio 2019 och Visual Studio 2017 visas en ny versionpåminnelse som en Visual Studio-avisering.

För Visual Studio 2015 och Visual Studio 2013 söker verktygen automatiskt efter nya versioner. Installera den senaste versionen genom att följa instruktionerna för att installera den senaste versionen.

## <a name="uninstall"></a>Avinstallera

Du kan avinstallera Azure Data Lake och Stream Analytics Tools. För Visual Studio 2019 eller Visual Studio 2017 väljer du **Verktyg** > **hämta verktyg och funktioner**. I **Ändra**avmarkerar du **Azure Data Lake och Stream Analytics Tools**. Det visas antingen under **datalagrings- och bearbetningsarbetsbelastningen** eller **Azure-utvecklingsarbetsbelastningen.**

Om du vill avinstallera från Visual Studio 2015 eller Visual Studio 2013 går du till**Program och funktioner på** **Kontrollpanelen** > . Avinstallera **Microsoft Azure Data Lake och Stream Analytics Tools för Visual Studio**.
