---
title: Självstudie om att identifiera avvikelser vid gränsen i en lösning | Microsoft Docs
description: I den här självstudiekursen lär du dig hur du övervakar IoT Edge-enheter med acceleratorn för fjärrövervakningslösningen.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/08/2018
ms.topic: tutorial
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: e234cc29589e7b104ea7ef54ab7493e27d11cf27
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460429"
---
# <a name="tutorial-detect-anomalies-at-the-edge-with-the-remote-monitoring-solution-accelerator"></a>Självstudie: Identifiera avvikelser vid gränsen med acceleratorn för fjärrövervakningslösningen

I den här självstudien konfigurerar du fjärrövervakningslösningen för att svara på avvikelser som identifierats av en IoT Edge-enhet. Med IoT Edge-enheter kan du bearbeta telemetri vid gränsen för att minska mängden telemetri som skickas till lösningen och få snabbare svar på händelser på enheter. Läs mer om fördelarna med Edge-bearbetning i [Vad är Azure IoT Edge](../iot-edge/about-iot-edge.md).

I den här självstudien används en simulerad oljepumpjackenhet för att introducera Edge-bearbetning med fjärrövervakning. Oljepumpjacket hanteras av en organisation som heter Contoso och är anslutet till acceleratorn Fjärrövervakningslösning. Sensorer på oljepumpjacket mäter temperatur och tryck. Operatörer på Contoso vet att en onormal temperaturökning kan orsaka att oljepumpjacket blir långsammare. Operatörer på Contoso behöver inte övervaka enhetens temperatur när det är inom det normala intervallet.

Contoso vill distribuera en modul för den intelligenta nätverksgränsen till oljepumpjacket som identifierar temperaturavvikelser. En annan Edge-modul skickar aviseringar till fjärrövervakningslösningen. När en avisering tas emot kan en Contoso-operator skicka en underhållstekniker. Contoso kan också konfigurera en automatisk åtgärd, som att skicka ett e-postmeddelande, som ska köras när lösningen tar emot en avisering.

Följande diagram visar huvudkomponenterna i självstudiescenariot:

![Diagram som visar att olje Pumps uttag är anslutet till i o T Edge Stream Analytics-modulen i o T Edge-enheten för telemetri och kommandon. Filtrerad telemetri går till i o T Edge-enheten i den fjärrstyrda lösnings acceleratorn i molnet. Molnet innehåller också distribution och paket. Distributionen distribuerar i o T Edge-körningsmiljön i enheten.](media/iot-accelerators-remote-monitoring-edge/overview.png)

I den här kursen får du:

>[!div class="checklist"]
> * Lägga till en IoT Edge-enhet till lösningen
> * Skapa ett Edge-manifest
> * Importera manifestet som ett paket som definierar modulerna som ska köras på enheten
> * Distribuera paketet till din IoT Edge-enhet
> * Visa aviseringar från enheten

På IoT Edge-enheten:

* Körningen tar emot paketet och installerar modulerna.
* Stream Analytics-modulen identifierar temperaturavvikelser i pumpen och skickar kommandon för att lösa problemet.
* Stream Analytics-modulen vidarebefordrar filtrerade data till lösningsacceleratorn.

I den här självstudien används en virtuell Linux-dator som en IoT Edge-enhet. Du installerar också en Edge-modul för att simulera oljepumpjackenheten.

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="add-an-iot-edge-device"></a>Lägga till en IoT Edge-enhet

Det finns två steg för att lägga till en IoT Edge-enhet i acceleratorn för fjärrövervakningslösningen. Det här avsnittet visar hur du använder:

* Lägg till en IoT Edge-enhet på sidan **Device Explorer** i webbgränssnittet för fjärrövervakning.
* Installera IoT Edge-körning i en Linux-dator (VM).

### <a name="add-an-iot-edge-device-to-your-solution"></a>Lägga till en IoT Edge-enhet till din lösning

Om du vill lägga till en IoT Edge-enhet till acceleratorn för fjärrövervakningslösningen navigerar du till sidan **Device Explorer** i webbgränssnittet och klickar på **+ Ny enhet**.

I panelen **Ny enhet** väljer du **IoT Edge-enhet** och anger **oil-pump** som enhetens ID. Du behålla standardvärden för de andra inställningarna. Klicka sedan på **Använd**:

[![Lägg till IoT Edge enhet](./media/iot-accelerators-remote-monitoring-edge/addedgedevice-inline.png)](./media/iot-accelerators-remote-monitoring-edge/addedgedevice-expanded.png#lightbox)

Anteckna enhetsanslutningssträngen. Du behöver den i nästa avsnitt av den här självstudien.

När du registrerar en enhet med IoT-hubben i acceleratorn för fjärrövervakningslösningen visas den på sidan **Device Explorer** i webbgränssnittet:

[![Ny IoT Edge enhet](./media/iot-accelerators-remote-monitoring-edge/newedgedevice-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newedgedevice-expanded.png#lightbox)

Om du vill göra det enklare att hantera IoT Edge-enheter i lösningen kan du skapa en enhetsgrupp och lägga till IoT Edge-enheten:

1. Välj enheten **oil-pump** i listan på sidan **Device Explorer** och klicka på **Jobb**.

1. Skapa ett jobb för att lägga till taggen **IsEdge** till tjänsten med följande inställningar:

    | Inställning | Värde |
    | ------- | ----- |
    | Jobb     | Taggar  |
    | Jobbnamn | AddEdgeTag |
    | Nyckel     | IsOilPump |
    | Värde   | Y     |
    | Typ    | Text  |

    [![Lägg till tagg](./media/iot-accelerators-remote-monitoring-edge/addtag-inline.png)](./media/iot-accelerators-remote-monitoring-edge/addtag-expanded.png#lightbox)

1. Klicka på **Använd** och sedan på **Stäng**.

1. På sidan **Device Explorer** klickar du på **Hantera enhets grupper**.

1. Klicka på **Skapa ny enhetsgrupp**. Skapa en ny enhetsgrupp med följande inställningar:

    | Inställning | Värde |
    | ------- | ----- |
    | Namn    | OilPumps |
    | Fält   | Tags.IsOilPump |
    | Operator | = Lika med |
    | Värde    | Y |
    | Typ     | Text |

    [![Skapa enhets grupp](./media/iot-accelerators-remote-monitoring-edge/createdevicegroup-inline.png)](./media/iot-accelerators-remote-monitoring-edge/createdevicegroup-expanded.png#lightbox)

1. Klicka på **Spara**.

Din IoT Edge-enhet är nu i gruppen **OilPumps**.

### <a name="install-the-edge-runtime"></a>Installera Edge-körning

En Edge-enhet kräver att Edge-körningen är installerad. I den här självstudien installerar du Edge-körningen i en virtuell Linux-dator i Azure för att testa scenariot. I följande steg används Azure Cloud Shell för att installera och konfigurera den virtuella datorn:

1. Kör följande kommandon för att skapa en Linux-VM i Azure. Du kan använda en plats i närheten av där du är:

    ```azurecli-interactive
    az group create \
      --name IoTEdgeDevices \
      --location eastus
    az vm create \
      --resource-group IoTEdgeDevices \
      --name EdgeVM \
      --image microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest \
      --admin-username azureuser \
      --generate-ssh-keys \
      --size Standard_B1ms
    ```

1. Kör följande kommando med den enhetsanslutningssträng du antecknade tidigare för att konfigurera Edge-körningen med enhetsanslutningssträngen:

    ```azurecli-interactive
    az vm run-command invoke \
      --resource-group IoTEdgeDevices \
      --name EdgeVM \
      --command-id RunShellScript \
      --scripts 'sudo /etc/iotedge/configedge.sh "YOUR_DEVICE_CONNECTION_STRING"'
    ```

    Kom ihåg att ange anslutningssträngen inom dubbla citattecken.

Du har nu installerat och konfigurerat IoT Edge-körningen på en Linux-enhet. Senare i den här självstudien använder du fjärrövervakningslösningen för att distribuera IoT Edge-moduler till den här enheten.

## <a name="create-an-edge-manifest"></a>Skapa ett Edge-manifest

Om du vill simulera oljejackpumpenheten måste du lägga till följande moduler i din Edge-enhet:

* Temperatursimuleringsläge.
* Avvikelseidentifiering i Azure Stream Analytics.

Följande steg visar hur du skapar ett manifest för Edge-distribution som innehåller dessa moduler. Senare i den här självstudien importerar du manifestet som ett paket i acceleratorn för fjärrövervakningslösningen.

### <a name="create-the-azure-stream-analytics-job"></a>Skapa Azure Stream Analytics-jobbet

Du definierar Stream Analytics-jobbet i portalen innan du paketerar den som en Edge-modul.

1. I Azure-portalen skapar du ett Azure Storage-konto med standardalternativen i resursgruppen **IoTEdgeDevices**. Anteckna namnet som du har valt.

1. I Azure-portalen skapar du ett **Stream Analytics-jobb** i resursgruppen **IoTEdgeDevices**. Använd följande konfigurationsvärden:

    | Alternativ | Värde |
    | ------ | ----- |
    | Jobbnamn | EdgeDeviceJob |
    | Prenumeration | Din Azure-prenumeration |
    | Resursgrupp | IoTEdgeDevices |
    | Plats | USA, östra |
    | Värdmiljö | Edge |
    | Strömningsenheter | 1 |

1. Öppna Stream Analytics-jobbet **EdgeDeviceJob** i portalen, klicka på Indata och lägger till **Edge Hub**-strömindata med namnet **telemetry**.

1. I Stream Analytics-jobbet **EdgeDeviceJob** i portalen klickar du på **Utdata** och lägger till **Edge Hub**-utdata med namnet **output**.

1. I Stream Analytics-jobbet **EdgeDeviceJob** i portalen klickar du på **Utdata** och lägger till ytterligare **Edge Hub**-utdata med namnet **alert**.

1. I **EdgeDeviceJob** Stream Analytics-jobbet i portalen klickar du på **Fråga** och lägger till följande **select**-instruktion:

    ```sql
    SELECT  
    avg(machine.temperature) as temperature, 
    'F' as temperatureUnit 
    INTO output 
    FROM telemetry TIMESTAMP BY timeCreated 
    GROUP BY TumblingWindow(second, 5) 
    SELECT 'reset' as command 
    INTO alert 
    FROM telemetry TIMESTAMP BY timeCreated 
    GROUP BY TumblingWindow(second, 3) 
    HAVING avg(machine.temperature) > 400
    ```

1. I **EdgeDeviceJob** Stream Analytics-jobbet i portalen klickar du på **Inställningar för lagringskonto**. Lägg till lagringskontot som du lade till i resursgruppen **IoTEdgeDevices** i början av det här avsnittet. Skapa en ny container med namnet **edgeconfig**.

Följande skärmbild visar det sparade Stream Analytics-jobbet:

[![Stream Analytics jobb](./media/iot-accelerators-remote-monitoring-edge/streamjob-inline.png)](./media/iot-accelerators-remote-monitoring-edge/streamjob-expanded.png#lightbox)

Du har nu definierat ett Stream Analytics-jobb som ska köras på din Edge-enhet. Jobbet beräknar medeltemperaturen i ett fönster i intervaller om fem sekunder. Jobbet skickar också en avisering om medeltemperaturen i ett tresekundersfönster överstiger 400.

### <a name="create-the-iot-edge-deployment"></a>Skapa IoT Edge-distributionen

Därefter skapar du ett IoT Edge-distributionsmanifest som definierar vilka moduler som ska köras på din Edge-enhet. I nästa avsnitt importerar du manifestet som ett paket i acceleratorn för fjärrövervakningslösningen.

1. Navigera till IoT-hubben för fjärrövervakningslösningen i Azure-portalen. Du kan hitta IoT-hubben i resursgruppen som har samma namn som fjärrövervakningslösningen.

1. I IoT-hubben klickar du på **IoT Edge** i avsnittet **Automatisk enhetshantering**. Klicka på **Lägg till en IoT Edge-distribution**.

1. På sidan **Skapa distribution > Namn och etikett** anger du namnet **oil-pump-device**. Klicka på **Nästa**.

1. På sidan **Skapa distribution > Lägg till moduler** klickar du på **+ Lägg till**. Välj **IoT Edge-modul**.

1. I panelen **Anpassade IoT Edge-moduler** anger du **temperatureSensor** som namn och **asaedgedockerhubtest/asa-edge-test-module:sensor-ad-linux-amd64** som bild-URI. Klicka på **Spara**.

1. På sidan **Skapa distribution > Lägg till moduler** klickar du på **+ Lägg till** för att lägga till en andra modul. Välj **Azure Stream Analytics-modul**.

1. I panelen **Edge-distribution** väljer du din prenumeration och det **EdgeDeviceJob** du skapade i föregående avsnitt. Klicka på **Spara**.

1. På sidan **Skapa distribution > Lägg till moduler** klickar du på **Nästa**.

1. På sidan **Skapa distribution > Ange rutter** lägger du till följande kod:

    ```sql
    {
      "routes": { 
        "alertsToReset": "FROM /messages/modules/EdgeDeviceJob/outputs/alert INTO BrokeredEndpoint(\"/modules/temperatureSensor/inputs/control\")", 
        "telemetryToAsa": "FROM /messages/modules/temperatureSensor/* INTO BrokeredEndpoint(\"/modules/EdgeDeviceJob/inputs/telemetry\")", 
        "ASAToCloud": "FROM /messages/modules/EdgeDeviceJob/* INTO $upstream" 
      } 
    }
    ```

    Den här koden dirigerar utdata från Stream Analytics-modulen till rätt platser.

    Klicka på **Nästa**.

1. På sidan **Skapa distribution > Ange mått** klickar du på **Nästa**.

1. På sidan **Skapa distribution > Målenheter** anger du 10 som prioritet. Klicka på **Nästa**.

1. På sidan **Skapa distribution > Granska distributionen** klickar du på **Skicka**:

    [![Granska distribution](./media/iot-accelerators-remote-monitoring-edge/reviewdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/reviewdeployment-expanded.png#lightbox)

1. På huvudsidan **IoT Edge** klickar du på **IoT Edge-distributioner**. Du kan se **oil-pump-device** i listan över distributioner.

1. Klicka på distributionen **oil-pump-device** och klicka sedan på **Ladda ned IoT Edge-manifest**. Spara filen som **oil-pump-device.json** på en lämplig plats på din lokala datorn. Du behöver den här filen i nästa avsnitt i den här självstudien.

Nu har du skapat ett IoT Edge manifest som du kan importera till den fjärranslutna övervaknings lösningen som ett paket. Vanligtvis skapar en utvecklare IoT Edge-modulerna och manifestfilen.

## <a name="import-a-package"></a>Importera ett paket

I det här avsnittet importerar du Edge-manifestet som ett paket i acceleratorn för fjärrövervakningslösningen.

1. I webbgränssnittet för fjärrövervakning går du till sidan **Paket** och klickar på **+ Nytt paket**:

    [![Nytt paket](./media/iot-accelerators-remote-monitoring-edge/newpackage-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newpackage-expanded.png#lightbox)

1. I panelen **Nytt paket** väljer du **Edge-manifest** som pakettyp, klickar på **Bläddra** för att hitta filen **oil-pump-device.json** på din lokala dator och klicka på **Ladda upp**:

    [![Ladda upp paket](./media/iot-accelerators-remote-monitoring-edge/uploadpackage-inline.png)](./media/iot-accelerators-remote-monitoring-edge/uploadpackage-expanded.png#lightbox)

    Listan över paket innehåller nu paketet **olje-pump-device.json**.

I nästa avsnitt skapar du en distribution som gäller paketet för din Edge-enhet.

## <a name="deploy-a-package"></a>Distribuera ett paket

Nu är du redo att distribuera paketet till din enhet.

1. I webbgränssnittet för fjärrövervakning går du till sidan **Distributioner** och klickar på **+ Ny distribution**:

    [![Ny distribution](./media/iot-accelerators-remote-monitoring-edge/newdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newdeployment-expanded.png#lightbox)

1. I panelen **Ny distribution** skapar du en distribution med följande inställningar:

    | Alternativ | Värde |
    | ------ | ----- |
    | Namn   | OilPumpDevices |
    | Pakettyp | Edge Manifest |
    | Paket | oil-pump-device.json |
    | Enhetsgrupp | OilPumps |
    | Prioritet | 10 |

    [![Skapa distribution](./media/iot-accelerators-remote-monitoring-edge/createdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/createdeployment-expanded.png#lightbox)

    Klicka på **Använd**.

Du måste vänta några minuter medan paketet distribueras till din enhet och telemetri börjar flöda från enheten.

[![Aktiv distribution](./media/iot-accelerators-remote-monitoring-edge/deploymentactive-inline.png)](./media/iot-accelerators-remote-monitoring-edge/deploymentactive-expanded.png#lightbox)

På sidan **Distributioner** visas följande mått:

* **Riktade** visar antalet enheter i enhetsgruppen.
* **Används** visar antalet enheter som har haft distributionens innehåll tillämpat.
* **Lyckades** visar antalet Edge-enheter i distributionen som rapporterar lyckade åtgärder från IoT Edge-klientkörningen.
* **Misslyckade** visar antalet Edge-enheter i distributionen som rapporterar misslyckade åtgärder från IoT Edge-klientkörningen.

## <a name="monitor-the-device"></a>Övervaka enheten

Du kan visa temperaturtelemetri från oljepumpenheten i webbgränssnittet för fjärrövervakning:

1. Navigera till sidan **Device Explorer** och välj din oljepumpenhet.
1. I avsnittet **Telemetri** i panelen **Enhetsinformation** i panelen, klickar du på **Temperatur**:

    [![Visa telemetri](./media/iot-accelerators-remote-monitoring-edge/viewtelemetry-inline.png)](./media/iot-accelerators-remote-monitoring-edge/viewtelemetry-expanded.png#lightbox)

Du kan se hur temperaturen ökar tills den når ett tröskelvärde. Stream Analytics Edge-modulen identifierar när temperaturen når den här tröskeln och skickar ett kommando till enheten för att minska temperaturen omedelbart. All bearbetning sker på enheten utan att kommunicera med molnet.

Om du vill meddela operatörer när tröskelvärdet har uppnåtts kan du skapa en regel i webbgränssnittet för fjärrövervakning:

1. Navigera till sidan **Regler** och klicka på **+ Ny regel**.
1. Skapa en ny regel med följande inställningar:

    | Alternativ | Värde |
    | ------ | ----- |
    | Regelnamn | Oljepumptemperatur |
    | Description | Oljapumptemperaturen överskred 300 |
    | Enhetsgrupp | OilPumps |
    | Beräkning | Direkt |
    | Fält | temperatur |
    | Operator | > |
    | Värde | 300 |
    | Allvarlighetsgrad | Information |

    [![Skapa regel](./media/iot-accelerators-remote-monitoring-edge/newrule-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newrule-expanded.png#lightbox)

    Klicka på **Använd**.

1. Gå till sidan **Instrumentpanel**. En avisering som visas i panelen **Aviseringar** när temperaturen i enheten **oil-pump** överstiger 300.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen visades hur du lägger till och konfigurerar en IoT Edge-enhet i acceleratorn för fjärrövervakningslösningen. Om du vill lära dig mer om hur du arbetar med IoT Edge-paket i fjärrövervakningslösningen kan du läsa följande instruktionsguide:

> [!div class="nextstepaction"]
> [Importera ett IoT Edge-paket till acceleratorn för fjärrövervakningslösningen](iot-accelerators-remote-monitoring-import-edge-package.md)

Mer information om hur du installerar IoT Edge-körningen finns i [Installera Azure IoT Edge-körning på Linux (x64)](../iot-edge/how-to-install-iot-edge.md).

Läs mer om Azure Stream Analytics på Edge-enheter i [Distribuera Azure Stream Analytics som en IoT Edge-modul](../iot-edge/tutorial-deploy-stream-analytics.md).