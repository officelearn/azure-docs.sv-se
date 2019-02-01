---
title: Skala en Machine Learning Studio-webbtjänst
titleSuffix: Azure Machine Learning Studio
description: Lär dig mer om att öka parallellkörningen av en Azure Machine Learning Studio-webbtjänst genom att lägga till ytterligare slutpunkter.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: seodec18, previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 01/23/2017
ms.openlocfilehash: c5874029e17b0ad7c9787beb0177b8211cbf6e6b
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55512082"
---
# <a name="scaling-an-azure-machine-learning-studio-web-service-by-adding-additional-endpoints"></a>Skala en Azure Machine Learning Studio-webbtjänst genom att lägga till ytterligare slutpunkter
> [!NOTE]
> Det här avsnittet beskrivs tekniker som kan användas till en **klassiska** Machine Learning-webbtjänst. 
> 
> 

Som standard varje publicerad webbtjänst är konfigurerad för att stödja 20 samtidiga begäranden och kan vara så mycket som 200 samtidiga begäranden. Azure Machine Learning optimerar automatiskt inställningen för att tillhandahålla bästa prestanda för din webbtjänst och portal värdet ignoreras. 

Om du planerar att anropa API med en högre belastning än maximalt antal samtidiga anrop värdet 200 ska stödja, bör du skapa flera slutpunkter på samma webbtjänsten. Du kan sedan slumpmässigt distribuera inläsningen mellan dem.

Skalning av en webbtjänst är en vanlig åtgärd. Några skäl att skala är stöd för fler än 200 samtidiga begäranden, öka tillgänglighet via flera slutpunkter eller ange separata slutpunkter för webbtjänsten. Du kan öka skalan genom att lägga till ytterligare slutpunkter för samma webbtjänsten via den [Azure Machine Learning-webbtjänsten](https://services.azureml.net/) portal.

Läs mer om att lägga till nya slutpunkter [skapa slutpunkter](create-endpoint.md).

Kom ihåg att med hjälp av ett antal hög samtidighet kan vara skadligt om du inte anropar API: et med en relativt hög hastighet. Du kan se sporadiska tidsgränser och/eller toppar i svarstiderna om du placerar en relativt låg belastning på ett API som konfigurerats för hög belastning.

Synkron API: er används vanligtvis i situationer där en låg fördröjning är det önskade. Här fördröjning innebär den tid det tar för API för att slutföra en begäran och inte hänsyn till eventuella fördröjningar i nätverket. Vi antar att du har ett API med en 50 ms fördröjning. Fullständigt förbruka tillgänglig kapacitet med begränsning nivån hög och maximalt antal samtidiga anrop = 20, måste du anropa detta API 20 * 1 000 / 50 = 400 gånger per sekund. Utöka detta ytterligare kan ett maximalt antal samtidiga anrop 200 du anropa API 4000 gånger per sekund, förutsatt att en 50 ms svarstid.

<!--Image references-->
[1]: ./media/scaling-webservice/machlearn-1.png
[2]: ./media/scaling-webservice/machlearn-2.png
