---
title: Installera Azure Data Lake Tools för Visual Studio
description: Den här artikeln beskriver hur du installerar Azure Data Lake Tools för Visual Studio.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.topic: conceptual
ms.date: 05/03/2018
ms.openlocfilehash: 3269d38b691ec4dd9573a241c89dadc285787143
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51254034"
---
# <a name="install-data-lake-tools-for-visual-studio"></a>Installera Data Lake-verktyg för Visual Studio

Lär dig hur du använder Visual Studio för att skapa Azure Data Lake Analytics-konton, definiera jobb i [U-SQL](data-lake-analytics-u-sql-get-started.md) och skicka jobb till Data Lake Analytics-tjänsten. Mer information om Data Lake Analytics finns i [Översikt över Azure Data Lake Analytics](data-lake-analytics-overview.md).

## <a name="prerequisites"></a>Förutsättningar

* **Visual Studio**: Alla utgåvor utom Express stöds.
    * Visual Studio 2017
    * Visual Studio 2015
    * Visual Studio 2013
* **Microsoft Azure SDK för .NET** version 2.7.1 eller senare.  Installera den med hjälp av [installationsprogrammet för webbplattformen](https://www.microsoft.com/web/downloads/platform.aspx).
* Ett **Data Lake Analytics**-konto. Information om hur du skapar ett konto finns i [Kom igång med Azure Data Lake Analytics med hjälp av Azure Portal](data-lake-analytics-get-started-portal.md).

## <a name="install-azure-data-lake-tools-for-visual-studio-2017"></a>Installera Azure Data Lake Tools för Visual Studio 2017

Azure Data Lake Tools för Visual Studio stöds i Visual Studio 2017, version 15.3 och senare. Verktyget är en del av arbetsbelastningarna **Lagring och bearbetning av data** och **Azure Development** i Visual Studio-installationsprogrammet. Aktivera någon av dessa två arbetsbelastningar som en del av Visual Studio-installationen.  

Aktivera arbetsbelastningen **Lagring och bearbetning av data** så här: ![Aktivera arbetsbelastningen Lagring och bearbetning av data](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2017-install-01.png)

Aktivera arbetsbelastningen **Azure Development** så här: ![Aktivera arbetsbelastningen Azure Development](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2017-install-02.png)

## <a name="install-azure-data-lake-tools-for-visual-studio-2013-and-2015"></a>Installera Azure Data Lake Tools för Visual Studio 2013 och 2015

Hämta och installera Azure Data Lake Tools för Visual Studio [från Download Center](https://aka.ms/adltoolsvs). Efter installationen kontrollerar du att:
* Noden **Server Explorer** > **Azure** innehåller en **Data Lake Analytics**-nod. 
* Alternativet **Data Lake** finns på **Verktyg**-menyn.


## <a name="next-steps"></a>Nästa steg
* Information om hur du loggar diagnostikinformation finns i [Åtkomst till diagnostikloggar för Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md)
* Om du vill se en mer komplex fråga, se [Analysera webbplatsloggar med hjälp av Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Om du vill använda Körningsvy Se [använda den Körningsvy i Data Lake Tools för Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)
