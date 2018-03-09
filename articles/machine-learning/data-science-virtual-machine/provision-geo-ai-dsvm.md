---
title: "Etablera en Geo artificiell Intelligence virtuell dator på Azure - Azure | Microsoft Docs"
description: "Hur du etablerar en Geo AI virtuell dator på Azure."
keywords: Djup learning AI datavetenskap verktyg, datavetenskap virtuell dator, geospatiala analytics
services: machine-learning
documentationcenter: 
author: gopitk
manager: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: gokuma
ms.openlocfilehash: 2994ef858e960640d98ab2f7d02a401b11aa9e8f
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="provision-a-geo-artificial-intelligence-virtual-machine-on-azure"></a>Etablera en Geo artificiell Intelligence virtuell dator på Azure 

Geo AI datavetenskap virtuell dator (Geo DSVM) är en utökning av den populära [virtuell dator i Azure datavetenskap](http://aka.ms/dsvm) som konfigureras särskilt för att kombinera AI och geospatiala analytics. Geospatiala analyser i den virtuella datorn är utrustad med [ArcGIS Pro](https://www.arcgis.com/features/index.html). Den virtuella datorn vetenskap Data kan snabb utbildning av machine learning modeller och även för djup learning-modeller för data som är utökat med geografisk information. Den stöds endast på Windows 2016 DSVM. 

Geo-DSVM innehåller flera verktyg för AI inklusive:

- GPU-utgåvor av populära djup learning ramverk som Microsoft kognitiva Toolkit, TensorFlow, Keras, Caffe2, klientprocess; 
- Verktyg för att hämta och före processer bilden, textinformation, 
- Verktyg för utveckling aktiviteter, till exempel Microsoft R Server Developer Edition, Anaconda Python Jupyter-anteckningsböcker för Python och R, IDEs för Python och R, SQL-databaser
- ESRI'S ArcGIS Pro programvara för skrivbordet tillsammans med Python och R-gränssnitt som fungerar med geospatiala data från dina AI-program. 


## <a name="create-your-geo-ai-data-science-vm"></a>Skapa din Geo AI-datavetenskap VM

Här är hur du skapar en instans av Geo AI datavetenskap VM: 


1. Navigera till den virtuella datorn visas i en lista på [Azure-portalen](https://ms.portal.azure.com/#create/microsoft-ads.geodsvmwindows).
2. Välj den **skapa** längst ned till tas med i en guide.
![create-geo-ai-dsvm](./media/provision-geo-ai-dsvm/Create-Geo-AI.png)
3. Guiden som används för att skapa Geo-DSVM kräver **indata** för var och en av de **fyra steg** räknas upp till höger på denna bild. Här följer de indata som behövs för att konfigurera var och en av de här stegen:



   - **Grundläggande inställningar**

      1. **Namnet**: namnet på den server för vetenskap av data som du skapar.

      2. **Användarnamnet**: Admin inloggnings-id för kontot.

      3. **Lösenordet**: Admin kontolösenord.

      4. **Prenumerationen**: Om du har mer än en prenumeration väljer du en som datorn ska skapas och debiteras.

      5. **Resursgruppen**: du kan skapa en ny eller Använd en **tom** befintliga Azure-resursgrupp i din prenumeration.

      6. **Plats**: Välj datacenter som är mest lämplig. Vanligtvis är det datacenter som har de flesta av dina data eller som är närmast den fysiska platsen för snabbaste nätverksåtkomst. Om du behöver göra djup inlärning på GPU måste du välja någon av platser i Azure som har NC-serien GPU VM-instanser. För närvarande platser som har GPU virtuella datorer är: **östra USA, norra centrala USA, södra centrala USA, västra USA 2, Nordeuropa, Västeuropa**. Den senaste listan Kontrollera den [Azure produkter efter Region sidan](https://azure.microsoft.com/regions/services/) och leta efter **NC-serien** under **Compute**. 


   - **Inställningar för**: Välj en NC-serien GPU virtuella storlek om du planerar att köra djup learning på GPU på din Geo DSVM. Annars kan du välja en av Processorn baserat instans.  Skapa ett lagringskonto för den virtuella datorn. 
   
   - **Sammanfattning**: Kontrollera att all information du angett är korrekt.

   - **Köpa**: Klicka på **köpa** att starta etableringen. En länk som villkoren i tjänsten. Den virtuella datorn inte har några ytterligare kostnader utöver beräkning för server-storlek som du har valt i den **storlek** steg. 

>[!NOTE]
> Etableringen bör ta ungefär 20 – 30 minuter. Status för etablering av visas på Azure-portalen.


## <a name="how-to-access-the-geo-ai-data-science-virtual-machine"></a>Hur du kommer åt Geo AI datavetenskap virtuell dator

När den virtuella datorn har skapats är du redo att börja använda verktygen som installeras och konfigureras före på den. Det finns start-menyn paneler och ikoner på skrivbordet för många av verktyg. Du kan fjärrskrivbord till den med administratörsautentiseringsuppgifter för kontot som du konfigurerade i föregående **grunderna** avsnitt. 


## <a name="using-arcgis-pro-installed-in-the-vm"></a>Med ArcGIS Pro installerat på den virtuella datorn

Geo-DSVM har redan ArcGIS Pro desktop förinstallerat och förkonfigurerade för att fungera med alla verktyg i DSVM miljön. När du startar ArcGIS uppmanar dig för en inloggning till ArcGIS-konto. Du kan använda din befintliga autentiseringsuppgifter om du redan har ett konto för ArcGIS och har licenser för programvaran.  

![Arc-GIS-Logon](./media/provision-geo-ai-dsvm/ArcGISLogon.png)

I annat fall du kan registrera dig för nya ArcGIS konto och licens eller hämta en [kostnadsfri utvärderingsversion](https://www.arcgis.com/features/free-trial.html). 

![ArcGIS-Free-Trial](./media/provision-geo-ai-dsvm/ArcGIS-Free-Trial.png)

När du har registreringen för antingen en betald eller ett kostnadsfritt utvärderingskonto ArcGIS kan du tillåta ArcGIS Pro för ditt konto genom att följa anvisningarna i den [komma igång med ArcGIS Pro dokumentationen](http://www.esri.com/library/brochures/getting-started-with-arcgis-pro.pdf). 

När du loggar in på ArcGIS Pro skrivbordet med ditt konto ArcGIS är du redo att börja använda datavetenskap verktygen som installeras och konfigureras på den virtuella datorn för Geospatial analytics och maskininlärning projekt.

## <a name="next-steps"></a>Nästa steg

Komma igång med Geo AI datavetenskap VM med vägledning från följande avsnitt:

* [Använd den Geo AI-datavetenskap VM](use-geo-ai-dsvm.md)