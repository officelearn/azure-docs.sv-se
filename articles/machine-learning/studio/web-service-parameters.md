---
title: Web Serviceparametrar i Azure Machine Learning Studio | Microsoft Docs
description: Hur du använder Azure Machine Learning Webbtjänstparametrar för att ändra funktionssättet för din modell vid åtkomst av webbtjänsten.
services: machine-learning
documentationcenter: ''
author: xiaoharper
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: c49187db-b976-4731-89d6-11a0bf653db1
ms.service: machine-learning
ms.subservice: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/12/2017
ms.openlocfilehash: a236043d5622e5a2e1ffd572c887fb5ffac2174a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60345455"
---
# <a name="use-azure-machine-learning-studio-web-service-parameters"></a>Använda webbtjänstparametrar för Azure Machine Learning Studio
En Azure Machine Learning-webbtjänst skapas genom att publicera ett experiment som innehåller moduler med konfigurerbara parametrar. I vissa fall kan du ändra beteendet modulen medan webbtjänsten körs. *Web tjänstparametrar* gör att du kan göra detta. 

Ett vanligt exempel att konfigurera den [importdata] [ reader] modulen så att användaren publicerade webbtjänsten kan ange en annan datakälla när webbtjänsten används. Eller konfigurera den [exportera Data] [ writer] modulen så att du kan ange ett annat mål. Några andra exempel är att ändra antalet bitar för den [funktions-hashning] [ feature-hashing] modul eller antalet önskade funktioner för den [Filter-baserade Funktionsurval] [ filter-based-feature-selection] modulen. 

Du kan ange Webbtjänstparametrar och koppla dem till en eller flera parametrar i ditt experiment, och du kan ange om de är obligatoriska eller valfria. Användaren av webbtjänsten kan sedan ange värden för dessa parametrar när de anropar webbtjänsten. 



## <a name="how-to-set-and-use-web-service-parameters"></a>Hur du ställer och använda Webbtjänstparametrar
Du kan definiera en Web Service-Parameter genom att klicka på ikonen bredvid parametern för en modul och välja ”Ange som web service parameter”. Detta skapar en ny Web Service Parameter och ansluter den till modulen parametern. Sedan när webbtjänsten används användaren kan ange ett värde för parametrarna för webbtjänsten och det används i parametern modulen.

När du har definierat en Web Service-Parameter är det tillgängligt för andra modulen parametrar i experimentet. Om du definierar en Web Service-Parameter som är associerade med en parameter för en modul kan du använda samma Web Service parametern för som andra moduler så länge parametern förväntar sig av samma typ av värde. Till exempel om parametrarna för webbtjänsten är ett numeriskt värde, kan sedan den endast användas för modulparametrar som förväntar sig ett numeriskt värde. När användaren anger ett värde för parametern Web Service, tillämpas den på alla associerade modulparametrar.

Du kan bestämma om du vill ange ett standardvärde för parametrarna för webbtjänsten. Om du gör sedan är parametern valfri för användaren av webbtjänsten. Om du inte anger ett standardvärde krävs användaren att ange ett värde vid åtkomst av webbtjänsten.

API-dokumentationen för webbtjänsten innehåller information för web service användaren om hur du anger parametrarna för webbtjänsten programmässigt vid åtkomst till webbtjänsten.

> [!NOTE]
> API-dokumentationen för en klassisk webbtjänst är tillgängligt via den **API-hjälpsidan** länken i webbtjänsten **INSTRUMENTPANELEN** i Machine Learning Studio. API-dokumentationen för en ny webbtjänst är tillgängligt via den [Azure Machine Learning Web Services](https://services.azureml.net/Quickstart) portalen på den **förbruka** och **Swagger API** sidor för webbplatsen tjänsten.
> 
> 

## <a name="example"></a>Exempel
Exempelvis kan vi antar att vi har ett experiment med en [exportera Data] [ writer] modul som skickar information till Azure blob storage. Vi ska definiera en Web Service-Parameter med namnet ”Blob sökvägen” som gör att web service-användaren kan ändra sökvägen till blob-lagringen när tjänsten används.

1. I Machine Learning Studio, klickar du på den [exportera Data] [ writer] modulen att välja den. Egenskaperna visas i fönstret egenskaper till höger om arbetsytan för experimentet.
2. Ange vilken lagringstyp:
   
   * Under **ange datamålet**, Välj ”Azure Blob Storage”.
   * Under **ange autentiseringstyp**, Välj ”konto”.
   * Ange kontoinformationen för Azure blob storage. 

3. Klicka på ikonen till höger om den **sökväg till blob som börjar med behållare parametern**. Det ser ut så här:
   
   ![Web Service-parametern ikon](./media/web-service-parameters/icon.png)
   
   Välj ”ange som web service parameter”.
   
   En post läggs till under **Webbtjänstparametrar** längst ned i fönstret Egenskaper med namnet ”sökväg till blob som börjar med behållare”. Det här är parametrarna för webbtjänsten som nu finns som är associerade med den här [exportera Data] [ writer] modulen parametern.
4. Att byta namn på parametrarna för webbtjänsten, klickar du på namnet, anger du ”Blob sökvägen”, och tryck på den **RETUR** nyckel. 
5. Om du vill ange ett standardvärde för parametern Web Service, klickar du på ikonen till höger om namnet, Välj ”ge standardvärdet”, ange ett värde (till exempel ”container1/output1.csv”), och tryck på den **RETUR** nyckel.
   
   ![Web Service-Parameter](./media/web-service-parameters/parameter.png)
6. Klicka på **Run** (Kör). 
7. Klicka på **distribuera webbtjänsten** och välj **distribuera webbtjänsten [klassisk]** eller **distribuera webbtjänsten [nyhet]** att distribuera webbtjänsten.

> [!NOTE] 
> Om du vill distribuera en ny webbtjänst måste du ha tillräcklig behörighet i prenumerationen som du distribuerar webbtjänsten. Mer information finns i [hantera en webbtjänst med hjälp av Azure Machine Learning Web Services-portalen](manage-new-webservice.md). 

Användaren av webbtjänsten kan nu ange ett nytt mål för den [exportera Data] [ writer] modulen vid åtkomst till webbtjänsten.

## <a name="more-information"></a>Mer information
Ett mer detaljerat exempel finns i [Webbtjänstparametrar](https://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx) post i den [Machine Learning Blog](https://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx).

Mer information om hur du använder en Machine Learning-webbtjänst finns i [hur du använder en Azure Machine Learning-webbtjänst](consume-web-services.md).

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[writer]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/

