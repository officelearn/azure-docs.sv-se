---
title: "Använda Azure Machine Learning-webbtjänster portal | Microsoft Docs"
description: "Hantera åtkomst till Azure Machine Learning arbetsytor och distribuera och hantera ml – API-tjänster"
services: machine-learning
documentationcenter: 
author: vDonGlover
manager: jhubbard
editor: cgronlun
ms.assetid: b62cf2ca-dd2a-4a83-bb54-469f948fb026
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: v-donglo
ms.openlocfilehash: 2fc71d3a03ff978485104bcd1cd9391c1d5ee392
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="manage-a-web-service-using-the-azure-machine-learning-web-services-portal"></a>Hantera en webbtjänst med hjälp av Azure Machine Learning-webbtjänster-portalen
Du kan hantera Machine Learning nya och klassiska webbtjänster med hjälp av portalen för Microsoft Azure Machine Learning-webbtjänster. Eftersom klassiska webbtjänster och nya Web services baseras på olika underliggande tekniker, ha lite olika hanteringsfunktioner för dem.

I Machine Learning-webbtjänster portalen kan du:

* Övervaka hur webbtjänsten används.
* Konfigurera beskrivningen, uppdatera nycklarna för web service (nytt), uppdatera din lagring konto nyckel (endast nytt), aktivera loggning och aktivera eller inaktivera exempeldata.
* Ta bort webbtjänsten.
* Skapa, ta bort eller uppdatera fakturering planer (nytt).
* Lägga till och ta bort slutpunkter (klassisk)

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="permissions-to-manage-new-resources-manager-based-web-services"></a>Behörigheter för att hantera nya resurser Manager baserat webbtjänsterna

Nya webbtjänsterna distribueras som Azure-resurser. Därför måste du ha behörighet för att distribuera och hantera nya webbtjänster.  Om du vill distribuera eller hantera nya webbtjänster måste du tilldelas rollen deltagare eller administratör på den prenumeration som webbtjänsten har distribuerats. Om du bjuda in en annan användare till en machine learning-arbetsytan måste du tilldela dem en roll för deltagare eller administratör för prenumerationen innan de kan distribuera eller hantera webbtjänster. 

Om användaren inte har behörighet att komma åt resurser i Azure Machine Learning-webbtjänster portal får de följande felmeddelande när du försöker distribuera en webbtjänst:

*Det gick inte att Web Service-distributionen. Det här kontot har inte tillräcklig åtkomst till Azure-prenumeration som innehåller arbetsytan. Samma konto måste bjudits in till arbetsytan för att distribuera en webbtjänst till Azure och få tillgång till Azure-prenumeration som innehåller arbetsytan.*

Mer information om hur du skapar en arbetsyta finns [skapa och dela en Azure Machine Learning-arbetsytan](create-workspace.md).

Mer information om inställningen åtkomstbehörighet finns [visa åtkomst-tilldelningar för användare och grupper i Azure portal – förhandsversion](../../active-directory/role-based-access-control-manage-assignments.md).


## <a name="manage-new-web-services"></a>Hantera nya webbtjänster
Hantera din nya webbtjänster:

1. Logga in på den [Microsoft Azure Machine Learning-webbtjänster](https://services.azureml.net/quickstart) portal med Microsoft Azure-konto – Använd det konto som är kopplad till den Azure-prenumerationen.
2. Klicka på menyn **Web Services**.

Detta visar en lista över distribuerade webbtjänster för din prenumeration. 

Klicka på webbtjänster för att hantera en webbtjänst. På sidan Web Services kan du:

* Klicka på webbtjänsten för att hantera den.
* Klicka på den fakturering planera för webbtjänsten för att uppdatera den.
* Ta bort en webbtjänst.
* Kopiera en webbtjänst och distribuera den till en annan region.

När du klickar på en webbtjänst öppnas sidan Snabbstart-tjänsten. Sidan Snabbstart tjänsten innehåller två menyalternativ som du kan hantera webbtjänsten:

* **INSTRUMENTPANELEN** -kan du visa Web service användning.
* **Konfigurera** – kan du lägga till beskrivande text, uppdatera nyckeln för lagringskontot som är associerade med webbtjänsten, och aktivera eller inaktivera exempeldata.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Övervaka hur webbtjänsten används
Klicka på den **INSTRUMENTPANELEN** fliken.

Du kan visa övergripande användning av webbtjänsten under en viss tidsperiod från instrumentpanelen. Du kan välja vilken om du vill visa från listrutan Period i det övre högra hörnet för användning. Instrumentpanelen visar följande information:

* **Begäranden över tid** visar ett steg diagram över hur många begäranden för den valda tidsperioden. Det hjälper dig identifiera om det uppstår toppar i användning.
* **Begäran och svar begäranden** visar det totala antalet anrop för begäran och svar tjänsten har tagit emot och den valda tidsperioden och hur många av dem misslyckades.
* **Genomsnittlig tid för begäran och svar Compute** visar ett genomsnitt av tiden som krävs för att köra den mottagna begäranden.
* **Batch-begäranden** visar det totala antalet gruppbegäranden tjänsten har tagit emot och den valda tidsperioden och hur många av dem misslyckades.
* **Genomsnittlig svarstid för jobbet** visar ett genomsnitt av tiden som krävs för att köra den mottagna begäranden.
* **Fel** visar det sammanlagda antalet fel som har inträffat på anrop till webbtjänsten.
* **Services kostnader** visar avgifterna för den faktureringsplan som är associerad med tjänsten.

### <a name="configuring-the-web-service"></a>Konfigurera webbtjänsten
Klicka på den **konfigurera** menyalternativet.

Du kan uppdatera följande egenskaper:

* **Beskrivning** kan du ange en beskrivning för webbtjänsten.
* **Rubrik** kan du ange en rubrik för webbtjänsten
* **Nycklar** gör att du kan rotera dina primära och sekundära API-nycklar.
* **Lagringskontonyckel** kan du uppdatera nyckeln för lagringskontot som är kopplade till Web-tjänsten ändras. 
* **Aktivera exempeldata** kan du ange exempeldata som du kan använda för att testa tjänsten begäran och svar. Om du har skapat webbtjänsten i Machine Learning Studio hämtas exempeldata från data din används för att träna din modell. Om du har skapat tjänsten programmässigt hämtas data från de exempel informationen som en del av JSON-paketet.

### <a name="managing-billing-plans"></a>Hantera fakturerings-planer
Klicka på den **planer** menyalternativet från sidan Snabbstart tjänster. Du kan också klicka på planen som är kopplade till specifika webbtjänsten för att hantera planen.

* **Nya** kan du skapa en ny plan.
* **Lägg till/ta bort Plan instans** kan du ”skala ut” en befintlig plan för att lägga till kapacitet.
* **Uppgradering/nedgradering** kan du ”skala upp” en befintlig plan för att lägga till kapacitet.
* **Ta bort** kan du ta bort en plan.

Klicka på en plan för att visa dess instrumentpanel. Instrumentpanelen ger ett ögonblicksbild eller planera användning för en vald tidsperiod. Klicka för att välja hur lång tid att visa den **Period** listrutan längst upp till höger i instrumentpanelen. 

Planera instrumentpanelen innehåller följande information:

* **Planera beskrivning** visar information om kostnader och kapacitet som är kopplade med planen.
* **Planera användning** visar antalet transaktioner och beräkningstimmar som har belastat planen.
* **Webbtjänster** visar antalet webbtjänster som använder den här planen.
* **De främsta av webbtjänstanrop** visar upp fyra webbtjänster som gör anrop som debiteras enligt planen.
* **De främsta Web Services genom att beräkna timmar** visar upp fyra webbtjänster som använder beräkningsresurser som debiteras enligt planen.

## <a name="manage-classic-web-services"></a>Hantera klassiska webbtjänster
> [!NOTE]
> Procedurerna i det här avsnittet är relevanta för att hantera klassisk webbtjänster via portalen Azure Machine Learning-webbtjänster. Information om hur du hanterar klassiska webbtjänster via Machine Learning Studio och den klassiska Azure-portalen finns [hantera en Azure Machine Learning-arbetsytan](manage-workspace.md).
> 
> 

Hantera din klassiska webbtjänster:

1. Logga in på den [Microsoft Azure Machine Learning-webbtjänster](https://services.azureml.net/quickstart) portal med Microsoft Azure-konto – Använd det konto som är kopplad till den Azure-prenumerationen.
2. Klicka på menyn **klassiska webbtjänster**.

Klicka på att hantera en klassiska webbtjänst **klassiska webbtjänster**. Från sidan klassiska webbtjänster kan du:

* Klicka på webbtjänsten för att visa de associera slutpunkterna.
* Ta bort en webbtjänst.

När du hanterar en klassiska webbtjänst kan hantera du var och en av slutpunkterna separat. När du klickar på en webbtjänst på sidan Web Services öppnas listan över slutpunkter för tjänsten. 

På sidan klassiska webbtjänsten endpoint kan du lägga till och ta bort slutpunkter för tjänsten. Mer information om att lägga till slutpunkter finns [skapar slutpunkter](create-endpoint.md).

Klicka på en av slutpunkterna att öppna sidan Snabbstart-tjänsten. Det finns två menyalternativ som du kan hantera webbtjänsten på sidan Snabbstart:

* **INSTRUMENTPANELEN** -kan du visa Web service användning.
* **Konfigurera** – kan du lägga till beskrivande text, aktivera eller inaktivera felloggning, uppdatera nyckeln för lagringskontot som är associerade med webbtjänsten, och aktivera och inaktivera exempeldata.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Övervaka hur webbtjänsten används
Klicka på den **INSTRUMENTPANELEN** fliken.

Du kan visa övergripande användning av webbtjänsten under en viss tidsperiod från instrumentpanelen. Du kan välja vilken om du vill visa från listrutan Period i det övre högra hörnet för användning. Instrumentpanelen visar följande information:

* **Begäranden över tid** visar ett steg diagram över hur många begäranden för den valda tidsperioden. Det hjälper dig identifiera om det uppstår toppar i användning.
* **Begäran och svar begäranden** visar det totala antalet anrop för begäran och svar tjänsten har tagit emot och den valda tidsperioden och hur många av dem misslyckades.
* **Genomsnittlig tid för begäran och svar Compute** visar ett genomsnitt av tiden som krävs för att köra den mottagna begäranden.
* **Batch-begäranden** visar det totala antalet gruppbegäranden tjänsten har tagit emot och den valda tidsperioden och hur många av dem misslyckades.
* **Genomsnittlig svarstid för jobbet** visar ett genomsnitt av tiden som krävs för att köra den mottagna begäranden.
* **Fel** visar det sammanlagda antalet fel som har inträffat på anrop till webbtjänsten.
* **Services kostnader** visar avgifterna för den faktureringsplan som är associerad med tjänsten.

### <a name="configuring-the-web-service"></a>Konfigurera webbtjänsten
Klicka på den **konfigurera** menyalternativet.

Du kan uppdatera följande egenskaper:

* **Beskrivning** kan du ange en beskrivning för webbtjänsten. Beskrivningen är ett obligatoriskt fält.
* **Loggning av** kan du aktivera eller inaktivera felloggning på slutpunkten. Mer information om loggning finns i Aktivera [loggning för Machine Learning-webbtjänster](web-services-logging.md).
* **Aktivera exempeldata** kan du ange exempeldata som du kan använda för att testa tjänsten begäran och svar. Om du har skapat webbtjänsten i Machine Learning Studio hämtas exempeldata från data din används för att träna din modell. Om du har skapat tjänsten programmässigt hämtas data från de exempel informationen som en del av JSON-paketet.

## <a name="grant-or-suspend-access-to-web-services-for-users-in-the-portal"></a>Bevilja eller inaktivera åtkomst till webbtjänster för användare i portalen
Med den klassiska Azure-portalen kan du tillåta eller neka åtkomst till specifika användare.

### <a name="access-for-users-of-new-web-services"></a>Åtkomst för användare av nya webbtjänster
Om du vill aktivera andra användare att arbeta med webbtjänster i Azure Machine Learning-webbtjänster portal, du måste lägga till dem som co-administratörer på Azure-prenumerationen.

Logga in på den [klassiska Azure-portalen](https://manage.windowsazure.com/) med Microsoft Azure-konto – Använd det konto som är kopplad till den Azure-prenumerationen.

1. I navigeringsfönstret klickar du på **inställningar**, klicka på **administratörer**.
2. Längst ned i fönstret klickar du på **Lägg till**. 
3. Skriv e-postadressen för den person som du vill lägga till som medadministratör och välj den prenumeration som du vill att komma åt medadministratörerna i dialogrutan ADD A CO-administratör.
4. Klicka på **Spara**.

### <a name="access-for-users-of-classic-web-services"></a>Åtkomst för användare av klassiska webbtjänster
Hantera en arbetsyta:

Logga in på den [klassiska Azure-portalen](https://manage.windowsazure.com/) med Microsoft Azure-konto – Använd det konto som är kopplad till den Azure-prenumerationen.

1. Klicka på panelen för Microsoft Azure-tjänster **MASKININLÄRNING**.
2. Klicka på arbetsytan som du vill hantera.
3. Klicka på den **konfigurera** fliken.

Från konfigurationsfliken kan du inaktivera åtkomst till Machine Learning-arbetsytan genom att klicka på **NEKA**. Användare kommer inte längre att kunna öppna arbetsytan i Machine Learning Studio. Om du vill återställa åtkomst **TILLÅT**.

Till specifika användare:

Om du vill hantera ytterligare konton som har åtkomst till arbetsytan i Machine Learning Studio klickar du på **logga in på ML Studio** i den **INSTRUMENTPANELEN** fliken. Då öppnas arbetsytan i Machine Learning Studio. Härifrån kan du klicka på den **inställningar** fliken och sedan **användare**. Du kan klicka på **bjuda in fler användare** att ge användare åtkomst till arbetsytan, eller välj en användare och klicka på **ta bort**.

> [!NOTE]
> Den **logga in på ML Studio** länken öppnar Machine Learning Studio med Account du för närvarande är inloggad på. Account du använde för att logga in på den klassiska Azure-portalen för att skapa en arbetsyta har inte behörighet att öppna arbetsytan automatiskt. Om du vill öppna en arbetsyta, måste du vara inloggad på Account som har definierats som ägare till arbetsytan eller måste du få en inbjudan från ägare att ansluta till arbetsytan.
> 
> 

