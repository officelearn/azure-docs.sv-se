---
title: 'Importera från webb adress via HTTP: Modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder modulen importera från webb-URL via HTTP i Azure Machine Learning-tjänsten för att läsa data från en offentlig webb sida för användning i ett maskin inlärnings experiment.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 59b8e2e73b9904a503c16d8891e5a5bd771fc87f
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128759"
---
# <a name="import-from-web-url-via-http-module"></a>Importera från webb-URL via HTTP-modul

I den här artikeln beskrivs en modul i Visual Interface (för hands version) för Azure Machine Learning tjänst.

Använd den här modulen för att läsa data från en offentlig webb sida för användning i ett maskin inlärnings experiment.

Följande begränsningar gäller för data som publiceras på en webb sida:

- Data måste vara i något av de format som stöds: CSV, TSV, ARFF eller SvmLight. Andra data kommer att orsaka fel.
- Ingen autentisering krävs eller stöds. Data måste vara offentligt tillgängliga. 

Det finns två sätt att hämta data: Använd guiden för att konfigurera data källan eller konfigurera den manuellt.

## <a name="use-the-data-import-wizard"></a>Använda guiden Importera data

1. Lägg till modulen **Importera data** i experimentet. Du hittar modulen i gränssnittet i kategorin **data indata och utdata** .

2. Klicka på **starta guiden Importera data** och välj webb adress via http.

3. Klistra in i URL: en och välj ett data format.

4. När konfigurationen är klar.

Om du vill redigera en befintlig data anslutning startar du guiden igen. Guiden läser in all tidigare konfigurations information så att du inte behöver börja om från början

## <a name="manually-set-properties-in-the-import-data-module"></a>Ange egenskaper manuellt i modulen importera data

Följande steg beskriver hur du konfigurerar import källan manuellt.

1. Lägg till modulen [Importera data](import-data.md) i experimentet. Du hittar modulen i gränssnittet i kategorin **data indata och utdata** .

2. För **data källa**väljer du **webb-URL via http**.

3. För **URL**skriver eller klistrar du in den fullständiga URL-adressen för sidan som innehåller de data du vill läsa in.

    URL: en måste innehålla webbplatsens URL och den fullständiga sökvägen, med fil namn och fil namns tillägg, på sidan som innehåller de data som ska läsas in.

    Följande sida innehåller till exempel data uppsättningen Iris från Machine Learning-lagringsplatsen för University of California, Irvine:

    `https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data`

4. För **data format**väljer du ett av de data format som stöds från listan.

    Vi rekommenderar att du alltid kontrollerar data i förväg för att fastställa formatet. På sidan UC-Irvine används CSV-formatet. Andra data format som stöds är TSV, ARFF och SvmLight.

5. Om data är i CSV-eller TSV-format kan du använda alternativet **fil med rubrik rad** för att ange om käll data innehåller en rubrik rad eller inte. Rubrik raden används för att tilldela kolumn namn.

6. Välj alternativet **Använd cachelagrat resultat** om du inte förväntar dig att data ska ändras mycket, eller om du vill undvika att ladda om data varje gången du kör experimentet.

    När det här alternativet väljs läser experimentet in data första gången modulen körs, och därefter används en cachelagrad version av data uppsättningen.

    Om du vill läsa in data uppsättningen på nytt på varje iteration av experiment data uppsättningen avmarkerar du alternativet **Använd cachelagrat resultat** . Resultaten läses också in om det finns ändringar i parametrarna för import av [data](import-data.md).

7. Kör experimentet.

## <a name="results"></a>Resultat

När du är klar klickar du på data uppsättningen för utdata och väljer **visualisera** för att se om data har importer ATS korrekt.


## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för att Azure Machine Learning-tjänsten. 