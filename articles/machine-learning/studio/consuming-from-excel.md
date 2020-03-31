---
title: Använda webbtjänst i Excel
titleSuffix: ML Studio (classic) - Azure
description: Azure Machine Learning Studio (klassisk) gör det enkelt att ringa webbtjänster direkt från Excel utan att behöva skriva någon kod.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: 333ed411ab818cff77a7cba6c7de4f42c36f5b6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218218"
---
# <a name="consuming-an-azure-machine-learning-studio-classic-web-service-from-excel"></a>Använda en webbkameratjänst för Azure Machine Learning Studio (klassisk) från Excel

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Azure Machine Learning Studio (klassisk) gör det enkelt att ringa webbtjänster direkt från Excel utan att behöva skriva någon kod.

Om du använder Excel 2013 (eller senare) eller Excel Online rekommenderar vi att du använder [excel Excel-tillägget](excel-add-in-for-web-services.md).



## <a name="steps"></a>Steg
Publicera en webbtjänst. [Självstudiekurs 3: Distribuera kreditriskmodell](tutorial-part3-credit-risk-deploy.md) förklarar hur man gör det. För närvarande stöds Excel-arbetsboksfunktionen endast för tjänster för begäran/svar som har en enda utdata (det vill ha en enda poängetikett). 

När du har en webbtjänst klickar du på avsnittet **WEBBTJÄNSTER** till vänster om studion och väljer sedan den webbtjänst som ska förbrukas från Excel.

**Klassisk webbtjänst**

1. På fliken **INSTRUMENTPANEL** för webbtjänsten finns en rad för tjänsten **REQUEST/RESPONSE.** Om den här tjänsten hade en enda utdata bör länken **Hämta Excel-arbetsbok** visas på den raden.

    ![Ladda ned Excel-arbetsbok med hjälp av webbtjänstportalen Studio (klassiskt)](./media/consuming-from-excel/excellink.png)
2. Klicka på **Hämta Excel-arbetsbok**.

**Ny webbtjänst**

1. I webbtjänstportalen för Azure Machine Learning väljer du **Förbruka**.
2. Klicka på Excel-ikonen i avsnittet Alternativ för **förbrukningsalternativ** för webbtjänster på sidan Förbruka.

**Använda arbetsboken**

1. Öppna arbetsboken.
2. En säkerhetsvarning visas. klicka på knappen **Aktivera redigering.**

    ![Aktivera redigering för att ta bort säkerhetsvarningen för skyddad vy](./media/consuming-from-excel/enableeditting.png)
3. En säkerhetsvarning visas. Klicka på knappen **Aktivera innehåll** om du vill köra makron i kalkylbladet.

    ![Aktivera innehåll för att stänga av säkerhetsvarningen som inaktiverar makron](./media/consuming-from-excel/enablecontent.png)
4. När makron har aktiverats genereras en tabell. Kolumner i blått krävs som indata i RRS-webbtjänsten eller **PARAMETRAR**. Observera utgången av RRS-tjänsten, **FÖRVÄNTADE VÄRDEN** i grönt. När alla kolumner för en viss rad är fyllda anropar arbetsboken automatiskt poäng-API:et och visar de poängsatta resultaten.

    ![Tabell för parameterindata och de resulterande förväntade värdena](./media/consuming-from-excel/sampletable.png)
5. Om du vill göra mer än en rad fyller du den andra raden med data och de förväntade värdena produceras. Du kan även klistra in flera rader samtidigt.

Du kan använda någon av Excel-funktionerna (diagram, power map, villkorsstyrd formatering, etc.) med de förväntade värdena för att visualisera data.

## <a name="sharing-your-workbook"></a>Dela arbetsboken
För att makrona ska fungera måste API-nyckeln vara en del av kalkylbladet. Det innebär att du endast bör dela arbetsboken med entiteter/personer som du litar på.

## <a name="automatic-updates"></a>Automatiska uppdateringar
Ett RRS-samtal görs i dessa två situationer:

1. Första gången en rad har innehåll i alla sina **PARAMETRAR**
2. Varje gång någon av **parametrarna ändras** i en rad som hade alla dess **parametrar** in.