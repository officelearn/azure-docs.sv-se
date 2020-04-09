---
title: Recept för Azure-behållarinstans
titleSuffix: Azure Cognitive Services
description: Lär dig hur du distribuerar Cognitive Services-behållare på Azure Container Instance
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: f247465c7e2c0a212df2821ebc7165d3ee5b15f3
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876665"
---
# <a name="deploy-and-run-container-on-azure-container-instance"></a>Distribuera och köra container på Azure Container-instans

Med följande steg kan azure Cognitive Services-program i molnet enkelt skalas med Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/). Containerization hjälper dig att fokusera på att bygga dina program i stället för att hantera infrastrukturen. Mer information om hur du använder behållare finns i [funktioner och fördelar](../cognitive-services-container-support.md#features-and-benefits).

## <a name="prerequisites"></a>Krav

Receptet fungerar med alla Cognitive Services-behållare. Cognitive Service-resursen måste skapas i Azure-portalen innan receptet. Varje Cognitive Service som stöder behållare har ett "Hur man installerar" dokument specifikt för att installera och konfigurera tjänsten för en behållare. Vissa tjänster kräver en fil eller uppsättning filer som indata för behållaren, det är viktigt att du förstår och har använt behållaren framgångsrikt innan du använder den här lösningen.

* En Cognitive Service-resurs som skapats i Azure-portalen.
* **Url:en** för cognitive service - granska din specifika tjänsts "Hur du installerar" för behållaren, för att ta reda på var slutpunkts-URL:en finns inifrån Azure-portalen och hur ett korrekt exempel på webbadressen ser ut. Det exakta formatet kan ändras från service till service.
* Cognitive **Service-nyckel** - nycklarna finns på sidan **Nycklar** för Azure-resursen. Du behöver bara en av de två nycklarna. Nyckeln är en sträng med 32 alfanumeriska tecken.
* En enda Cognitive Services-behållare på din lokala värd (din dator). Se till att du kan:
  * Dra ned bilden `docker pull` med ett kommando.
  * Kör den lokala behållaren med alla `docker run` nödvändiga konfigurationsinställningar med ett kommando.
  * Anropa behållarens slutpunkt och få ett svar på HTTP 2xx och ett JSON-svar tillbaka.

Alla variabler i vinkelparenteser, `<>`måste ersättas med dina egna värden. Denna ersättning inkluderar vinkelfästen.

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](includes/create-container-instances-resource.md)]

## <a name="use-the-container-instance"></a>Använda behållarinstansen

1. Välj **översikten** och kopiera IP-adressen. Det kommer att vara en `55.55.55.55`numerisk IP-adress som .
1. Öppna en ny webbläsarflik och använd `http://<IP-address>:5000 (http://55.55.55.55:5000`till exempel IP-adressen ). Du kommer att se behållarens hemsida, så att du vet att behållaren körs.

1. Välj **Service API-beskrivning** om du vill visa skrytsidan för behållaren.

1. Välj någon av **POST API:erna** och välj Prova **det**.  Parametrarna visas inklusive indata. Fyll i parametrarna.

1. Välj **Kör om** du vill skicka begäran till behållarinstansen.

    Du har skapat och använt Cognitive Services-behållare i Azure Container Instance.
