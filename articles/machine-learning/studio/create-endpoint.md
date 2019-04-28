---
title: Skapa webb-Tjänsteslutpunkter
titleSuffix: Azure Machine Learning Studio
description: Skapa webb-tjänstslutpunkter i Azure Machine Learning Studio. Varje slutpunkt i webbtjänsten är oberoende av varandra åtgärdas, begränsas och hanteras.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 02/15/2019
ms.openlocfilehash: ac434a696f6e77e5ce61b430232166e7727eda38
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60751186"
---
# <a name="create-endpoints-for-deployed-azure-machine-learning-studio-web-services"></a>Skapa slutpunkter för distribuerade Azure Machine Learning Studio-webbtjänster

> [!NOTE]
> Det här avsnittet beskrivs tekniker som kan användas till en **klassiska** Machine Learning-webbtjänst.

När en webbtjänst har distribuerats skapas en standardslutpunkt för tjänsten. Standardslutpunkten kan anropas med dess API-nyckel. Du kan lägga till fler slutpunkter med deras egna nycklar från Web Services-portalen.
Varje slutpunkt i webbtjänsten är oberoende av varandra åtgärdas, begränsas och hanteras. Varje slutpunkt är en unik URL med en auktoriseringsnyckeln som du kan distribuera till dina kunder.

## <a name="add-endpoints-to-a-web-service"></a>Lägga till slutpunkter till en webbtjänst

Du kan lägga till en slutpunkt till en webbtjänst med hjälp av Azure Machine Learning Web Services-portalen. När slutpunkten har skapats kan du använda via synkron API: er, batch API: er, och excel-kalkylblad.

> [!NOTE]
> Om du har lagt till ytterligare slutpunkter till webbtjänsten kan du inte ta bort standardslutpunkten.

1. Klicka på webbtjänster i Machine Learning Studio, på den vänstra kolumnen.
2. Längst ned i instrumentpanelen för webbtjänsten klickar du på **hantera slutpunkter för**. Azure Machine Learning Web Services-portalen öppnas på sidan slutpunkter för webbtjänsten.
3. Klicka på **Ny**.
4. Skriv ett namn och beskrivning för den nya slutpunkten. Slutpunktsnamn måste vara 24 tecken eller mindre långt och måste bestå av gemena bokstäver eller siffror. Välj loggningsnivån och om exempeldata är aktiverad. Mer information om loggning finns i [aktivera loggning för Machine Learning web services](web-services-logging.md).

## <a id="scaling"></a> Skala en webbtjänst genom att lägga till ytterligare slutpunkter

Som standard varje publicerad webbtjänst är konfigurerad för att stödja 20 samtidiga begäranden och kan vara så mycket som 200 samtidiga begäranden. Azure Machine Learning Studio optimerar automatiskt inställningen för att tillhandahålla bästa prestanda för din webbtjänst och portal värdet ignoreras.

Om du planerar att anropa API med en högre belastning än maximalt antal samtidiga anrop värdet 200 ska stödja, bör du skapa flera slutpunkter på samma webbtjänsten. Du kan sedan slumpmässigt distribuera inläsningen mellan dem.

Skalning av en webbtjänst är en vanlig åtgärd. Några skäl att skala är stöd för fler än 200 samtidiga begäranden, öka tillgänglighet via flera slutpunkter eller ange separata slutpunkter för webbtjänsten. Du kan öka skalan genom att lägga till ytterligare slutpunkter för samma webbtjänsten via den [Azure Machine Learning-webbtjänsten](https://services.azureml.net/) portal.

Kom ihåg att med hjälp av ett antal hög samtidighet kan vara skadligt om du inte anropar API: et med en relativt hög hastighet. Du kan se sporadiska tidsgränser och/eller toppar i svarstiderna om du placerar en relativt låg belastning på ett API som konfigurerats för hög belastning.

Synkron API: er används vanligtvis i situationer där en låg fördröjning är det önskade. Här fördröjning innebär den tid det tar för API för att slutföra en begäran och inte hänsyn till eventuella fördröjningar i nätverket. Vi antar att du har ett API med en 50 ms fördröjning. Fullständigt förbruka tillgänglig kapacitet med begränsning nivån hög och maximalt antal samtidiga anrop = 20, måste du anropa detta API 20 * 1 000 / 50 = 400 gånger per sekund. Utöka detta ytterligare kan ett maximalt antal samtidiga anrop 200 du anropa API 4000 gånger per sekund, förutsatt att en 50 ms svarstid.

## <a name="next-steps"></a>Nästa steg

[Hur du använder en Azure Machine Learning-webbtjänst](consume-web-services.md).
