---
title: Webbtjänstloggning – Azure Machine Learning Studio (klassisk) | Microsoft-dokument
description: Läs om hur du aktiverar loggning för Machine Learning Studio (klassiska) webbtjänster. Loggning innehåller ytterligare information som hjälper dig att felsöka API:erna.
services: machine-learning
author: xiaoharper
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: c54d41e1-0300-46ef-bbfc-d6f7dca85086
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
ms.date: 06/15/2017
ms.openlocfilehash: 90e7692fe0e254074d8176d719d0ca9abad54a9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217851"
---
# <a name="enable-logging-for-azure-machine-learning-studio-classic-web-services"></a>Aktivera loggning för Azure Machine Learning Studio (klassiska) webbtjänster

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Det här dokumentet innehåller information om loggningsfunktionen i Machine Learning Studio (klassiska) webbtjänster. Loggning ger ytterligare information, utöver bara ett felnummer och ett meddelande, som kan hjälpa dig att felsöka dina samtal till Machine Learning Studio (klassiska) API:er.  

## <a name="how-to-enable-logging-for-a-web-service"></a>Aktivera loggning för en webbtjänst

Du aktiverar loggning från [Azure Machine Learning Studio -portalen (klassiskt) webbtjänster.](https://services.azureml.net) 

1. Logga in på Azure Machine Learning Studio -portalen (klassiskt) på [https://services.azureml.net](https://services.azureml.net). För en klassisk webbtjänst kan du också komma till portalen genom att klicka på **New Web Services Experience** på machine learning studio (klassisk) web services-sidan i Studio (klassisk).

   ![Länken Ny webbtjänstupplevelse](./media/web-services-logging/new-web-services-experience-link.png)

2. Klicka på **Webbtjänster** för en ny webbtjänst på den övre menyraden eller klicka på **Klassiska webbtjänster** för en klassisk webbtjänst.

   ![Välj Nya eller klassiska webbtjänster](./media/web-services-logging/select-web-service.png)

3. Klicka på webbtjänstnamnet för en ny webbtjänst. För en klassisk webbtjänst klickar du på webbtjänstnamnet och klickar sedan på rätt slutpunkt på nästa sida.

4. Klicka på **Konfigurera**på den övre menyraden .

5. Ange alternativet **Aktivera loggning** till *Fel* (för att bara logga fel) eller *Alla* (för fullständig loggning).

   ![Välj loggningsnivå](./media/web-services-logging/enable-logging.png)

6. Klicka på **Spara**.

7. Skapa **behållaren ml-diagnostik** för klassiska webbtjänster.

   Alla webbtjänstloggar lagras i en blob-behållare med namnet **ml-diagnostik** i lagringskontot som är associerat med webbtjänsten. För Nya webbtjänster skapas den här behållaren första gången du öppnar webbtjänsten. För klassiska webbtjänster måste du skapa behållaren om den inte redan finns. 

   1. Gå till det lagringskonto som är associerat med webbtjänsten i [Azure-portalen.](https://portal.azure.com)

   2. Under **Blob Service** klickar du på **Containers**.

   3. Om behållaren **ml-diagnostik** inte finns klickar du på **+Behållare**, ger behållaren namnet "ml-diagnostik" och väljer **åtkomsttypen** som "Blob". Klicka på **OK**.

      ![Skapa en ny behållare för att lagra diagnostikloggarna](./media/web-services-logging/create-ml-diagnostics-container.png)

> [!TIP]
>
> För en klassisk webbtjänst har Web Services Dashboard in Machine Learning Studio (klassisk) också en växel för loggning. Men eftersom loggning nu hanteras via Web Services-portalen måste du aktivera loggning via portalen enligt beskrivningen i den här artikeln. Om du redan har aktiverat loggning i Studio (klassisk) inaktiverar du loggning och aktiverar det igen i Web Services Portal.


## <a name="the-effects-of-enabling-logging"></a>Effekterna av att möjliggöra loggning
När loggning är aktiverat loggas diagnostiken och felen från slutpunkten för webbtjänsten i **ml-diagnostik blob-behållaren** i Azure Storage-kontot som är länkat till användarens arbetsyta. Den här behållaren innehåller all diagnostikinformation för alla webbtjänstslutpunkter för alla arbetsytor som är associerade med det här lagringskontot.

Loggarna kan visas med något av de flera verktyg som finns tillgängliga för att utforska ett Azure Storage-konto. Det enklaste kan vara att navigera till lagringskontot i Azure-portalen, klicka på **Behållare**och sedan klicka på behållaren **ml-diagnostik**.  

## <a name="log-blob-detail-information"></a>Information om loggblobbinformation
Varje blob i behållaren innehåller diagnostikinformationen för exakt en av följande åtgärder:

* En körning av metoden Batch-Execution  
* En körning av metoden Request-Response  
* Initiering av en behållare för begäran-svar

Namnet på varje blob har ett prefix i följande formulär: 


`{Workspace Id}-{Web service Id}-{Endpoint Id}/{Log type}`


Om _loggtyp_ är ett av följande värden:  

* batch  
* poäng/begäranden  
* poäng/init  

