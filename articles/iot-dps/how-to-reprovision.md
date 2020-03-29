---
title: Etablera om enheter i Azure IoT Hub Device Provisioning Service
description: Lär dig hur du återetablerar enheter med DPS-instansen (Device Provisioning Service) och varför du kan behöva göra detta.
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 0ded494debab19daa15a953715b1ab7b0b10ad18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974912"
---
# <a name="how-to-reprovision-devices"></a>Så här återetableras enheter

Under livscykeln för en IoT-lösning är det vanligt att flytta enheter mellan IoT-hubbar. Orsakerna till det här steget kan vara följande scenarier:

* **Geolokalisering**: När en enhet flyttas mellan platser förbättras nätverksfördröjningen genom att enheten migreras till en IoT-hubb närmare varje plats.

* **Multi-arrende**: En enhet kan användas inom samma IoT-lösning, men, omtilldelas eller hyrs ut till en ny kund eller kundwebbplats. Den här nya kunden kan servas med en annan IoT-hubb.

* **Ändring av lösning:** En enhet kan flyttas till en ny eller uppdaterad IoT-lösning. Den här omtilldelningen kan kräva att enheten kommunicerar med en ny IoT-hubb som är ansluten till andra backend-komponenter. 

* **Karantän**: Liknar en lösningsändring. En enhet som inte fungerar, komprometteras eller är inaktuell kan omfördelas till en IoT-hubb där allt den kan göra är att uppdatera och komma tillbaka i enlighet. När enheten fungerar som den ska migreras den sedan tillbaka till huvudnavet.

Mer information om ometablering finns i [begrepp för ometablering av IoT Hub Device](concepts-device-reprovision.md).


## <a name="configure-the-enrollment-allocation-policy"></a>Konfigurera princip för registreringsallokering

Allokeringsprincipen bestämmer hur de enheter som är associerade med registreringen ska fördelas eller tilldelas till en IoT-hubb när de har ometablerats.

I följande steg konfigureras allokeringsprincipen för en enhets registrering:

1. Logga in på [Azure-portalen](https://portal.azure.com) och navigera till din enhetsetableringstjänstinstans.

2. Klicka på Hantera registreringar och klicka på den **registreringsgrupp**eller enskilda registrering som du vill konfigurera för ometablering. 

3. Under **Välj hur du vill tilldela enheter till hubbar**väljer du en av följande allokeringsprinciper:

    * **Lägsta svarstid:** Den här principen tilldelar enheter till den länkade IoT-hubben som resulterar i den lägsta latenskommunikationen mellan enheten och IoT Hub. Med det här alternativet kan enheten kommunicera med närmaste IoT-hubb baserat på plats. 
    
    * **Jämnt viktad distribution**: Den här principen distribuerar enheter över de länkade IoT-hubbar baserat på allokeringsvikten som tilldelats varje länkad IoT-hubb. Med den här principen kan du läsa in fördelningsenheter i en grupp länkade nav baserat på de allokeringsvikter som angetts för dessa nav. Om du etablerar enheter till endast en IoT Hub rekommenderar vi den här inställningen. Den här inställningen är standardinställningen. 
    
    * **Statisk konfiguration**: Den här principen kräver att en önskad IoT Hub anges i registreringsposten för att en enhet ska etableras. Med den här principen kan du ange en enda specifik IoT-hubb som du vill tilldela enheter till.

4. Under **Välj de IoT-hubbar som den här gruppen kan tilldelas**väljer du de länkade IoT-hubbar som du vill ska inkludera i fördelningsprincipen. Du kan också lägga till en ny länkad Iot-hubb med knappen **Länka en ny IoT Hub.**

    Med principen **fördelning av lägsta latens** inkluderas de nav som du väljer i svarstidsutvärderingen för att fastställa närmaste nav för enhetstilldelning.

    Med principen **jämnt viktad** distributionsallokering kommer enheter att belastningsbalanseras över de nav du väljer baserat på deras konfigurerade allokeringsvikter och deras aktuella enhetsbelastning.

    Med principen Statisk konfigurationsallokering väljer du den IoT-hubb som du vill att enheter ska tilldelas. **Static configuration**

4. Klicka på **Spara**eller gå vidare till nästa avsnitt för att ange ometableringsprincipen.

    ![Välj princip för registreringsallokering](./media/how-to-reprovision/enrollment-allocation-policy.png)



## <a name="set-the-reprovisioning-policy"></a>Ange ometableringsprincipen

1. Logga in på [Azure-portalen](https://portal.azure.com) och navigera till din enhetsetableringstjänstinstans.

2. Klicka på Hantera registreringar och klicka på den **registreringsgrupp**eller enskilda registrering som du vill konfigurera för ometablering.

3. Under **Välj hur du vill att enhetsdata ska hanteras vid ometablering till en annan IoT-hubb**väljer du en av följande ometableringsprinciper:

    * **Etablera om och migrera data**: Den här principen vidtar åtgärder när enheter som är associerade med registreringsposten skickar en ny etableringsbegäran. Beroende på konfigurationen av registreringsposten kan enheten tilldelas en annan IoT-hubb. Om enheten ändrar IoT-hubbar tas enhetsregistreringen med den första IoT-hubben bort. All enhetstillståndsinformation från den ursprungliga IoT-hubben migreras till den nya IoT-hubben. Under migreringen rapporteras enhetens status som **Tilldela**

    * **Återetablering och återställning till inledande konfiguration:** Den här principen vidtar åtgärder när enheter som är associerade med registreringsposten skickar en ny etableringsbegäran. Beroende på konfigurationen av registreringsposten kan enheten tilldelas en annan IoT-hubb. Om enheten ändrar IoT-hubbar tas enhetsregistreringen med den första IoT-hubben bort. De inledande konfigurationsdata som etableringstjänstinstansen togs emot när enheten etablerades tillhandahålls till den nya IoT-hubben. Under migreringen rapporteras enhetens status som **Tilldela**.

4. Klicka på **Spara** om du vill aktivera ometablering av enheten baserat på dina ändringar.

    ![Välj princip för registreringsallokering](./media/how-to-reprovision/reprovisioning-policy.png)



## <a name="send-a-provisioning-request-from-the-device"></a>Skicka en etableringsbegäran från enheten

För att enheter ska kunna återetableras baserat på de konfigurationsändringar som gjorts i föregående avsnitt måste dessa enheter begära ometablering. 

Hur ofta en enhet skickar en etableringsbegäran beror på scenariot. Det rekommenderas dock att programmera dina enheter att skicka en etableringsbegäran till en etableringstjänstinstans vid omstart och stödja en [metod](../iot-hub/iot-hub-devguide-direct-methods.md) för att manuellt utlösa etablering på begäran. Etablering kan också utlösas genom att ange en [önskad egenskap](../iot-hub/iot-hub-devguide-device-twins.md#desired-property-example). 

Principen om återetablering för en registreringspost avgör hur enhetsetableringstjänstinstansen hanterar dessa etableringsbegäranden och om enhetstillståndsdata ska migreras under återetablering. Samma principer är tillgängliga för enskilda registreringar och registreringsgrupper:

Kod för att skicka etableringsbegäranden från en enhet under en startsekvens finns i [Automatiskt etablera en simulerad enhet](quick-create-simulated-device.md).


## <a name="next-steps"></a>Nästa steg

- Mer ometablering finns i [IoT Hub Device reetableing concepts](concepts-device-reprovision.md) 
- Mer information om avetablering finns i [Så här avetablering av enheter som tidigare var automatiskt etablerade](how-to-unprovision-devices.md) 











