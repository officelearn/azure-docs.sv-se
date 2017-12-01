---
title: "Skapa slutpunkter för webbtjänster i Machine Learning | Microsoft Docs"
description: "Skapa slutpunkter för webbtjänster i Azure Machine Learning"
services: machine-learning
documentationcenter: 
author: hiteshmadan
manager: padou
editor: cgronlun
ms.assetid: 4657fc1b-5228-4950-a29e-bc709259f728
ms.service: machine-learning
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 10/04/2016
ms.author: himad
ms.openlocfilehash: 6de83042779a1a4edae57499f108dcddc9d68309
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2017
---
# <a name="creating-endpoints"></a>Skapa slutpunkter
> [!NOTE]
>  Det här avsnittet beskrivs metoder som gäller för en **klassiska** Machine Learning-webbtjänst.
> 
> 

Du måste ange tränade modeller för varje kund som fortfarande är kopplade till experiment som webbtjänsten skapades från när du skapar webbtjänster som du säljer vidarebefordra till dina kunder. Dessutom tillämpas alla uppdateringar experimentet selektivt till en slutpunkt utan att skriva över anpassningar.

För att åstadkomma detta kan du skapa flera slutpunkter för en distribuerad webbtjänst i Azure Machine Learning. Varje slutpunkt i webbtjänsten är oberoende av varandra åtgärdas, begränsas och hanteras. Varje slutpunkt är en unik URL och auktoriseringsnyckeln som du kan distribuera till dina kunder.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="adding-endpoints-to-a-web-service"></a>Att lägga till slutpunkter till en webbtjänst
Det finns två sätt att lägga till en slutpunkt till en webbtjänst.

* Programmässigt
* Via portalen Azure Machine Learning-webbtjänster

När slutpunkten har skapats kan du använda via synkron API: er, batch-API: er, och excel-kalkylblad. Förutom att lägga till slutpunkter via Användargränssnittet, kan du också använda Endpoint Management-API: er att programmatiskt lägga till slutpunkter.

> [!NOTE]
> Om du har lagt till ytterligare slutpunkter webbtjänsten kan du inte ta bort standardslutpunkten.
> 
> 

## <a name="adding-an-endpoint-programmatically"></a>Lägga till en slutpunkt programmässigt
Du kan lägga till en slutpunkt för webbtjänsten via programmering med hjälp av [AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs) exempelkod.

## <a name="adding-an-endpoint-using-the-azure-machine-learning-web-services-portal"></a>Lägga till en slutpunkt med hjälp av Azure Machine Learning-webbtjänster-portalen
1. Klicka på webbtjänster på kolumnen navigeringen till vänster i Machine Learning Studio.
2. Längst ned i Web service instrumentpanelen klickar du på **hantera slutpunkter**. Azure Machine Learning-webbtjänster portal öppnar sidan slutpunkter för webbtjänsten.
3. Klicka på **Ny**.
4. Skriv ett namn och beskrivning för den nya slutpunkten. Slutpunkten namn måste vara 24 tecken eller mindre längd och måste bestå av gemena bokstäver eller siffror. Välj loggningsnivån och om exempeldata är aktiverad. Mer information om loggning finns [aktivera loggning för Machine Learning-webbtjänster](web-services-logging.md).

## <a name="next-steps"></a>Nästa steg
[Använda en Azure Machine Learning-webbtjänst](consume-web-services.md).

