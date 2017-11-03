---
title: "Flera Geo hjälpdokumentationen | Microsoft Docs"
description: "Lär dig hur du skapar en arbetsyta och publicera en webbtjänst i en Azure-region olika från södra centrala USA (SCUS) Azure-region."
services: machine-learning
documentationcenter: 
author: tedway
manager: jhubbard
editor: rmca14
tags: 
ms.assetid: ed0ca8a8-fa53-4e56-b824-2d7e44641967
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/6/2017
ms.author: tedway; neerajkh
ms.openlocfilehash: e4941ccf8c6d7a0c77527e9c1d722bc3a770114a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="multi-geo-help-documentation"></a>Hjälpdokumentation för flera geografiska platser
Den här artikeln beskriver hur du kan skapa en arbetsyta och publicera en webbtjänst i olika Azure-regioner.  Den [Azure produkter efter Region sidan](https://azure.microsoft.com/en-us/regions/services/) visar en lista över regioner där Azure Machine Learning är tillgängligt.

## <a name="create-a-workspace"></a>Skapa en arbetsyta
1. Logga in på den klassiska Azure-portalen.
2. Klicka på **+ ny** > **DATATJÄNSTER** > **MASKININLÄRNING** > **SNABBREGISTRERING**.  Under **plats** välja en annan region, till exempel **Sydostasien**.
   ![Flera Geo hjälpa bild 1][1]
3. Välj arbetsytan och klicka sedan på **logga in på ML Studio**.
   ![Flera Geo hjälpa bild 2][2]
4. Nu har du en arbetsyta i en annan region som du kan använda precis som andra arbetsytan. Om du vill växla mellan arbetsytorna ser du att det övre högra hörnet av skärmen. Klicka på listrutan, väljer du regionen och markera arbetsytan. Allt är lokala för den arbetsyta regionen.  Till exempel blir alla webbtjänster som skapas från en arbetsyta i samma region arbetsytan finns i.
   ![Flera Geo hjälpa bild 3][3]

## <a name="open-an-experiment-from-gallery"></a>Öppna ett experiment från galleriet
Om du öppnar ett experiment från galleriet måste välja du också vilken region som du vill kopiera experimentet.

![Flera Geo hjälpa bild 4][4a]

## <a name="web-service-management"></a>Webbtjänsthantering
Använd regionspecifika adress för programmässig hantering webbtjänster för omtränings såsom:

* https://asiasoutheast.Management.azureml.NET
* https://europewest.Management.azureml.NET

### <a name="things-to-note"></a>Saker att Observera
1. Du kan bara kopiera experiment mellan arbetsytor som tillhör samma region som det här sättet. Om du måste kopiera experiment över arbetsytor i olika regioner, kan du använda den [PowerShell](http://aka.ms/amlps) kommandot [ *kopiera AmlExperiment* ](https://github.com/hning86/azuremlps/blob/master/README.md#copy-amlexperiment) att göra det. En annan lösning är att publicera experiment i galleriet i olistade läge, öppna den i arbetsytan från andra regionen.
2. Region selector visar endast arbetsytor för en region i taget.  
3. En kostnadsfri arbetsyta eller gästbehörighet (anonym) arbetsytan ska skapas och hanteras i södra centrala USA  
4. Webbtjänster som distribueras från en arbetsyta i Sydostasien ska också finnas i Sydostasien.  

## <a name="more-information"></a>Mer information
Ställ en fråga på den [Azure Machine Learning-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=MachineLearning).

<!--Image references-->
[1]: ./media/multi-geo/multi-geo_1.png
[2]: ./media/multi-geo/multi-geo_2.png
[3]: ./media/multi-geo/multi-geo_3.png
[4a]: ./media/multi-geo/multi-geo_4a.png
