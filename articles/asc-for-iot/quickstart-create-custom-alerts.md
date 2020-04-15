---
title: 'Snabbstart: Skapa anpassade aviseringar'
description: Förstå, skapa och tilldela anpassade enhetsaviseringar för Azure Security Center för IoT-säkerhetstjänst.
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
ms.openlocfilehash: 8ba81b669c25d91efa8ad1f07b1a7835b42c5cf4
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310839"
---
# <a name="quickstart-create-custom-alerts"></a>Snabbstart: Skapa anpassade aviseringar

Med hjälp av anpassade säkerhetsgrupper och aviseringar drar du full nytta av säkerhetsinformationen från slutna till slutna enheter och kategorisk enhetskunskap för att säkerställa bättre säkerhet i din IoT-lösning.

## <a name="why-use-custom-alerts"></a>Varför använda anpassade aviseringar?

Du känner till dina IoT-enheter bäst.

För kunder som till fullo förstår sitt förväntade enhetsbeteende kan du med Azure Security Center for IoT översätta den här förståelsen till en enhetsbeteendeprincip och avisera eventuella avvikelser från förväntat, normalt beteende.

## <a name="security-groups"></a>Säkerhetsgrupper

Med säkerhetsgrupper kan du definiera logiska grupper av enheter och hantera deras säkerhetstillstånd på ett centraliserat sätt.

Dessa grupper kan representera enheter med specifik maskinvara, enheter som distribueras på en viss plats eller någon annan grupp som passar dina specifika behov.

Säkerhetsgrupper definieras av en enhetsvillingtaggsegenskap med namnet **SecurityGroup**. Som standard har varje IoT-lösning på IoT Hub en säkerhetsgrupp med namnet **standard**. Ändra värdet för egenskapen **SecurityGroup** om du vill ändra säkerhetsgruppen för en enhet.

Ett exempel:

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

Använd säkerhetsgrupper för att gruppera dina enheter i logiska kategorier. När du har skapat grupperna tilldelar du dem till de anpassade aviseringar som du väljer, för den mest effektiva end-to-end IoT-säkerhetslösningen.

## <a name="customize-an-alert"></a>Anpassa en avisering

1. Öppna din IoT Hub.
1. Klicka på **Anpassade aviseringar** i avsnittet **Säkerhet.**
1. Välj en säkerhetsgrupp som du vill använda anpassningen på.
1. Klicka på **Lägg till en anpassad avisering**.
1. Välj en anpassad avisering i listrutan.
1. Redigera de egenskaper som krävs, klicka på **OK**.
1. Se till att klicka på **SPARA**. Utan att spara den nya aviseringen tas aviseringen bort nästa gång du stänger IoT Hub.

## <a name="alerts-available-for-customization"></a>Varningar tillgängliga för anpassning

Azure Security Center för IoT erbjuder ett stort antal aviseringar som kan anpassas efter dina specifika behov. Granska den [anpassningsbara varningstabellen](concept-customizable-security-alerts.md) för allvarlighetsgrad, datakälla, beskrivning och våra föreslagna reparationssteg om och när varje avisering tas emot.

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel om du vill lära dig hur du distribuerar en säkerhetsagent...

> [!div class="nextstepaction"]
> [Distribuera en säkerhetsagent](how-to-deploy-agent.md)
