---
title: "Installationsinstruktioner för Azure Stream Analytics-verktyg för Visual Studio | Microsoft Docs"
description: "Installationsinstruktioner för Azure Stream Analytics-verktyg för Visual Studio"
keywords: Visual studio
documentationcenter: 
services: stream-analytics
author: su-jie
manager: 
editor: 
ms.assetid: a473ea0a-3eaa-4e5b-aaa1-fec7e9069f20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 9/19/2017
ms.author: sujie
ms.openlocfilehash: 80ce672ae91231e432f7ac9da49df29bb03efeca
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/20/2017
---
# <a name="installation-instructions-for-stream-analytics-tools-for-visual-studio"></a>Installationsinstruktioner för Stream Analytics-verktyg för Visual Studio
Azure Stream Analytics-verktyg har nu stöd för Visual Studio 2017 2015 och 2013. I det här dokumentet introducerar vi hur du installerar och avinstallerar verktyg.

Lär dig hur du använder [Stream Analytics tools för Visual Studio](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-tools-for-visual-studio).

## <a name="install"></a>Installera
### <a name="visual-studio-2017"></a>Visual Studio 2017
* Hämta [Visual Studio-2017 (15.3 eller senare)](https://www.visualstudio.com/). Enterprise (Ultimate/Premium), Professional och Community-versioner stöds. Express edition stöds inte. 
* Stream Analytics verktyg är en del av den **Azure-utveckling** och **bearbetning och lagring av Data** arbetsbelastningar i Visual Studio-2017. Aktivera någon av dessa två arbetsbelastningar som en del av installationen av Visual Studio.

Aktivera den **bearbetning och lagring av Data** arbetsbelastning som visas:

![Arbetsbelastningen för lagring och bearbetning av data](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-2017-install-01.png)

Aktivera den **Azure-utveckling** arbetsbelastning som visas:

![Azure-utveckling arbetsbelastning](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-2017-install-02.png)


### <a name="visual-studio-2013-2015"></a>Visual Studio 2013, 2015
* Installera Visual Studio 2015 eller Visual Studio 2013 uppdatering 4. Enterprise (Ultimate/Premium), Professional och Community-versioner stöds. Express edition stöds inte. 
* Installera Microsoft Azure SDK för .NET version 2.7.1 eller senare med hjälp av den [installationsprogram för webbplattform](http://www.microsoft.com/web/downloads/platform.aspx).
* Installera [Azure Stream Analytics tools för Visual Studio](http://aka.ms/asatoolsvs).



## <a name="update"></a>Uppdatering

### <a name="visual-studio-2017"></a>Visual Studio 2017
Den nya version påminnelsen visas i Visual Studio-meddelande. 

### <a name="visual-studio-2013-2015"></a>Visual Studio 2013, 2015
De installerade Stream Analytics-verktyg för Visual Studio att söka efter nya versioner automatiskt. Följ instruktionerna i popup-fönstret för att installera den senaste versionen. 


## <a name="uninstall"></a>Avinstallera

### <a name="visual-studio-2017"></a>Visual Studio 2017
Dubbelklicka på installationsprogrammet för Visual Studio och välj **ändra**. Avmarkera den **Azure Data Lake och Stream Analytics verktyg** kryssrutan från antingen den **bearbetning och lagring av Data** arbetsbelastning eller **Azure-utveckling** arbetsbelastning.

### <a name="visual-studio-2013-2015"></a>Visual Studio 2013, 2015
Gå till Kontrollpanelen och avinstallera **Microsoft Azure Data Lake och Stream Analytics tools för Visual Studio**.





