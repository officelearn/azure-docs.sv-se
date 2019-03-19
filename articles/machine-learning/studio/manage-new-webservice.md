---
title: Hantera webbtjänster
titleSuffix: Azure Machine Learning Studio
description: Hantera dina Machine Learning nya och klassiska Web-tjänster med hjälp av Microsoft Azure Machine Learning Web Services-portalen. Eftersom klassiska webbtjänster och nya Web services baseras på olika underliggande teknik, har du lite olika hanteringsfunktioner för var och en av dem.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 02/28/2017
ms.openlocfilehash: 711cb674cb00a880eadda11b03da87631df90b0d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57838382"
---
# <a name="manage-a-web-service-using-the-azure-machine-learning-studio-web-services-portal"></a>Hantera en webbtjänst med hjälp av Azure Machine Learning Studio Web Services-portalen
Du kan hantera dina Machine Learning nya och klassiska webbtjänster med hjälp av Microsoft Azure Machine Learning Web Services-portalen. Eftersom klassiska webbtjänster och nya Web services baseras på olika underliggande teknik, har du lite olika hanteringsfunktioner för var och en av dem.

I Machine Learning Web Services-portalen kan du:

* Övervaka hur webbtjänsten används.
* Konfigurera beskrivningen, uppdatera nycklarna för webb-tjänsten (nyhet), uppdatera ditt storage-konto nyckel (endast nyhet), så loggning, och aktivera eller inaktivera exempeldata.
* Ta bort webbtjänsten.
* Skapa, ta bort eller uppdatera fakturering planer (nyhet).
* Lägga till och ta bort slutpunkter (klassisk)

>[!NOTE]
>Du kan också hantera klassiska webbtjänster i [Machine Learning Studio](https://studio.azureml.net) på den **webbtjänster** fliken.

## <a name="permissions-to-manage-new-resources-manager-based-web-services"></a>Behörigheter för att hantera nya Resources Manager-baserade webbtjänster

Nya webbtjänsterna distribueras som Azure-resurser. Därför måste du ha behörighet för att distribuera och hantera nya webbtjänster.  Om du vill distribuera och hantera nya webbtjänster måste du vara tilldelad rollen deltagare / administratör för prenumerationen där webbtjänsten har distribuerats. Om du bjuder in en annan användare till en machine learning-arbetsytan måste du tilldela dem till rollen deltagare / administratör för prenumerationen innan de kan distribuera eller hantera webbtjänster. 

Om användaren inte har tillräckliga behörigheter för att komma åt resurser i Azure Machine Learning Web Services-portalen, får de följande fel vid försök att distribuera en webbtjänst:

*Det gick inte att Web Service-distributionen. Det här kontot har inte tillräcklig åtkomst till Azure-prenumerationen som innehåller arbetsytan. För att kunna distribuera en webbtjänst till Azure måste samma konto måste bli inbjuden till arbetsytan och ges åtkomst till Azure-prenumerationen som innehåller arbetsytan.*

Läs mer om hur du skapar en arbetsyta, [skapa och dela en Azure Machine Learning Studio-arbetsyta](create-workspace.md).

Läs mer på inställningen åtkomstbehörigheter [hantera åtkomst med RBAC och Azure portal](../../role-based-access-control/role-assignments-portal.md).


## <a name="manage-new-web-services"></a>Hantera nya webbtjänster
Hantera dina nya webbtjänster:

1. Logga in på den [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/quickstart) portal med ditt Microsoft Azure-konto – Använd det konto som är associerat med Azure-prenumeration.
2. På menyn klickar du på **webbtjänster**.

Då visas en lista över distribuerade webbtjänster för din prenumeration. 

Klicka på webbtjänster för att hantera en webbtjänst. Från sidan webbtjänster kan du:

* Klicka på webbtjänsten för att hantera den.
* Klicka på den fakturering planerar för webbtjänsten att uppdatera den.
* Ta bort en webbtjänst.
* Kopiera en webbtjänst och distribuera den till en annan region.

När du klickar på en webbtjänst, öppnas sidan Snabbstart-tjänsten. Sidan Snabbstart-tjänsten har två menyalternativen så att du kan hantera din webbtjänst:

* **INSTRUMENTPANELEN** – kan du visa Web service användning.
* **Konfigurera** – kan du lägga till beskrivande text, uppdaterar nyckeln för lagringskontot som associerats med webbtjänsten och aktivera eller inaktivera exempeldata.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Övervaka hur webbtjänsten används
Klicka på den **INSTRUMENTPANELEN** fliken.

Från instrumentpanelen kan du visa övergripande användning av webbtjänsten under en viss tidsperiod. Du kan välja i vilken du vill visa Period listrutemenyn i det övre högra hörnet av dessa diagram för användning. Instrumentpanelen visar följande information:

* **Förfrågningar över tid** visar ett steg diagram över antal begäranden under den valda tidsperioden. Det kan identifiera om du upplever toppar i användningen.
* **Begäran / svar-begäranden** visar det totala antalet begäranden och svar-anrop som tjänsten har tagits emot över den valda tidsperioden och hur många av dem misslyckades.
* **Genomsnittlig tid för begäran-svar Compute** visar ett medelvärde av tiden som krävs för att köra de mottagna förfrågningarna.
* **Batch-begäranden** visar det totala antalet gruppbegäranden tjänsten har tagit emot under den valda tidsperioden och hur många av dem misslyckades.
* **Genomsnittlig svarstid för jobbet** visar ett medelvärde av tiden som krävs för att köra de mottagna förfrågningarna.
* **Fel** visar det sammanlagda antalet fel som har inträffat på anrop till webbtjänsten.
* **Services kostnader** visar avgifterna för den faktureringsplan som är kopplade till tjänsten.

### <a name="configuring-the-web-service"></a>Konfigurera webbtjänsten
Klicka på den **konfigurera** menyalternativ.

Du kan uppdatera följande egenskaper:

* **Beskrivning av** kan du ange en beskrivning för webbtjänsten.
* **Rubrik** kan du ange en rubrik för webbtjänsten
* **Nycklar** gör att du kan rotera din primära och sekundära API-nycklar.
* **Lagringskontonyckel** kan du uppdatera nyckeln för lagringskontot som associerats med Web service-ändringar. 
* **Aktivera exempeldata** kan du ange exempeldata som du kan använda för att testa tjänsten begäranden och svar. Om du har skapat webbtjänsten i Machine Learning Studio hämtas exempeldata från data din används för att träna din modell. Om du har skapat tjänsten programmässigt hämtas data från exempeldata som du angav som en del av JSON-paketet.

### <a name="managing-billing-plans"></a>Hantera faktureringsplaner
Klicka på den **planer** menyalternativet från sidan Snabbstart tjänster. Du kan också klicka på den plan som är associerade med specifika webbtjänsten att hantera den här planen.

* **Nya** kan du skapa en ny plan.
* **Lägg till/ta bort planinstans** kan du ”skala ut” en befintlig plan för att lägga till kapacitet.
* **Uppgradera/nedgradera** kan du ”skala upp” en befintlig plan för att lägga till kapacitet.
* **Ta bort** kan du ta bort en plan.

Klicka på en plan för att visa dess instrumentpanel. Instrumentpanelen ger ett ögonblicksbild eller plan användning för en vald tidsperiod. Klicka för att välja hur lång tid att visa den **Period** listrutan längst upp till höger på instrumentpanelen. 

Instrumentpanelen för planen innehåller följande information:

* **Planera beskrivning** visar information om kostnader och kapacitet som är associerade med planen.
* **Planera användning** visar antalet transaktioner och beräkningstimmar som har belastat planen.
* **Webbtjänster** visar antalet webbtjänster som använder den här planen.
* **De främsta av webbtjänstanrop** visar de översta fyra webbtjänster som gör anrop som debiteras enligt planen.
* **Främsta Web Services efter Compute timmar** visar de översta fyra webbtjänster som använder beräkningsresurser som debiteras enligt planen.

## <a name="manage-classic-web-services"></a>Hantera klassiska webbtjänster
> [!NOTE]
> Procedurerna i det här avsnittet är relevanta för att hantera klassiska webbtjänster via Azure Machine Learning Web Services-portalen. Information om hur du hanterar klassiska webbtjänster i Machine Learning Studio och Azure portal finns i [hantera en Azure Machine Learning Studio-arbetsyta](manage-workspace.md).
> 
> 

Hantera dina klassiska webbtjänster:

1. Logga in på den [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/quickstart) portal med ditt Microsoft Azure-konto – Använd det konto som är associerat med Azure-prenumeration.
2. På menyn klickar du på **klassiska webbtjänster**.

Om du vill hantera en klassiska webbtjänst, klickar du på **klassiska webbtjänster**. Från sidan klassiska webbtjänster kan du:

* Klicka på webbtjänsten för att visa de associerade slutpunkterna.
* Ta bort en webbtjänst.

När du hanterar en klassisk webbtjänst kan hantera du var och en av slutpunkterna separat. När du klickar på en webbtjänst i Web Services-sidan öppnas listan med slutpunkter som är kopplade till tjänsten. 

På sidan klassisk webbtjänst slutpunkt kan du lägga till och ta bort slutpunkter på tjänsten. Läs mer om att lägga till slutpunkter, [skapa slutpunkter](create-endpoint.md).

Klicka på en av slutpunkterna att öppna sidan Snabbstart-tjänsten. På sidan Snabbstart finns två menyalternativen så att du kan hantera din webbtjänst:

* **INSTRUMENTPANELEN** – kan du visa Web service användning.
* **Konfigurera** – kan du lägga till beskrivande text, aktivera och inaktivera, uppdaterar nyckeln för lagringskontot som associerats med webbtjänsten och aktivera och inaktivera exempeldata.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Övervaka hur webbtjänsten används
Klicka på den **INSTRUMENTPANELEN** fliken.

Från instrumentpanelen kan du visa övergripande användning av webbtjänsten under en viss tidsperiod. Du kan välja i vilken du vill visa Period listrutemenyn i det övre högra hörnet av dessa diagram för användning. Instrumentpanelen visar följande information:

* **Förfrågningar över tid** visar ett steg diagram över antal begäranden under den valda tidsperioden. Det kan identifiera om du upplever toppar i användningen.
* **Begäran / svar-begäranden** visar det totala antalet begäranden och svar-anrop som tjänsten har tagits emot över den valda tidsperioden och hur många av dem misslyckades.
* **Genomsnittlig tid för begäran-svar Compute** visar ett medelvärde av tiden som krävs för att köra de mottagna förfrågningarna.
* **Batch-begäranden** visar det totala antalet gruppbegäranden tjänsten har tagit emot under den valda tidsperioden och hur många av dem misslyckades.
* **Genomsnittlig svarstid för jobbet** visar ett medelvärde av tiden som krävs för att köra de mottagna förfrågningarna.
* **Fel** visar det sammanlagda antalet fel som har inträffat på anrop till webbtjänsten.
* **Services kostnader** visar avgifterna för den faktureringsplan som är kopplade till tjänsten.

### <a name="configuring-the-web-service"></a>Konfigurera webbtjänsten
Klicka på den **konfigurera** menyalternativ.

Du kan uppdatera följande egenskaper:

* **Beskrivning av** kan du ange en beskrivning för webbtjänsten. Beskrivningen är ett obligatoriskt fält.
* **Loggning** gör att du kan aktivera eller inaktivera felloggning på slutpunkten. Mer information om loggning finns i Aktivera [loggning för Machine Learning-webbtjänster](web-services-logging.md).
* **Aktivera exempeldata** kan du ange exempeldata som du kan använda för att testa tjänsten begäranden och svar. Om du har skapat webbtjänsten i Machine Learning Studio hämtas exempeldata från data din används för att träna din modell. Om du har skapat tjänsten programmässigt hämtas data från exempeldata som du angav som en del av JSON-paketet.


