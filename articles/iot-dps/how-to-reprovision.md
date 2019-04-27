---
title: Hur du etablera om enheter i Azure IoT Hub Device Provisioning-tjänsten | Microsoft Docs
description: Hur du etablera om enheter med din device provisioning-tjänstinstans
author: wesmc7777
ms.author: v-yiso
origin.date: 04/04/2019
ms.date: 05/06/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 92680a453d93c8dc0189c6ae376449a8e7a22076
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60627362"
---
# <a name="how-to-reprovision-devices"></a>Hur du etablera om enheter

Under livscykeln för en IoT-lösning är det vanligt att flytta enheter mellan IoT-hubbar. Orsaker till flytten kan innehålla följande scenarier:

* **Geoplats**: När en enhet flyttas mellan platser Nätverksfördröjningen förbättras genom att låta enheten migreras till en IoT-hubb närmare till varje plats.

* **Multitenans**: En enhet kan användas inom samma IoT-lösning, men, omtilldelas eller lånade till en ny kund eller kundens plats. Den här nya kunden kan betjänas med hjälp av en annan IoT-hubb.

* **Lösning ändra**: En enhet kan flyttas till en ny eller uppdaterad IoT-lösning. Omtilldelningen kan kräva att enheten kommunicerar med en ny IoT-hubb som är ansluten till andra backend-komponenter. 

* **Karantän**: Liknar en ändring av lösningen. En enhet som är felaktig, komprometterade eller inaktuella får omtilldelas till en IoT hub där allt du kan göra är att uppdatera och få tillbaka efterlevnad. Den flyttas sedan tillbaka till dess huvudsakliga hub när enheten fungerar korrekt.

Läs mer en mer detaljerad översikt över reprovisioning, [IoT Hub Device reprovisioning begrepp](concepts-device-reprovision.md).


## <a name="configure-the-enrollment-allocation-policy"></a>Konfigurera registrering allokeringsprincip

Allokeringsprincipen avgör hur de enheter som är associerade med registreringen ska allokeras, eller tilldelats en IoT-hubb nätverkskonfigurationsinställningar en gång.

Med följande steg konfigurerar allokeringsprincip för registrering av en enhet:

1. Logga in på den [Azure-portalen](https://portal.azure.com) och navigera till din instans av Device Provisioning-tjänsten.

2. Klicka på **hantera registreringar**, och klicka på den grupp för registrering eller en enskild registrering som du vill konfigurera för reprovisioning. 

3. Under **väljer du hur du vill tilldela enheter till hubs**, väljer du något av följande allokeringsprinciper:

    * **Kortast svarstid**: Den här principen tilldelar enheter till den länkade IoT-hubben som leder till den lägsta svarstid kommunikationen mellan enheten och IoT Hub. Det här alternativet gör att enheten att kommunicera med den närmaste IoT-hubb baserat på plats. 
    
    * **Jämnt viktad distribution**: Den här principen fördelar enheter över länkade IoT-hubbar utifrån av allokeringsvikt som tilldelats varje länkad IoT-hubb. Den här principen kan du belastningsutjämna enheter för en grupp med länkade hubbar baserat på tilldelning vikterna på dessa hubbar. Om du etablerar enheter till endast en IoT Hub, rekommenderar vi den här inställningen. Den här inställningen är standardinställningen. 
    
    * **Statisk konfiguration**: Den här principen kräver att en önskad IoT Hub att listas i registreringsposten för en enhet som ska etableras. Den här principen kan du ange en enda specifika IoT-hubb som du vill tilldela enheter till.

4. Under **Välj IoT-hubbar som den här gruppen kan tilldelas till**, Välj de länkade IoT-hubbar som önskas ingår i din princip. Du kan också lägga till en ny länkad Iot hub med den **länka en ny IoT Hub** knappen.

    Med den **kortast svarstid** allokeringsprincip, hubs som du väljer kommer att inkluderas i svarstid utvärderingen att fastställa den närmaste hubben för enhetstilldelning.

    Med den **jämnt viktad distribution** allokeringsprincip, enheter kommer att belastningsutjämnas mellan hubs som du väljer utifrån deras konfigurerade allokering vikt och deras aktuella enhetsbelastningen.

    Med den **statisk konfiguration** allokeringsprincip, Välj IoT-hubben som du vill att enheter har tilldelats.

4. Klicka på **spara**, eller gå vidare till nästa avsnitt för att ange reprovisioning principen.

    ![Välj registrering allokeringsprincip](./media/how-to-reprovision/enrollment-allocation-policy.png)



## <a name="set-the-reprovisioning-policy"></a>Ange princip för reprovisioning

1. Logga in på den [Azure-portalen](https://portal.azure.com) och navigera till din instans av Device Provisioning-tjänsten.

2. Klicka på **hantera registreringar**, och klicka på den grupp för registrering eller en enskild registrering som du vill konfigurera för reprovisioning.

3. Under **väljer du hur enhetsdata som ska hanteras på nytt etableringsförsök till en annan IoT-hubb**, väljer du något av följande reprovisioning principer:

    * **Etablera och migrera data**: Den här principen vidtar åtgärder när enheter som är associerade med registreringsposten skickar in en ny begäran om etablering. Beroende på posten registreringskonfigurationen får enheten omtilldelas till en annan IoT-hubb. Om enheten ändras IoT-hubbar, tas enhetsregistrering med den första IoT-hubben bort. All information om enhetstillstånd från den första IoT-hubben kommer att migreras över till den nya IoT-hubben. Under migreringen, enhetens status kommer att rapporteras som **tilldela**

    * **Etablera och återställa till ursprungliga config**: Den här principen vidtar åtgärder när enheter som är associerade med registreringsposten skickar in en ny begäran om etablering. Beroende på posten registreringskonfigurationen får enheten omtilldelas till en annan IoT-hubb. Om enheten ändras IoT-hubbar, tas enhetsregistrering med den första IoT-hubben bort. Inledande konfigurationsdata som etablering tjänstinstansen tas emot när enheten har etablerats har angetts för den nya IoT-hubben. Under migreringen, enhetens status kommer att rapporteras som **tilldela**.

4. Klicka på **spara** att aktivera reprovisioning baserat på dina ändringar.

    ![Välj registrering allokeringsprincip](./media/how-to-reprovision/reprovisioning-policy.png)



## <a name="send-a-provisioning-request-from-the-device"></a>Skicka en begäran om etablering från enheten

För enheter som ska etableras baserat på konfigurationsändringar i föregående avsnitt, dessa enheter måste begära reprovisioning. 

Hur ofta en enhet skickar en etableringsbegäran beror på scenariot. Dock är det bäst att programmera dina enheter att skicka en etableringsbegäran till en allokering tjänstinstans för omstart, och kan hantera en [metoden](../iot-hub/iot-hub-devguide-direct-methods.md) att manuellt starta etablering på begäran. Etablering kan även aktiveras genom att ange en [önskad egenskap](../iot-hub/iot-hub-devguide-device-twins.md#desired-property-example). 

Reprovisioning principen på en registreringspost avgör hur enheten etablering tjänstinstansen hanterar dessa etableringsbegäranden och om enhetens tillståndsdata ska migreras under reprovisioning. Samma principer är tillgängliga för enskilda registreringar och registreringsgrupper:

Till exempel kod för att skicka etablera begäranden från en enhet under en startsekvens Se [Automatisk etablering av en simulerad enhet](quick-create-simulated-device.md).


## <a name="next-steps"></a>Nästa steg

- Läs mer Reprovisioning i [reprovisioning koncept för IoT Hub-enhet](concepts-device-reprovision.md) 
- Läs mer avetablering i [så ta bort etableringen av enheter som har tidigare Automatisk etablering](how-to-unprovision-devices.md) 











