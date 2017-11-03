---
title: "Så här ökar du samtidighet på en Azure Machine Learning-webbtjänst | Microsoft Docs"
description: "Lär dig hur du ökar samtidighet på en Azure Machine Learning-webbtjänst genom att lägga till ytterligare slutpunkter."
services: machine-learning
documentationcenter: 
author: neerajkh
manager: srikants
editor: cgronlun
keywords: "Azure maskininlärning webbtjänster, operationalization, skalning, slutpunkt, samtidighet"
ms.assetid: c2c51d7f-fd2d-4f03-bc51-bf47e6969296
ms.service: machine-learning
ms.devlang: NA
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/23/2017
ms.author: neerajkh
ms.openlocfilehash: eea085308a5ca755cbf570465b97019de800768e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="scaling-an-azure-machine-learning-web-service-by-adding-additional-endpoints"></a>Skala en Azure Machine Learning-webbtjänst genom att lägga till ytterligare slutpunkter
> [!NOTE]
> Det här avsnittet beskrivs metoder som gäller för en **klassiska** Machine Learning-webbtjänst. 
> 
> 

Som standard varje publicerade webbtjänsten har konfigurerats för att stödja 20 samtidiga begäranden och kan vara så högt som 200 samtidiga förfrågningar. När den klassiska Azure-portalen kan du ange det här värdet, Azure Machine Learning optimerar automatiskt inställningen för att tillhandahålla bästa prestanda för webbtjänsten och portalen värdet ignoreras. 

Om du planerar att anropa API: N med högre belastning än maximalt antal samtidiga anrop värdet 200 stöder, bör du skapa flera slutpunkter på samma webbtjänsten. Du kan sedan slumpmässigt distribuera din belastningen över alla.

Skalning av en webbtjänst är en vanlig aktivitet. Några skäl att skala är stöd för fler än 200 samtidiga förfrågningar, öka tillgängligheten via flera slutpunkter eller tillhandahålla separata slutpunkter för webbtjänsten. Du kan öka skalan genom att lägga till ytterligare slutpunkter för samma webbtjänsten via [klassiska Azure-portalen](https://manage.windowsazure.com/) eller [Azure Machine Learning-webbtjänst](https://services.azureml.net/) portal.

Mer information om att lägga till nya slutpunkter finns [skapar slutpunkter](create-endpoint.md).

Tänk på att med hjälp av ett antal hög samtidighet kan vara skadliga om du inte anropar API: et med en relativt hög hastighet. Du kan se sporadiska timeout och/eller toppar i svarstiderna om du placerar en relativt låg belastning på ett API som konfigurerats för hög belastning.

Synkron API: er används vanligtvis i situationer där en låg latens önskas. Latens här innebär att den tid det tar att slutföra en begäran-API och inte kontot för alla fördröjningar i nätverket. Anta att du har ett API med en 50 ms fördröjning. Fullständigt förbruka tillgänglig kapacitet med begränsningen nivån hög och maximalt antal samtidiga anrop = 20, måste du anropa denna API 20 * 1000 / 50 = 400 gånger per sekund. Utöka detta ytterligare kan ett maximalt antal samtidiga anrop 200 du anropa API 4000 gånger per sekund, förutsatt att en 50 ms fördröjning.

<!--Image references-->
[1]: ./media/scaling-webservice/machlearn-1.png
[2]: ./media/scaling-webservice/machlearn-2.png
