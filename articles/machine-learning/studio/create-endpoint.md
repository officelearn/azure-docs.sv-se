---
title: Skapa slutpunkter för webbtjänsten
titleSuffix: ML Studio (classic) - Azure
description: Skapa slutpunkter för webbtjänsten i Azure Machine Learning Studio (klassisk). Varje slutpunkt i webbtjänsten adresseras, begränsas och hanteras oberoende av varandra.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/15/2019
ms.openlocfilehash: 980ed1e54de30ec8a2dc0c1fdac6546d31f48a00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218202"
---
# <a name="create-endpoints-for-deployed-azure-machine-learning-studio-classic-web-services"></a>Skapa slutpunkter för distribuerade Azure Machine Learning Studio -webbtjänster (klassiskt)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

> [!NOTE]
> I det här avsnittet beskrivs tekniker som gäller för en **klassisk** machine learning-webbtjänst.

När en webbtjänst har distribuerats skapas en standardslutpunkt för tjänsten. Standardslutpunkten kan anropas med dess API-nyckel. Du kan lägga till fler slutpunkter med sina egna nycklar från Web Services-portalen.
Varje slutpunkt i webbtjänsten adresseras, begränsas och hanteras oberoende av varandra. Varje slutpunkt är en unik URL med en auktoriseringsnyckel som du kan distribuera till dina kunder.

## <a name="add-endpoints-to-a-web-service"></a>Lägga till slutpunkter i en webbtjänst

Du kan lägga till en slutpunkt i en webbtjänst med hjälp av Portalen för Azure Machine Learning Web Services. När slutpunkten har skapats kan du använda den via synkrona API:er, batch-API:er och excel-kalkylblad.

> [!NOTE]
> Om du har lagt till ytterligare slutpunkter i webbtjänsten kan du inte ta bort standardslutpunkten.

1. Klicka på Webbtjänster i kolumnen Machine Learning Studio (klassiskt) i den vänstra navigeringskolumnen.
2. Klicka på **Hantera ändpunkter**längst ned på instrumentpanelen för webbtjänsten . Portalen Azure Machine Learning Web Services öppnas till slutpunkter sidan för webbtjänsten.
3. Klicka på **Ny**.
4. Skriv ett namn och en beskrivning för den nya slutpunkten. Slutpunktsnamnen måste vara 24 tecken eller mindre långa och måste bestå av gemener eller tal. Välj loggningsnivå och om exempeldata är aktiverade. Mer information om loggning finns i [Aktivera loggning för machine learning-webbtjänster](web-services-logging.md).

## <a name="scale-a-web-service-by-adding-additional-endpoints"></a><a id="scaling"></a>Skala en webbtjänst genom att lägga till ytterligare slutpunkter

Som standard är varje publicerad webbtjänst konfigurerad för att stödja 20 samtidiga begäranden och kan vara så hög som 200 samtidiga begäranden. Azure Machine Learning Studio (klassisk) optimerar automatiskt inställningen för att ge bästa prestanda för din webbtjänst och portalvärdet ignoreras.

Om du planerar att anropa API:et med en högre belastning än värdet för max samtidiga samtal på 200 stöder, bör du skapa flera slutpunkter på samma webbtjänst. Du kan sedan slumpmässigt fördela din belastning över dem alla.

Skalning av en webbtjänst är en vanlig uppgift. Några skäl att skala är att stödja mer än 200 samtidiga begäranden, öka tillgängligheten via flera slutpunkter eller tillhandahålla separata slutpunkter för webbtjänsten. Du kan öka skalan genom att lägga till ytterligare slutpunkter för samma webbtjänst via [Azure Machine Learning Web Service-portalen.](https://services.azureml.net/)

Tänk på att det kan vara skadligt att använda ett högt antal samtidighet om du inte anropar API:et med en motsvarande hög hastighet. Du kan se sporadiska timeouts och/eller toppar i svarstiden om du lägger en relativt låg belastning på ett API som konfigurerats för hög belastning.

De synkrona API:erna används vanligtvis i situationer där en låg svarstid önskas. Svarstid här innebär den tid det tar för API:et att slutföra en begäran och tar inte hänsyn till några nätverksförseningar. Anta att du har ett API med en svarstid på 50 ms. För att helt förbruka den tillgängliga kapaciteten med gaspåståndsnivå Hög och Max Samtidiga samtal = 20, måste du ringa detta API 20 * 1000 / 50 = 400 gånger per sekund. Om du utökar detta ytterligare kan du med ett Max Samtidiga anrop på 200 anropa API:4 000 gånger per sekund, förutsatt att du har en svarstid på 50 ms.

## <a name="next-steps"></a>Nästa steg

[Så här använder du en Azure Machine Learning-webbtjänst](consume-web-services.md).
