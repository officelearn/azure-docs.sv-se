---
title: Recept för Azure Container instance
titleSuffix: Azure Cognitive Services
description: Lär dig hur du distribuerar Cognitive Services behållare på Azure Container instance
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: f247465c7e2c0a212df2821ebc7165d3ee5b15f3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "80876665"
---
# <a name="deploy-and-run-container-on-azure-container-instance"></a>Distribuera och köra container på Azure Container-instans

Med följande steg kan du enkelt skala Azure Cognitive Services-program i molnet med Azure [container instances](https://docs.microsoft.com/azure/container-instances/). Skapa behållare hjälper dig att fokusera på att skapa dina program i stället för att hantera infrastrukturen. Mer information om hur du använder behållare finns i [funktioner och förmåner](../cognitive-services-container-support.md#features-and-benefits).

## <a name="prerequisites"></a>Krav

Receptet fungerar med alla Cognitive Services behållare. Kognitiva tjänst resursen måste skapas i Azure Portal innan du kan använda receptet. Varje kognitiv tjänst som stöder behållare har ett "hur man installerar"-dokumentet för att installera och konfigurera tjänsten för en behållare. Vissa tjänster kräver en fil eller en uppsättning filer som indata för behållaren, det är viktigt att du förstår och har använt behållaren innan du använder den här lösningen.

* En kognitiv tjänst resurs som skapats i Azure Portal.
* **Webbplats slut punkts-URL** – granska din tjänsts "hur du installerar" för behållaren för att ta reda på var slut punkts-URL: en är inifrån Azure Portal och hur ett korrekt exempel på URL: en ser ut. Det exakta formatet kan ändras från tjänst till tjänst.
* **Nyckel** för kognitiva tjänster – nycklarna finns på sidan **nycklar** för Azure-resursen. Du behöver bara en av de två nycklarna. Nyckeln är en sträng med 32 alfanumeriska tecken.
* En enda Cognitive Services-behållare på din lokala värd (datorn). Kontrol lera att du kan:
  * Dra ned avbildningen med ett `docker pull` kommando.
  * Kör den lokala behållaren korrekt med alla konfigurations inställningar som krävs med ett `docker run` kommando.
  * Anropa behållarens slut punkt, få svar på HTTP-2xx och ett JSON-svar tillbaka.

Alla variabler inom vinkelparenteser, `<>` måste ersättas med dina egna värden. Den här ersättningen innehåller vinkelparenteser.

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](includes/create-container-instances-resource.md)]

## <a name="use-the-container-instance"></a>Använda behållar instansen

1. Välj **Översikt** och kopiera IP-adressen. Det kommer att vara en numerisk IP-adress, till exempel `55.55.55.55` .
1. Öppna en ny flik i webbläsaren och Använd IP-adressen, till exempel, `http://<IP-address>:5000 (http://55.55.55.55:5000` . Behållarens start sida visas, så att du vet att behållaren körs.

1. Välj **Service API-Beskrivning** för att Visa Swagger-sidan för behållaren.

1. Välj någon av **post** -API: erna och välj **prova**.  Parametrarna visas inklusive indatamängden. Fyll i parametrarna.

1. Välj **Kör** för att skicka begäran till behållar instansen.

    Du har skapat och använt Cognitive Services behållare i Azure Container instance.
