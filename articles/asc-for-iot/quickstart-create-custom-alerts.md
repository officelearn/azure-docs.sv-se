---
title: 'Snabb start: skapa anpassade aviseringar för Azure Security Center för IoT'
description: Förstå, skapa och tilldela anpassade enhets aviseringar för tjänsten Azure Security Center för IoT-säkerhet.
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
ms.date: 1/27/2020
ms.author: mlottner
ms.openlocfilehash: 063e5c9e7d75fd1c07d148c265b1fe64eee3cbc8
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303536"
---
# <a name="quickstart-create-custom-alerts"></a>Snabb start: skapa anpassade aviseringar


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

Azure Security Center for IoT erbjuder ett stort antal aviseringar som kan anpassas efter dina egna behov. Granska den [anpassningsbara aviserings tabellen](concept-customizable-security-alerts.md) för aviserings allvarlighets grad, data källa, beskrivning och våra föreslagna reparations steg om och när varje avisering tas emot. 


## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel om du vill lära dig hur du distribuerar en säkerhets agent...

> [!div class="nextstepaction"]
> [Distribuera en säkerhets agent](how-to-deploy-agent.md)
