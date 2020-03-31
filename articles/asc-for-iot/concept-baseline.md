---
title: Förstå Azure Security Center för IoT-baslinje| Microsoft-dokument
description: Lär dig mer om konceptet Azure Security Center för IoT-baslinje.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: ef839708-4574-4a40-bc45-07005f8e9daf
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/07/2019
ms.author: mlottner
ms.openlocfilehash: ce5a0625a16c5a02d03ee74f894c585820414fa4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72176622"
---
# <a name="azure-security-center-for-iot-baseline-and-custom-checks"></a>Azure Security Center för IoT-baslinje och anpassade kontroller

I den här artikeln beskrivs Azure Security Center för IoT-baslinje och alla associerade egenskaper för anpassade kontroller från baslinjen summeras.

## <a name="baseline"></a>Baslinje

En baslinje upprättar standardbeteende för varje enhet och gör det lättare att etablera ovanligt beteende eller avvikelse från förväntade normer.  

## <a name="baseline-custom-checks"></a>Anpassade kontroller av originalbaslinjen

Anpassade kontroller vid baslinjen upprättar en anpassad lista med kontroller för varje enhets baslinje med hjälp av **enhetens modulidentitetstvilling.** 

## <a name="setting-baseline-properties"></a>Ange baslinjeegenskaper

1. Leta reda på och välj den enhet du vill ändra i IoT Hub.
1. Klicka på enheten och klicka sedan på **azureiotsecurity-modulen.**
1. Klicka på **Modulidentitet Twin**.
1. Ladda upp **den anpassade referensfilen** för baslinjen till enheten.
1. Lägg till baslinjeegenskaper i säkerhetsmodulen och klicka på **Spara**.

### <a name="baseline-custom-check-file-example"></a>Exempel på anpassad checkfil för baslinje

Så här konfigurerar du anpassade kontroller vid baslinjen:

   ```json
    "desired": {
      "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
        "baselineCustomChecksEnabled": {
          "value" : true
        },
        "baselineCustomChecksFilePath": {
          "value" : "/home/user/full_path.xml"
        },
        "baselineCustomChecksFileHash": {
          "value" : "#hashexample!"
        }
      }
    },
   ```

## <a name="baseline-custom-check-properties"></a>Anpassade kontrollegenskaper för baslinjen

| Namn| Status | Giltiga värden| Standardvärden| Beskrivning |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|baslinjeCustomChecksEnabled|Obligatoriskt: sant |Giltiga värden: **Boolean** |Standardvärde: **falskt** |Maxtidsintervall innan meddelanden med hög prioritet skickas.|
|baslinjeCustomChecksFilePath |Obligatoriskt: sant|Giltiga värden: **Sträng**, **null** |Standardvärde: **null** |Fullständig sökväg för xml-konfigurationen för baslinjen|
|baslinjeCustomChecksFileHash |Obligatoriskt: sant|Giltiga värden: **Sträng**, **null** |Standardvärde: **null** |`sha256sum`xml-konfigurationsfilen. Använd [sha256sum-referensen](https://linux.die.net/man/1/sha256sum) för ytterligare information. |

Om du vill granska ytterligare baslinjeexempel läser du [anpassade baslinjeexempel -1](https://ascforiot.blob.core.windows.net/public/custom_baseline_example_hyperv_ubuntu1804.xml) och [anpassat baslinjeexempel -2](https://ascforiot.blob.core.windows.net/public/oms_audits.xml).

## <a name="next-steps"></a>Nästa steg

- Få tillgång till [rådata](how-to-security-data-access.md)
- [Undersöka en enhet](how-to-investigate-device.md)
- Förstå och utforska [säkerhetsrekommendationer](concept-recommendations.md)
- Förstå och utforska [säkerhetsvarningar](concept-security-alerts.md)
