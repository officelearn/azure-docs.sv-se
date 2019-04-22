---
title: Distribuera Azure Security Center för IoT Edge-modul | Microsoft Docs
description: Läs mer om hur du distribuerar en Azure Security Center för IoT security-agenten på IoT Edge.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/1/2019
ms.author: mlottner
ms.openlocfilehash: d72980d6e27600cb844d5477d3b9a61d9e1573e4
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59505625"
---
# <a name="deploy-a-security-module-on-your-iot-edge-device"></a>Distribuera en modul för maskinvarusäkerhet på din IoT Edge-enhet

> [!IMPORTANT]
> Azure Security Center för IoT är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

**Azure Security Center (ASC) för IoT** modulen innehåller en omfattande lösning för din IoT Edge-enhet.
Modul för maskinvarusäkerhet samlar in, aggregerar och analyserar raw säkerhetsdata från datorn operativsystem och en behållare i handlingsbara rekommendationer och aviseringar.
Mer information finns i [säkerhetsmodul för IoT Edge](security-edge-architecture.md).

I den här guiden lär du dig att distribuera en modul för maskinvarusäkerhet på din IoT Edge-enhet.

## <a name="deploy-security-module"></a>Distribuera säkerhetsmodul

Använd följande steg för att distribuera en ASC för IoT-säkerhetsmodul för IoT Edge.

### <a name="prerequisites"></a>Nödvändiga komponenter

- Kontrollera att enheten är i din IoT-hubb [registrerad som en IoT Edge-enhet](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal).

- ASC för IoT Edge-modulen kräver [AuditD framework](https://linux.die.net/man/8/auditd) är installerad på IoT Edge-enhet.

    - Installera framework genom att köra följande kommando på IoT Edge-enhet:
   
      `sudo apt-get install auditd audispd-plugins`
   
    - Kontrollera AuditD är aktiv genom att köra följande kommando:
   
      `sudo systemctl status auditd`
      
        Det förväntade svaret är `active (running)`. 

### <a name="deployment-using-azure-portal"></a>Distributionen med hjälp av Azure-portalen

1. Från Azure-portalen öppnar **Marketplace**.

1. Välj **Internet of Things**, Sök sedan efter **Azure Security Center för IoT** och markera den.

   ![Välj Azure Security Center för IoT](media/howto/edge-onboarding-8.png)

1. Klicka på **skapa** att konfigurera distribution. 

1. Välj Azure **prenumeration** för IoT-hubben, väljer din **IoT Hub**.<br>Välj **distribuera till en enhet** att rikta en enskild enhet eller välja **distribuera skalenligt** rikta flera enheter och klickar på **skapa**. Läs mer om hur du distribuerar i stor skala, [hur du distribuerar](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor). 

    >[!Note] 
    >Om du har valt **distribuera skalenligt**, lägga till enhetens namn och information innan du fortsätter med den **Lägg till moduler** flik i följande anvisningar.     

Det finns tre steg för att skapa en IoT Edge-distribution för Azure Security Center för IoT. I följande avsnitt beskriver var och en. 

#### <a name="step-1-add-modules"></a>Steg 1: Lägg till moduler

1. Från den **Lägg till moduler** fliken **distribution moduler** området klickar du på **AzureSecurityCenterforIoT**. 
   
1. Ändra den **namn** till **azureiotsecurity**.
1. Ändra den **bild-URI: N** till **mcr.microsoft.com/ascforiot/azureiotsecurity:0.0.3**.
1. Kontrollera den **behållare skapa alternativ** har värdet:      
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
1. Kontrollera att **önskade egenskaper för modultvilling Set** är markerad och ändra konfigurationsobjektet till:
      
    ``` json
      "properties.desired": {
        "azureiot*com^securityAgentConfiguration^1*0*0": {
        }
      }
      ```

1. Klicka på **Spara**.
1. Bläddra till längst ned på fliken och välj **konfigurera avancerade Edge-körningsinställningar**.
   
   >[!Note]
   > Gör **inte** inaktivera AMQP kommunikation för IoT Edge-hubben.
   > Azure Security Center för IoT-modulen kräver AMQP kommunikation med IoT Edge Hub.
   
1. Ändra den **bild** under **Edge Hub** till **mcr.microsoft.com/ascforiot/edgehub:1.0.9-preview**.

   >[!Note]
   > Azure Security Center för IoT-modulen kräver en förgrenade versionen av IoT Edge Hub, baserat på SDK-version 1,20.
   > Genom att ändra IoT Edge Hub-avbildning, ger du din IoT Edge-enhet att ersätta den senaste stabila versionen med den förgrenade versionen av IoT Edge Hub, vilket inte stöds officiellt av IoT Edge-tjänsten.

1. Kontrollera **skapa alternativ** har angetts till: 
         
    ``` json
    {
      "HostConfig": {
        "PortBindings": {
          "8883/tcp": [{"HostPort": "8883"}],
          "443/tcp": [{"HostPort": "443"}],
          "5671/tcp": [{"HostPort": "5671"}]
        }
      }
    }
    ```
      
1. Klicka på **Spara**.
   
1. Klicka på **Nästa**.

#### <a name="step-2-specify-routes"></a>Steg 2: Ange rutter 

1. I den **ange vägar** fliken genom att ange den **ASCForIoTToIoTHub** dirigera till **”från/meddelanden/moduler/azureiotsecurity/\* i $uppströms”**, och klicka på  **Nästa**.

   ![Ange vägar](media/howto/edge-onboarding-9.png)

#### <a name="step-3-review-deployment"></a>Steg 3: Granska distributionen

1. I den **granska distribution** , granska din information om distribution och sedan välja **skicka** att slutföra distributionen.

## <a name="diagnostic-steps"></a>Diagnostiken

Om det uppstår ett problem, är container loggarna det bästa sättet att lära dig om tillståndet för en IoT Edge-modulen säkerhetsenhet. Använd kommandona och verktygen i det här avsnittet för att samla in information.

### <a name="verify-the-required-containers-are-installed-and-functioning-as-expected"></a>Kontrollera behållarna som krävs är installerade och fungerar som förväntat

1. Kör följande kommando på IoT Edge-enhet:
    
     `sudo docker ps`
   
1. Kontrollera att följande behållare körs:
   
   | Namn | BILD |
   | --- | --- |
   | azureIoTSecurity | mcr.microsoft.com/ascforiot/azureiotsecurity:0.0.3 |
   | edgeHub | mcr.microsoft.com/ascforiot/edgehub:1.0.9-preview |
   | edgeAgent | mcr.microsoft.com/azureiotedge-agent:1.0 |
   
   Om den begärda minimiversionen behållare inte finns, kontrollera om ditt manifest för IoT Edge-distribution är i linje med de rekommenderade inställningarna. Mer information finns i [distribuera IoT Edge-modul](#deployment-using-azure-portal).

### <a name="inspect-the-module-logs-for-errors"></a>Inspektera modulloggar efter fel
   
1. Kör följande kommando på IoT Edge-enhet:

   `sudo docker logs azureiotsecurity`
   
1. För mer utförliga loggar, lägger du till följande miljövariabler till **azureiotsecurity** distribution av principmodul: `logLevel=Debug`.

## <a name="next-steps"></a>Nästa steg

Mer information om konfigurationsalternativ fortsätter du att den här guiden för Modulkonfiguration av. 
> [!div class="nextstepaction"]
> [Modulen how-to konfigurationsguide](./how-to-agent-configuration.md)
