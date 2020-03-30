---
title: 'Snabbstart: Aktivera Azure Security Center för IoT-tjänst i IoT Hub'
description: Lär dig hur du lägger till och aktiverar azure security center för IoT-säkerhetstjänsten i din Azure IoT Hub.
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
ms.date: 11/08/2019
ms.author: mlottner
ms.openlocfilehash: 0b1f09cfaf107802e1ce6586b3f96b14269aaced
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "74664870"
---
# <a name="quickstart-onboard-azure-security-center-for-iot-service-in-iot-hub"></a>Snabbstart: Inbyggd Azure Security Center för IoT-tjänst i IoT Hub

Den här artikeln innehåller en förklaring av hur du aktiverar Azure Security Center för IoT-tjänsten på din befintliga IoT Hub. Om du för närvarande inte har en IoT Hub läser du [Skapa en IoT-hubb med Azure-portalen](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal) för att komma igång. 

> [!NOTE]
> Azure Security Center för IoT stöder för närvarande endast standardnivå IoT Hubs.


## <a name="prerequisites-for-enabling-the-service"></a>Förutsättningar för att aktivera tjänsten

- Log Analytics-arbetsyta
  - Två typer av information lagras som standard i logganalysarbetsytan av Azure Security Center för IoT. **säkerhetsvarningar** och **rekommendationer**. 
  - Du kan välja att lägga till lagring av ytterligare en informationstyp, **råa händelser**. Observera att lagring av **råhändelser** i Log Analytics medför ytterligare lagringskostnader. 
- IoT Hub (standardnivå)
- Uppfylla alla [servicekrav](service-prerequisites.md) 

## <a name="enable-azure-security-center-for-iot-on-your-iot-hub"></a>Aktivera Azure Security Center för IoT på din IoT-hubb 

Så här aktiverar du säkerhet på din IoT Hub: 

1. Öppna din **IoT Hub** i Azure-portalen. 
1. Klicka på Skydda **din IoT-lösning**under **säkerhetsmenyn.**    


Grattis! Du har slutfört aktiveringen av Azure Security Center för IoT på din IoT Hub. 

### <a name="geolocation-and-ip-address-handling"></a>Geolokalisering och IP-adresshantering

För att skydda din IoT-lösning samlas och lagras IP-adresser för inkommande och utgående anslutningar till och från dina IoT-enheter, IoT Edge och IoT Hub(er) som standard. Den här informationen är nödvändig för att identifiera onormal anslutning från misstänkta IP-källor. Till exempel när försök görs att upprätta anslutningar från en IP-källa för ett känt botnät eller från en IP-källa utanför geolokaliseringen. Azure Security Center för IoT-tjänsten erbjuder flexibiliteten att aktivera och inaktivera insamling av IP-adressdata när som helst. 

Så här aktiverar eller inaktiverar du insamling av IP-adressdata: 

1. Öppna IoT Hub och välj sedan **Översikt** på **säkerhetsmenyn.** 
2. Välj skärmen **Inställningar** och ändra inställningarna för geolokalisering och/eller IP-hantering som du vill.

### <a name="log-analytics-creation"></a>Skapa logganalys

När Azure Security Center för IoT är aktiverat skapas en standardarbetsyta för Azure Log Analytics för att lagra råa säkerhetshändelser, aviseringar och rekommendationer för dina IoT-enheter, IoT Edge och IoT Hub. Varje månad är de första fem (5) GB data som intas per kund till Azure Log Analytics-tjänsten gratis. Alla GB data som intas i din Azure Log Analytics-arbetsyta behålls utan kostnad under de första 31 dagarna. Läs mer om [Log Analytics-priser.](https://azure.microsoft.com/pricing/details/monitor/)

Så här ändrar du arbetsytans konfiguration av Log Analytics:

1. Öppna IoT Hub och välj sedan **Översikt** på **säkerhetsmenyn.** 
2. Välj skärmen **Inställningar** och ändra arbetsytans konfiguration av Logganalysinställningar som du vill.

### <a name="customize-your-iot-security-solution"></a>Anpassa din IoT-säkerhetslösning
Som standard skyddar du automatiskt alla IoT-hubbar för att aktivera Azure Security Center för IoT-lösning automatiskt under din Azure-prenumeration. 

Så här aktiverar eller inaktiverar du Azure Security Center för IoT-tjänst på en specifik IoT Hub: 

1. Öppna IoT Hub och välj sedan **Översikt** på **säkerhetsmenyn.** 
2. Välj skärmen **Inställningar** och ändra säkerhetsinställningarna för alla IoT-hubb i din Azure-prenumeration som du vill.


## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel för att konfigurera din lösning ...

> [!div class="nextstepaction"]
> [Konfigurera lösningen](quickstart-configure-your-solution.md)
