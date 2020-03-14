---
title: Webb tjänst parametrar – Azure Machine Learning Studio (klassisk) | Microsoft Docs
description: Hur du använder Azure Machine Learning Webbtjänstparametrar för att ändra funktionssättet för din modell vid åtkomst av webbtjänsten.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79204417"
---
# <a name="use-azure-machine-learning-studio-classic-web-service-parameters"></a>Använd Azure Machine Learning Studio (klassiska) webb tjänst parametrar

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

En Azure Machine Learning-webbtjänst skapas genom att publicera ett experiment som innehåller moduler med konfigurerbara parametrar. I vissa fall kan du ändra beteendet modulen medan webbtjänsten körs. Med *webb tjänst parametrar* kan du göra det här. 

Ett vanligt exempel är att konfigurera modulen [Importera data][reader] så att användaren av den publicerade webb tjänsten kan ange en annan data källa när webb tjänsten nås. Eller konfigurera modulen [Exportera data][writer] så att du kan ange ett annat mål. Några andra exempel är att ändra antalet bitar för modulens [hash][feature-hashing] -modul eller antalet önskade funktioner för modulen för [filtrerings-baserade funktions val][filter-based-feature-selection] . 

Du kan ange Webbtjänstparametrar och koppla dem till en eller flera parametrar i ditt experiment, och du kan ange om de är obligatoriska eller valfria. Användaren av webbtjänsten kan sedan ange värden för dessa parametrar när de anropar webbtjänsten. 



## <a name="how-to-set-and-use-web-service-parameters"></a>Hur du ställer och använda Webbtjänstparametrar
Du kan definiera en Web Service-Parameter genom att klicka på ikonen bredvid parametern för en modul och välja ”Ange som web service parameter”. Detta skapar en ny Web Service Parameter och ansluter den till modulen parametern. Sedan när webbtjänsten används användaren kan ange ett värde för parametrarna för webbtjänsten och det används i parametern modulen.

När du har definierat en Web Service-Parameter är det tillgängligt för andra modulen parametrar i experimentet. Om du definierar en Web Service-Parameter som är associerade med en parameter för en modul kan du använda samma Web Service parametern för som andra moduler så länge parametern förväntar sig av samma typ av värde. Till exempel om parametrarna för webbtjänsten är ett numeriskt värde, kan sedan den endast användas för modulparametrar som förväntar sig ett numeriskt värde. När användaren anger ett värde för parametern Web Service, tillämpas den på alla associerade modulparametrar.

Du kan bestämma om du vill ange ett standardvärde för parametrarna för webbtjänsten. Om du gör sedan är parametern valfri för användaren av webbtjänsten. Om du inte anger ett standardvärde krävs användaren att ange ett värde vid åtkomst av webbtjänsten.

API-dokumentationen för webbtjänsten innehåller information för web service användaren om hur du anger parametrarna för webbtjänsten programmässigt vid åtkomst till webbtjänsten.

> [!NOTE]
> API-dokumentationen för en klassisk webb tjänst tillhandahålls via länken **API-hjälp sida** på **instrument panelen** för webb tjänster i Machine Learning Studio (klassisk). API-dokumentationen för en ny webb tjänst tillhandahålls via [Azure Machine Learning Web Services-](https://services.azureml.net/Quickstart) portalen på API-sidorna **konsumera** och **Swagger** för din webb tjänst.
> 
> 

## <a name="example"></a>Exempel
Vi kan till exempel anta att vi har ett experiment med en modul för [export av data][writer] som skickar information till Azure Blob Storage. Vi ska definiera en Web Service-Parameter med namnet ”Blob sökvägen” som gör att web service-användaren kan ändra sökvägen till blob-lagringen när tjänsten används.

1. I Machine Learning Studio (klassisk) klickar du på modulen [Exportera data][writer] för att markera den. Egenskaperna visas i fönstret egenskaper till höger om arbetsytan för experimentet.
2. Ange vilken lagringstyp:
   
   * Under **ange data mål väljer du**Azure Blob Storage.
   * Under **Ange Autentiseringstyp väljer du**"konto".
   * Ange kontoinformationen för Azure blob storage. 

3. Klicka på ikonen till höger om **sökvägen till bloben som börjar med container parametern**. Det ser ut så här:
   
   ![Web Service-parametern ikon](./media/web-service-parameters/icon.png)
   
   Välj ”ange som web service parameter”.
   
   En post läggs till under **webb tjänst parametrar** längst ned i fönstret Egenskaper med namnet "path to BLOB Start with container". Det här är den webb tjänst parameter som nu är associerad med den här parametern för [export data][writer] modul.
4. Om du vill byta namn på parametern för webb tjänsten klickar du på namnet, anger "blobb Sök väg" och trycker på **RETUR** -tangenten. 
5. Ange ett standardvärde för webb tjänst parametern genom att klicka på ikonen till höger om namnet, välj "Ange standardvärde", ange ett värde (till exempel "container1/output1. csv") och tryck på **RETUR** -tangenten.
   
   ![Web Service-Parameter](./media/web-service-parameters/parameter.png)
6. Klicka på **Run** (Kör). 
7. Klicka på **distribuera webb tjänst** och välj **distribuera webb tjänst [klassisk]** eller **distribuera webb tjänst [ny]** för att distribuera webb tjänsten.

> [!NOTE] 
> Om du vill distribuera en ny webbtjänst måste du ha tillräcklig behörighet i prenumerationen som du distribuerar webbtjänsten. Mer information finns i [hantera en webb tjänst med hjälp av Azure Machine Learning Web Services-portalen](manage-new-webservice.md). 

Användaren av webb tjänsten kan nu ange ett nytt mål för modulen [Exportera data][writer] vid åtkomst till webb tjänsten.

## <a name="more-information"></a>Mer information
Ett mer detaljerat exempel finns i posten [Web Service Parameters](https://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx) i [Machine Learning blogg](https://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx).

Mer information om hur du kommer åt en Machine Learning-webbtjänst finns i [så här använder du en Azure Machine Learning-webb tjänst](consume-web-services.md).

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[writer]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/

