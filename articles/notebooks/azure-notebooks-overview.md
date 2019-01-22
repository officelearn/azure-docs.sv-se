---
title: Översikt över Azure Notebooks
description: Kör Jupyter-notebooks i molnet med den kostnadsfria Azure Notebooks-tjänsten där varken installation eller konfiguration krävs.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 9cea5a8e-c52d-4bdc-9e4a-cecdc1ad02c1
ms.service: notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 01/11/2019
ms.author: kraigb
ms.openlocfilehash: ba06e568b3d7b0125d5868c1ad4a85e5e42f7e18
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2019
ms.locfileid: "54246219"
---
# <a name="overview-of-azure-notebooks"></a>Översikt över Azure Notebooks

Azure Notebooks är en kostnadsfri värdbaserad tjänst som används för att utveckla och köra Jupyter Notebooks i molnet utan installation. [Jupyter](https://jupyter.org/) (tidigare IPython) är ett projekt med öppen källkod som gör att du enkelt kan kombinera Markdown-text, körbar kod, beständiga data, grafik och visualiseringar på en enda delbar arbetsyta, *notebook-filen* (bilden tillhör jupyter.org):

[![](https://jupyter.org/assets/jupyterpreview.png "Jupyter Notebooks")](https://jupyter.org/assets/jupyterpreview.png#lightbox)

Tack vare den här kraftfulla kombinationen av kod, grafik och förklarande text har Jupyter blivit populärt för många användningsområden, däribland datavetenskapsinstruktion, datarensning och transformering, numerisk simulering, statistisk modellering och utveckling av maskininlärningsmodeller.

## <a name="hassle-free-experience"></a>Problemfri upplevelse

Med Azure Notebooks kommer du igång snabbt med prototyper, datavetenskap, akademisk forskning eller att lära dig programmera i Python:

- Dataforskare har omedelbar åtkomst till en fullständig Anaconda-miljö utan installation.
- Lärare kan tillhandahålla en problemfri Python-miljö för studenter.
- Föredragshållare kan ge en ”like talk” eller ett webbseminarium utan att be deltagarna att tillbringa 45 minuter med att installera programvara.
- Utvecklare eller hobbyanvändare kan använda Notebooks som ett snabbt anteckningsblock för kod.

Notebooks blir ännu mer kraftfullt när personer samarbetar via en webbläsartillgänglig molntjänst såsom Azure Notebooks (i förhandsversion). I molnet behöver användarna inte installera Jupyter lokalt eller underhålla en miljö. Molnet gör det även enkelt att dela notebook-filer (och associerade datafiler) med andra behöriga användare. Därmed slipper de krånglet med att dela notebook-filer via externa medel såsom lagringsplatser för källkontroll. Med Azure Notebooks kan användarna dessutom kopiera (eller ”klona”) notebook-filer till sitt eget konto för ändring eller experimentering, vilket är särskilt användbart i instruktionssyfte.

Eftersom Azure Notebooks är en allmän plattform för skrivande, körning och delning av kod kan du använda den för många olika scenarier:

- Lär dig ett nytt programmeringsspråk – prova någon av [självstudierna på startsidan](https://notebooks.azure.com/Microsoft/projects/samples/html/Introduction%20to%20Python.ipynb)
- Lär dig datavetenskap – prova på [Jake VanderPlas bok](https://notebooks.azure.com/jakevdp/projects/PythonDataScienceHandbook)
- [Undervisa en kurs](https://notebooks.azure.com/garth-wells/projects/CUED-IA-Computing-Michaelmas) för hundratals studenter
- Håll ett webbseminarium online eller vid en konferens utan att lägga tid på installation 
- Gör så att GitHub-användare direkt kan läsa in och köra notebook-filer genom att [skapar en GitHub-startsymbol](https://notebooks.azure.com/help/projects/sharing/create-a-github-badge)
- Visa [PowerPoint-liknande bildspel](https://notebooks.azure.com/help/jupyter-notebooks/slides) där koden i bilderna är körbar!

Kort sagt hjälper Azure Notebooks dig att utföra ditt arbete effektivare och därmed uppnå mer.

> [!Note]
> Mer information om Jupyter i sig finns på [jupyter.org](https://jupyter.org/) och i [Jupyter-dokumentationen](https://jupyter-notebook.readthedocs.io/en/latest/).

## <a name="pricing-and-quotas"></a>Priser och kvoter

Azure Notebooks är en kostnadsfri tjänst, men varje projekt är begränsat till 4 GB minne och 1 GB data för att förhindra missbruk. Legitima användare som överskrider dessa gränser får en Captcha-utmaning för att fortsätta köra notebook-filer.

Släpp alla gränser genom att logga in på Azure Notebooks med ett konto med Azure Active Directory (till exempel ett företagskonto). Om kontot är associerat med en Azure-prenumeration kan du använda beräkningsnivåer i Azure Data Science Virtual Machine.

## <a name="available-kernels-and-environments"></a>Tillgängliga kernels och miljöer

För varje notebook-fil väljer du den kernel (det vill säga körningsmiljö) som används för att köra kodceller. Azure Notebooks stöder följande kernels:

- Python 2.7 + Anaconda2-5.3.0
- Python 3.6 + Anaconda3-5.3.0
- Python 3.5 + Anaconda3-4.2.0 (kommer att bli inaktuell)
- R 3.4.1 + Microsoft R Open 3.4.1
- F# 4.1.9

Azure Notebooks innehåller även extra paket utöver de grundläggande distributionerna. Python-kernlarna innehåller till exempel biblioteken numpy, pandas, scikit-learn, matplotlib och bokeh.

Du kan även anpassa ett projekt för att skapa en miljö för alla de notebook-filer i projektet. Mer information finns i [ Snabbstart: Skapa ett projekt med en anpassad miljö](quickstart-create-jupyter-notebook-project-environment.md).

Utöver de grundläggande distributionerna levereras Azure Notebooks förinstallerat med många extra paket som är användbara för dataexperter. Du kan även installera egna paket med hjälp av den typiska processen för varje språk.

## <a name="pre-configured-jupyter-extensions"></a>Förkonfigurerade Jupyter-tillägg

Azure Notebooks är förkonfigurerat med följande Jupyter-tillägg:

- [RISE](https://github.com/damianavila/RISE): Ett Jupyter-tillägg för bildspel (kallas även live_reveal). Mer information finns i avsnittet om att [köra ett notebook-bildspel](present-jupyter-notebooks-slideshow.md).
- [JupyterLab](https://github.com/jupyterlab/jupyterlab): En fullständig beräkningsmiljö för att arbeta med Jupyter-notebooks.
- [Altair](https://github.com/ellisonbg/altair): Ett bibliotek för visualisering av deklarativ statistisk för Python.
- [BQPlot](https://github.com/bloomberg/bqplot): Ett interaktivt ritningsramverk för Jupyter Notebooks.
- [IpyWidgets](https://github.com/jupyter-widgets/ipywidgets): Interaktiva HTML-widgetar för Jupyter Notebooks.

## <a name="issues-and-getting-help"></a>Problem och få hjälp

Eftersom Azure Notebooks fortfarande är i förhandsversion kan det uppstå tillfälliga avbrott i tjänsten som kan vara mer frekventa eller långvariga än för andra Azure-tjänster. Vissa funktioner kan vara ofullständiga eller innehålla buggar.

För närvarande rekommenderar inte att du använder förhandsversionen av Azure Notebooks för affärskritiska program eller känsliga notebook-filer och data.

Om du vill diskutera frågor om Azure Notebooks kan du rapportera ett problem på [GitHub-lagringsplatsen](https://github.com/Microsoft/AzureNotebooks/issues).

## <a name="next-steps"></a>Nästa steg  

- [Utforska notebook-exempelfiler](azure-notebooks-samples.md)

- Snabbstarter:

  - [Skapa och dela en notebook-fil](quickstart-create-share-jupyter-notebook.md)
  - [Klona en notebook-fil](quickstart-clone-jupyter-notebook.md)
  - [Migrera en lokal Jupyter Notebook](quickstart-migrate-local-jupyter-notebook.md)
  - [Använda en anpassad miljö](quickstart-create-jupyter-notebook-project-environment.md)
  - [Logga in och ange ett användar-ID](quickstart-sign-in-azure-notebooks.md)

- Självstudier:

  - [Skapa och köra en notebook-fil](tutorial-create-run-jupyter-notebook.md  )

- Instruktionsartiklar:
  
  - [Skapa och klona projekt](create-clone-jupyter-notebooks.md)
  - [Konfigurera och hantera projekt](configure-manage-azure-notebooks-projects.md)
  - [Installera paket inifrån en notebook-fil](install-packages-jupyter-notebook.md)
  - [Presentera ett bildspel](present-jupyter-notebooks-slideshow.md)
  - [Arbeta med datafiler](work-with-project-data-files.md)
  - [Få åtkomst till dataresurser](access-data-resources-jupyter-notebooks.md)
  - [Använda Azure Machine Learning Services](use-machine-learning-services-jupyter-notebooks.md)
