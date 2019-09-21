---
title: 'Snabbstart: Skapa en Geo AI-DSVM'
description: Konfigurera och skapa en geo AI-Data Science Virtual Machine på Azure för geospatial analys och maskin inlärning.
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 09/13/2019
ms.openlocfilehash: 6b32d1f76091d7bbb8f870402020d0ac247d425b
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/20/2019
ms.locfileid: "71170721"
---
# <a name="quickstart-set-up-a-geo-artificial-intelligence-virtual-machine-on-azure"></a>Snabbstart: Konfigurera en virtuell geo-informations dator på Azure 

Geo AI-Data Science Virtual Machine (geo-DSVM) är en utökning av de populära [Azure-Data Science Virtual Machine](https://aka.ms/dsvm) som är särskilt konfigurerade för att kombinera AI och geospatial analys. Geospatial analys i den virtuella datorn drivs av [ArcGIS Pro](https://www.arcgis.com/features/index.html). Data Science Virtual Machine (DSVM) gör det möjligt att snabbt träna maskin inlärnings modeller och till och med djup inlärnings modeller. För att utveckla dessa modeller använder den data som har berikats med geografisk information. Geo-DSVM stöds endast i Windows 2016 DSVM. 

AI-verktygen som ingår i Geo-DSVM innehåller följande:

- GPU-versioner av populära djup inlärnings ramverk som Microsoft Cognitive Toolkit, TensorFlow, keras, Caffe2 och kedjor
- Verktyg för att hämta och Förbearbeta bild-och text data
- Verktyg för utvecklings aktiviteter som Microsoft Machine Learning Server Developer Edition, Anaconda python, Jupyter Notebooks för python och R, IDE: er för python och R och SQL-databaser
- ArcGIS Pro Desktop-programvara från ESRI, tillsammans med python-och R-gränssnitt som kan arbeta med geospatiala data från dina AI-program
 

## <a name="create-your-geo-ai-data-science-vm"></a>Skapa din Geo-spatial AI Data Science VM

Följ dessa steg om du vill skapa en instans av Geo AI-Data Science VM:

1. Gå till listan med virtuella datorer på [Azure Portal](https://ms.portal.azure.com/#create/microsoft-ads.geodsvmwindows).
1. Skapa en guide genom att välja **skapa** längst ned:

   ![Create-geo-AI – dsvm](./media/provision-geo-ai-dsvm/Create-Geo-AI.png)

1. Guiden kräver ininformation för vart och ett av de fyra stegen. Detaljerad information om indata finns i följande avsnitt.

### <a name="wizard-details"></a>Guide information ###

**Grunderna**:

- **Namn på**: Namnet på den data science-server som du skapar.
    
- **Användar namn**: Inloggnings-ID för administratörs konto.
    
- **Lösenord**: Lösen ord för administratörs kontot.
    
- **Prenumeration**: Om du har mer än en prenumeration väljer du det som datorn är skapas och faktureras.
    
- **Resursgrupp**: Du kan skapa en ny eller använda en **Tom** befintlig Azure-resurs grupp i din prenumeration.
    
- **Plats**: Välj det data Center som passar bäst. Vanligt vis är det en som har de flesta data eller som är närmast din fysiska plats för snabbast nätverks åtkomst. Om du planerar att köra djup inlärning på en GPU måste du välja en av de platser i Azure som har NC-seriens GPU VM-instanser. För närvarande är dessa platser: **Östra USA, norra centrala USA, södra centrala USA, västra USA 2, norra Europa,** Västeuropa. För den senaste listan, se sidan [Azure-produkter efter region](https://azure.microsoft.com/regions/services/) och leta efter **NC-serien** under **Compute**. 
    
    
**Inställningar för**: Välj en av NC-seriens storlekar på virtuella GPU-datorer om du planerar att köra djup inlärning på en GPU på din geo-DSVM. Annars kan du välja en av de CPU-baserade instanserna. Skapa ett lagringskonto för den virtuella datorn. 
       
**Sammanfattning av**: Kontrollera att all information som du angett är korrekt.
    
**Köpa**: Starta etablerings processen genom att klicka på **köp**. Det finns en länk till användningsvillkor. Den virtuella datorn har inga ytterligare avgifter utöver beräknings avgifterna för den server storlek du valde i steget **storlek** . 
 
 >[!NOTE]
 > Etableringen bör ta cirka 20 till 30 minuter. Status för etableringen visas på Azure portal.

 
## <a name="how-to-access-the-geo-ai-data-science-virtual-machine"></a>Hur du kommer åt Geo AI Data Science Virtual Machine

 När den virtuella datorn har skapats är du redo att börja använda de verktyg som är installerade och förkonfigurerade på den. Det finns Start meny paneler och skriv bords ikoner för många av verktygen. Du kan komma åt den virtuella datorn via fjärr skrivbord genom att använda de autentiseringsuppgifter för administratörs kontot som du konfigurerade i avsnittet **grundläggande** .

 
## <a name="using-arcgis-pro-installed-in-the-vm"></a>Med hjälp av ArcGIS Pro installerad på den virtuella datorn

På Geo-DSVM är ArcGIS Pro-skrivbordet förinstallerat och miljön är förkonfigurerad för att fungera med alla verktyg i DSVM. När du startar ArcGIS uppmanas du att ange autentiseringsuppgifter för ditt ArcGIS-konto. Om du redan har en ArcGIS-konto och har licenser för programvaran, kan du använda din befintliga autentiseringsuppgifter.  

![Båge-GIS-inloggning](./media/provision-geo-ai-dsvm/ArcGISLogon.png)

Annars kan du registrera dig för ett nytt ArcGIS-konto och en licens eller få en [kostnads fri utvärderings version](https://www.arcgis.com/features/free-trial.html). 

![ArcGIS-Free-Trial](./media/provision-geo-ai-dsvm/ArcGIS-Free-Trial.png)

När du har registrerat dig för ett standard ArcGIS-konto eller en kostnads fri utvärderings version kan du godkänna ArcGIS Pro för ditt konto genom att följa anvisningarna i [komma igång med ArcGIS Pro](https://www.esri.com/library/brochures/getting-started-with-arcgis-pro.pdf).

När du har loggat in på ArcGIS Pro-skrivbordet via ditt ArcGIS-konto är du redo att börja använda de data vetenskaps verktyg som är installerade och konfigurerade på den virtuella datorn för dina geospatiala analyser och maskin inlärnings projekt.

## <a name="next-steps"></a>Nästa steg

Börja använda geo AI-Data Science VM med vägledning från följande resurs:

* [Använda Geo-spatial AI Data Science VM](use-geo-ai-dsvm.md)
