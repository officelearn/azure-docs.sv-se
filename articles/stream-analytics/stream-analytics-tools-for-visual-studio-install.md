---
title: Konfigurera Azure Stream Analytics-verktyg för Visual Studio
description: Den här artikeln beskriver Installationskrav och hur du ställer in Azure Stream Analytics-verktyg för Visual Studio.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/22/2018
ms.openlocfilehash: 94ed603990859d12f709e4a6121e3736221cf10a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34651186"
---
# <a name="install-azure-stream-analytics-tools-for-visual-studio"></a>Installera Azure Stream Analytics tools för Visual Studio
Azure Stream Analytics-verktyg stöd för Visual Studio 2017 2015 och 2013. Den här artikeln beskriver hur du installerar och avinstallerar verktyg.

Mer information om hur du använder verktygen finns [Stream Analytics tools för Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio).

## <a name="install"></a>Installera
### <a name="visual-studio-2017"></a>Visual Studio 2017
* Hämta [Visual Studio-2017 (15.3 eller senare)](https://www.visualstudio.com/). Enterprise (Ultimate/Premium), Professional och Community-versioner stöds. Express edition stöds inte. 
* Stream Analytics verktyg är en del av den **Azure-utveckling** och **bearbetning och lagring av Data** arbetsbelastningar i Visual Studio-2017. Aktivera någon av dessa två arbetsbelastningar som en del av Visual Studio-installationen.

Aktivera den **bearbetning och lagring av Data** arbetsbelastning som visas:

![Arbetsbelastningen för lagring och bearbetning av data är markerad](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2017-install-01.png)

Aktivera den **Azure-utveckling** arbetsbelastning som visas:

![Azure-utveckling arbetsbelastningen är markerat](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2017-install-02.png)


### <a name="visual-studio-2013-2015"></a>Visual Studio 2013, 2015
* Installera Visual Studio 2015 eller Visual Studio 2013 uppdatering 4. Enterprise (Ultimate/Premium), Professional och Community-versioner stöds. Express edition stöds inte. 
* Installera Microsoft Azure SDK för .NET version 2.7.1 eller senare med hjälp av den [installationsprogram för webbplattform](http://www.microsoft.com/web/downloads/platform.aspx).
* Installera [Azure Stream Analytics tools för Visual Studio](http://aka.ms/asatoolsvs).

## <a name="update"></a>Uppdatering

### <a name="visual-studio-2017"></a>Visual Studio 2017
Den nya version påminnelsen visas i Visual Studio-meddelande. 

### <a name="visual-studio-2013-and-visual-studio-2015"></a>Visual Studio 2013 och Visual Studio 2015
De installerade Stream Analytics-verktyg för Visual Studio att söka efter nya versioner automatiskt. Följ instruktionerna i popup-fönstret för att installera den senaste versionen. 


## <a name="uninstall"></a>Avinstallera

### <a name="visual-studio-2017"></a>Visual Studio 2017
Dubbelklicka på installationsprogrammet för Visual Studio och välj **ändra**. Avmarkera den **Azure Data Lake och Stream Analytics verktyg** kryssrutan från antingen den **bearbetning och lagring av Data** arbetsbelastning eller **Azure-utveckling** arbetsbelastning.

### <a name="visual-studio-2013-and-visual-studio-2015"></a>Visual Studio 2013 och Visual Studio 2015
Gå till Kontrollpanelen och avinstallera **Microsoft Azure Data Lake och Stream Analytics tools för Visual Studio**.





