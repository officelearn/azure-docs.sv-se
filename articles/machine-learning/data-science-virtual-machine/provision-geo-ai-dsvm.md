---
title: 'Snabbstart: Skapa en virtuell virtuell geo AI-datavetenskap'
titleSuffix: Azure Data Science Virtual Machine
description: Konfigurera och skapa en virtuell geo AI-datavetenskapdator på Azure för geospatial analys och maskininlärning.
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 09/13/2019
ms.openlocfilehash: f3ff9bd64f54d8f83fd1889078e8a4c01827d135
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77525897"
---
# <a name="quickstart-set-up-a-geo-artificial-intelligence-virtual-machine-on-azure"></a>Snabbstart: Konfigurera en virtuell virtuell dator för geo artificiell intelligens på Azure 

Geo AI Data Science Virtual Machine (Geo-DSVM) är ett tillägg till den populära [virtuella Azure Data Science-datorn](https://aka.ms/dsvm) som är speciellt konfigurerad för att kombinera AI- och geospatial analys. Geospatial analytics i den virtuella datorn drivs av [ArcGIS Pro](https://www.arcgis.com/features/index.html). Data Science Virtual Machine (DSVM) möjliggör snabb utbildning av maskininlärning och till och med djupinlärningsmodeller. För att utveckla dessa modeller använder den data som är berikade med geografisk information. Geo-DSVM stöds endast på Windows 2016 DSVM. 

De AI-verktyg som ingår i Geo-DSVM innehåller följande:

- GPU-utgåvor av populära djupinlärningsramverk som Microsoft Cognitive Toolkit, TensorFlow, Keras, Caffe2 och Chainer
- Verktyg för att hämta och förbehandla bild- och textdata
- Verktyg för utvecklingsaktiviteter som Microsoft Machine Learning Server Developer Edition, Anaconda Python, Jupyter-anteckningsböcker för Python och R, ID:er för Python och R och SQL-databaser
- ArcGIS Pro-skrivbordsprogram från ESRI, tillsammans med Python- och R-gränssnitt som kan fungera med geospatiala data från dina AI-program
 

## <a name="create-your-geo-ai-data-science-vm"></a>Skapa din virtuella virtuella geo AI-datavetenskap

Så här skapar du en instans av den virtuella datorn för geo AI-datavetenskap:

1. Gå till listan för virtuella datorer på [Azure-portalen](https://ms.portal.azure.com/#create/microsoft-ads.geodsvmwindows).
1. Välj **Skapa** längst ned om du vill skapa en guide:

   ![skapa-geo-ai-dsvm](./media/provision-geo-ai-dsvm/Create-Geo-AI.png)

1. Guiden kräver indata för vart och ett av de fyra stegen. Detaljerad information om den här indata finns i följande avsnitt.

### <a name="wizard-details"></a>Information om guiden ###

**Grunderna:**

- **Namn**: Namnet på den datavetenskapsserver du skapar.
    
- **Användarnamn**: Inloggnings-ID för administratörskonto.
    
- **Lösenord**: Administratörskontolösenord.
    
- **Prenumeration**: Om du har mer än en prenumeration väljer du den prenumeration på vilken datorn ska skapas och faktureras.
    
- **Resursgrupp**: Du kan skapa en ny eller använda en **tom** befintlig Azure-resursgrupp i din prenumeration.
    
- **Plats**: Välj det datacenter som är mest lämpligt. Vanligtvis är det den som har de flesta av dina data eller som är närmast din fysiska plats för snabbaste nätverksåtkomst. Om du planerar att köra djupinlärning på en GPU måste du välja en av platserna i Azure som har NC-Series GPU VM-instanser. För närvarande dessa platser är: **Östra USA, Norra centrala USA, Södra centrala USA, Västra USA 2, Norra Europa, Västeuropa**. Den senaste listan finns på sidan [Azure-produkter efter region](https://azure.microsoft.com/regions/services/) och letar efter **NC-serien** under **Beräkning**. 
    
    
**Inställningar:** Välj en av nc-serien GPU virtuella maskinstorlekar om du planerar att köra djupinlärning på en GPU på din Geo DSVM. Annars kan du välja en av de CPU-baserade instanserna. Skapa ett lagringskonto för den virtuella datorn. 
       
**Sammanfattning**: Kontrollera att all information du har angett är korrekt.
    
**Köp:** Om du vill starta etableringsprocessen klickar du på **Köp.** En länk tillhandahålls till villkoren för tjänsten. Den virtuella datorn har inga ytterligare avgifter utöver beräkningsavgifterna för den serverstorlek du valde i **storlekssteget.** 
 
 >[!NOTE]
 > Etablering bör ta ca 20 till 30 minuter. Status för etableringen visas på Azure-portalen.

 
## <a name="how-to-access-the-geo-ai-data-science-virtual-machine"></a>Så här kommer du åt virtual machine för geo AI-datavetenskap

 När den virtuella datorn har skapats är du redo att börja använda de verktyg som är installerade och förkonfigurerade på den. Det finns Start-menyn plattor och skrivbordsikoner för många av verktygen. Du kan komma åt den virtuella datorn via fjärrskrivbord med hjälp av autentiseringsuppgifterna för administratörskonto som du har konfigurerat i avsnittet **Grunderna.**

 
## <a name="using-arcgis-pro-installed-in-the-vm"></a>Använda ArcGIS Pro installerat i den virtuella datorn

På Geo-DSVM är ArcGIS Pro-skrivbordet förinstallerat och miljön är förkonfigurerad för att fungera med alla verktyg i DSVM. När du startar ArcGIS uppmanas du att ange autentiseringsuppgifter till ditt ArcGIS-konto. Om du redan har ett ArcGIS-konto och har licenser för programvaran kan du använda dina befintliga autentiseringsuppgifter.  

![Arc-GIS-inloggning](./media/provision-geo-ai-dsvm/ArcGISLogon.png)

Annars kan du registrera dig för ett nytt ArcGIS-konto och -licens, eller få en [kostnadsfri utvärderingsversion](https://www.arcgis.com/features/free-trial.html). 

![ArcGIS-fri-rättegång](./media/provision-geo-ai-dsvm/ArcGIS-Free-Trial.png)

När du har registrerat dig för antingen ett Standard ArcGIS-konto eller en kostnadsfri utvärderingsversion kan du auktorisera ArcGIS Pro för ditt konto genom att följa instruktionerna för [att komma igång med ArcGIS Pro](https://www.esri.com/library/brochures/getting-started-with-arcgis-pro.pdf).

När du har loggat in på ArcGIS Pro-skrivbordet via ditt ArcGIS-konto är du redo att börja använda de datavetenskapsverktyg som är installerade och konfigurerade på den virtuella datorn för dina geospatiala analys- och maskininlärningsprojekt.

## <a name="next-steps"></a>Nästa steg

Börja använda den virtuella datorn för geo AI-datavetenskap med vägledning från följande resurs:

* [Använda den virtuella datorn för geo AI-datavetenskap](use-geo-ai-dsvm.md)
