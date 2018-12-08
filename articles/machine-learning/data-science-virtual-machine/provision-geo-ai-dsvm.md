---
title: Etablera en Geo artificiell intelligens virtuell dator på Azure – Azure | Microsoft Docs
description: Lär dig hur du skapar och konfigurerar Geo AI Data Science Virtual Machine. Geo AI Data Science Virtual Machine ger dig verktyg för att skapa AI och ML-lösningar med geografiska data.
keywords: djupinlärning, AI, verktyg för datavetenskap, virtuell dator för datavetenskap, Geospatial analys
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: gokuma
ms.openlocfilehash: 395604da3465e66214f89b4f74ddf9d6bcdc48f8
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53103956"
---
# <a name="provision-a-geo-artificial-intelligence-virtual-machine-on-azure"></a>Etablera en Geo artificiell intelligens virtuell dator på Azure 

Geo AI Data Science Virtual Machine (Geo-DSVM) är en utökning av det populära [Azure Data Science Virtual Machine](https://aka.ms/dsvm) som är speciellt konfigurerad för att kombinera AI och geospatial analys. Geospatial analys i den virtuella datorn drivs av [ArcGIS Pro](https://www.arcgis.com/features/index.html). Den virtuella datorn för datavetenskap kan snabb utbildning av machine learning-modeller och även av deep learning-modeller på data som berikats med geografisk information. Den stöds endast på Windows 2016 DSVM. 

Geo-DSVM innehåller flera verktyg för AI, inklusive:

- GPU-versionerna av populära deep learning-ramverk som Microsoft Cognitive Toolkit, TensorFlow, Keras, Caffe2, Chainer; 
- Verktyg för att hämta och förprocess bild, textdata, 
- Verktyg för utveckling av aktiviteter, till exempel Microsoft R Server Developer Edition, Anaconda Python, Jupyter-anteckningsböcker för Python och R IDE: er för Python och R, SQL-databaser
- ESRIS ArcGIS Pro programvara tillsammans med Python- och R-gränssnitt som fungerar med geospatiala data från dina AI-program. 


## <a name="create-your-geo-ai-data-science-vm"></a>Skapa din Geo-spatial AI Data Science VM

Här följer stegen för att skapa en instans av Geo AI Data Science VM: 


1. Navigera till den virtuella datorn på [Azure-portalen](https://ms.portal.azure.com/#create/microsoft-ads.geodsvmwindows).
2. Välj den **skapa** längst ned för att gå till en guide.
![Skapa-geo-ai-dsvm](./media/provision-geo-ai-dsvm/Create-Geo-AI.png)
3. I guiden som används för att skapa Geo-DSVM kräver **indata** för var och en av de **fyra steg** räknas upp till höger i den här bilden. Här följer de indata som behövs för att konfigurera var och en av de här stegen:



   - **Grundläggande inställningar**

      1. **Namn på**: namnet på den data science-server som du skapar.

      2. **Användarnamnet**: inloggnings-id för Admin-konto.

      3. **Lösenord**: lösenord för administratörskonto.

      4. **Prenumeration**: Om du har mer än en prenumeration kan du välja den som som datorn är skapas och faktureras.

      5. **Resursgrupp**: du kan skapa en ny eller Använd en **tom** befintliga Azure-resursgrupp i din prenumeration.

      6. **Plats**: Välj det datacenter som är mest lämplig. Vanligtvis är det datacenter som har de flesta av dina data eller som är närmast dina fysiska platsen för snabbaste nätverksåtkomst. Om du behöver göra deep learning på GPU, måste du välja någon av platser i Azure som har NC-serien GPU VM-instanser. De platser som har virtuella GPU-datorer finns för närvarande: **östra USA, norra centrala USA, södra centrala USA, västra USA 2, Norra Europa, Västeuropa**. Den senaste listan finns i [Azure-produkter per Region sidan](https://azure.microsoft.com/regions/services/) och leta efter **NC-serien** under **Compute**. 


   - **Inställningar för**: Välj något av NC-serien GPU VM-storlek om du planerar att köra deep learning på GPU på din Geo DSVM. I annat fall kan du välja någon av Processorn baserad instans.  Skapa ett lagringskonto för den virtuella datorn. 
   
   - **Sammanfattning**: Kontrollera att all information du angett är korrekt.

   - **Köpa**: Klicka på **köpa** att starta etableringen. Det finns en länk till användningsvillkor. Den virtuella datorn har inte några ytterligare avgifter utöver beräkning för serverstorlek som du valde i den **storlek** steg. 

>[!NOTE]
> Etableringen tar cirka 20 – 30 minuter. Status för etableringen visas på Azure portal.


## <a name="how-to-access-the-geo-ai-data-science-virtual-machine"></a>Hur du kommer åt Geo AI Data Science Virtual Machine

När den virtuella datorn har skapats är du redo att börja använda de verktyg som är installerade och redan har konfigurerats på den. Det finns paneler för start-menyn och ikoner på skrivbordet för många av verktygen. Du kan fjärrskrivbord till den med hjälp av autentiseringsuppgifter som administratör-konto som du konfigurerade i föregående **grunderna** avsnittet. 


## <a name="using-arcgis-pro-installed-in-the-vm"></a>Med hjälp av ArcGIS Pro installerad på den virtuella datorn

Geo-DSVM har redan ArcGIS Pro desktop förinstallerat och den miljö som redan har konfigurerats för att fungera med alla verktyg i DSVM. När du startar ArcGIS uppmanar dig för en inloggning på ArcGIS-konto. Om du redan har en ArcGIS-konto och har licenser för programvaran, kan du använda din befintliga autentiseringsuppgifter.  

![Båge-GIS-inloggning](./media/provision-geo-ai-dsvm/ArcGISLogon.png)

Annars kan du kan registrera dig för nya ArcGIS-konto och licens eller få en [kostnadsfri utvärderingsversion](https://www.arcgis.com/features/free-trial.html). 

![ArcGIS-Free-Trial](./media/provision-geo-ai-dsvm/ArcGIS-Free-Trial.png)

När du har registreringen för en antingen en betald eller ett kostnadsfritt utvärderingskonto ArcGIS kan du kan auktorisera ArcGIS Pro för ditt konto genom att följa anvisningarna i den [komma igång med ArcGIS Pro dokumentation](http://www.esri.com/library/brochures/getting-started-with-arcgis-pro.pdf). 

När du loggar in på ArcGIS Pro desktop med ArcGIS-konto är du redo att börja använda data science-verktygen som installeras och konfigureras på den virtuella datorn för geospatiala analytics och machine learning-projekt.

## <a name="next-steps"></a>Nästa steg

Kom igång med hjälp av Geo AI Data Science VM med vägledning från följande avsnitt:

* [Använda Geo-spatial AI Data Science VM](use-geo-ai-dsvm.md)