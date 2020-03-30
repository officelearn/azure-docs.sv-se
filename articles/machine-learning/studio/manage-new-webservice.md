---
title: Hantera webbtjänster
titleSuffix: ML Studio (classic) - Azure
description: Hantera dina machine learning nya och klassiska webbtjänster med hjälp av Microsoft Azure Machine Learning Web Services-portalen. Eftersom klassiska webbtjänster och nya webbtjänster baseras på olika underliggande tekniker har du lite olika hanteringsfunktioner för var och en av dem.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 02/28/2017
ms.openlocfilehash: 2277aa3de5955efe5a3e4cb938fa557352f89006
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217962"
---
# <a name="manage-a-web-service-using-the-azure-machine-learning-studio-classic-web-services-portal"></a>Hantera en webbtjänst med azure machine learning studio-portalen (klassisk) Web Services

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Du kan hantera dina machine learning nya och klassiska webbtjänster med hjälp av Microsoft Azure Machine Learning Web Services-portalen. Eftersom klassiska webbtjänster och nya webbtjänster baseras på olika underliggande tekniker har du lite olika hanteringsfunktioner för var och en av dem.

I portalen Machine Learning Web Services kan du:

* Övervaka hur webbtjänsten används.
* Konfigurera beskrivningen, uppdatera nycklarna för webbtjänsten (endast ny), uppdatera din lagringskontonyckel (endast ny), aktivera loggning och aktivera eller inaktivera exempeldata.
* Ta bort webbtjänsten.
* Skapa, ta bort eller uppdatera faktureringsplaner (endast nytt).
* Lägga till och ta bort slutpunkter (endast klassiskt)

>[!NOTE]
>Du kan också hantera klassiska webbtjänster i [Machine Learning Studio (klassiskt)](https://studio.azureml.net) på fliken **Webbtjänster.**

## <a name="permissions-to-manage-new-resources-manager-based-web-services"></a>Behörigheter för att hantera Nya Resurser Manager-baserade webbtjänster

Nya webbtjänster distribueras som Azure-resurser. Därför måste du ha rätt behörighet för att distribuera och hantera nya webbtjänster.  Om du vill distribuera eller hantera nya webbtjänster måste du tilldelas en deltagar- eller administratörsroll på den prenumeration som webbtjänsten distribueras till. Om du bjuder in en annan användare till en maskininlärningsarbetsyta måste du tilldela dem till en deltagarroll eller administratörsroll i prenumerationen innan de kan distribuera eller hantera webbtjänster. 

Om användaren inte har rätt behörighet att komma åt resurser i Azure Machine Learning Web Services-portalen visas följande fel när de försöker distribuera en webbtjänst:

*Webbtjänstdistributionen misslyckades. Det här kontot har inte tillräcklig åtkomst till Azure-prenumerationen som innehåller arbetsytan. För att distribuera en webbtjänst till Azure måste samma konto bjudas in till arbetsytan och få åtkomst till Azure-prenumerationen som innehåller arbetsytan.*

Mer information om hur du skapar en arbetsyta finns i [Skapa och dela en arbetsyta för Azure Machine Learning Studio (klassiskt).](create-workspace.md)

Mer information om hur du anger åtkomstbehörigheter finns i [Hantera åtkomst med RBAC och Azure-portalen](../../role-based-access-control/role-assignments-portal.md).


## <a name="manage-new-web-services"></a>Hantera nya webbtjänster
Så här hanterar du dina nya webbtjänster:

1. Logga in på [Microsoft Azure Machine Learning Web Services-portalen](https://services.azureml.net/quickstart) med ditt Microsoft Azure-konto – använd kontot som är kopplat till Azure-prenumerationen.
2. Klicka på **Webbtjänster**på menyn .

Då visas en lista över distribuerade webbtjänster för din prenumeration. 

Om du vill hantera en webbtjänst klickar du på Webbtjänster. På sidan Webbtjänster kan du:

* Klicka på webbtjänsten för att hantera den.
* Klicka på webbtjänstens faktureringsplan för att uppdatera den.
* Ta bort en webbtjänst.
* Kopiera en webbtjänst och distribuera den till en annan region.

När du klickar på en webbtjänst öppnas snabbstartssidan för webbtjänsten. Snabbstartssidan för webbtjänsten har två menyalternativ som gör att du kan hantera webbtjänsten:

* **DASHBOARD** - Gör att du kan visa användning av webbtjänster.
* **CONFIGURE** - Gör att du kan lägga till beskrivande text, uppdatera nyckeln för lagringskontot som är associerat med webbtjänsten och aktivera eller inaktivera exempeldata.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Övervaka hur webbtjänsten används
Klicka på fliken **DASHBOARD.**

Från instrumentpanelen kan du visa den övergripande användningen av webbtjänsten under en viss tidsperiod. Du kan välja den period som du vill visa från listrutan Period längst upp till höger i användningsdiagrammen. Instrumentpanelen visar följande information:

* **Begäranden över tid** visar ett stegdiagram över antalet begäranden under den valda tidsperioden. Det kan hjälpa till att identifiera om du upplever toppar i användning.
* **Begärandens begäranden** visar det totala antalet begäranden-svar-samtal som tjänsten har tagit emot under den valda tidsperioden och hur många av dem som misslyckades.
* **Genomsnittlig beräkningstid för begäran-svar** visar ett genomsnitt av den tid som krävs för att köra de mottagna begärandena.
* **Batchbegäranden** visar det totala antalet batchbegäranden som tjänsten har tagit emot under den valda tidsperioden och hur många av dem som misslyckades.
* **Genomsnittlig jobbfördröjning** visar ett genomsnitt av den tid som krävs för att köra de mottagna begärandena.
* **Fel** visar det sammanlagda antalet fel som har uppstått vid anrop till webbtjänsten.
* **Servicekostnader** visar avgifterna för faktureringsplanen som är kopplad till tjänsten.

### <a name="configuring-the-web-service"></a>Konfigurera webbtjänsten
Klicka **CONFIGURE** på alternativet KONFIGURERA-menyn.

Du kan uppdatera följande egenskaper:

* **Med beskrivningen** kan du ange en beskrivning av webbtjänsten.
* **Med titeln** kan du ange en rubrik för webbtjänsten
* **Med tangenter** kan du rotera primära och sekundära API-nycklar.
* Med **lagringskontonyckeln** kan du uppdatera nyckeln för lagringskontot som är kopplat till webbtjänständringarna. 
* **Med Aktivera exempeldata** kan du tillhandahålla exempeldata som du kan använda för att testa tjänsten Begäran-svar. Om du har skapat webbtjänsten i Machine Learning Studio (klassisk) hämtas exempeldata från de data som du använde för att träna din modell. Om du skapade tjänsten programmässigt hämtas data från exempeldata som du angav som en del av JSON-paketet.

### <a name="managing-billing-plans"></a>Hantera faktureringsplaner
Klicka på menyalternativet **Planer** på snabbstartssidan för webbtjänster. Du kan också klicka på planen som är associerad med en viss webbtjänst för att hantera den planen.

* **Med** nya nyheter kan du skapa en ny plan.
* **Lägg till/ta bort plan-instansen** kan du "skala ut" en befintlig plan för att lägga till kapacitet.
* **Uppgradera/nedgradera** kan du "skala upp" en befintlig plan för att lägga till kapacitet.
* **Ta bort** kan du ta bort en plan.

Klicka på en plan för att visa instrumentpanelen. Instrumentpanelen ger dig ögonblicksbild eller planera användning under en vald tidsperiod. Om du vill välja den tidsperiod som ska visas klickar du på listrutan **Period** längst upp till höger på instrumentpanelen. 

Instrumentpanelen för planen innehåller följande information:

* **Planbeskrivning** visar information om kostnader och kapacitet som är associerade med planen.
* **Plananvändning** visar antalet transaktioner och beräkningstimmar som har debiterats mot planen.
* **Webbtjänster** visar antalet webbtjänster som använder det här abonnemanget.
* **Top Web Service By Calls** visar de fyra bästa webbtjänster som ringer samtal som debiteras mot planen.
* **Top Web Services by Compute Hrs** visar de fyra bästa webbtjänsterna som använder beräkningsresurser som debiteras mot planen.

## <a name="manage-classic-web-services"></a>Hantera klassiska webbtjänster
> [!NOTE]
> Procedurerna i det här avsnittet är relevanta för hantering av klassiska webbtjänster via Azure Machine Learning Web Services-portalen. Information om hur du hanterar klassiska webbtjänster via Machine Learning Studio (klassisk) och Azure-portalen finns i [Hantera en arbetsyta i Azure Machine Learning Studio (klassisk).](manage-workspace.md)
> 
> 

Så här hanterar du dina klassiska webbtjänster:

1. Logga in på [Microsoft Azure Machine Learning Web Services-portalen](https://services.azureml.net/quickstart) med ditt Microsoft Azure-konto – använd kontot som är kopplat till Azure-prenumerationen.
2. Klicka på **Klassiska webbtjänster**på menyn .

Om du vill hantera en klassisk webbtjänst klickar du på **Klassiska webbtjänster**. På sidan Klassiska webbtjänster kan du:

* Klicka på webbtjänsten om du vill visa de associerade slutpunkterna.
* Ta bort en webbtjänst.

När du hanterar en klassisk webbtjänst hanterar du var och en av slutpunkterna separat. När du klickar på en webbtjänst på sidan Webbtjänster öppnas listan över slutpunkter som är associerade med tjänsten. 

På slutpunktssidan för Klassiska webbtjänsten kan du lägga till och ta bort slutpunkter på tjänsten. Mer information om hur du lägger till slutpunkter finns i [Skapa slutpunkter](create-endpoint.md).

Klicka på en av slutpunkterna för att öppna snabbstartssidan för webbtjänsten. På snabbstartssidan finns det två menyalternativ som gör att du kan hantera webbtjänsten:

* **DASHBOARD** - Gör att du kan visa användning av webbtjänster.
* **CONFIGURE** - Gör att du kan lägga till beskrivande text, aktivera och inaktivera felloggning, uppdatera nyckeln för lagringskontot som är associerat med webbtjänsten och aktivera och inaktivera exempeldata.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Övervaka hur webbtjänsten används
Klicka på fliken **DASHBOARD.**

Från instrumentpanelen kan du visa den övergripande användningen av webbtjänsten under en viss tidsperiod. Du kan välja den period som du vill visa från listrutan Period längst upp till höger i användningsdiagrammen. Instrumentpanelen visar följande information:

* **Begäranden över tid** visar ett stegdiagram över antalet begäranden under den valda tidsperioden. Det kan hjälpa till att identifiera om du upplever toppar i användning.
* **Begärandens begäranden** visar det totala antalet begäranden-svar-samtal som tjänsten har tagit emot under den valda tidsperioden och hur många av dem som misslyckades.
* **Genomsnittlig beräkningstid för begäran-svar** visar ett genomsnitt av den tid som krävs för att köra de mottagna begärandena.
* **Batchbegäranden** visar det totala antalet batchbegäranden som tjänsten har tagit emot under den valda tidsperioden och hur många av dem som misslyckades.
* **Genomsnittlig jobbfördröjning** visar ett genomsnitt av den tid som krävs för att köra de mottagna begärandena.
* **Fel** visar det sammanlagda antalet fel som har uppstått vid anrop till webbtjänsten.
* **Servicekostnader** visar avgifterna för faktureringsplanen som är kopplad till tjänsten.

### <a name="configuring-the-web-service"></a>Konfigurera webbtjänsten
Klicka **CONFIGURE** på alternativet KONFIGURERA-menyn.

Du kan uppdatera följande egenskaper:

* **Med beskrivningen** kan du ange en beskrivning av webbtjänsten. Beskrivning är ett obligatoriskt fält.
* **Genom** att logga kan du aktivera eller inaktivera felloggning på slutpunkten. Mer information om loggning finns i Aktivera [loggning för machine learning-webbtjänster](web-services-logging.md).
* **Med Aktivera exempeldata** kan du tillhandahålla exempeldata som du kan använda för att testa tjänsten Begäran-svar. Om du har skapat webbtjänsten i Machine Learning Studio (klassisk) hämtas exempeldata från de data som du använde för att träna din modell. Om du skapade tjänsten programmässigt hämtas data från exempeldata som du angav som en del av JSON-paketet.


