---
title: ta med fil
description: ta med fil
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/05/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 400f12237ae8b8cbaf6d66bda1663ecb680136f3
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58630984"
---
[Meddelanderoutning](../articles/iot-hub/iot-hub-devguide-messages-d2c.md) kan skicka telemetridata från dina IoT-enheter till den inbyggda Event Hub-kompatibla slutpunkter eller anpassade slutpunkter som blob storage, Service Bus-köer, Service Bus-ämnen och Händelsehubbar. Om du vill konfigurera anpassade meddelanderoutning måste du skapa [routning frågor](../articles/iot-hub/iot-hub-devguide-routing-query-syntax.md) att anpassa den väg som matchar ett visst villkor. Därefter dirigeras inkommande data automatiskt till slutpunkterna av IoT Hub. Om meddelandet inte matchar någon av de definierade routning frågorna, dirigeras den till standardslutpunkt.

I de här självstudierna 2 delar du lära dig hur du konfigurerar och använder dessa anpassade routning frågor med IoT Hub. Du vidarebefordra meddelanden från en IoT-enhet till en av flera slutpunkter, inklusive blob-lagring och en Service Bus-kö. Meddelanden i Service Bus-kön slutpunktsstatus uppfattas av en Logikapp och skickas via e-post. Meddelanden som inte har anpassat meddelanderoutning definierats skickas till standardslutpunkt och sedan plockas av Azure Stream Analytics och visas i en Power BI-visualisering.

 För fullständig delar 1 och 2 i den här självstudien kan du utföra följande uppgifter:

**En del I: Skapa resurser, konfigurera meddelanderoutning**
> [!div class="checklist"]
> * Skapa resurser – en IoT-hubb, ett lagringskonto, en Service Bus-kö och en simulerad enhet. Detta kan göras med hjälp av portalen, Azure CLI, Azure PowerShell eller en Azure Resource Manager-mall.
> * Konfigurera slutpunkter och meddelandevägar i IoT Hub för storage-konto och Service Bus-kö.

**Del II: Skicka meddelanden till hubben, visa routade resultat**
> [!div class="checklist"]
> * Skapa en logikapp som utlöses och skickar e-post när ett meddelande läggs till i Service Bus-kön.
> * Ladda ned och kör en app som simulerar en IoT-enhet som skickar meddelanden till hubben för de olika dirigeringsalternativen.
> * Skapa en Power BI-visualisering för data som skickas till standardslutpunkten.
> * Visa resultatet ...
> * ...i Service Bus-kön och e-postmeddelandena.
> * ...i lagringskontot.
> * ...i Power BI-visualiseringen.

## <a name="prerequisites"></a>Förutsättningar

* För del 1 av självstudien:
  - Du måste ha en Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

* För en del 2 av den här självstudien:
  - Du har slutfört del 1 av självstudiekursen och har resurserna som är fortfarande tillgängliga.
  - Installera [Visual Studio](https://www.visualstudio.com/).
  - Ett Power BI-konto för att analysera Stream Analytics för standardslutpunkten. ([Prova Power BI utan kostnad](https://app.powerbi.com/signupredirect?pbi_source=web).)
  - Ett Office 365-konto för att skicka e-postmeddelanden.

[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]
