---
title: Loggning av webb tjänst – Azure Machine Learning Studio (klassisk) | Microsoft Docs
description: 'Lär dig hur du aktiverar loggning för Machine Learning Studio (klassiska) webb tjänster. Loggning ger ytterligare information för felsökning av API: erna.'
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79217851"
---
# <a name="enable-logging-for-azure-machine-learning-studio-classic-web-services"></a>Aktivera loggning för webb tjänster för Azure Machine Learning Studio (klassisk)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Det här dokumentet innehåller information om loggnings funktionerna i Machine Learning Studio (klassiska) webb tjänster. Loggning ger ytterligare information, utöver bara ett fel nummer och ett meddelande, som kan hjälpa dig att felsöka anrop till API: erna Machine Learning Studio (klassisk).  

## <a name="how-to-enable-logging-for-a-web-service"></a>Så här aktiverar du loggning för en webbtjänst

Du aktiverar loggning från den [Azure Machine Learning Studio (klassiska) webb tjänst](https://services.azureml.net) portalen. 

1. Logga in på den Azure Machine Learning Studio (klassiska) webb tjänst portalen på [https://services.azureml.net](https://services.azureml.net). För en klassisk webb tjänst kan du också gå till portalen genom att klicka på **ny webb tjänst upplevelse** på sidan Machine Learning Studio (klassisk) webb tjänster i Studio (klassisk).

   ![Nya tjänster webbupplevelse länk](./media/web-services-logging/new-web-services-experience-link.png)

2. På den översta meny raden klickar du på **webb tjänster** för en ny webb tjänst eller på **klassiska webb tjänster** för en klassisk webb tjänst.

   ![Välj ny eller klassiska webbtjänster](./media/web-services-logging/select-web-service.png)

3. Klicka på webbtjänstnamn för en ny webbtjänst. För en klassisk webbtjänst klickar du på webbtjänstnamn och sedan på nästa sida klickar du på lämplig slutpunkt.

4. Klicka på **Konfigurera**på den översta meny raden.

5. Ange alternativet **Aktivera loggning** till *fel* (Logga endast fel) eller *alla* (för fullständig loggning).

   ![Välj loggningsnivån](./media/web-services-logging/enable-logging.png)

6. Klicka på **Save** (Spara).

7. För klassiska webb tjänster skapar du behållaren för **ml-diagnostik** .

   Alla webb tjänst loggar sparas i en BLOB-behållare med namnet **ml-Diagnostics** i det lagrings konto som är associerat med webb tjänsten. För nya webbtjänster skapas den här behållaren första gången du få åtkomst till webbtjänsten. För klassiska webbtjänster måste du skapa behållaren om den inte redan finns. 

   1. I [Azure Portal](https://portal.azure.com)går du till det lagrings konto som är associerat med webb tjänsten.

   2. Under **Blob Service** klickar du på **Containers**.

   3. Om behållarens **ml-diagnostik** inte finns klickar du på **+ container**, ger behållaren namnet "ml-diagnostik" och väljer **åtkomst typen** som "BLOB". Klicka på **OK**

      ![Skapa en ny behållare för att lagra dina diagnostikloggar](./media/web-services-logging/create-ml-diagnostics-container.png)

> [!TIP]
>
> För en klassisk webb tjänst har instrument panelen för webb tjänster i Machine Learning Studio (klassisk) också en växel för att aktivera loggning. Men eftersom loggning hanteras nu via Web Services-portalen, måste du aktivera loggning via portalen, enligt beskrivningen i den här artikeln. Om du redan har aktiverat loggning i Studio (klassisk) inaktiverar du loggning på webb tjänst portalen och aktiverar det igen.


## <a name="the-effects-of-enabling-logging"></a>Effekterna av att aktivera loggning
När loggning är aktiverat loggas diagnostiken och felen från webb tjänst slut punkten i BLOB-behållaren för **ml-diagnostik** i det Azure Storage konto som är kopplat till användarens arbets yta. Den här behållaren innehåller alla diagnostikinformation för alla slutpunkterna för webbtjänster för alla arbetsytor som är associerade med det här lagringskontot.

Loggarna kan granskas med någon av flera verktyg som finns tillgängliga att utforska Azure Storage-kontot. Det enklaste sättet kan vara att navigera till lagrings kontot i Azure Portal, klicka på **behållare**och sedan klicka på container **ml-diagnostik**.  

## <a name="log-blob-detail-information"></a>Detaljerad information från blob
Varje blobb i behållaren innehåller diagnostikinformationen för exakt ett av följande åtgärder:

* Körningen av Batch-Execution-metod  
* Körningen av metoden begäranden och svar  
* Initieringen av en behållare för begäranden och svar

Namnet på varje blob har ett prefix följande format: 


`{Workspace Id}-{Web service Id}-{Endpoint Id}/{Log type}`


Var _logg typ_ är ett av följande värden:  

* batch  
* poäng/begäranden  
* poäng/init  

