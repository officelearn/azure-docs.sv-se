---
title: 'Snabb start: Konfigurera och aktivera säkerhetsmodulen för Azure återställnings tider'
description: Lär dig att publicera och aktivera säkerhetsmodulen för Azure återställnings tider service i Azure IoT Hub.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2020
ms.author: rkarlin
ms.openlocfilehash: 321c8d2b9e58aba943c5bf19adf54d6359c5be96
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351784"
---
# <a name="quickstart-security-module-for-azure-rtos-preview"></a>Snabb start: säkerhetsmodul för Azure-återställnings tider (för hands version)

Den här artikeln innehåller en förklaring av kraven innan du börjar och förklarar hur du aktiverar säkerhetsmodulen för Azure återställnings tider service på en IoT Hub. Om du för närvarande inte har en IoT Hub kan du läsa [skapa ett IoT Hub med hjälp av Azure Portal](../iot-hub/iot-hub-create-through-portal.md) för att komma igång.

> [!NOTE]
> Säkerhetsmodulen för Azure-återställnings tider stöds bara på standard-nivå IoT Hub.

## <a name="prerequisites"></a>Förutsättningar 

### <a name="supported-devices"></a>Enheter som stöds

- ST STM32F746G Discovery Kit
- NXP i.MX RT1060 EVK
- Mikrochip SAM E54 Xplained Pro EVK

Ladda ned, kompilera och kör en av. zip-filerna för den speciella tavlan och verktyget (IAR, semi, IDE eller PC) som du väljer från [säkerhetsmodulen för Azure återställnings tider GitHub-resursen](https://github.com/azure-rtos/azure-iot-preview/releases).

### <a name="azure-resources"></a>Azure-resurser

Nästa steg för att komma igång förbereder dina Azure-resurser. Du behöver ett IoT Hub och vi föreslår en Log Analytics arbets yta. För IoT Hub behöver du din IoT Hub anslutnings sträng för att ansluta till enheten. 
  
### <a name="iot-hub-connection"></a>IoT Hub anslutning

Det krävs en IoT Hub anslutning för att komma igång. 

1. Öppna din **IoT Hub** i Azure Portal.
1. Kopiera IoT-anslutningssträngen till [konfigurations filen](how-to-azure-rtos-security-module.md).


Autentiseringsuppgifterna för anslutningen hämtas från konfiguration av användar program **HOST_NAME**, **DEVICE_ID** och **DEVICE_SYMMETRIC_KEY**.

Säkerhetsmodulen för Azure återställnings tider använder Azure IoT mellanprogram anslutningar baserat på **MQTT** -protokollet.


### <a name="log-analytics-workspace"></a>Log Analytics-arbetsyta

Log Analytics inmatning i IoT Hub är inaktiverat som standard Defender för IoT-lösning. Gör så här för att aktivera det för att arbeta med säkerhetsmodulen för Azure återställnings tider: 
1. I Azure Portal går du till din IoT Hub.
1. Välj **Inställningar** under **säkerhets** menyn.
   :::image type="content" source="media/quickstart/azure-rtos-hub-settings.png" alt-text="Åtkomst till data insamlings alternativ för Azure-återställnings tider"::: 
1. Välj **data insamling**. 
1. I **konfigurations alternativet för arbets ytan** växlar du till **på**. 
1. Skapa en ny Log Analytics arbets yta eller bifoga en befintlig. Se till att alternativet **åtkomst till rå data för säkerhets data** är markerat. 
 :::image type="content" source="media/quickstart/azure-rtos-data-collection-on.png" alt-text="Azure återställnings tider-konfiguration som visar data insamlings alternativ och alternativ för rå säkerhets data som valts":::
1. Välj **Spara**
1. Gå tillbaka till listan med Azure-resurser och bekräfta att du ser Log Analytics arbets ytan som du skapade eller kopplade är aktive rad för IoT Hub.
    :::image type="content" source="media/quickstart/verify-azure-resource-list.png" alt-text="Kontrol lera Azures resurs lista för att bekräfta att rätt Log Analytics arbets yta har lagts till för en IoT Hub"::: 

## <a name="next-steps"></a>Nästa steg

Fortsätt till nästa artikel och Slutför konfigurationen och anpassningen av lösningen.

> [!div class="nextstepaction"]
> [Konfigurera säkerhetsmodul för Azure RTOS](how-to-azure-rtos-security-module.md)