---
title: Diagnostisera och felsöka frånkopplingar med Azure IoT Hub DPS
description: Lär dig att diagnostisera och felsöka vanliga fel med enhetsanslutning för DPS (Azure IoT Hub Device Provisioning Service)
author: xujing-ms
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: xujing
ms.openlocfilehash: 3cbab09c6b50abb590cfe9f2720713a8fa547aa7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646480"
---
# <a name="troubleshooting-with-azure-iot-hub-device-provisioning-service"></a>Felsökning med Azure IoT Hub Device Provisioning Service

Anslutningsproblem för IoT-enheter kan vara svåra att felsöka eftersom det finns många möjliga felpunkter, till exempel attesteringsfel, registreringsfel etc. Den här artikeln innehåller vägledning om hur du identifierar och felsöker problem med enhetsanslutning via [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview).

## <a name="using-azure-monitor-to-view-metrics-and-set-up-alerts"></a>Använda Azure Monitor för att visa mått och ställa in aviseringar

I följande procedur beskrivs hur du visar och ställer in avisering på mätvärden för etablering av IoT Hub Device Provisioning Service. 

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Bläddra till etableringstjänsten för IoT Hub Device.

3. Välj **Mått**.

4. Välj önskat mått. 
   <br />För närvarande finns det tre mått för DPS:

    | Måttnamn | Beskrivning |
    |-------|------------|
    | Attesteringsförsök | Antal enheter som försökte autentisera med enhetsetableringstjänsten|
    | Registreringsförsök | Antal enheter som försökte registrera sig till IoT Hub efter lyckad autentisering|
    | Enhet tilldelad | Antal enheter som har tilldelats IoT Hub|

5. Välj önskad aggregeringsmetod för att skapa en visuell vy av måttet. 

6. Om du vill ställa in en avisering om ett mått väljer du **Nya aviseringsregler** längst upp till höger i måttbladet, på samma sätt kan du gå till **Varningsbladet** och välja **Nya varningsregler**.

7. Välj **Lägg till villkor**och välj sedan önskat mått och tröskelvärde genom att följa uppmaningar.

Mer information finns [i Vad är klassiska aviseringar i Microsoft Azure?](../azure-monitor/platform/alerts-overview.md)

## <a name="using-log-analytic-to-view-and-resolve-errors"></a>Använda Loggalytic för att visa och lösa fel

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Bläddra till din IoT-hubb.

3. Välj **diagnostikinställningar**.

4. Välj **Aktivera diagnostik**.

5. Aktivera önskade loggar som ska samlas in.

    | Loggnamn | Beskrivning |
    |-------|------------|
    | DeviceOperations | Loggar relaterade till enhetsanslutningshändelser |
    | ServiceOperations | Händelseloggar relaterade till användning av tjänst SDK (t.ex. skapa eller uppdatera registreringsgrupper)|

6. Aktivera **Skicka till logganalys** ([se priser](https://azure.microsoft.com/pricing/details/log-analytics/)). 

7. Gå till fliken **Loggar** i Azure-portalen under Enhetsetableringstjänstresurs.

8. Klicka på **Kör** om du vill visa de senaste händelserna.

9. Om det finns resultat `OperationName` `ResultType`letar `ResultSignature`du `ResultDescription` efter , , och (felmeddelande) för att få mer information om felet.


## <a name="common-error-codes"></a>Vanliga felkoder
Använd den här tabellen för att förstå och lösa vanliga fel.

| Felkod| Beskrivning | HTTP-statuskod |
|-------|------------|------------|
| 400 | Begärans brödtext är ogiltig. Det går till exempel inte att tolkas eller så kan inte objektet valideras.| 400 Dåligt format |
| 401 | Auktoriseringstoken kan inte valideras. Den har till exempel upphört att gälla eller gäller inte för begärans URI. Den här felkoden returneras också till enheter som en del av TPM-attestationflödet. | 401 Obehörig|
| 404 | Instansen enhetsetableringstjänst eller en resurs (t.ex. en registrering) finns inte. |404 – Hittades inte |
| 412 | ETag i begäran matchar inte ETag för den befintliga resursen, enligt RFC7232. | 412 Förutsättningen misslyckades |
| 429 | Åtgärder begränsas av tjänsten. Information om specifika tjänstbegränsningar finns i begränsningar för [IoT Hub Device Provisioning Service](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#iot-hub-device-provisioning-service-limits). | 429 För många förfrågningar |
| 500 | Ett internt fel uppstod. | 500 Internt serverfel|
