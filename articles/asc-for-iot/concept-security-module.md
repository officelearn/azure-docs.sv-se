---
title: Förstå Azure Security Center för IoT-säkerhetsmodulen, dubbla | Microsoft Docs
description: Lär dig mer om konceptet med säkerhetsmoduler och hur de används i Azure Security Center för IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: a5c25cba-59a4-488b-abbe-c37ff9b151f9
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2019
ms.author: mlottner
ms.openlocfilehash: ab3b6e740e644a1ed1495eb776045888be448047
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596492"
---
# <a name="security-module"></a>Säkerhetsmodul


I den här artikeln förklaras hur Azure Security Center for IoT använder enhets dubbla och moduler. 

## <a name="device-twins"></a>Enhets dubbla

För IoT-lösningar som är inbyggda i Azure spelar enheten upp en viktig roll i både enhets hantering och process automatisering.  

Azure Security Center for IoT erbjuder fullständig integrering med din befintliga plattform för IoT-enhets hantering, så att du kan hantera din enhets säkerhets status och använda befintliga funktioner för enhets kontroll. Integreringen uppnås genom att använda den IoT Hub dubbla mekanismen.  

Lär dig mer om begreppet [enhets enheter](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins) i Azure IoT Hub. 

## <a name="security-module-twins"></a>Säkerhetsmodul, dubbla

Azure Security Center for IoT underhåller en säkerhetsmodul för varje enhet i tjänsten.
Säkerhetsmodulen är dubbelt innehåller all information som är relevant för enhets säkerhet för varje enskild enhet i din lösning.
Egenskaperna för enhets säkerhet underhålls i en dedikerad säkerhetsmodul för säkrare kommunikation och för att aktivera uppdateringar och underhåll som kräver färre resurser.  

Mer information om hur du skapar, anpassar och konfigurerar den dubbla finns i [skapa säkerhetsmodul](quickstart-create-security-twin.md) , grupp två och [Konfigurera säkerhets agenter](how-to-agent-configuration.md) . Mer information om hur modulen finns i IoT Hub finns i [förstå modulernas dubblare](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) . 
 

## <a name="see-also"></a>Se också
- [Översikt över Azure Security Center för IoT](overview.md)
- [Distribuera säkerhets agenter](how-to-deploy-agent.md)
- [Autentiseringsmetoder för säkerhets agent](concept-security-agent-authentication-methods.md)