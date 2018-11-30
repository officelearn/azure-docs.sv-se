---
title: Använda en Machine Learning Studio-webbtjänst från Excel - Azure Machine Learning Studio | Microsoft Docs
description: Azure Machine Learning Studio gör det enkelt att anropa webbtjänster direkt från Excel utan att behöva skriva någon kod.
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=yahajiza, author=YasinMSFT)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: 3f3cdd2f-1816-487e-ab78-530e01e9788f
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/1/2018
ms.openlocfilehash: 7084e62df3cd4872d90661ad6b94e1ebf7b54d8d
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2018
ms.locfileid: "52312761"
---
# <a name="consuming-an-azure-machine-learning-web-service-from-excel"></a>Använda en Azure Machine Learning-webbtjänst från Excel
 Azure Machine Learning Studio gör det enkelt att anropa webbtjänster direkt från Excel utan att behöva skriva någon kod.

Om du använder Excel 2013 (eller senare) eller Excel Online, så vi rekommenderar att du använder Excel [Excel-tillägg](excel-add-in-for-web-services.md).

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="steps"></a>Steg
Publicera en webbtjänst. [Den här sidan](walkthrough-5-publish-web-service.md) förklarar hur du gör. Excel-arbetsbok funktionen stöds för närvarande endast för begäran/svar-tjänster som har ett enda utflöde (det vill säga en enda bedömnings-etiketten). 

När du har en webbtjänst, klickar du på den **WEBBTJÄNSTER** till vänster i studio och välj sedan webbtjänsten för att använda från Excel.

**Klassisk webbtjänst**

1. På den **INSTRUMENTPANELEN** fliken för webbtjänsten är en rad för den **begäran/svar** service. Om den här tjänsten har haft ett enda utflöde, bör du se den **ladda ned Excel-arbetsbok** länk på den raden.
   
    ![][1]
2. Klicka på **ladda ned Excel-arbetsbok**.

**Ny webbtjänst**

1. I portalen Azure Machine Learning-webbtjänsten väljer **förbruka**.
2. På sidan använda i den **Web service förbrukning alternativ** klickar du på ikonen för Excel.

**Använda arbetsboken**

1. Öppna arbetsboken.
2. En säkerhetsvarning visas. Klicka på den **Aktivera redigering** knappen.
   
    ![][2]
3. En säkerhetsvarning visas. Klicka på den **Aktivera innehåll** för att köra makron i kalkylbladet.
   
    ![][3]
4. När makron är aktiverade, skapas en tabell. Kolumnerna i blå är obligatoriska som indata till webbtjänsten RRS eller **parametrar**. Observera utdata för RRS-tjänsten **FÖRUTSE värden** i grönt. När alla kolumner för en viss rad är fyllda arbetsboken automatiskt bedömnings-API: n och visar poängsatta resultat.
   
    ![][4]
5. För att bedöma mer än en rad, Fyll den andra raden med data och de förväntade värdena produceras. Du kan även klistra in flera rader på samma gång.

Du kan använda någon av funktionerna i Excel (diagram, power map, villkorsstyrd formatering, osv.) med de förväntade värdena för att visualisera data.    

## <a name="sharing-your-workbook"></a>Dela din arbetsbok
Om makron ska fungera måste måste din API-nyckel ingå i kalkylbladet. Det innebär att du ska dela arbetsboken endast med entiteter/personer som du litar på.

## <a name="automatic-updates"></a>Automatiska uppdateringar
En RRS-anrop görs i de här två situationer:

1. Första gången en rad har innehållet i alla dess **parametrar**
2. Varje gång någon av de **parametrar** ändringar i en rad där alla dess **parametrar** har angett.

[1]: ./media/consuming-from-excel/excellink.png
[2]: ./media/consuming-from-excel/enableeditting.png
[3]: ./media/consuming-from-excel/enablecontent.png
[4]: ./media/consuming-from-excel/sampletable.png
