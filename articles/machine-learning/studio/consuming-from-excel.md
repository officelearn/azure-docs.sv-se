---
title: Använda webb tjänsten i Excel
titleSuffix: ML Studio (classic) - Azure
description: Azure Machine Learning Studio (klassisk) gör det enkelt att anropa webb tjänster direkt från Excel utan att behöva skriva någon kod.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: 333ed411ab818cff77a7cba6c7de4f42c36f5b6b
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79218218"
---
# <a name="consuming-an-azure-machine-learning-studio-classic-web-service-from-excel"></a>Använda en Azure Machine Learning Studio (klassisk) webb tjänst från Excel

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Azure Machine Learning Studio (klassisk) gör det enkelt att anropa webb tjänster direkt från Excel utan att behöva skriva någon kod.

Om du använder Excel 2013 (eller senare) eller Excel Online rekommenderar vi att du använder Excel [Excel Excel-tillägget](excel-add-in-for-web-services.md).



## <a name="steps"></a>Steg
Publicera en webbtjänst. [Självstudie 3: Distribuera kredit risk modell](tutorial-part3-credit-risk-deploy.md) förklarar hur du gör det. Excel-arbetsbok funktionen stöds för närvarande endast för begäran/svar-tjänster som har ett enda utflöde (det vill säga en enda bedömnings-etiketten). 

När du har en webb tjänst klickar du på avsnittet **webb tjänster** till vänster om Studio och väljer sedan den webb tjänst som du vill använda från Excel.

**Klassisk webb tjänst**

1. På fliken **instrument panel** för webb tjänsten finns en rad för tjänsten **Request/Response** . Om tjänsten hade en enda utdata bör du se länken **Hämta Excel-arbetsbok** på den raden.

    ![Hämta Excel-arbetsboken med hjälp av webb tjänst portalen Studio (klassisk)](./media/consuming-from-excel/excellink.png)
2. Klicka på **Hämta Excel-arbetsbok**.

**Ny webb tjänst**

1. I Azure Machine Learning-webbtjänst-portalen väljer du **förbruka**.
2. På sidan konsumera i avsnittet alternativ för **webb tjänst användning** klickar du på Excel-ikonen.

**Använda arbets boken**

1. Öppna arbetsboken.
2. En säkerhets varning visas. Klicka på knappen **Aktivera redigering** .

    ![Aktivera redigering för att ta bort säkerhets varningen skyddad vy](./media/consuming-from-excel/enableeditting.png)
3. En säkerhetsvarning visas. Klicka på knappen **Aktivera innehåll** för att köra makron i kalkyl bladet.

    ![Aktivera innehåll för att stänga säkerhets varningen genom att inaktivera makron](./media/consuming-from-excel/enablecontent.png)
4. När makron är aktiverade, skapas en tabell. Kolumner i blått krävs som inmatade i webb tjänsten resurs resurs, eller **parametrar**. Notera resultatet av resurs resurs tjänsten, **förutsägande värden** i grönt. När alla kolumner för en viss rad är fyllda arbetsboken automatiskt bedömnings-API: n och visar poängsatta resultat.

    ![Tabell för parameter indata och resulterande förutsägande värden](./media/consuming-from-excel/sampletable.png)
5. För att bedöma mer än en rad, Fyll den andra raden med data och de förväntade värdena produceras. Du kan även klistra in flera rader på samma gång.

Du kan använda någon av funktionerna i Excel (diagram, power map, villkorsstyrd formatering, osv.) med de förväntade värdena för att visualisera data.

## <a name="sharing-your-workbook"></a>Dela din arbetsbok
Om makron ska fungera måste måste din API-nyckel ingå i kalkylbladet. Det innebär att du ska dela arbetsboken endast med entiteter/personer som du litar på.

## <a name="automatic-updates"></a>Automatiska uppdateringar
En RRS-anrop görs i de här två situationer:

1. Första gången en rad har innehåll i alla dess **parametrar**
2. När någon av **parametrarna** ändras i en rad där alla **parametrar** har angetts.