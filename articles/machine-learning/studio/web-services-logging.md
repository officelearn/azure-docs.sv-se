---
title: "Loggning för Machine Learning-webbtjänster | Microsoft Docs"
description: "Lär dig hur du aktiverar loggning för Machine Learning-webbtjänster. Loggning ger ytterligare information för att felsöka de API: er."
services: machine-learning
documentationcenter: 
author: aashishb
ms.author: aashishb
manager: hjerez
editor: cgronlun
ms.assetid: c54d41e1-0300-46ef-bbfc-d6f7dca85086
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/15/2017
ms.openlocfilehash: 1e04ef638c46ef0f3b40fd56d27ba3673565bdc7
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/17/2018
---
# <a name="enable-logging-for-machine-learning-web-services"></a>Aktivera loggning för Machine Learning-webbtjänster
Det här dokumentet innehåller information om loggningsfunktioner av Machine Learning-webbtjänster. Loggning ger ytterligare information, förutom bara ett felnummer och ett meddelande som kan hjälpa dig att felsöka dina anrop till API: er för Machine Learning.  

## <a name="how-to-enable-logging-for-a-web-service"></a>Så här aktiverar du loggning för en webbtjänst

Du aktiverar loggning från den [Azure Machine Learning-webbtjänster](https://services.azureml.net) portal. 

1. Logga in på Azure Machine Learning-webbtjänster portalen på [ https://services.azureml.net ](https://services.azureml.net). För en klassisk webbtjänst, du kan också få till portalen genom att klicka på **nya Web Services upplevelsen** på sidan Machine Learning-webbtjänster i Machine Learning Studio.

   ![Ny Services webbupplevelse länk](./media/web-services-logging/new-web-services-experience-link.png)

2. På översta menyraden klickar du på **Web Services** för en ny webbtjänst eller klicka på **klassiska webbtjänster** ett klassiskt webbtjänsten.

   ![Välj ny eller klassisk webbtjänster](./media/web-services-logging/select-web-service.png)

3. Klicka på namnet på webben för en ny webbtjänst. För en klassisk webbtjänst klickar du på namnet på webben och sedan på nästa sida klickar du på lämplig slutpunkten.

4. Klicka på översta verktygsfältet och **konfigurera**.

5. Ange den **aktivera loggning** att *fel* (för att logga endast fel) eller *alla* (för fullständig loggning).

   ![Välj loggningsnivån](./media/web-services-logging/enable-logging.png)

6. Klicka på **Spara**.

7. Klassisk webbtjänster, skapa den **ml-diagnostik** behållare.

   Alla web service loggar sparas i en blobbbehållare med namnet **ml-diagnostik** i storage-konto som är kopplade till webbtjänsten. För nya webbtjänster skapas den här behållaren första gången du få åtkomst till webbtjänsten. För klassisk webbtjänster måste du skapa behållaren om den inte redan finns. 

   1. I den [Azure-portalen](https://portal.azure.com)går du till lagringskontot som är associerade med webbtjänsten.

   2. Under **Blob-tjänsten**, klickar du på **behållare**.

   3. Om behållaren **ml-diagnostik** inte finns, klickar du på **+ behållare**, och ge behållaren namnet ”ml-diagnostik” och välj den **komma åt typen** som ”Blob”. Klicka på **OK**.

      ![Välj loggningsnivån](./media/web-services-logging/create-ml-diagnostics-container.png)

> [!TIP]
>
> Web Services instrumentpanelen i Machine Learning Studio har också en växel för att aktivera loggning för en klassisk webbtjänst. Men eftersom loggning hanteras nu via Web Services-portalen, måste du aktivera loggning på portalen, enligt beskrivningen i den här artikeln. Om du redan loggar in Studio, inaktivera loggning i Web Services-portalen och aktivera det igen.


## <a name="the-effects-of-enabling-logging"></a>Effekterna av att aktivera loggning
När loggning är aktiverat, diagnostik och fel från webbtjänstslutpunkt loggas i den **ml-diagnostik** blob-behållaren i Azure Storage-konto som är kopplad till användarens arbetsytan. Den här behållaren innehåller diagnostikinformation för alla slutpunkterna för webbtjänster för alla arbetsytor som är associerade med det här lagringskontot.

Loggarna kan visas med hjälp av flera verktyg som finns tillgängliga att utforska Azure Storage-konto. Den enklaste kanske vill navigera till storage-konto i Azure-portalen klickar du på **behållare**, och klicka sedan på behållaren **ml-diagnostik**.  

## <a name="log-blob-detail-information"></a>Information om blob logg
Varje blobb i behållaren innehåller diagnostikinformationen för exakt ett av följande åtgärder:

* En körning av metoden Batch Execution  
* En körning av metoden begäran och svar  
* Initieringen av en begäran och svar-behållare

Namnet på varje blobb har ett prefix i följande format: 


`{Workspace Id}-{Web service Id}-{Endpoint Id}/{Log type}`


Där _logga typen_ är ett av följande värden:  

* batch  
* score-begäranden  
* poäng/init  

