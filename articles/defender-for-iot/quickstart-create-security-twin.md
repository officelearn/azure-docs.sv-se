---
title: 'Snabb start: skapa en säkerhetsmodul med dubbla'
description: I den här snabb starten får du lära dig hur du skapar en Defender för IoT-modul med dubbla för användning med Azure Defender för IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/08/2019
ms.author: mlottner
ms.openlocfilehash: 74e0e8daa662f4dd49f1886972236b5b0a3b100a
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348865"
---
# <a name="quickstart-create-an-azureiotsecurity-module-twin"></a>Snabb start: skapa en azureiotsecurity-modul med dubbla

I den här snabb starten beskrivs hur du skapar en enskild _azureiotsecurity_ -modul för nya enheter, eller om du skapar en modul med flera enheter i en IoT Hub.

## <a name="understanding-azureiotsecurity-module-twins"></a>Att förstå azureiotsecurity-modulen är dubbla

För IoT-lösningar som är inbyggda i Azure spelar enheten upp en viktig roll i både enhets hantering och process automatisering.

Defender för IoT erbjuder fullständig integrering med din befintliga plattform för IoT-enhets hantering, så att du kan hantera din enhets säkerhets status och använda befintliga funktioner för enhets kontroll.
Defender för IoT-integrering uppnås genom att använda den IoT Hub dubbla mekanismen.

Se [IoT Hub moduls dubblare](../iot-hub/iot-hub-devguide-module-twins.md) för att lära dig mer om det allmänna begreppet modul-i Azure IoT Hub.

Defender for IoT använder modulens dubbla mekanism och underhåller en säkerhetsmodul med dubbla namngivna _azureiotsecurity_ för var och en av dina enheter.

Säkerhetsmodulen är dubbelt lagra all information som är relevant för enhets säkerhet för var och en av dina enheter.

För att kunna använda Defender för IoT-funktioner måste du skapa, konfigurera och använda den här säkerhetsmodulen för alla enheter i tjänsten.

## <a name="create-azureiotsecurity-module-twin"></a>Skapa azureiotsecurity-modul, dubbla

_azureiotsecurity_ -modulens dubblare kan skapas på två sätt:

1. [Modulens batch-skript](https://aka.ms/iot-security-github-create-module) – skapar automatiskt modul dubbla för nya enheter eller enheter utan att en modul är dubbel med standard konfigurationen.
1. Manuellt redigera varje modul, separat med vissa konfigurationer för varje enhet.

>[!NOTE]
> När du använder batch-metoden skrivs inte befintliga azureiotsecurity-moduler över. När du använder batch-metoden skapas bara en ny modul med dubbla objekt för enheter som inte redan har en säkerhetsmodul.

Se [agent konfiguration](how-to-agent-configuration.md) för att lära dig att ändra eller ändra konfigurationen för en befintlig modul med dubbla.

Om du vill skapa en ny _azureiotsecurity_ -modul för en enhet manuellt använder du följande instruktioner:

1. Leta upp och välj den enhet som du vill skapa en säkerhetsmodul för i IoT Hub.
1. Klicka på enheten och sedan på **Lägg till modulens identitet**.
1. Ange **azureiotsecurity** i fältet **namn på modulens identitet** .

1. Klicka på **Spara**.

## <a name="verify-creation-of-a-module-twin"></a>Verifiera att en modul har skapats

Så här kontrollerar du om det finns en säkerhetsmodul för en speciell enhet:

1. I Azure IoT Hub väljer du **IoT-enheter** på menyn **Explorer** .
1. Ange enhets-ID eller Välj ett alternativ i **fältet fråga enhet** och klicka på **fråga enheter**.
    ![Fråga enheter](./media/quickstart/verify-security-module-twin.png)
1. Välj enheten eller dubbelklicka på den för att öppna sidan med enhets information.
1. Välj menyn **modul identiteter** och bekräfta att **azureiotsecurity** -modulen finns i listan över modul identiteter som är associerade med enheten.
    ![Moduler som är kopplade till en enhet](./media/quickstart/verify-security-module-twin-3.png)

Mer information om hur du anpassar egenskaper för Defender for IoT-modulen finns i [agent konfiguration](how-to-agent-configuration.md).

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel och lär dig hur du undersöker säkerhets rekommendationer...

> [!div class="nextstepaction"]
> [Undersöka säkerhetsrekommendationer](quickstart-investigate-security-recommendations.md)