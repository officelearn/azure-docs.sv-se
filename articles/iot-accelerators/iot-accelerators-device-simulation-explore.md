---
title: Kom igång med enheten simuleringen lösningen - Azure | Microsoft Docs
description: IoT-lösningen acceleratorer simuleringen-lösningen är ett verktyg som kan användas för att hjälpa utvecklingen och testningen av en IoT-lösning. Tjänsten simuleringen är ett fristående erbjudande som kan användas tillsammans med andra solution Accelerator eller användas med din egen anpassade lösningar.
author: troyhopwood
manager: ''
ms.author: troyhop
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 12/15/2017
ms.topic: conceptual
ms.openlocfilehash: c427f2640e605533324eb349579c6a40a2a6a47f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34627133"
---
# <a name="device-simulation-walkthrough"></a>Enheten simuleringen genomgång

Azure IoT-enhet simuleringen är ett verktyg som kan användas för att hjälpa utvecklingen och testningen av en IoT-lösning. Enheten simuleringen är en fristående erbjudande som du kan använda tillsammans med andra solution Accelerator eller med din egen anpassade lösningar.

Den här självstudiekursen vägleder dig igenom några av funktionerna i enheten simuleringen. Den visar hur det fungerar och gör att du kan använda den för att testa din egen IoT-lösningar.

I den här guiden får du lära dig att:

>[!div class="checklist"]
> * Konfigurera en simulering
> * Starta och stoppa en simulering
> * Visa telemetri mått

## <a name="prerequisites"></a>Förutsättningar

Den här kursen behöver en distribuerad instans av Azure IoT-enhet simulering i din Azure-prenumeration.

Om du inte har distribuerat enheten simuleringen ännu, bör du genomföra den [distribuera Azure IoT-enhet simuleringen](iot-accelerators-device-simulation-deploy.md) kursen.

## <a name="configuring-device-simulation"></a>Konfigurera simulering av enheten

Du kan konfigurera och köra enheten simuleringen helt från instrumentpanelen. Öppna instrumentpanelen från IoT solution Accelerator [etablerade lösningar](https://www.azureiotsolutions.com/) sidan. Klicka på **starta** under distributionen av nya enheter simuleringen.

### <a name="target-iot-hub"></a>Målet IoT-hubb

Du kan använda enheten simuleringen företablerad IoT-hubb eller med andra IoT-hubb:

![Målet IoT-hubb](./media/iot-accelerators-device-simulation-explore/targethub.png)

> [!NOTE]
> Alternativet att använda en företablerad IoT-hubb är endast tillgängligt om du valde att skapa en ny IoT-hubb när du distribuerade enheten simuleringen. Om du inte har en IoT-hubb, kan du alltid skapa en ny från den [Azure-portalen](https://portal.azure.com).

Om du vill rikta specifika IoT-hubb, ange den **iothubowner** anslutningssträngen. Du kan hämta den här anslutningssträngen från den [Azure-portalen](https://portal.azure.com):

1. På konfigurationssidan IoT-hubb i Azure-portalen klickar du på **principer för delad åtkomst**.
1. Klicka på **iothubowner**.
1. Kopiera antingen den primära och sekundära nycklarna.
1. Klistra in det här värdet i textrutan under mål IoT-hubb.

![Målet IoT-hubb](./media/iot-accelerators-device-simulation-explore/connectionstring.png)

### <a name="device-model"></a>Enhetsmodell

Enhetsmodellen kan du välja vilken typ av enhet för att simulera. Du kan välja något av förkonfigurerade enhetsmodeller eller definiera en lista över sensorer för en anpassad enhetsmodell:

![Enhetsmodell](./media/iot-accelerators-device-simulation-explore/devicemodel.png)

#### <a name="pre-configured-device-models"></a>Förkonfigurerade enhetsmodeller

Enheten simuleringen innehåller tre förkonfigurerade enhetsmodeller. Enhetsmodeller för Chillers och hissar lastbilar är tillgängliga.

Förkonfigurerade enhetsmodeller inkluderar flera sensorer med avancerade funktioner som definierats i en JavaScript-fil. Dessa anpassade beteenden stöds inte i webbgränssnittet. 

I följande tabell visas en lista över konfigurationer för varje förkonfigurerade enhetsmodell:

| Enhetsmodell | Sensor | Enhet | 
| -------------| ------ | -----| 
| Kylaggregat | fuktighet | % |
| | tryck | psig | 
| | temperatur | F | 
| Snabba | våning | 
| | Vibration | mm | 
| | Temperatur | F | 
| Lastbil | Latitud | |
| | Longitud | | 
| | Hastighet | mph | 
| | cargotemperature | F | 

#### <a name="custom-device-model"></a>Anpassade enhetsmodell

Anpassade enhetsmodeller för att aktivera till modellen sensorer som representerar närmare egna enheter. En anpassad enhet kan ha upp till 10 anpassade sensorer.

När du väljer modelltypen anpassad enhet kan du lägga till nya sensorer genom att klicka på **+ Lägg till sensor**.

![Lägg till sensorer](./media/iot-accelerators-device-simulation-explore/customsensors.png)

Anpassade sensorer har följande egenskaper:

| Fält | Beskrivning |
| ----- | ----------- |
| Sensornamnet | Ett eget namn för sensor som **temperatur** eller **hastighet**. |
| Beteende | Beteenden aktivera telemetridata att skilja sig från ett meddelande till nästa att simulera verkliga data. **Öka** ökar värdet med ett i varje meddelande som skickas från minimivärdet. När det maximala värdet är uppfyllt, sedan börjar över igen på det lägsta värdet. **Minska** fungerar på samma sätt som **ökning** men räknar ned. Den **slumpmässiga** beteende genererar ett slumpmässigt värde mellan det lägsta värdet och högsta värde. |
| Värdet för minsta | Den lägsta tal som representerar det godkända intervallet. |
| Värdet för maximalt antal | Det största antalet som representerar det godkända intervallet. |
| Enhet | Måttenheten för sensor, till exempel ° F eller MPH. |

### <a name="number-of-devices"></a>Antal enheter

Enheten simuleringen kan för närvarande du simulera upp till 20 000 enheter.

![Antal enheter](./media/iot-accelerators-device-simulation-explore/numberofdevices.png)

### <a name="telemetry-frequency"></a>Telemetri frekvens

Telemetri frekvens kan du ange hur ofta simulerade enheterna ska skicka data till IoT-hubben. Du kan skicka data som ofta som var 10: e sekund eller som sällan varje som 99 timmar, 59 minuter och 59 sekunder.

![Telemetri frekvens](./media/iot-accelerators-device-simulation-explore/frequency.png)

> [!NOTE]
> Om du använder en IoT-hubb än företablerad IoT-hubben, bör du överväga meddelandet gränser för din IoT-hubb för målet. Till exempel om du har 1 000 simulerade enheter skickas telemetri var 10: e sekund till en S1 hubb når du gränsen telemetri för hubben i endast över en timme.

### <a name="simulation-duration"></a>Simuleringen varaktighet

Du kan välja att köra simuleringen under en viss tid eller att köras tills du avbryter den. När du väljer en bestämd tidsperiod, väljer du varaktighet från 10 minuter upp till 99 timmar, 59 minuter och 59 sekunder.

![Simuleringen varaktighet](./media/iot-accelerators-device-simulation-explore/duration.png)

### <a name="start-and-stop-the-simulation"></a>Starta och stoppa simuleringen

När du har lagt till alla nödvändiga konfigurationsdata i formuläret på **starta simuleringen** knappen är aktiverad. Klicka här om du vill starta simuleringen.

![Starta simulering](./media/iot-accelerators-device-simulation-explore/start.png)

Om du har angett en viss tid för simuleringen sedan stoppas automatiskt när tiden har uppnåtts. Du kan alltid stoppa simuleringen tidigt genom att klicka på **stoppa simuleringen.**

Om du väljer att köra simuleringen på obestämd tid, så att den körs förrän du klickar på **stoppa simuleringen**. Du kan stänga webbläsaren och gå tillbaka till sidan simulering av enheten för att stoppa simuleringen när som helst.

![Stoppa simulering](./media/iot-accelerators-device-simulation-explore/stop.png)

> [!NOTE]
> Endast en simulering kan köras samtidigt. Innan du startar en ny simulering måste du stoppa pågående simuleringen.
