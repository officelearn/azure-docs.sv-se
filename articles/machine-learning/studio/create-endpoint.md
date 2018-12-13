---
title: Skapa slutpunkter för webbtjänster – Azure Machine Learning Studio | Microsoft Docs
description: Skapa slutpunkter för webbtjänster i Azure Machine Learning. Varje slutpunkt i webbtjänsten är oberoende av varandra åtgärdas, begränsas och hanteras.
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: 4657fc1b-5228-4950-a29e-bc709259f728
ms.service: machine-learning
ms.component: studio
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 10/04/2016
ms.openlocfilehash: 58418357d6e96204485887cca5d7724c42183db7
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53275898"
---
# <a name="creating-endpoints-for-deployed-azure-machine-learning-studio-web-services"></a>Skapa slutpunkter för distribuerade Azure Machine Learning Studio-webbtjänster
> [!NOTE]
>  Det här avsnittet beskrivs tekniker som kan användas till en **klassiska** Machine Learning-webbtjänster.
> 
> 

När du skapar webbtjänster som du säljer framåt till dina kunder kan behöva du tillhandahålla anpassade modeller till kunder som fortfarande är länkade till arbetsytan för experimentet som webbtjänsten har skapats. Dessutom bör eventuella uppdateringar till arbetsytan för experimentet tillämpas selektivt på en slutpunkt utan att skriva över anpassningarna.

För att åstadkomma detta kan Azure Machine Learning Studio du skapa flera slutpunkter för en distribuerad webbtjänst. Varje slutpunkt i webbtjänsten är oberoende av varandra åtgärdas, begränsas och hanteras. Varje slutpunkt är en unik URL och auktoriseringsnyckeln som du kan distribuera till dina kunder.



## <a name="adding-endpoints-to-a-web-service"></a>Lägga till slutpunkter till en webbtjänst
Det finns två sätt att lägga till en slutpunkt i en webbtjänst.

* Programmässigt
* Via Azure Machine Learning Web Services-portalen

När slutpunkten har skapats kan du använda via synkron API: er, batch API: er, och excel-kalkylblad. Förutom att lägga till slutpunkter via det här Användargränssnittet kan använda du också Management-API: er för slutpunkten att programmatiskt lägga till slutpunkter.

> [!NOTE]
> Om du har lagt till ytterligare slutpunkter till webbtjänsten kan du inte ta bort standardslutpunkten.
> 
> 

## <a name="adding-an-endpoint-programmatically"></a>Att lägga till en slutpunkt programmässigt
Du kan lägga till en slutpunkt för webbtjänsten via programmering med hjälp av den [AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs) exempelkoden.

## <a name="adding-an-endpoint-using-the-azure-machine-learning-web-services-portal"></a>Att lägga till en slutpunkt med hjälp av Azure Machine Learning Web Services-portalen
1. Klicka på webbtjänster i Machine Learning Studio, på den vänstra kolumnen.
2. Längst ned i instrumentpanelen för webbtjänsten klickar du på **hantera slutpunkter för**. Azure Machine Learning Web Services-portalen öppnas på sidan slutpunkter för webbtjänsten.
3. Klicka på **Ny**.
4. Skriv ett namn och beskrivning för den nya slutpunkten. Slutpunktsnamn måste vara 24 tecken eller mindre långt och måste bestå av gemena bokstäver eller siffror. Välj loggningsnivån och om exempeldata är aktiverad. Mer information om loggning finns i [aktivera loggning för Machine Learning Web services](web-services-logging.md).

## <a name="next-steps"></a>Nästa steg
[Hur du använder en Azure Machine Learning-webbtjänst](consume-web-services.md).

