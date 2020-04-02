---
title: Distribuera Azure Security Center för IoT Edge-modul| Microsoft-dokument
description: Lär dig mer om hur du distribuerar en Azure Security Center för IoT-säkerhetsagent på IoT Edge.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/30/2020
ms.author: mlottner
ms.openlocfilehash: 3aee30e8ad82f9657c3bc9e97a7657a1e8c7989d
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548917"
---
# <a name="deploy-a-security-module-on-your-iot-edge-device"></a>Distribuera en säkerhetsmodul på din IoT Edge-enhet


**Azure Security Center för IoT-modul** tillhandahåller en omfattande säkerhetslösning för dina IoT Edge-enheter.
Säkerhetsmodulen samlar in, sammanställer och analyserar råsäkerhetsdata från ditt operativsystem och behållarsystem till användbara säkerhetsrekommendationer och aviseringar.
Mer information finns i [Säkerhetsmodul för IoT Edge](security-edge-architecture.md).

I den här artikeln får du lära dig hur du distribuerar en säkerhetsmodul på din IoT Edge-enhet.

## <a name="deploy-security-module"></a>Distribuera säkerhetsmodul

Använd följande steg för att distribuera en Azure Security Center för IoT-säkerhetsmodul för IoT Edge.

### <a name="prerequisites"></a>Krav

1. Kontrollera att enheten är registrerad som [en IoT Edge-enhet i IoT Hub.](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal)

1. Azure Security Center för IoT Edge-module kräver [att AuditD-ramverket](https://linux.die.net/man/8/auditd) är installerat på IoT Edge-enheten.

    - Installera ramverket genom att köra följande kommando på IoT Edge-enheten:
   
    `sudo apt-get install auditd audispd-plugins`

    - Kontrollera att GranskningSD är aktivt genom att köra följande kommando: 
   
    `sudo systemctl status auditd`<br>
    - Förväntat svar är:`active (running)` 
        

### <a name="deployment-using-azure-portal"></a>Distribution med Azure-portal

1. Öppna **Marketplace**från Azure-portalen .

1. Välj **Sakernas Internet**och sök sedan efter **Azure Security Center för IoT** och välj det.

   ![Välj Azure Security Center för IoT](media/howto/edge-onboarding-8.png)

1. Klicka på **Skapa** för att konfigurera distributionen. 

1. Välj **Azure-prenumerationen** för din IoT-hubb och välj sedan din **IoT Hub**.<br>Välj **Distribuera till en enhet** för att rikta in dig på en enhet eller välj Distribuera vid skala för att rikta in dig **på** flera enheter och klicka på **Skapa**. Mer information om hur du distribuerar i stor skala finns i [Så här distribuerar du](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor). 

    >[!Note] 
    >Om du har valt **Distribuera i skala**lägger du till **enhetens** namn och information innan du fortsätter till fliken Lägg till moduler i följande instruktioner.     

Slutför varje steg för att slutföra din IoT Edge-distribution för Azure Security Center för IoT. 

#### <a name="step-1-modules"></a>Steg 1: Moduler

1. Välj **Modulen AzureSecurityCenterforIoT.**
1. Ändra **namnet** till **azureiotsecurity**på fliken **Modulinställningar** .
1. Lägg till en variabel om det behövs på fliken **Miljövariabler** (till exempel felsökningsnivå).
1. Lägg till följande konfiguration på fliken **Alternativ för behållar:**

    ``` json
    {
        "NetworkingConfig": {
            "EndpointsConfig": {
                "host": {}
            }
        },
        "HostConfig": {
            "Privileged": true,
            "NetworkMode": "host",
            "PidMode": "host",
            "Binds": [
                "/:/host"
            ]
        }
    }    
    ```
    
1. Lägg till följande konfiguration på fliken **Modultvillinginställningar:**
      
    ``` json
      "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration"
    ```

1. Välj **Uppdatera**.

#### <a name="step-2-runtime-settings"></a>Steg 2: Körningsinställningar

1. Välj **Körtidsinställningar**.
1. Ändra **bilden** till **mcr.microsoft.com/azureiotedge-hub:1.0.8.3**under **Kanthubb**.
1. Verifiera **Skapa alternativ** är inställt på följande konfiguration: 
         
    ``` json
    { 
       "HostConfig":{ 
          "PortBindings":{ 
             "8883/tcp":[ 
                { 
                   "HostPort":"8883"
                }
             ],
             "443/tcp":[ 
                { 
                   "HostPort":"443"
                }
             ],
             "5671/tcp":[ 
                { 
                   "HostPort":"5671"
                }
             ]
          }
       }
    }
    ```
    
1. Välj **Spara**.
   
1. Välj **Nästa**.

#### <a name="step-3-specify-routes"></a>Steg 3: Ange rutter 

1. På fliken **Ange rutter** kontrollerar du att du har en väg (explicit eller implicit) som vidarebefordrar meddelanden från **azureiotsecurity-modulen** till **$upstream** enligt följande exempel. Välj Först när rutten är på plats väljer du **Nästa**.

   Exempel på rutter:

    ~~~Default implicit route
    "route": "FROM /messages/* INTO $upstream" 
    ~~~

    ~~~Explicit route
    "ASCForIoTRoute": "FROM /messages/modules/azureiotsecurity/* INTO $upstream"
    ~~~

1. Välj **Nästa**.

#### <a name="step-4-review-deployment"></a>Steg 4: Granska distributionen

- Granska **distributionsinformationen** på fliken Granska distribution och välj sedan **Skapa** för att slutföra distributionen.

## <a name="diagnostic-steps"></a>Diagnostiska steg

Om du stöter på ett problem är behållarloggar det bästa sättet att lära dig om tillståndet för en IoT Edge-säkerhetsmodulenhet. Använd kommandona och verktygen i det här avsnittet för att samla in information.

### <a name="verify-the-required-containers-are-installed-and-functioning-as-expected"></a>Kontrollera att de nödvändiga behållarna är installerade och fungerar som förväntat

1. Kör följande kommando på IoT Edge-enheten:
    
    `sudo docker ps`
   
1. Kontrollera att följande behållare körs:
   
   | Namn | BILD |
   | --- | --- |
   | azureiotsecurity azureiotsecurity azureiotsecurity azureio | mcr.microsoft.com/ascforiot/azureiotsecurity:1.0.2 |
   | kantHub | mcr.microsoft.com/azureiotedge-hub:1.0.8.3 |
   | edgeAgent (kantagent) | mcr.microsoft.com/azureiotedge-agent:1.0.1 |
   
   Om de minsta nödvändiga behållarna inte finns kontrollerar du om distributionsmanifestet för IoT Edge är justerat med de rekommenderade inställningarna. Mer information finns i [Distribuera IoT Edge-modul](#deployment-using-azure-portal).

### <a name="inspect-the-module-logs-for-errors"></a>Kontrollera om det finns fel i modulloggarna
   
1. Kör följande kommando på IoT Edge-enheten:

   `sudo docker logs azureiotsecurity`
   
1. För mer utförliga loggar lägger du till följande miljövariabel `logLevel=Debug`i **azureiotsecurity-modulen** distribution: .

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om konfigurationsalternativ fortsätter du till den programväga för modulkonfiguration. 
> [!div class="nextstepaction"]
> [Hur du styr modulkonfiguration](./how-to-agent-configuration.md)
