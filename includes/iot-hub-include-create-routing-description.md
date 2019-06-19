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
ms.openlocfilehash: b640a3cb9382ad72bb48e06c6a7074c96409e2e4
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67187964"
---
<!-- description of message routing used in the Azure CLI, PowerShell, and RM routing articles.-->

Du kommer att dirigera meddelanden till olika resurser baserat på egenskaper som är anslutna till meddelandet av den simulerade enheten. Meddelanden som inte är dirigeringsanpassade skickas till standardslutpunkten (meddelanden/händelser). I nästa självstudie, skicka meddelanden till IoT Hub och se att de dirigeras till olika mål.

|value |Resultat|
|------|------|
|level="storage" |Skriv till Azure Storage.|
|level="critical" |Skriv till en Service Bus-kö. En logikapp hämtar meddelandet från kön och använder Office 365 för att skicka meddelandet via e-post.|
|standard |Visa dessa data med Power BI.|

Det första steget är att konfigurera slutpunkten som data ska dirigeras. Det andra steget är att ställa in meddelandeväg som använder denna slutpunkt. När du har installerat routning, kan du visa slutpunkter och meddelandevägar i portalen.