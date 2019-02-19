---
title: Testa att distribuera och köra en lösning för enhetssimulering på Azure | Microsoft Docs
description: I den här snabbstarten distribuerar du Azure IoT-enhetssimulering och kör en simulering
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/28/2018
ms.author: troyhop
ms.openlocfilehash: 84b232b175d45a12bb6e0f8b462849282144c19b
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56097584"
---
# <a name="quickstart-deploy-and-run-an-iot-device-simulation-in-azure"></a>Snabbstart: Distribuera och köra en IoT-enhetssimulering i Azure

Den här snabbstarten visar hur du distribuerar Azure IoT-enhetssimulering för att testa din IoT-lösning. När du har distribuerat lösningsacceleratorn kör du en exempelsimulering för att komma igång.

Du behöver en aktiv Azure-prenumeration för att kunna utföra den här snabbstarten.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="deploy-device-simulation"></a>Distribuera enhetssimulering

När du distribuerar enhetssimuleringen till din Azure-prenumeration måste du ange vissa konfigurationsalternativ.

Logga in på [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators) med dina Azure-kontouppgifter.

Klicka på panelen **Enhetssimulering**:

![Välj enhetssimulering](./media/quickstart-device-simulation-deploy/devicesimulation.png)

Klicka på **Testa nu** på sidan med beskrivning av enhetssimulering:

![Klicka på Testa nu](./media/quickstart-device-simulation-deploy/devicesimulationPDP.png)

På sidan **Skapa enhetssimuleringslösning** anger du ett unikt **Lösningsnamn**.

Välj den **prenumeration** och den **region** du vill använda för att distribuera lösningsacceleratorn. Normalt väljer du regionen närmast dig. Du måste vara [global administratör eller användare](iot-accelerators-permissions.md) i prenumerationen.

Markera rutan för att distribuera en IoT Hub som ska användas med din enhetssimuleringslösning. Du kan alltid ändra den IoT Hub-simuleringen du använder senare.

Klicka på **Skapa** för att börja etablera din lösning. Processen tar minst fem minuter att köra:

![Detaljer för enhetssimuleringslösning](./media/quickstart-device-simulation-deploy/createform.png)

## <a name="sign-in-to-the-solution"></a>Logga in på lösningen

När etableringsprocessen är klar kan du logga in på din instans av enhetssimulering genom att klicka på knappen **Starta**:

![Öppna enhetssimulering](./media/quickstart-device-simulation-deploy/choosenew.png)

Klicka på **Acceptera** för att acceptera den begäran om behörigheter som instrumentpanelen för enhetssimuleringslösningen visar i webbläsaren.

Vid första öppningen ser du instrumentpanelen för enhetssimulering med en **komma igång**-guide. Klicka på den första panelen så öppnas en exempelsimulering. Om du stänger **komma igång**-guiden kan du öppna **den enkla exempelsimuleringen** från instrumentpanelen genom att klicka på dess panel:

![Instrumentpanel för lösningen](./media/quickstart-device-simulation-deploy/GettingStarted.png)

## <a name="sample-simulation"></a>Exempelsimulering

Eftersom den är en exempelsimulering kan den inte redigeras. Simuleringen konfigureras med följande inställningar:

| Inställning             | Värde                       |
| ------------------- | --------------------------- |
| Mål-IoT Hub      | Använd i förväg etablerad IoT Hub |
| Enhetsmodell        | Lastbil                       |
| Antal enheter   | 10                          |
| Telemetrifrekvens | 10 sekunder                  |
| Simuleringens varaktighet | Kör utan tidsbegränsning            |

![Simuleringskonfiguration](./media/quickstart-device-simulation-deploy/SampleSimulation.png)

## <a name="run-the-simulation"></a>Köra simuleringen

Klicka på **Starta simulering**. Simuleringen körs utan tidsbegränsning enligt konfigurationen. Du kan stoppa simuleringen när som helst genom att klicka på **Stoppa simulering**. Simuleringen visar statistik för den aktuella körningen.

![Simuleringskörning](./media/quickstart-device-simulation-deploy/runningsimulation.png)

Du kan bara köra en simulering i taget från en enhetssimuleringsinstans.

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill utforska ytterligare låter du enhetssimuleringen förbli distribuerad.

Om du inte längre behöver enhetssimuleringen tar du bort den från sidan [Etablerade lösningar](https://www.azureiotsolutions.com/Accelerators#dashboard) genom att klicka på dess panel och sedan på **Ta bort lösning**:

![Ta bort lösningen](media/quickstart-device-simulation-deploy/deletesolution.png)

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du distribuerat och kört en exempelsimulering för IoT-enheter.

> [!div class="nextstepaction"]
> [Skapa en simulering med en eller flera enhetstyper](iot-accelerators-device-simulation-create-simulation.md)