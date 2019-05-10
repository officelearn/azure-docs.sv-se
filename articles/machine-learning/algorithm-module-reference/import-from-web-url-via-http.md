---
title: 'Importera från Webbadress via HTTP: Modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder Import Adresy URL via HTTP-modul i Azure Machine Learning-tjänsten för att läsa data från en offentlig webbplats för användning i en machine learning-experiment.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: bff913efb38c9e5589c795386dfbbc480d799a37
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65411448"
---
# <a name="import-from-web-url-via-http-module"></a>Importera från URL: en för webbtjänst via HTTP-modul

Den här artikeln beskrivs en modul av det visuella gränssnittet (förhandsversion) för Azure Machine Learning-tjänsten.

Använd den här modulen för att läsa data från en offentlig webbplats för användning i en machine learning-experiment.

Följande begränsningar gäller för data som publiceras på en webbsida:

- Data måste vara i något av format som stöds: CSV, TVS, ARFF eller SvmLight. Andra data kommer att orsaka fel.
- Ingen autentisering krävs eller stöds. Data måste vara offentligt tillgängliga. 

Det finns två sätt att hämta data: Använd guiden för att konfigurera datakällan eller konfigurera den manuellt.

## <a name="use-the-data-import-wizard"></a>Använd guiden Importera Data

1. Lägg till den **importdata** modulen i experimentet. Du kan också hitta modulen i gränssnittet, i den **Data indata och utdata** kategori.

2. Klicka på **starta guiden Importera** och välj Webbadress via HTTP.

3. Klistra in URL: en och välj ett dataformat.

4. När konfigurationen är klar.

Starta guiden igen om du vill redigera en befintlig dataanslutning. Guiden läser in konfigurationsinformation om alla föregående så att du inte behöver starta igen från början

## <a name="manually-set-properties-in-the-import-data-module"></a>Manuellt ange egenskaper i modulen importera Data

Följande steg beskriver hur du konfigurerar manuellt importera källa.

1. Lägg till den [importdata](import-data.md) modulen i experimentet. Du kan också hitta modulen i gränssnittet, i den **Data indata och utdata** kategori.

2. För **datakälla**väljer **Webbadress via HTTP**.

3. För **URL**, Skriv eller klistra in en fullständig URL för den sida som innehåller de data som du vill läsa in.

    URL: en ska inkludera webbplatsens URL och den fullständiga sökvägen, filnamnet och tillägg på den sida som innehåller data att läsa in.

    Följande sida innehåller till exempel Iris-datauppsättningen från de machine learning-databasen vid University of California, Irvine:

    `https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data`

4. För **dataformat**, Välj en av data som stöds format i listan.

    Vi rekommenderar att du alltid kontrollera data i förväg för att identifiera. Sidan UC Irvine använder CSV-format. Andra format för data som stöds är TVS, ARFF och SvmLight.

5. Om data i CSV- eller TSV format använder den **filen har rubrikrad** alternativet för att indikera om källdata innehåller en rubrikrad. Rubrikraden används för att tilldela kolumnnamn.

6. Välj den **Använd cachelagrade resultat** alternativ om du inte tror att informationen för att ändra mycket, eller om du vill undvika läser in igen data varje gång du kör experimentet.

    När det här alternativet väljs, laddas experimentet data första gången modulen körs, och därefter använder en cachelagrad version av datauppsättningen.

    Om du vill läsa in datauppsättningen för varje iteration av experimentet datauppsättningen, avmarkera de **Använd cachelagrade resultat** alternativet. Resultatet är också lästs in på nytt om det finns ändringar av parametrarna för [importdata](import-data.md).

7. Kör experimentet.

## <a name="results"></a>Resultat

När du är klar klickar du på datauppsättningen för utdata och välj **visualisera** att se om data har importerats.


## <a name="next-steps"></a>Nästa steg

Se den [uppsättning moduler som är tillgängliga](module-reference.md) till Azure Machine Learning-tjänsten. 