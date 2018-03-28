---
title: Azure ML-arbetsstationen viktig information för sprint 0 oktober 2017
description: Det här dokumentet beskriver uppdateringar för sprint 0-versionen av Azure ML
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 10/12/2017
ms.openlocfilehash: 37e0a4461e8a0de631a6194a1eb8cc16b610954f
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2018
---
# <a name="sprint-0---october-2017"></a>Sprint 0 – Oktober 2017 

**Versionsnummer: 0.1.1710.04013**

Välkommen till den första uppdateringen av Azure Machine Learning arbetsstationen efter vårt första förhandsversion på Microsoft Ignite 2017 konferens. De huvudsakliga uppdateringarna i den här versionen är tillförlitlighet och stabilisering åtgärdas.  Några av de viktiga problem vi är:

## <a name="new-features"></a>Nya funktioner
- macOS hög Sierra stöds nu

## <a name="bug-fixes"></a>Felkorrigeringar
### <a name="workbench-experience"></a>Arbetsstationen upplevelse
- Dra och släppa en fil till arbetsstationen orsaker arbetsstationen kraschar.
- Terminalfönster i VS kod som konfigurerats som en IDE för arbetsstationen inte känner igen _az ml_ kommandon.

### <a name="workbench-authentication"></a>Autentisering av arbetsstation
Vi har gjort ett antal uppdateringar för att förbättra olika inloggning och autentisering problem som har rapporterats.
- Autentisering fönstret behåller poppar upp, särskilt när Internet-anslutning inte är stabil.
- Förbättrad tillförlitlighetsproblem runt autentiseringstokens.
- I vissa fall visas autentisering två gånger.
- Arbetsstationen huvudfönstret visar fortfarande ”autentisering” visas när autentiseringsprocessen faktiskt har slutförts och popup-dialogrutan redan stängts.
- Om det finns ingen Internet-anslutning, visas autentiseringsdialogen med ett tomt fönster.

### <a name="data-preparation"></a>Förberedelse av data 
- När ett specifikt värde filtreras fel och värden som saknas också filtreras bort.
- Ändra en provtagning tar bort efterföljande befintliga join-operationer.
- Ersätta saknas värdet tar transformeringen inte NaN tas med i beräkningen.
- Datum typhärledning utlöser undantag när null-värde påträffades.

### <a name="job-execution"></a>Jobbkörningen
- Det finns inget Rensa felmeddelande när jobbkörningen inte kan överföra projektmappen eftersom den överskred storleksgränsen.
- Om användarens Python-skriptet ändras arbetskatalogen spåras inte filer som skrivits till utdata mappar. 
- Om aktiva Azure-prenumerationen är annat än det aktuella projektet tillhör resulterar jobbet 403-fel.
- Om Docker inte finns returneras inget Rensa felmeddelande om användaren försöker använda Docker som ett mål för körning.
- .runconfig filen sparas inte automatiskt när användaren klickar på _kör_ knappen.

### <a name="jupyter-notebook"></a>Jupyter Notebook
- Bärbar dator server kan inte starta om användare använder med vissa typer av inloggningen.
- Felmeddelanden för anteckningsboken server inte identifierades i loggar som är synliga för användare.

### <a name="azure-portal"></a>Azure Portal
- Att välja det mörka temat på Azure-portalen gör modellen hanteringsblad ska visas som en svart ruta.

### <a name="operationalization"></a>Operationalization
- Återanvända ett manifest för att uppdatera en webbtjänst gör en ny Docker-avbildning som skapats med ett slumpmässigt namn.
- Tjänsten webbloggar kan inte hämtas från Kubernetes kluster.
- Missvisande felmeddelandet skrivs ut när användare försöker skapa en modell Management-konto eller ett ML beräkning och påträffar behörighetsproblem.
