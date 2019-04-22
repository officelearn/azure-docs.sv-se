---
title: Använd Azure Security Center för IoT-tjänsten i förhandsversionen av IoT Hub | Microsoft Docs
description: Lär dig hur du aktiverar Azure Security Center för IoT-tjänsten i din IoT-hubb.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 670e6d2b-e168-4b14-a9bf-51a33c2a9aad
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 59021d09f2af9d430b118acdeb8aa977094e683e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58862394"
---
# <a name="quickstart-enable-service-in-iot-hub"></a>Snabbstart: Aktivera tjänsten i IoT Hub

> [!IMPORTANT]
> Azure Security Center för IoT är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Den här artikeln innehåller en förklaring på hur du aktiverar Azure Security Center (ASC) för IoT-förhandsversioner på IoT Hub.  

> [!NOTE]
> Azure Security Center för IoT för närvarande stöder endast standard-nivån och högre IoT-hubbar.
> Azure Security Center för IoT är en enda hubb-lösning. Om du behöver flera hubbar krävs flera lösningar. 

## <a name="prerequisites-for-enabling-the-service"></a>Förutsättningar för att aktivera tjänsten

- Log Analytics-arbetsyta
  - Två typer av information lagras som standard i Log Analytics-arbetsytan av ASC för IoT; **säkerhetsaviseringar** och **rekommendationer**. 
  - Du kan välja att lägga till lagring av en typ för ytterligare information, **råhändelser**. Observera att lagra **råhändelser** innebär ytterligare lagringskostnader i Log Analytics. 
- IoT Hub (standard-nivån eller senare)

## <a name="enable-asc-for-iot-on-your-iot-hub"></a>Aktivera ASC för IoT i din IoT-hubb 

Om du vill aktivera säkerheten på din IoT-hubb, gör du följande: 

1. Öppna din **IoT Hub** i Azure-portalen. 
2. Välj och öppna **Security** menyn till vänster. 
3. Välj **aktivera IoT-säkerhet**. 
4. Ange information om din Log Analytics-arbetsytan. 
   - Välja att lagra **råhändelser** utöver typerna av information för lagring genom att låta den **raw händelse** växla **på**. 
   - Välja att aktivera **twin samling** genom att låta den **twin samling** växla **på**. 
5. Klicka på **OK**. 
6. Klicka på **Spara**. 

Grattis! Du har slutfört att aktivera ASC för IoT i din IoT-hubb. 

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel om du vill lära dig hur du konfigurerar din lösning...

> [!div class="nextstepaction"]
> [Konfigurera din lösning](quickstart-configure-your-solution.md)