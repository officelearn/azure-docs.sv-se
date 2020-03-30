---
title: Parametrar för webbtjänster – Azure Machine Learning Studio (klassisk) | Microsoft-dokument
description: Så här använder du Azure Machine Learning Web Service-parametrar för att ändra beteendet för din modell när webbtjänsten används.
services: machine-learning
author: xiaoharper
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: c49187db-b976-4731-89d6-11a0bf653db1
ms.service: machine-learning
ms.subservice: studio
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/12/2017
ms.openlocfilehash: d6ddd9603f22bd3820d18be020b9c620cf06aa42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204417"
---
# <a name="use-azure-machine-learning-studio-classic-web-service-parameters"></a>Använda azure machine learning studio (klassiska) webbtjänstparametrar

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

En Azure Machine Learning-webbtjänst skapas genom att publicera ett experiment som innehåller moduler med konfigurerbara parametrar. I vissa fall kanske du vill ändra modulbeteendet medan webbtjänsten körs. *Med webbtjänstparametrar* kan du utföra den här uppgiften. 

Ett vanligt exempel är att ställa in modulen [Importera data][reader] så att användaren av den publicerade webbtjänsten kan ange en annan datakälla när webbtjänsten används. Eller konfigurera modulen [Exportera data][writer] så att ett annat mål kan anges. Några andra exempel är att ändra antalet bitar för [modulen Funktionshage][feature-hashing] eller antalet önskade funktioner för modulen [Filterbaserad funktionsval.][filter-based-feature-selection] 

Du kan ange webbtjänstparametrar och associera dem med en eller flera modulparametrar i experimentet, och du kan ange om de krävs eller är valfria. Användaren av webbtjänsten kan sedan ange värden för dessa parametrar när de anropar webbtjänsten. 



## <a name="how-to-set-and-use-web-service-parameters"></a>Så här ställer du in och använder webbtjänstparametrar
Du definierar en webbtjänstparameter genom att klicka på ikonen bredvid parametern för en modul och välja "Ange som webbtjänstparameter". Detta skapar en ny webbtjänstparameter och ansluter den till den modulparametern. När webbtjänsten används kan användaren ange ett värde för parametern Web Service och tillämpas på modulparametern.

När du har definierat en webbtjänstparameter är den tillgänglig för alla andra modulparameter i experimentet. Om du definierar en webbtjänstparameter som är associerad med en parameter för en modul kan du använda samma webbtjänstparameter för en annan modul, så länge parametern förväntar sig samma typ av värde. Om parametern Web Service till exempel är ett numeriskt värde kan den bara användas för modulparametrar som förväntar sig ett numeriskt värde. När användaren anger ett värde för webbtjänstparametern tillämpas det på alla associerade modulparametrar.

Du kan bestämma om du vill ange ett standardvärde för parametern för webbtjänst. Om du gör det är parametern valfri för användaren av webbtjänsten. Om du inte anger något standardvärde måste användaren ange ett värde när webbtjänsten används.

API-dokumentationen för webbtjänsten innehåller information för webbtjänstanvändaren om hur webbtjänstparametern ska anges programmässigt när webbtjänsten används.

> [!NOTE]
> API-dokumentationen för en klassisk webbtjänst tillhandahålls via **api-hjälpsidan** i webbtjänsten **DASHBOARD** i Machine Learning Studio (klassisk). API-dokumentationen för en ny webbtjänst tillhandahålls via Azure [Machine Learning Web Services-portalen](https://services.azureml.net/Quickstart) på **API-sidorna För använda** och **swagger** för din webbtjänst.
> 
> 

## <a name="example"></a>Exempel
Anta att vi har ett experiment med en [exportdatamodul][writer] som skickar information till Azure blob storage. Vi definierar en webbtjänstparameter med namnet "Blob-sökväg" som gör att webbtjänstanvändaren kan ändra sökvägen till blob-lagringen när tjänsten används.

1. I Machine Learning Studio (klassisk) klickar du på modulen [Exportera data][writer] för att markera den. Dess egenskaper visas i fönstret Egenskaper till höger om experimentarbetsytan.
2. Ange lagringstyp:
   
   * Under **Ange datamål**väljer du "Azure Blob Storage".
   * Under **Ange autentiseringstyp**väljer du "Konto".
   * Ange kontoinformation för Azure blob storage. 

3. Klicka på ikonen till höger om **sökvägen till blob som börjar med behållarparametern**. Så här ser den ut:
   
   ![Ikon för parametern för webbtjänst](./media/web-service-parameters/icon.png)
   
   Välj "Ange som webbtjänstparameter".
   
   En post läggs till under **Webbtjänstparametrar** längst ned i egenskapsfönstret med namnet "Sökväg till blob som börjar med behållaren". Det här är parametern för webbtjänst som nu är associerad med den här exportdatamodulparametern. [Export Data][writer]
4. Om du vill byta namn på parametern för webbtjänsten klickar du på namnet, skriver "Blob-sökväg" och trycker på **Retur.** 
5. Om du vill ange ett standardvärde för parametern Web Service klickar du på ikonen till höger om namnet, väljer "Ange standardvärde", anger ett värde (till exempel "container1/output1.csv" och trycker på **Retur.**
   
   ![Parameter för webbtjänst](./media/web-service-parameters/parameter.png)
6. Klicka på **Kör**. 
7. Klicka på **Distribuera webbtjänst** och välj **Distribuera webbtjänst [Klassisk]** eller **Distribuera webbtjänst [Ny]** för att distribuera webbtjänsten.

> [!NOTE] 
> Om du vill distribuera en ny webbtjänst måste du ha tillräcklig behörighet i prenumerationen som du distribuerar webbtjänsten till. Mer information finns i [Hantera en webbtjänst med hjälp av Portalen för Azure Machine Learning Web Services](manage-new-webservice.md). 

Användaren av webbtjänsten kan nu ange ett nytt mål för modulen [Exportera data][writer] när du öppnar webbtjänsten.

## <a name="more-information"></a>Mer information
Ett mer detaljerat exempel finns i posten [Parametrar för webbtjänst](https://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx) i [machine learning-bloggen](https://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx).

Mer information om hur du använder en machine learning-webbtjänst finns i [Så här använder du en Azure Machine Learning Web-tjänst](consume-web-services.md).

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[writer]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/

