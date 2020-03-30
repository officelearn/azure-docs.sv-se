---
title: 'Snabbstart: Skapa en säkerhetsmodultvilling för Azure Security Center för IoT'
description: I den här snabbstarten kan du lära dig hur du skapar en Azure Security Center för IoT-modultvilling för användning med Azure Security Center för IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: c782692e-1284-4c54-9d76-567bc13787cc
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/08/2019
ms.author: mlottner
ms.openlocfilehash: b362130c2b717f813a6332f81a3c8179bea4166a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "73904170"
---
# <a name="quickstart-create-an-azureiotsecurity-module-twin"></a>Snabbstart: Skapa en azureiotsecurity-modultvilling

Den här snabbstarten förklarar hur du skapar enskilda azureiotsecurity-modultvillingar för nya enheter eller batch skapar modultvillingar för alla enheter i en IoT Hub. _azureiotsecurity_  

## <a name="understanding-azureiotsecurity-module-twins"></a>Förstå azureiotsecurity-modultvillingar 

För IoT-lösningar som är inbyggda i Azure spelar enhetstvillingar en nyckelroll i både enhetshantering och processautomatisering. 

Azure Security Center för IoT erbjuder fullständig integrering med din befintliga IoT-enhetshanteringsplattform, så att du kan hantera enhetens säkerhetsstatus samt använda befintliga enhetskontrollfunktioner.
Azure Security Center för IoT-integrering uppnås genom att använda IoT Hub twin-mekanismen.  

Se tvillingar i [IoT Hub-modul](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) om du vill veta mer om det allmänna konceptet med modultvillingar i Azure IoT Hub. 
 
Azure Security Center för IoT använder sig av modulens dubbla mekanism och underhåller en tvilling för säkerhetsmodul med namnet _azureiotsecurity_ för var och en av dina enheter.

Säkerhetsmodulen twin innehåller all information som är relevant för enhetens säkerhet för var och en av dina enheter. 
 
Om du vill utnyttja Azure Security Center för IoT-funktioner fullt ut måste du skapa, konfigurera och använda dessa säkerhetsmodultvillingar för varje enhet i tjänsten.  

## <a name="create-azureiotsecurity-module-twin"></a>Skapa azureiotsecurity-modultvilling 

_azureiotsecurity_ modul tvillingar kan skapas på två sätt:
1. [Modulbatchskript](https://aka.ms/iot-security-github-create-module) - skapar automatiskt modultvilling för nya enheter eller enheter utan en modultvilling med standardkonfigurationen.
2. Manuellt redigera varje modul tvilling individuellt med specifika konfigurationer för varje enhet.

>[!NOTE] 
> Om du använder batchmetoden skrivs inte befintliga azureiotsecurity-modultvillingar över. Med batchmetoden skapas bara nya modultvillingar för enheter som inte redan har en säkerhetsmodultvilling. 

Se [agentkonfigurationen](how-to-agent-configuration.md) för att lära dig hur du ändrar eller ändrar konfigurationen av en befintlig modultvilling. 

Om du vill skapa en ny _azureiotsecurity-modultvilling_ manuellt för en enhet använder du följande instruktioner: 

1. Leta reda på och välj den enhet som du vill skapa en säkerhetsmodultvilling för i IoT-hubben.
1. Klicka på enheten och sedan på **Lägg till modulidentitet**.
1. Ange **azureiotsecurity**i fältet **Modulidentitetsnamn** .

1. Klicka på **Spara**. 

## <a name="verify-creation-of-a-module-twin"></a>Verifiera skapandet av en modultvilling

Så här kontrollerar du om det finns en tvilling för en säkerhetsmodul för en viss enhet:

1. I din Azure IoT Hub väljer du **IoT-enheter** på **Explorers-menyn.**    
1. Ange enhets-ID:t eller välj ett alternativ i **fältet Fråga enhet** och klicka på Fråga **enheter**. 
    ![Fråga enheter](./media/quickstart/verify-security-module-twin.png)
1. Markera enheten eller dubbelklicka på den för att öppna sidan Enhetsinformation. 
1. Välj menyn **Modulidentiteter** och bekräfta förekomsten av **azureiotsecurity-modulen** i listan över modulidentiteter som är associerade med enheten. 
    ![Moduler som är associerade med en enhet](./media/quickstart/verify-security-module-twin-3.png)


Mer information om hur du anpassar egenskaperna för Azure Security Center för IoT-modultvillingar finns i [Agentkonfiguration](how-to-agent-configuration.md).

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel om du vill lära dig hur du konfigurerar anpassade aviseringar...

> [!div class="nextstepaction"]
> [Konfigurera anpassade aviseringar](quickstart-create-custom-alerts.md)
