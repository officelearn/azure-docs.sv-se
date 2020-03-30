---
title: Installera Azure Data Lake Tools för Visual Studio
description: I den här artikeln beskrivs hur du installerar Azure Data Lake Tools för Visual Studio.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 08f0456b6a8b663f0af68fbb179de14b9e3acf79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70914060"
---
# <a name="install-data-lake-tools-for-visual-studio"></a>Installera Data Lake-verktyg för Visual Studio

Lär dig hur du använder Visual Studio för att skapa Azure Data Lake Analytics-konton. Du kan definiera jobb i [U-SQL](data-lake-analytics-u-sql-get-started.md) och skicka jobb till Data Lake Analytics-tjänsten. Mer information om DataSjöanalys finns i [Översikt över Azure Data Lake Analytics](data-lake-analytics-overview.md).

## <a name="prerequisites"></a>Krav

* **Visual Studio**: Alla utgåvor utom Express stöds.

  * Visual Studio 2019
  * Visual Studio 2017
  * Visual Studio 2015
  * Visual Studio 2013

* **Microsoft Azure SDK för .NET** version 2.7.1 eller senare. Installera den med hjälp av [installationsprogrammet för webbplattformen](https://www.microsoft.com/web/downloads/platform.aspx).
* Ett **Data Lake Analytics**-konto. Information om hur du skapar ett konto finns i [Kom igång med Azure Data Lake Analytics med hjälp av Azure Portal](data-lake-analytics-get-started-portal.md).

## <a name="install-azure-data-lake-tools-for-visual-studio-2017-or-visual-studio-2019"></a>Installera Azure Data Lake Tools för Visual Studio 2017 eller Visual Studio 2019

Azure Data Lake Tools för Visual Studio stöds i Visual Studio 2017 15.3 eller senare. Verktyget är en del av **datalagring och bearbetning** och **Azure-utvecklingsarbetsbelastningar.** Aktivera någon av dessa två arbetsbelastningar som en del av Visual Studio-installationen.

Aktivera **datalagrings- och bearbetningsarbetsbelastningen** enligt bilden:

![Aktivera datalagring och bearbetning av arbetsbelastning](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2019-install-01.png)

Aktivera **Azure-utvecklingsarbetsbelastningen** som visas:

![Välj Azure-utvecklingsarbetsbelastning](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2019-install-02.png)

## <a name="install-azure-data-lake-tools-for-visual-studio-2013-and-2015"></a>Installera Azure Data Lake Tools för Visual Studio 2013 och 2015

Ladda ned och installera [Microsoft Azure Data Lake and Stream Analytics Tools för Visual Studio ](https://aka.ms/adltoolsvs). Efter installationen har Visual Studio följande ändringar:

* **Azure-noden** **För Server Explorer** > innehåller en **datasjöanalysnod.**
* Alternativet **Data Lake** finns på **Verktyg**-menyn.

## <a name="next-steps"></a>Nästa steg

* Information om hur du loggar diagnostikinformation finns i [Komma åt diagnostikloggar för Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md).
* Om du vill se en mer komplex fråga, se [Analysera webbplatsloggar med hjälp av Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Information om hur du använder hörnkörningsvyn finns [i Använda vertexutförandevyn i DataSjöverktyg för Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
