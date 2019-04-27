---
title: Använda webbtjänst i Excel
titleSuffix: Azure Machine Learning Studio
description: Azure Machine Learning Studio gör det enkelt att anropa webbtjänster direkt från Excel utan att behöva skriva någon kod.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: ef1d8f1a72c5936ff661636c4c51acf439a0a5ea
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60773798"
---
# <a name="consuming-an-azure-machine-learning-studio-web-service-from-excel"></a>Använda en Azure Machine Learning Studio-webbtjänst från Excel

 Azure Machine Learning Studio gör det enkelt att anropa webbtjänster direkt från Excel utan att behöva skriva någon kod.

Om du använder Excel 2013 (eller senare) eller Excel Online, så vi rekommenderar att du använder Excel [Excel-tillägg](excel-add-in-for-web-services.md).



## <a name="steps"></a>Steg
Publicera en webbtjänst. [Självstudie 3: Distribuera kredit risk modell](tutorial-part3-credit-risk-deploy.md) förklarar hur du gör. Excel-arbetsbok funktionen stöds för närvarande endast för begäran/svar-tjänster som har ett enda utflöde (det vill säga en enda bedömnings-etiketten). 

När du har en webbtjänst, klickar du på den **WEBBTJÄNSTER** till vänster i studio och välj sedan webbtjänsten för att använda från Excel.

**Klassisk webbtjänst**

1. På den **INSTRUMENTPANELEN** fliken för webbtjänsten är en rad för den **begäran/svar** service. Om den här tjänsten har haft ett enda utflöde, bör du se den **ladda ned Excel-arbetsbok** länk på den raden.

    ![Hämta Excel-arbetsbok med hjälp av Studio-webbtjänst-portal](./media/consuming-from-excel/excellink.png)
2. Klicka på **ladda ned Excel-arbetsbok**.

**Ny webbtjänst**

1. I portalen Azure Machine Learning-webbtjänsten väljer **förbruka**.
2. På sidan använda i den **Web service förbrukning alternativ** klickar du på ikonen för Excel.

**Använda arbetsboken**

1. Öppna arbetsboken.
2. En säkerhetsvarning visas. Klicka på den **Aktivera redigering** knappen.

    ![Aktivera redigering att ta bort säkerhetsvarningen skyddad vy](./media/consuming-from-excel/enableeditting.png)
3. En säkerhetsvarning visas. Klicka på den **Aktivera innehåll** för att köra makron i kalkylbladet.

    ![Aktivera innehåll att Stäng säkerhetsvarningen makron](./media/consuming-from-excel/enablecontent.png)
4. När makron är aktiverade, skapas en tabell. Kolumnerna i blå är obligatoriska som indata till webbtjänsten RRS eller **parametrar**. Observera utdata för RRS-tjänsten **FÖRUTSE värden** i grönt. När alla kolumner för en viss rad är fyllda arbetsboken automatiskt bedömnings-API: n och visar poängsatta resultat.

    ![Indata för parametern och den resulterande förutse värden](./media/consuming-from-excel/sampletable.png)
5. För att bedöma mer än en rad, Fyll den andra raden med data och de förväntade värdena produceras. Du kan även klistra in flera rader på samma gång.

Du kan använda någon av funktionerna i Excel (diagram, power map, villkorsstyrd formatering, osv.) med de förväntade värdena för att visualisera data.

## <a name="sharing-your-workbook"></a>Dela din arbetsbok
Om makron ska fungera måste måste din API-nyckel ingå i kalkylbladet. Det innebär att du ska dela arbetsboken endast med entiteter/personer som du litar på.

## <a name="automatic-updates"></a>Automatiska uppdateringar
En RRS-anrop görs i de här två situationer:

1. Första gången en rad har innehållet i alla dess **parametrar**
2. Varje gång någon av de **parametrar** ändringar i en rad där alla dess **parametrar** har angett.