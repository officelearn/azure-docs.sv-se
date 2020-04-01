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
ms.openlocfilehash: 9a20dca71727e83db98c4c97567949bd127fc7fb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77111244"
---
[Meddelanderoutning](../articles/iot-hub/iot-hub-devguide-messages-d2c.md) gör det möjligt att skicka telemetridata från dina IoT-enheter till inbyggda eventnana-kompatibla slutpunkter eller anpassade slutpunkter som blob-lagring, servicebussköer, servicebussavsnitt och eventhubbar. Om du vill konfigurera anpassad meddelanderoutning skapar du [routningsfrågor](../articles/iot-hub/iot-hub-devguide-routing-query-syntax.md) för att anpassa vägen som matchar ett visst villkor. Därefter dirigeras inkommande data automatiskt till slutpunkterna av IoT Hub. Om ett meddelande inte matchar någon av de definierade routningsfrågorna dirigeras det till standardslutpunkten.

I den här självstudien i två delar får du lära dig hur du konfigurerar och använder dessa anpassade routningsfrågor med IoT Hub. Du dirigerar meddelanden från en IoT-enhet till en av flera slutpunkter, inklusive blob-lagring och en Service Bus-kö. Meddelanden till servicebusskön hämtas av en Logikapp och skickas via e-post. Meddelanden som inte har definierat anpassad meddelanderoutning skickas till standardslutpunkten och plockas sedan upp av Azure Stream Analytics och visas i en Power BI-visualisering.

Om du vill slutföra del 1 och 2 i den här självstudien utför du följande uppgifter:

**Del I: Skapa resurser, konfigurera meddelanderoutning**
> [!div class="checklist"]
> * Skapa resurserna – en IoT-hubb, ett lagringskonto, en Service Bus-kö och en simulerad enhet. Detta kan göras med hjälp av Azure-portalen, en Azure Resource Manager-mall, Azure CLI eller Azure PowerShell.
> * Konfigurera slutpunkter och meddelandevägar i IoT Hub för lagringskontot och Service Bus-kön.

**Del II: Skicka meddelanden till navet, visa dirigerade resultat**
> [!div class="checklist"]
> * Skapa en logikapp som utlöses och skickar e-post när ett meddelande läggs till i Service Bus-kön.
> * Ladda ned och kör en app som simulerar en IoT-enhet som skickar meddelanden till hubben för de olika dirigeringsalternativen.
> * Skapa en Power BI-visualisering för data som skickas till standardslutpunkten.
> * Visa resultatet ...
> * ...i Service Bus-kön och e-postmeddelandena.
> * ...i lagringskontot.
> * ...i Power BI-visualiseringen.

## <a name="prerequisites"></a>Krav

* För del 1 av den här självstudien:
  - Du måste ha en Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

* För del 2 av den här guiden:
  - Du måste ha slutfört del 1 av den här självstudien och ha resurserna fortfarande tillgängliga.
  - Installera [Visual Studio](https://www.visualstudio.com/).
  - Ha tillgång till ett Power BI-konto för att analysera standardslutpunktens strömanalys. ([Prova Power BI utan kostnad](https://app.powerbi.com/signupredirect?pbi_source=web).)
  - Har ett Office 365-konto för att skicka e-postmeddelanden.
  - Kontrollera att port 8883 är öppen i brandväggen. Exemplet i den här självstudien använder MQTT-protokollet, som kommunicerar över port 8883. Den här porten kan vara blockerad i vissa företags- och utbildningsnätverksmiljöer. Mer information och sätt att lösa problemet finns i [Ansluta till IoT Hub (MQTT)](../articles/iot-hub/iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]
