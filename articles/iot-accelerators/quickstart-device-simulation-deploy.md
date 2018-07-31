---
title: Prova att distribuera och köra en lösning för simulering av enheter på Azure | Microsoft Docs
description: I den här snabbstarten distribuerar du enhetsimuleringen Azure IoT-lösningsaccelerator. Du loggar in med instrumentpanelen för lösningen för att skapa en simulering.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: quickstart
ms.custom: mvc
ms.date: 07/05/2018
ms.author: dobett
ms.openlocfilehash: 549a1b867ad35c6de42969722ba5a2bd28c8f99a
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213164"
---
# <a name="quickstart-deploy-and-run-a-cloud-based-device-simulation-solution"></a>Snabbstart: Distribuera och köra en molnbaserad lösning för enhetssimulering

Den här snabbstarten visar hur du distribuerar den lösningsaccelerator för Azure IoT-enhetssimulering du använder för att testa din IoT-lösning. När du har distribuerat lösningsacceleratorn kan du använda sidan **Simulering** för att skapa och köra en simulering.

Du behöver en aktiv Azure-prenumeration för att kunna utföra den här snabbstarten.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="deploy-the-solution"></a>Distribuera lösningen

När du distribuerar lösningsacceleratorn till Azure-prenumerationen måste du ange några konfigurationsalternativ.

Logga in på [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators) med dina Azure-kontouppgifter.

Klicka på **Testa nu** på panelen **Enhetssimulering**.

![Välj enhetssimulering](./media/quickstart-device-simulation-deploy/devicesimulation.png)

På sidan **Skapa enhetssimuleringslösning** anger du ett unikt **Lösningsnamn**. Anteckna namnet på lösningen, det är namnet på den Azure-resursgrupp som innehåller alla resurser för lösningen.

Välj den **prenumeration** och den **region** du vill använda för att distribuera lösningsacceleratorn. Normalt väljer du regionen närmast dig. Du måste vara [global administratör eller användare](iot-accelerators-permissions.md) i prenumerationen.

Markera rutan för att distribuera en IoT Hub som ska användas med din enhetssimuleringslösning. Du kan alltid ändra den IoT Hub-simuleringen du använder senare.

Klicka på **Skapa lösning** för att påbörja etablera lösningen. Processen tar minst fem minuter att köra:

![Detaljer för enhetssimuleringslösning](./media/quickstart-device-simulation-deploy/createform.png)

## <a name="sign-in-to-the-solution"></a>Logga in på lösningen

När etableringen är klar kan logga du in på instrumentpanelen för lösningsacceleratorn för enhetssimuleringen.

På sidan **Etablerade lösningar** klickar du på den nya lösningsacceleratorn för enhetssimulering:

![Välj en ny lösning](./media/quickstart-device-simulation-deploy/choosenew.png)

Du kan visa information om lösningsacceleratorn för enhetssimuleringen på panelen som visas. Välj **Lösningens instrumentpanel** för att visa lösningsacceleratorn för enhetssimuleringen:

![Lösningens panel](./media/quickstart-device-simulation-deploy/solutionpanel.png)

Klicka på **Acceptera** för att acceptera begäran om behörigheter som instrumentpanelen för enhetssimuleringslösningen visar i webbläsaren:

[![Instrumentpanel för lösningen](./media/quickstart-device-simulation-deploy/solutiondashboard-inline.png)](./media/quickstart-device-simulation-deploy/solutiondashboard-expanded.png#lightbox)

## <a name="configure-the-simulation"></a>Konfigurera simuleringen

Du konfigurerar och kör en simulering från instrumentpanelen. Använd värdena i följande tabell för att konfigurera simuleringen:

| Inställning             | Värde                       |
| ------------------- | --------------------------- |
| Mål-IoT Hub      | Använd i förväg etablerad IoT Hub |
| Enhetsmodell        | Kylaggregat                     |
| Antal enheter   | 10                          |
| Telemetrifrekvens | 10 sekunder                  |
| Simuleringens varaktighet | 5 minuter                   |

[![Simuleringens konfiguration](./media/quickstart-device-simulation-deploy/simulationconfig-inline.png)](./media/quickstart-device-simulation-deploy/simulationconfig-expanded.png#lightbox)

## <a name="run-the-simulation"></a>Köra simuleringen

Klicka på **Starta simulering**. Simuleringen körs under hela den tid du har valt. Du kan stoppa simuleringen när som helst genom att klicka på **Stoppa simulering**. Simuleringen visar statistik för den aktuella körningen. Klicka på **View IoT Hub metrics in the Azure portal** (Visa IoT Hub-mått i Azure-portalen) för att se måtten som rapporteras av IoT Hub:

[![Körning av simulering](./media/quickstart-device-simulation-deploy/simulationrun-inline.png)](./media/quickstart-device-simulation-deploy/simulationrun-expanded.png#lightbox)

Du kan bara köra en simulering i taget från en etablerad instans av lösningsacceleratorn.

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill utforska ytterligare låter du lösningsacceleratorn för enhetssimuleringen förbli distribuerad.

Om du inte längre behöver lösningsacceleratorn tar du bort den från sidan [Etablerade lösningar](https://www.azureiotsolutions.com/Accelerators#dashboard) genom att välja den och sedan klicka på **Ta bort lösning**:

![Ta bort lösningen](media/quickstart-device-simulation-deploy/deletesolution.png)

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du distribuerat lösningsacceleratorn för enhetssimuleringen och kört en IoT-enhetssimulering.

I följande guide får du lära dig att använda en befintlig IoT Hub i en simulering:

> [!div class="nextstepaction"]
> [Använda en befintlig IoT Hub med lösningsaccelerator för enhetssimulering](iot-accelerators-device-simulation-choose-hub.md)
