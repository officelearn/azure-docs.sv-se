---
title: ta med fil
description: ta med fil
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 10/10/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: b453a04a170764a037eed7415eaf71e5a4d37526
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844604"
---
## <a name="deploy-event-grid-iot-edge-module"></a>Distribuera IoT Edge-module för händelserutnät

Det finns flera sätt att distribuera moduler till en IoT Edge-enhet och alla fungerar för Azure Event Grid på IoT Edge. I den här artikeln beskrivs stegen för att distribuera Event Grid på IoT Edge från Azure-portalen.

>[!NOTE]
> I den här självstudien distribuerar du modulen Event Grid utan att vara beständig. Det innebär att alla ämnen och prenumerationer som du skapar i den här självstudien tas bort när du distribuerar om modulen. Mer information om hur du konfigurerar persistens finns i följande artiklar: [Beständigt tillstånd i Linux](../articles/event-grid/edge/persist-state-linux.md) eller [Kvarstår i Windows](../articles/event-grid/edge/persist-state-windows.md). För produktionsarbetsbelastningar rekommenderar vi att du installerar modulen Event Grid med uthållighet.

>[!IMPORTANT]
> I den här självstudien distribueras Event Grid-modulen med klientautentisering inaktiverad och tillåter HTTP-prenumeranter. För produktionsarbetsbelastningar rekommenderar vi att du endast aktiverar HTTPS-begäranden och prenumeranter med klientautentisering aktiverad. Mer information om hur du konfigurerar Modulen Event Grid på ett säkert sätt finns i [Säkerhet och autentisering](../articles/event-grid/edge/security-authentication.md).
 
### <a name="select-your-iot-edge-device"></a>Välj din IoT Edge-enhet

1. Logga in på [Azure-portalen](https://portal.azure.com)
1. Navigera till din IoT Hub.
1. Välj **IoT Edge** på menyn i avsnittet **Automatisk enhetshantering.** 
1. Klicka på målenhetens ID från listan över enheter
1. Välj **Ange moduler**. Håll sidan öppen. Du fortsätter med stegen i nästa avsnitt.

### <a name="configure-a-deployment-manifest"></a>Konfigurera ett distributionsmanifest

Ett distributionsmanifest är ett JSON-dokument som beskriver vilka moduler som ska distribueras, hur data flödar mellan modulerna och önskade egenskaper för modultvillingarna. Azure-portalen har en guide som hjälper dig att skapa ett distributionsmanifest i stället för att skapa JSON-dokumentet manuellt.  Den har tre steg: **Lägg till moduler,** **Ange vägar**och Granska **distribution**.

### <a name="add-modules"></a>Lägga till moduler

1. I avsnittet **Distributionsmoduler** väljer du **Lägg till**
1. Välj **IoT Edge Module** i de typer av moduler som finns i listrutan
1. Ange alternativ för att skapa namn, avbildning, behållare för behållaren:

[!INCLUDE [event-grid-edge-module-version-update](event-grid-edge-module-version-update.md)]

   * **Namn**: eventgridmodule
   * **Bild URI:**`mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **Skapa alternativ för att skapa behållare:**

    ```json
        {
          "Env": [
            "inbound__clientAuth:clientCert__enabled=false",
            "outbound__webhook__httpsOnly=false"
          ],
          "HostConfig": {
            "PortBindings": {
              "4438/tcp": [
                {
                  "HostPort": "4438"
                }
              ]
            }
          }
        }
    ```

 1. Klicka på **Spara**
 1. Klicka på **Nästa** om du vill fortsätta till flödet

    > [!NOTE]
    > Om du använder en Virtuell Azure-dator som en kantenhet lägger du till en inkommande portregel för att tillåta inkommande trafik på port 4438. Instruktioner om hur du lägger till regeln finns i [Så här öppnar du portar till en virtuell dator](../articles/virtual-machines/windows/nsg-quickstart-portal.md).


### <a name="setup-routes"></a>Installationsvägar

 Behåll standardvägarna och välj **Nästa** om du vill fortsätta till granskningsavsnittet

### <a name="review-deployment"></a>Granska distribution

1. Granskningsavsnittet visar det JSON-distributionsmanifest som skapades baserat på dina val i de två föregående avsnitten. Bekräfta att du ser de två modulerna i listan: **$edgeAgent** och **$edgeHub**. Dessa två moduler utgör IoT Edge-körningen och krävs standardvärden i varje distribution.
1. Granska distributionsinformationen och välj sedan **Skicka**.

### <a name="verify-your-deployment"></a>Verifiera distributionen

1. När du har skickat distributionen går du tillbaka till IoT Edge-sidan i din IoT-hubb.
1. Välj den **IoT Edge-enhet** som du riktade in dig på med distributionen för att öppna dess information.
1. Kontrollera att modulen Event Grid visas både **i distributionen** och **rapporterad per enhet**i enhetsinformationen.

Det kan ta en stund innan modulen startas på enheten och sedan rapporteras tillbaka till IoT Hub. Uppdatera sidan för att se en uppdaterad status.