---
title: Hantera en arbetsyta i Machine Learning | Microsoft Docs
description: "Hantera åtkomst till Azure Machine Learning arbetsytor och distribuera och hantera ml – API-tjänster"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: daf3d413-7a77-4beb-9a7a-6b4bdf717719
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: garye
ms.openlocfilehash: 2f90234bdd5c917a502d24cd16256bc11c7fbed0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="manage-an-azure-machine-learning-workspace"></a>Hantera en Azure Machine Learning-arbetsyta

> [!NOTE]
> Information om hur du hanterar webbtjänster i Machine Learning-webbtjänster portal finns [hantera en webbtjänst med hjälp av Azure Machine Learning-webbtjänster portal](manage-new-webservice.md).
> 
> 

Du kan hantera Machine Learning arbetsytor i Azure-portalen eller den klassiska Azure-portalen.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="use-the-azure-portal"></a>Använda Azure-portalen

Hantera en arbetsyta i Azure-portalen:

1. Logga in på den [Azure-portalen](https://portal.azure.com/) med ett administratörskonto för Azure-prenumeration.
2. Ange ”dator learning arbetsytor” i sökrutan överst på sidan och välj sedan **Machine Learning arbetsytor**.
3. Klicka på arbetsytan som du vill hantera.

Förutom standard resursinformation för hantering och alternativ som är tillgängliga kan du:

- Visa **egenskaper** – den här sidan visar arbetsytan och resursen och du kan ändra gruppen prenumeration och resurs som den här arbetsytan är kopplad till.
- **Omsynkronisering Lagringsnycklar** -arbetsytan underhåller nycklar till lagringskontot. Om lagringskontot ändras nycklar och du kan klicka på **omsynkronisering nycklar** att synkronisera nycklar med arbetsytan.

Använd portalen Machine Learning-webbtjänster för att hantera de webbtjänster som är associerade med den här arbetsytan. Se [hantera en webbtjänst med hjälp av Azure Machine Learning-webbtjänster portal](manage-new-webservice.md) fullständig information.

> [!NOTE]
> Om du vill distribuera eller hantera nya webbtjänster måste du tilldelas rollen deltagare eller administratör på den prenumeration som webbtjänsten har distribuerats. Om du bjuda in en annan användare till en machine learning-arbetsytan måste du tilldela dem en roll för deltagare eller administratör för prenumerationen innan de kan distribuera eller hantera webbtjänster. 
> 
>Mer information om inställningen åtkomstbehörighet finns [visa åtkomst-tilldelningar för användare och grupper i Azure portal – förhandsversion](../../active-directory/role-based-access-control-manage-assignments.md).

## <a name="use-the-azure-classic-portal"></a>Använd den klassiska Azure-portalen

Med den klassiska Azure-portalen kan hantera du arbetsytorna Machine Learning till:

* Övervaka hur arbetsytan används
* Konfigurera arbetsyta om du vill tillåta eller neka åtkomst
* Hantera webbtjänster som skapats i arbetsytan
* Ta bort arbetsytan

Dessutom innehåller en översikt över arbetsytan-användning och en snabb överblick över din arbetsyta information fliken instrumentpanel.  

> [!TIP]
> I Azure Machine Learning Studio på den **WEB SERVICES** fliken, du kan lägga till, uppdatera eller ta bort en Machine Learning-webbtjänst.
> 
> 

Hantera en arbetsyta i den klassiska Azure-portalen:

1. Logga in på den [klassiska Azure-portalen](https://manage.windowsazure.com/) med Microsoft Azure-konto – Använd det konto som är kopplad till den Azure-prenumerationen.
2. Klicka på panelen för Microsoft Azure-tjänster **MASKININLÄRNING**.
3. Klicka på arbetsytan som du vill hantera.

Arbetsytssidan har tre flikar:

* **INSTRUMENTPANELEN** -gör att du kan visa arbetsytan användnings- och information
* **Konfigurera** -kan du hantera åtkomst till arbetsytan
* **WEBBTJÄNSTER** -låter dig hantera webbtjänster som har publicerats från arbetsytan

### <a name="to-monitor-how-the-workspace-is-being-used"></a>Att övervaka hur arbetsytan används
Klicka på den **INSTRUMENTPANELEN** fliken.

Du kan visa övergripande användning av arbetsytan och få en snabb överblick över arbetsytan information från instrumentpanelen.

* Den **COMPUTE** diagrammet visar de beräkningsresurser som används av arbetsytan. Du kan ändra vy för att visa relativa eller absoluta värden och du kan ändra den tidsperiod som visas i diagrammet.
* **Användningsöversikten** visar Azure-lagring som används av arbetsytan.
* **Snabböversikten** innehåller en översikt över arbetsyteinformation och länkar.

> [!NOTE]
> Den **logga in på ML Studio** länken öppnar Machine Learning Studio med Account du för närvarande är inloggad på. Account du använde för att logga in på den klassiska Azure-portalen för att skapa en arbetsyta har inte behörighet att öppna arbetsytan automatiskt. Om du vill öppna en arbetsyta, måste du vara inloggad på Account som har definierats som ägare till arbetsytan eller måste du få en inbjudan från ägare att ansluta till arbetsytan.
> 
> 

### <a name="to-grant-or-suspend-access-for-users"></a>Att bevilja eller pausa åtkomst för användare
Klicka på den **konfigurera** fliken.

På konfigurationsfliken kan du:

* Inaktivera åtkomst till Machine Learning-arbetsytan genom att klicka på NEKA. Användare kommer inte längre att kunna öppna arbetsytan i Machine Learning Studio. För att återställa åtkomsten, klickar du på TILLÅT.

Om du vill hantera ytterligare konton som har åtkomst till arbetsytan i Machine Learning Studio klickar du på **logga in på ML Studio** i den **INSTRUMENTPANELEN** fliken (se anmärkning som föregår angående **logga in på ML Studio**). Då öppnas arbetsytan i Machine Learning Studio. Härifrån kan du klicka på den **inställningar** fliken och sedan **användare**. Du kan klicka på **bjuda in fler användare** att ge användare åtkomst till arbetsytan, eller välj en användare och klicka på **ta bort**.

### <a name="to-manage-web-services-in-this-workspace"></a>Hantera webbtjänster i den här arbetsytan
Klicka på den **WEB SERVICES** fliken.

Detta visar en lista över webbtjänster som publicerats från den här arbetsytan.
Klicka på namnet i listan för att öppna tjänstsidan för att hantera en webbtjänst.

En webbtjänst kan ha en eller flera slutpunkter som definierats.

* Du kan definiera flera slutpunkter förutom ”Default”-slutpunkten. Om du vill lägga till slutpunkten, klickar du på **hantera slutpunkter** längst ned i instrumentpanelen för att öppna Azure Machine Learning-webbtjänster-portalen.
* Ta bort en slutpunkt (du inte kan ta bort slutpunkten för ”Default”) och klicka på kryssrutan i början av raden slutpunkt och på **ta bort**. Detta tar bort slutpunkten från webbtjänsten.
  
  > [!NOTE]
  > Om ett program använder webbtjänstslutpunkt när slutpunkten har tagits bort, får programmet ett fel nästa gång den försöker komma åt tjänsten.
  > 
  > 

Klicka på namnet på en slutpunkt för webbtjänsten så att den öppnas. 

Du kan visa övergripande användning av webbtjänsten under en viss tidsperiod från instrumentpanelen. Du kan välja vilken om du vill visa från listrutan Period i det övre högra hörnet för användning. Instrumentpanelen visar följande information:

* **Begäranden över tid** visar ett steg diagram över hur många begäranden för den valda tidsperioden. Det hjälper dig identifiera om det uppstår toppar i användning.
* **Begäran och svar begäranden** visar det totala antalet anrop för begäran och svar tjänsten har tagit emot och den valda tidsperioden och hur många av dem misslyckades.
* **Genomsnittlig tid för begäran och svar Compute** visar ett genomsnitt av tiden som krävs för att köra den mottagna begäranden.
* **Batch-begäranden** visar det totala antalet gruppbegäranden tjänsten har tagit emot och den valda tidsperioden och hur många av dem misslyckades.
* **Genomsnittlig svarstid för jobbet** visar ett genomsnitt av tiden som krävs för att köra den mottagna begäranden.
* **Fel** visar det sammanlagda antalet fel som har inträffat på anrop till webbtjänsten.
* **Services kostnader** visar avgifterna för den faktureringsplan som är associerad med tjänsten.

Du kan använda sidan Konfigurera för att uppdatera följande egenskaper:

* **Beskrivning** kan du ange en beskrivning för webbtjänsten. Beskrivningen är ett obligatoriskt fält.
* **Loggning av** kan du aktivera eller inaktivera felloggning på slutpunkten. Mer information om loggning finns i Aktivera [loggning för Machine Learning-webbtjänster](web-services-logging.md).
* **Aktivera exempeldata** kan du ange exempeldata som du kan använda för att testa tjänsten begäran och svar. Om du har skapat webbtjänsten i Machine Learning Studio hämtas exempeldata från data din används för att träna din modell. Om du har skapat tjänsten programmässigt hämtas data från de exempel informationen som en del av JSON-paketet.

