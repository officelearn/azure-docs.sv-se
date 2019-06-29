---
title: Azure Container Instance recept
titleSuffix: Azure Cognitive Services
description: Lär dig hur du distribuerar Cognitive Services-behållare i Azure Container Instance
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 45a03a0912681b4fc33ef8df88fa00fd5458f720
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445825"
---
# <a name="deploy-and-run-container-on-azure-container-instance-aci"></a>Distribuera och köra behållare på Azure Container Instance (ACI)

Med följande steg, skala Azure Cognitive Services-program i molnet enkelt med Azure [Behållarinstans](https://docs.microsoft.com/azure/container-instances/) (ACI). Det här kan du fokusera på att bygga dina program i stället för att hantera infrastrukturen.

## <a name="prerequisites"></a>Förutsättningar

Den här lösningen fungerar med alla Cognitive Services-behållare. Cognitive Service-resursen måste skapas i Azure-portalen innan du använder den här recept. Alla kognitiva tjänster som stöder behållare har ett ”hur du installerar” dokument specifikt för att installera och konfigurera tjänsten för en behållare. Eftersom vissa tjänster kräver en fil eller en uppsättning filer som indata för behållaren, är det viktigt att du förstår och har använt behållaren har innan du använder den här lösningen.

* Resurs en Cognitive Service som skapas i Azure-portalen.
* Cognitive Service **slutpunkts-URL** -granska din specifik tjänst ”hur du installerar” för behållaren, för att hitta där slutpunkts-URL är från Azure-portalen och vad en rätt exempel på URL: en kan se ut. Vilket format du kan ändra från tjänst till tjänst.
* Cognitive Service **nyckel** -nycklarna finns på den **nycklar** för Azure-resursen. Du behöver bara en av två nycklar. Nyckeln är en sträng med 32 alfanumeriska tecken.
* En enda Cognitive Services-behållare på din lokala värd (din dator). Kontrollera att du kan:
  * Hämta avbildningen med en `docker pull` kommando.
  * Körts lokal behållare med alla nödvändiga konfigurationsinställningar med en `docker run` kommando.
  * Anropa behållarslutpunkten, få svar 2xx och ett JSON-svar tillbaka.

Alla variabler i vinkelparenteser, `<>`, måste de ersättas med dina egna värden. Den här ersättning innehåller hakparenteser.

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances (ACI)](./includes/create-aci-resource.md)]

## <a name="use-the-container-instance"></a>Använd Container-instans

1. Välj den **översikt** och kopiera den IP-adressen. Det ska vara en numerisk IP-adress som `55.55.55.55`.
1. Öppna en ny webbläsarflik och använda IP-adressen, till exempel `http://<IP-address>:5000 (http://55.55.55.55:5000`). Du kan se startsidan för behållarens att behållaren körs.

1. Välj **API tjänstbeskrivning** att visa sidan swagger för behållaren.

1. Välj någon av de **POST** API: er och välj **prova**.  Parametrarna visas inklusive indata. Fyll i parametrar.

1. Välj **kör** att skicka din begäran till din Container Instance.

    Du har skapat och används Cognitive Services-behållare i Azure Container-instans.
