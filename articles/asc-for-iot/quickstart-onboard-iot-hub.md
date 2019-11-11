---
title: 'Snabb start: Aktivera Azure Security Center för IoT-tjänsten i IoT Hub'
description: I den här snabb starten lär du dig hur du aktiverar Azure Security Center för IoT-tjänsten i IoT Hub.
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
ms.openlocfilehash: 641ba4c8d3b0e54132c19a493e1e4bf17bb28e13
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904116"
---
# <a name="quickstart-onboard-azure-security-center-for-iot-service-in-iot-hub"></a>Snabb start: onboard Azure Security Center for IoT service i IoT Hub

Den här artikeln innehåller en förklaring av hur du aktiverar Azure Security Center för IoT-tjänsten på din befintliga IoT Hub. Om du för närvarande inte har en IoT Hub kan du läsa [skapa ett IoT Hub med hjälp av Azure Portal](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal) för att komma igång. 

> [!NOTE]
> Azure Security Center for IoT stöder för närvarande bara IoT-hubbar på standard nivå.


## <a name="prerequisites-for-enabling-the-service"></a>Krav för att aktivera tjänsten

- Log Analytics arbets yta
  - Två typer av information lagras som standard i Log Analytics-arbetsytan med Azure Security Center för IoT; **säkerhets aviseringar** och **rekommendationer**. 
  - Du kan välja att lägga till lagring av ytterligare en informations typ, **rå händelser**. Observera att lagring av **rå händelser** i Log Analytics medför ytterligare lagrings kostnader. 
- IoT Hub (standard-nivå)
- Uppfylla alla [tjänst krav](service-prerequisites.md) 

## <a name="enable-azure-security-center-for-iot-on-your-iot-hub"></a>Aktivera Azure Security Center för IoT på din IoT Hub 

Så här aktiverar du säkerhet på IoT Hub: 

1. Öppna din **IoT Hub** i Azure Portal. 
1. Under menyn **säkerhet** klickar du på **skydda din IoT-lösning**.    


Gratulerar! Du har slutfört aktiveringen Azure Security Center för IoT på din IoT Hub. 

### <a name="geolocation-and-ip-address-handling"></a>Hantering av geolokalisering och IP-adresser

För att skydda din IoT-lösning kan IP-adresser för inkommande och utgående anslutningar till och från dina IoT-enheter, IoT Edge och IoT Hub samlas in och lagras som standard. Den här informationen är nödvändig för att identifiera onormal anslutning från misstänkta IP-källor. Till exempel när försök görs att upprätta anslutningar från en IP-källa för en känd botnät eller från en IP-källa utanför din plats. Azure Security Center för IoT-tjänsten erbjuder flexibiliteten att aktivera och inaktivera insamling av IP-Datadata när som helst. 

Aktivera eller inaktivera insamling av IP-adress data: 

1. Öppna din IoT Hub och välj sedan **Översikt** på menyn **säkerhet** . 
2. Välj skärmen **Inställningar** och ändra inställningarna för geolokalisering och/eller IP-hantering som du vill.

### <a name="log-analytics-creation"></a>Skapa Log Analytics

När Azure Security Center för IoT är aktiverat skapas en standard arbets yta för Azure Log Analytics som lagrar säkerhets händelser, aviseringar och rekommendationer för dina IoT-enheter, IoT Edge och IoT Hub. Varje månad är de första fem (5) GB data som matas in per kund till Azure Log Analytics-tjänsten kostnads fritt. Varje GB data som matas in i din Log Analytics-arbetsyta sparas utan kostnad under de första 31 dagarna. Läs mer om [Log Analytics](https://azure.microsoft.com/pricing/details/monitor/) prissättning.

Ändra arbets ytans konfiguration för Log Analytics:

1. Öppna din IoT Hub och välj sedan **Översikt** på menyn **säkerhet** . 
2. Välj skärmen **Inställningar** och ändra arbets ytans konfiguration för Log Analytics inställningar som du vill.

### <a name="customize-your-iot-security-solution"></a>Anpassa din IoT-säkerhetslösning
Som standard säkrar och aktiverar Azure Security Center för IoT-lösningen automatiskt alla IoT-hubbar i din Azure-prenumeration. 

Så här aktiverar eller inaktiverar du Azure Security Center för IoT-tjänsten för en speciell IoT Hub: 

1. Öppna din IoT Hub och välj sedan **Översikt** på menyn **säkerhet** . 
2. Välj skärmen **Inställningar** och ändra säkerhets inställningarna för alla IoT-hubbar i din Azure-prenumeration som du vill.


## <a name="next-steps"></a>Nästa steg

Fortsätt till nästa artikel för att konfigurera din lösning...

> [!div class="nextstepaction"]
> [Konfigurera din lösning](quickstart-configure-your-solution.md)
