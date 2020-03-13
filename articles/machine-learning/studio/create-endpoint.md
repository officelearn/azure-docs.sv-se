---
title: Skapa webb tjänst slut punkter
titleSuffix: ML Studio (classic) - Azure
description: Skapa webb tjänst slut punkter i Azure Machine Learning Studio (klassisk). Varje slut punkt i webb tjänsten är separat adresserad, begränsad och hanterad.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/15/2019
ms.openlocfilehash: 980ed1e54de30ec8a2dc0c1fdac6546d31f48a00
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79218202"
---
# <a name="create-endpoints-for-deployed-azure-machine-learning-studio-classic-web-services"></a>Skapa slut punkter för distribuerade Azure Machine Learning Studio (klassiska) webb tjänster

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

> [!NOTE]
> I det här avsnittet beskrivs de tekniker som är tillämpliga för en **klassisk** Machine Learning-webbtjänst.

När en webbtjänst har distribuerats skapas en standardslutpunkt för tjänsten. Standardslutpunkten kan anropas med dess API-nyckel. Du kan lägga till fler slut punkter med sina egna nycklar från webb tjänst portalen.
Varje slut punkt i webb tjänsten är separat adresserad, begränsad och hanterad. Varje slut punkt är en unik URL med en auktoriseringskod som du kan distribuera till dina kunder.

## <a name="add-endpoints-to-a-web-service"></a>Lägga till slut punkter till en webb tjänst

Du kan lägga till en slut punkt till en webb tjänst med hjälp av Azure Machine Learning Web Services-portalen. När slutpunkten har skapats kan du använda via synkron API: er, batch API: er, och excel-kalkylblad.

> [!NOTE]
> Om du har lagt till ytterligare slut punkter i webb tjänsten kan du inte ta bort standard slut punkten.

1. I Machine Learning Studio (klassisk) i den vänstra navigerings kolumnen klickar du på webb tjänster.
2. Klicka på **Hantera slut punkter**längst ned på instrument panelen för webb tjänster. Azure Machine Learning Web Services-portalen öppnas på sidan slut punkter för webb tjänsten.
3. Klicka på **Ny**.
4. Skriv ett namn och beskrivning för den nya slutpunkten. Slutpunktsnamn måste vara 24 tecken eller mindre långt och måste bestå av gemena bokstäver eller siffror. Välj loggningsnivån och om exempeldata är aktiverad. Mer information om loggning finns i [Aktivera loggning för Machine Learning-webbtjänster](web-services-logging.md).

## <a id="scaling"></a>Skala en webb tjänst genom att lägga till ytterligare slut punkter

Som standard är varje publicerad webb tjänst konfigurerad för att stödja 20 samtidiga begär Anden och kan vara så hög som 200 samtidiga begär Anden. Azure Machine Learning Studio (klassisk) optimerar automatiskt inställningen för att ge den bästa prestandan för din webb tjänst och värdet för portalen ignoreras.

Om du planerar att anropa API: et med en högre belastning än det högsta antalet samtidiga anrop som 200 kommer att stödja, bör du skapa flera slut punkter i samma webb tjänst. Du kan sedan slumpmässigt distribuera din belastning för alla.

Skalningen av en webb tjänst är en vanlig uppgift. Vissa orsaker till skalning är att stödja fler än 200 samtidiga begär Anden, öka tillgängligheten genom flera slut punkter eller tillhandahålla separata slut punkter för webb tjänsten. Du kan öka skalan genom att lägga till ytterligare slut punkter för samma webb tjänst via [Azure Machine Learning-webbtjänst-](https://services.azureml.net/) portalen.

Tänk på att det kan vara skadligt att använda ett högt samtidighets antal om du inte anropar API: et med en motsvarande hög hastighet. Du kan se sporadiska tids gränser och/eller toppar i svars tiden om du sätter en relativt låg belastning på ett API som kon figurer ATS för hög belastning.

De synkrona API: erna används vanligt vis i situationer där en låg latens önskas. Svars tiden här förutsätter hur lång tid det tar för API: et att slutföra en begäran och inte tar hänsyn till eventuella fördröjningar i nätverket. Anta att du har ett API med en 50-ms-latens. För att få full användning av den tillgängliga kapaciteten med högsta och högsta antal samtidiga anrop = 20 måste du anropa API 20 * 1000/50 = 400 gånger per sekund. Genom att utöka detta ytterligare kan du med ett maximalt antal samtidiga anrop på 200 anropa API 4000-tiderna per sekund, förutsatt en 50-ms-latens.

## <a name="next-steps"></a>Nästa steg

[Använda en Azure Machine Learning-webb tjänst](consume-web-services.md).
