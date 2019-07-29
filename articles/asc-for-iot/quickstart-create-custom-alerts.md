---
title: Skapa anpassade aviseringar för Azure Security Center för IoT | Microsoft Docs
description: Skapa och tilldela anpassade enhets aviseringar för Azure Security Center för IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: d1757868-da3d-4453-803a-7e3a309c8ce8
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: ed10cbf89f878f8d27b43476d26ac93dd373ed66
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68597003"
---
# <a name="quickstart-create-custom-alerts"></a>Snabbstart: Skapa anpassade aviseringar


Genom att använda anpassade säkerhets grupper och aviseringar kan du utnyttja all fullständig säkerhets information och kategoriska för att säkerställa bättre säkerhet i din IoT-lösning. 

## <a name="why-use-custom-alerts"></a>Varför ska jag använda anpassade aviseringar? 

Du vet att dina IoT-enheter är bäst.

För kunder som helt förstår sina förväntade enhets beteende kan du med hjälp av Azure Security Center för IoT översätta den här förståelsen till en princip för enhets beteende och en avisering om eventuella avvikelser från förväntat, normalt beteende.

## <a name="security-groups"></a>Säkerhetsgrupper

Med säkerhets grupper kan du definiera logiska grupper av enheter och hantera deras säkerhets tillstånd på ett centraliserat sätt.

Dessa grupper kan representera enheter med specifik maskin vara, enheter som har distribuerats på en viss plats eller någon annan grupp som passar dina specifika behov.

Säkerhets grupper definieras av en enhets dubbla tag-egenskap med namnet **SecurityGroup**. Som standard har varje IoT-lösning på IoT Hub en säkerhets grupp med namnet **default**. Ändra värdet för egenskapen **SecurityGroup** om du vill ändra säkerhets gruppen för en enhet.
 
Exempel:

```
{
  "deviceId": "VM-Contoso12",
  "etag": "AAAAAAAAAAM=",
  "deviceEtag": "ODA1BzA5QjM2",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00",
  "connectionState": "Disconnected",
  "lastActivityTime": "0001-01-01T00:00:00",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "version": 4,
  "tags": {
    "SecurityGroup": "default"
  }, 
```

Använd säkerhets grupper för att gruppera dina enheter i logiska kategorier. När du har skapat grupperna tilldelar du dem till de anpassade aviseringarna som du önskar, för den mest effektiva IoT-säkerhetslösningen från slut punkt till slut punkt. 

## <a name="customize-an-alert"></a>Anpassa en avisering

1. Öppna din IoT Hub. 
2. Klicka på **anpassade aviseringar** i avsnittet **säkerhet** . 
3. Välj en säkerhets grupp som du vill tillämpa anpassningen på. 
4. Klicka på **Lägg till en anpassad avisering**.
5. Välj en anpassad avisering i list rutan. 
6. Redigera de nödvändiga egenskaperna och klicka på **OK**.
7. Se till att klicka på **Spara**. Om du inte sparar den nya aviseringen tas aviseringen bort nästa gången du stänger IoT Hub.

 
## <a name="alerts-available-for-customization"></a>Aviseringar tillgängliga för anpassning

Följande tabell innehåller en sammanfattning av aviseringar som är tillgängliga för anpassning.


| severity | Namn | Datakälla | Beskrivning | Rekommenderad reparation|
|---|---|---|---|---|
| Låg      | Anpassad avisering – antalet moln till enhets meddelanden i AMQP-protokollet ligger utanför det tillåtna intervallet          | IoT-hubb     | Antalet moln till enhets meddelanden (AMQP-protokoll) inom ett särskilt tidsintervall är utanför det konfigurerade och tillåtna intervallet.||
| Låg      | Anpassad avisering – antal nekade moln till enhets meddelanden i AMQP-protokollet ligger utanför det tillåtna intervallet | IoT-hubb     | Antalet moln till enhets meddelanden (AMQP-protokoll) som avvisats av enheten inom ett särskilt tidsintervall är utanför det konfigurerade och tillåtna intervallet.||
| Låg      | Anpassad avisering – antalet enheter till moln meddelanden i AMQP-protokollet ligger utanför det tillåtna intervallet      | IoT-hubb     | Mängden enhet till moln meddelanden (AMQP-protokoll) inom ett visst tidsintervall är utanför det konfigurerade och tillåtna intervallet.|   |
| Låg      | Anpassad avisering – antalet direkta metod anrop ligger utanför det tillåtna intervallet | IoT-hubb     | Mängden direkta metod anrop inom ett visst tidsintervall är utanför det konfigurerade och tillåtna intervallet.||
| Låg      | Anpassad avisering – antalet fil överföringar ligger utanför det tillåtna intervallet | IoT-hubb     | Mängden fil överföringar inom ett visst tidsintervall är utanför det konfigurerade och tillåtna intervallet.| |
| Låg      | Anpassad avisering – antalet moln till enhets meddelanden i HTTP-protokollet ligger utanför det tillåtna intervallet | IoT-hubb     | Mängden moln till enhets meddelanden (HTTP-protokoll) i ett tids fönster är inte inom det konfigurerade tillåtna intervallet                                  |
| Låg      | Anpassad avisering-antal nekade moln till enhets meddelanden i HTTP-protokollet är inte inom det tillåtna intervallet | IoT-hubb     | Mängden moln till enhets meddelanden (HTTP-protokoll) inom en angiven tids period är utanför det konfigurerade och tillåtna intervallet. |
| Låg      | Anpassad avisering – antalet enheter till moln meddelanden i HTTP-protokollet ligger utanför det tillåtna intervallet | IoT-hubb| Mängden enhets-till-moln-meddelanden (HTTP-protokoll) inom ett visst tidsintervall är utanför det konfigurerade och tillåtna intervallet.|    |
| Låg      | Anpassad avisering – antalet moln till enhets meddelanden i MQTT-protokollet ligger utanför det tillåtna intervallet | IoT-hubb     | Mängden moln till enhets meddelanden (MQTT-protokoll) inom ett visst tidsintervall är utanför det aktuella konfigurerade och tillåtna intervallet.|   |
| Låg      | Anpassad avisering – antal nekade moln till enhets meddelanden i MQTT-protokollet ligger utanför det tillåtna intervallet | IoT-hubb     | Mängden moln till enhets meddelanden (MQTT-protokoll) som avvisats av enheten inom ett visst tidsintervall är utanför det konfigurerade och tillåtna intervallet. |
| Låg      | Anpassad avisering – antalet enheter till moln meddelanden i MQTT-protokollet ligger utanför det tillåtna intervallet          | IoT-hubb     | Mängden enhet till moln meddelanden (MQTT-protokoll) inom ett visst tidsintervall är utanför det konfigurerade och tillåtna intervallet.|
| Låg      | Anpassad avisering – antalet rensningar av kommando kön ligger utanför det tillåtna intervallet                               | IoT-hubb     | Mängden kommando köer som rensas inom ett visst tidsintervall är utanför det konfigurerade och tillåtna intervallet.||
| Låg      | Anpassad avisering – antalet dubbla uppdateringar i modulen ligger utanför det tillåtna intervallet                                       | IoT-hubb     | Mängden dubbla uppdateringar inom en bestämd tids period ligger utanför det konfigurerade och tillåtna intervallet.|
| Låg      | Anpassad avisering-antalet otillåtna åtgärder ligger utanför det tillåtna intervallet  | IoT-hubb     | Mängden ej auktoriserade åtgärder inom ett visst tidsintervall är utanför det konfigurerade och tillåtna intervallet.|
| Låg      | Anpassad avisering – antalet aktiva anslutningar ligger utanför det tillåtna intervallet  | agent       | Antalet aktiva anslutningar inom ett särskilt tidsintervall är utanför det aktuella konfigurerade och tillåtna intervallet.|  Undersök enhets loggarna. Lär dig var anslutningen kommer och ta reda på om den är ofarlig eller skadlig. Ta bort eventuell skadlig kod och förstå källan om det är skadligt. Om det är ofarligt kan du lägga till källan i listan över tillåtna anslutningar.  |
| Låg      | Anpassad avisering – utgående anslutning har skapats till en IP-adress som inte är tillåten                             | agent       | En utgående anslutning skapades till en IP-adress som ligger utanför din tillåtna IP-lista. |Undersök enhets loggarna. Lär dig var anslutningen kommer och ta reda på om den är ofarlig eller skadlig. Ta bort eventuell skadlig kod och förstå källan om det är skadligt. Om det är ofarligt kan du lägga till källan i listan över tillåtna IP-adresser.                        |
| Låg      | Anpassad avisering – antalet misslyckade lokala inloggningar ligger utanför det tillåtna intervallet                               | agent       | Mängden misslyckade lokala inloggningar inom ett visst tidsintervall är utanför det aktuella konfigurerade och tillåtna intervallet. |   |
| Låg      | Anpassad avisering – inloggning för en användare som inte finns med i listan över tillåtna användare | agent       | En lokal användare utanför listan över tillåtna användare som är inloggad på enheten.|  Om du sparar rå data navigerar du till ditt Log Analytics-konto och använder data för att undersöka enheten, identifiera källan och sedan reparera listan över tillåtna/blockerade för dessa inställningar. Om du inte sparar rå data för tillfället går du till enheten och korrigerar listan över tillåtna/blockerade för dessa inställningar.|
| Låg      | Anpassad avisering – en process kördes som inte är tillåten | agent       | En process som inte är tillåten kördes på enheten. |Om du sparar rå data navigerar du till ditt Log Analytics-konto och använder data för att undersöka enheten, identifiera källan och sedan reparera listan över tillåtna/blockerade för dessa inställningar. Om du inte sparar rå data för tillfället går du till enheten och korrigerar listan över tillåtna/blockerade för dessa inställningar.  |
|


## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel om du vill lära dig hur du distribuerar en säkerhets agent...

> [!div class="nextstepaction"]
> [Distribuera en säkerhets agent](how-to-deploy-agent.md)
