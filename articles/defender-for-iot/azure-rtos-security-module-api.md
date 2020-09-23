---
title: Säkerhetsmodul för Azure RTOS-API
description: Referens-API för säkerhetsmodulen för Azure-återställnings tider.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2020
ms.author: mlottner
ms.openlocfilehash: 8191d68f701a3be6a37d8623d2d900f6aa6c1851
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90941729"
---
# <a name="security-module-for-azure-rtos-api-preview"></a>Säkerhetsmodul för Azure återställnings tider-API (för hands version)

Detta API är avsett att användas med säkerhetsmodulen endast för Azure-återställnings tider. Ytterligare resurser finns i [säkerhetsmodulen för Azure återställnings tider GitHub-resursen](https://github.com/azure-rtos/azure-iot-preview/releases). 

## <a name="enable-security-module-for-azure-rtos"></a>Aktivera säkerhetsmodulen för Azure-återställnings tider

**nx_azure_iot_security_module_enable**

### <a name="prototype"></a>Prototyp

```c
UINT nx_azure_iot_security_module_enable(NX_AZURE_IOT *nx_azure_iot_ptr);
```

### <a name="description"></a>Beskrivning

Den här rutinen aktiverar under systemet Azure IoT Security Module. En intern tillstånds dator hanterar insamling av säkerhets händelser och skickar dem till Azure IoT Hub. Endast en NX_AZURE_IOT_SECURITY_MODULE instans krävs och krävs för att hantera data insamling.

### <a name="parameters"></a>Parametrar

| Name | Beskrivning |
|---------|---------|
| nx_azure_iot_ptr [i]    | En pekare till en `NX_AZURE_IOT` .  |

### <a name="return-values"></a>Returvärden

|Returvärden  |Beskrivning |
|---------|---------|
|NX_AZURE_IOT_SUCCESS|   Azure IoT-säkerhetsmodulen har Aktiver ATS.     |
|NX_AZURE_IOT_FAILURE   |  Det gick inte att aktivera Azure IoT Security-modulen på grund av ett internt fel.    |
|NX_AZURE_IOT_INVALID_PARAMETER   |  Säkerhetsmodulen kräver en giltig #NX_AZURE_IOT instans.      |

### <a name="allowed-from"></a>Tillåten från

Konversation

## <a name="disable-azure-iot-security-module"></a>Inaktivera Azure IoT Security-modulen

**nx_azure_iot_security_module_disable**


### <a name="prototype"></a>Prototyp

```c
UINT nx_azure_iot_security_module_disable(NX_AZURE_IOT *nx_azure_iot_ptr);
```

### <a name="description"></a>Beskrivning

Den här rutinen inaktiverar under systemet Azure IoT Security Module.

### <a name="parameters"></a>Parametrar

| Name | Beskrivning |
|---------|---------|
| nx_azure_iot_ptr [i]    | En pekare till `NX_AZURE_IOT` . Om NULL är inaktive rad singleton-instansen. |

### <a name="return-values"></a>Returvärden

|Returvärden  |Beskrivning |
|---------|---------|
|NX_AZURE_IOT_SUCCESS     |   Lyckades när modulen Azure IoT Security har inaktiverats.      |
|NX_AZURE_IOT_INVALID_PARAMETER   |  Azure IoT Hub instansen skiljer sig från den sammansatta singleton-instansen.       |
|NX_AZURE_IOT_FAILURE    |  Det gick inte att inaktivera Azure IoT-säkerhetsmodulen på grund av ett internt fel.       |

### <a name="allowed-from"></a>Tillåten från

Konversation


## <a name="next-steps"></a>Nästa steg

Mer information om hur du kommer igång med Azure återställnings tider Security-modulen finns i följande artiklar:

- Granska [Översikt över](iot-security-azure-rtos.md)Defender for IoT återställnings tider-säkerhetsmodulen.