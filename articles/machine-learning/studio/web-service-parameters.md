---
title: Använda Azure Machine Learning Webbtjänstparametrar | Microsoft Docs
description: Hur du använder Azure Machine Learning Webbtjänstparametrar för att ändra funktionssättet för din modell vid åtkomst av webbtjänsten.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: c49187db-b976-4731-89d6-11a0bf653db1
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2017
ms.openlocfilehash: 91b3c9df8a7fd0e1abb79c21b1e1d833e57c24d5
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34835934"
---
# <a name="use-azure-machine-learning-web-service-parameters"></a>Använda parametrar för Azure Machine Learning-webbtjänst
En Azure Machine Learning-webbtjänst skapas genom att publicera ett experiment som innehåller moduler med parametrar. I vissa fall kanske du vill ändra modulen beteendet när webbtjänsten körs. *Webbtjänstparametrar* gör att du kan göra detta. 

Ett vanligt exempel att konfigurera den [importera Data] [ reader] modulen så att användaren av webbtjänsten för publicerade kan ange en annan datakälla när webbtjänsten används. Eller konfigurera den [exportera Data] [ writer] modulen så att du kan ange ett annat mål. Andra exempel är att ändra antalet bitar för den [funktions-hashning] [ feature-hashing] modul eller antalet önskade funktioner för den [Filter-baserade Funktionsurval] [ filter-based-feature-selection] modul. 

Du kan ange Webbtjänstparametrar och koppla dem till en eller flera parametrar i experimentet och du kan ange om de är obligatoriska eller valfria. Användaren av webbtjänsten kan sedan ge värdena för dessa parametrar när de anropa webbtjänsten. 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="how-to-set-and-use-web-service-parameters"></a>Ställa in och använda Webbtjänstparametrar
Du kan definiera en Web Service-Parameter genom att klicka på ikonen bredvid parametern för en modul och välja ”Ange som web service parameter”. Detta skapar en ny Parameter i Web Service och ansluter till den modul-parametern. Sedan, när webbtjänsten används användaren kan ange ett värde för parametern Web Service och den tillämpas på parametern modulen.

När du definierar en Web Service-Parameter, går det att andra modulen parameter i experimentet. Om du definierar en Web Service-Parameter som är associerade med en parameter för en modul, kan du använda samma Web Service parametern för en annan modul som parametern förväntar sig samma typ av värde. Till exempel om parametrarna för webbtjänsten är ett numeriskt värde, kan sedan den bara användas för parametrar som förväntar sig ett numeriskt värde. När användaren anger ett värde för parametern Web Service, tillämpas den på alla associerade parametrar.

Du kan bestämma om du vill ange ett standardvärde för parametrarna för webbtjänsten. Om du gör sedan är parametern valfri för användaren av webbtjänsten. Om du inte anger ett standardvärde krävs användaren att ange ett värde vid åtkomst av webbtjänsten.

API-dokumentationen för webbtjänsten innehåller information för web Serviceanvändare om hur du anger parametrarna för webbtjänsten programmässigt vid åtkomst till webbtjänsten.

> [!NOTE]
> API-dokumentationen för det klassiska tillhandahålls via den **API hjälpsidan** länken i webbtjänsten **INSTRUMENTPANELEN** i Machine Learning Studio. API-dokumentationen för en ny webbtjänst tillhandahålls via den [Azure Machine Learning-webbtjänster](https://services.azureml.net/Quickstart) Företagsportalen på den **förbruka** och **Swagger API** sidor för webbtjänsten.
> 
> 

## <a name="example"></a>Exempel
Ett exempel antar vi att vi har ett experiment med en [exportera Data] [ writer] modul som skickar information till Azure blob storage. Ska vi definiera Web Service Parameter med namnet ”Blob sökvägen” som gör att web service användaren kan ändra sökvägen till blob-lagring när tjänsten används.

1. I Machine Learning Studio klickar du på den [exportera Data] [ writer] modulen så att den markeras. Egenskaperna som visas i fönstret egenskaper till höger om arbetsytan för experimentet.
2. Ange vilken lagringstyp:
   
   * Under **ange datamål**, Välj ”Azure Blob Storage”.
   * Under **ange autentiseringstypen**, Välj ”konto”.
   * Ange kontoinformationen för Azure blob storage. 

3. Klicka på ikonen till höger om den **sökväg till blob som börjar med behållaren parametern**. Det ser ut så här:
   
   ![Web Service-parametern ikonen][icon]
   
   Välj ”ange som web service parameter”.
   
   En post läggs till under **Webbtjänstparametrar** längst ned i fönstret Egenskaper med namnet ”sökväg till blob som börjar med behållaren”. Detta är parametrarna för webbtjänsten som nu är associerade med den här [exportera Data] [ writer] modulen parametern.
4. Att byta namn på parametrarna för webbtjänsten, klickar du på namnet, anger du ”blobbsökvägen”, och tryck på den **RETUR** nyckel. 
5. Om du vill ange ett standardvärde för parametern Web Service, klickar du på ikonen till höger om namnet väljer ”ge standardvärdet”, ange ett värde (till exempel ”container1/output1.csv”), och tryck på den **RETUR** nyckel.
   
   ![Web Service-Parameter][parameter]
6. Klicka på **Run** (Kör). 
7. Klicka på **distribuera webbtjänsten** och välj **distribuera webbtjänsten [klassisk]** eller **distribuera webbtjänsten [ny]** att distribuera webbtjänsten.

> [!NOTE] 
> Om du vill distribuera en ny webbtjänst måste du ha tillräckliga behörigheter i prenumerationen som du distribuerar webbtjänsten. Mer information finns i [hantera en webbtjänst med hjälp av Azure Machine Learning-webbtjänster portal](manage-new-webservice.md). 

Användaren av webbtjänsten kan nu ange ett nytt mål för den [exportera Data] [ writer] modulen vid åtkomst till webbtjänsten.

## <a name="more-information"></a>Mer information
En mer detaljerad exempel finns i [Webbtjänstparametrar](http://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx) post i den [Machine Learning blogg](http://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx).

Mer information om hur du använder en Machine Learning-webbtjänst finns [använda en Azure Machine Learning-webbtjänst](consume-web-services.md).

<!-- Images -->
[icon]: ./media/web-service-parameters/icon.png
[parameter]: ./media/web-service-parameters/parameter.png


<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[writer]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/

