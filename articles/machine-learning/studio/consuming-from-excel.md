---
title: "Använda en Machine Learning-webbtjänst från Excel | Microsoft Docs"
description: "Använda en Azure Machine Learning-webbtjänst från Excel"
services: machine-learning
documentationcenter: 
author: tedway
manager: jhubbard
editor: cgronlun
ms.assetid: 3f3cdd2f-1816-487e-ab78-530e01e9788f
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/13/2017
ms.author: tedway
ms.openlocfilehash: 9a8b39853e1a90815758af1d8fd772db7cc18ac5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="consuming-an-azure-machine-learning-web-service-from-excel"></a>Använda en Azure Machine Learning-webbtjänst från Excel
 Azure Machine Learning Studio gör det enkelt att anropa webbtjänster direkt från Excel utan att behöva skriva någon kod.

Om du använder Excel 2013 (eller senare) eller Excel Online, så vi rekommenderar att du använder Excel [Excel-tillägg](excel-add-in-for-web-services.md).

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="steps"></a>Steg
Publicera en webbtjänst. [Den här sidan](walkthrough-5-publish-web-service.md) förklarar hur du gör. Funktionen Excel-arbetsboken stöds för närvarande endast för begäranden/svar-tjänsterna som har ett enda utflöde (det vill säga en enda bedömningsprofil etikett). 

När du har en webbtjänst kan klicka på den **WEB SERVICES** avsnittet till vänster om studio och väljer sedan webbtjänsten för att kunna använda från Excel.

**Klassiska webbtjänst**

1. På den **INSTRUMENTPANELEN** fliken för webbtjänsten är en rad för den **frågor och svar** service. Om den här tjänsten har ett enda utflöde, bör du se den **hämta Excel-arbetsbok** länk på den raden.
   
    ![][1]
2. Klicka på **hämta Excel-arbetsbok**.

**Ny webbtjänst**

1. Välj i Azure Machine Learning-webbtjänst-portal **förbruka**.
2. På sidan förbruka i den **Web-förbrukning alternativ** klickar du på ikonen för Excel.

**Använda arbetsboken**

1. Öppna arbetsboken.
2. En säkerhetsvarning visas. Klicka på den **Aktivera redigering av** knappen.
   
    ![][2]
3. En säkerhetsvarning visas. Klicka på den **Aktivera innehåll** för att köra makron i kalkylbladet.
   
    ![][3]
4. När makron är aktiverade, skapas en tabell. Kolumnerna i blå är obligatoriska som indata till webbtjänst Resursposter eller **parametrar**. Observera utdata från tjänsten Resursposter **FÖRUTSADE värden** i grönt. När alla kolumner för en viss rad fylls arbetsboken automatiskt bedömningsprofil API-anrop och visar poängsatta resultat.
   
    ![][4]
5. Fyll den andra raden med data och de förväntade värdena produceras för att poängsätta mer än en rad. Du kan även klistra in flera rader på samma gång.

Du kan använda någon av funktionerna i Excel (diagram, power karta, villkorsstyrd formatering, etc.) med de förväntade värdena för att visualisera data.    

## <a name="sharing-your-workbook"></a>Dela din arbetsbok
API-nyckel måste vara en del av kalkylbladet för makron ska fungera. Det innebär att du ska dela arbetsboken endast med entiteter/personer som du litar på.

## <a name="automatic-updates"></a>Automatiska uppdateringar
En RR-anrop i dessa två situationer:

1. Första gången en rad har innehåll i alla dess **parametrar**
2. När någon av de **parametrar** ändringar i en rad som har alla dess **parametrar** anges.

[1]: ./media/consuming-from-excel/excellink.png
[2]: ./media/consuming-from-excel/enableeditting.png
[3]: ./media/consuming-from-excel/enablecontent.png
[4]: ./media/consuming-from-excel/sampletable.png
