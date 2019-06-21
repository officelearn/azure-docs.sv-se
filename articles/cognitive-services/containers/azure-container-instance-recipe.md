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
ms.date: 06/19/2019
ms.author: dapine
ms.openlocfilehash: db73d4e30c960eb09e6b5fbc9411901c69c28b01
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67272968"
---
# <a name="deploy-and-run-container-on-azure-container-instance-aci"></a>Distribuera och köra behållare på Azure Container Instance (ACI)

Med följande steg, skala Azure Cognitive Services-program i molnet enkelt med Azure [Behållarinstans](https://docs.microsoft.com/azure/container-instances/) (ACI). Det här kan du fokusera på att bygga dina program i stället för att hantera infrastrukturen.

## <a name="prerequisites"></a>Nödvändiga komponenter

Den här lösningen fungerar med alla Cognitive Services-behållare. Cognitive Service-resursen måste skapas i Azure-portalen innan du använder den här recept. Alla kognitiva tjänster som stöder behållare har ett ”hur du installerar” dokument specifikt för att installera och konfigurera tjänsten för en behållare. Eftersom vissa tjänster kräver en fil eller en uppsättning filer som indata för behållaren, är det viktigt att du förstår och har använt behållaren har innan du använder den här lösningen.

* Resurs en Cognitive Service som skapas i Azure-portalen.
* Cognitive Service **slutpunkts-URL** -granska din specifik tjänst ”hur du installerar” för behållaren, för att hitta där slutpunkts-URL är från Azure-portalen och vad en rätt exempel på URL: en kan se ut. Vilket format du kan ändra från tjänst till tjänst.
* Cognitive Service **nyckel** -nycklarna finns på den **nycklar** för Azure-resursen. Du behöver bara en av två nycklar. Nyckeln är en sträng med 32 alfanumeriska tecken.
* En enda Cognitive Services-behållare på din lokala värd (din dator). Kontrollera att du kan:
  * Hämta avbildningen med en `docker pull` kommando.
  * Körts lokal behållare med alla nödvändiga konfigurationsinställningar med en `docker run` kommando.
  * Anropa behållarslutpunkten, få svar 2xx och ett JSON-svar tillbaka.

Alla variabler i vinkelparenteser, `<>`, måste de ersättas med dina egna värden. Den här ersättning innehåller hakparenteser.

## <a name="step-2-launch-your-container-on-azure-container-instances-aci"></a>Steg 2: Starta din behållare på Azure Container Instances (ACI)

**Skapa en resurs i Azure Container Instance (ACI).**

1. Gå till den [skapa](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) för Container Instances.

1. På den **grunderna** ange följande information:

    |Sidan|Inställning|Värde|
    |--|--|--|
    |Grundläggande inställningar|Prenumeration|Välj din prenumeration.|
    |Grundläggande inställningar|Resursgrupp|Välj den tillgängliga resursgruppen eller skapa en ny som `cognitive-services`.|
    |Grundläggande inställningar|Containerns namn|Ange ett namn som `cognitive-container-instance`. Det här namnet måste vara i lägre versaler.|
    |Grundläggande inställningar|Location|Välj en region för distribution.|
    |Grundläggande inställningar|Avbildningstyp|`Public`|
    |Grundläggande inställningar|Avbildningens namn|Ange platsen för Cognitive Services-behållare. Detta kan vara på samma plats som du använde i den `docker pull` kommandot _till exempel_: <br>`mcr.microsoft.com/azure-cognitive-services/sentiment`|
    |Grundläggande inställningar|OS-typ|`Linux`|
    |Grundläggande inställningar|Storlek|Ändra storlek till de föreslagna rekommendationerna för din specifika Cognitive Service-behållare.:<br>2 kärnor<br>4 GB
    ||||
  
1. På den **nätverk** ange följande information:

    |Sidan|Inställning|Värde|
    |--|--|--|
    |Nätverk|Portar|Redigera den befintliga porten för TCP från `80` till `5000`. Det innebär att du exponera behållaren på port 5000.|
    ||||

1. På den **Avancerat** ange följande information för att passera den behållare som krävs för fakturering inställningar till resursen i Container-instans:

    |Sidan Avancerat nyckel|Sidan Avancerat värde|
    |--|--|
    |`apikey`|Kopierade från den **nycklar** för resursen. Du behöver bara en av två nycklar. Det är en 32 alfanumeriska tecken lång sträng utan mellanslag eller tankstreck, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
    |`billing`|Kopierade från den **översikt** för resursen. |
    |`eula`|`accept`|

    Om din behållare har andra konfigurationsinställningar som indata monterar, utdata monterar eller loggning kan måste dessa inställningar också läggas till.

1. Välj **granska och skapa**.
1. När valideringen är godkänd väljer **skapa** att slutföra processen.
1. Välj på klockikonen i det övre navigeringsfältet. Det här är meddelandefönstret. Det visas en blå **gå till resurs** knappen när resursen har skapats. Välj knappen för att gå den nya resursen.

## <a name="use-the-container-instance"></a>Använd Container-instans

1. Välj den **översikt** och kopiera den IP-adressen. Det ska vara en numerisk IP-adress som `55.55.55.55`.
1. Öppna en ny webbläsarflik och använda IP-adressen, till exempel `http://<IP-address>:5000 (http://55.55.55.55:5000`). Du kan se startsidan för behållarens att behållaren körs.

1. Välj **API tjänstbeskrivning** att visa sidan swagger för behållaren.

1. Välj någon av de **POST** API: er och välj **prova**.  Parametrarna visas inklusive indata. Fyll i parametrar.

1. Välj **kör** att skicka din begäran till din Container Instance.

    Du har skapat och används Cognitive Services-behållare i Azure Container-instans.
