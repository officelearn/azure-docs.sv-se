---
title: Aktivera Azure Security Center för IoT-tjänsten i IoT Hub | Microsoft Docs
description: Lär dig hur du aktiverar Azure Security Center för IoT-tjänsten i IoT Hub.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 670e6d2b-e168-4b14-a9bf-51a33c2a9aad
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/16/2019
ms.author: mlottner
ms.openlocfilehash: a794ccea13323f38b20906458e216f85652bfc3e
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596958"
---
# <a name="quickstart-onboard-azure-security-center-for-iot-service-in-iot-hub"></a>Snabbstart: Publicera Azure Security Center för IoT-tjänsten i IoT Hub

Den här artikeln innehåller en förklaring av hur du aktiverar Azure Security Center för IoT-tjänsten på din befintliga IoT Hub. Om du för närvarande inte har en IoT Hub kan du läsa [skapa ett IoT Hub med hjälp av Azure Portal](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal) för att komma igång. 

> [!NOTE]
> Azure Security Center for IoT stöder för närvarande bara IoT-hubbar på standard nivå.
> Azure Security Center for IoT är en lösning för enkel hubb. Om du behöver flera hubbar krävs flera Azure Security Center för IoT-lösningar. 

## <a name="prerequisites-for-enabling-the-service"></a>Krav för att aktivera tjänsten

- Log Analytics-arbetsyta
  - Två typer av information lagras som standard i Log Analytics-arbetsytan med Azure Security Center för IoT; **säkerhets aviseringar** och **rekommendationer**. 
  - Du kan välja att lägga till lagring av ytterligare en informations typ, **rå händelser**. Observera att lagring av **rå händelser** i Log Analytics medför ytterligare lagrings kostnader. 
- IoT Hub (standard-nivå)
- Uppfylla alla [tjänst krav](service-prerequisites.md) 

|Azure Service-regioner som stöds | ||
|---|---|---|
| Centrala USA |East US |USA, östra 2 |
| Västra centrala USA |Västra USA |USA, västra 2 |
| Centrala USA, södra|Norra centrala USA | Centrala Kanada|
| Östra Kanada| Norra Europa|Södra Brasilien|
| Frankrike, centrala| Storbritannien, västra|Storbritannien, södra|
|Västra Europa|Norra Europa| Västra Japan|
|Östra Japan | Sydöstra Australien|Östra Australien|
|Östasien| Sydostasien| Sydkorea, centrala|
|Sydkorea, södra| Indien, centrala| Indien, södra|
|

## <a name="enable-azure-security-center-for-iot-on-your-iot-hub"></a>Aktivera Azure Security Center för IoT på din IoT Hub 

Gör så här om du vill aktivera säkerhet på IoT Hub: 

1. Öppna din **IoT Hub** i Azure Portal. 
1. Under menyn **säkerhet** klickar du på **skydda din IoT-lösning**
1. Lämna **Aktivera** valt som standard. 
1. Välj Log Analytics-arbetsytan.
1. Ange Log Analytics arbets ytans information. 
   - Välj om du vill aktivera en **delad samling** genom att låta den **dubbla samlingen** vara aktive **ras.**
   - Välj att lagra **rå händelser** utöver standard informations typerna för lagring genom att välja lagrings **enhetens säkerhets händelser** i Log Analytics. Lämna den **råa händelsen** växling **vid**händelse. 
    
1. Klicka på **Spara**. 

Grattis! Du har slutfört aktiveringen Azure Security Center för IoT på din IoT Hub. 

## <a name="next-steps"></a>Nästa steg

Fortsätt till nästa artikel för att konfigurera din lösning...

> [!div class="nextstepaction"]
> [Konfigurera din lösning](quickstart-configure-your-solution.md)


