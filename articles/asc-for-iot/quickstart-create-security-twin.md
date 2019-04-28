---
title: Skapa en security modultvillingen för Azure Security Center för förhandsversionen av IoT | Microsoft Docs
description: Lär dig hur du skapar en Azure Security Center för IoT-modultvilling för användning med ASC för IoT.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: c782692e-1284-4c54-9d76-567bc13787cc
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 16b5525973b93bc6b073c50c0c657dcbb4679040
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61347904"
---
# <a name="quickstart-create-an-azureiotsecurity-module-twin"></a>Snabbstart: Skapa en azureiotsecurity modultvilling

> [!IMPORTANT]
> Azure Security Center för IoT är för närvarande i offentlig förhandsversion. Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Den här snabbstarten förklaringar av hur du skapar enskilda _azureiotsecurity_ modultvillingar för nya enheter eller batch skapar modultvillingar för alla enheter i en IoT-hubb.  

## <a name="understanding-azureiotsecurity-module-twins"></a>Förstå azureiotsecurity modultvillingar 

För IoT-lösningar som skapats i Azure, spela enhetstvillingar en viktig roll i både enhetshantering och automatisering av affärsprocesser. 

Azure Security Center (ASC) för IoT erbjuder fullständig integrering med din befintliga IoT plattform för enhetshantering, så att du kan hantera säkerhetsstatusen enheten samt kontrollera användning av befintliga funktioner för kontroll av enheter.
ASC för IoT-integrering uppnås genom att använda IoT-hubben twin mekanism.  

Se [IoT Hub modultvillingar](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) mer information om allmänna begrepp för modultvillingar i Azure IoT Hub. 
 
ASC för IoT gör använda mekanismen för modulen twin och underhåller en security modultvilling med namnet _azureiotsecurity_ för var och en av dina enheter.
Modultvilling security innehåller all information som rör enhetssäkerhet för var och en av dina enheter. 
 
Om du vill dra full nytta av ASC för IoT-funktioner, måste du skapa, konfigurera och använda dessa security modultvillingar för varje enhet i tjänsten.  

## <a name="create-azureiotsecurity-module-twin"></a>Skapa azureiotsecurity modultvilling 

_azureiotsecurity_ modultvillingar kan skapas på två sätt:
1. [Modulen batchskript](https://aka.ms/iot-security-github-create-module) – automatiskt skapar modultvillingen för nya enheter eller enheter utan en modultvilling använder standardkonfigurationen.
2. Redigera varje modultvilling individuellt med specifika konfigurationer för varje enhet manuellt.

>[!NOTE] 
> Med batch-metoden inte skriver över befintliga azureiotsecurity modultvillingar. Med batch-metod skapar endast nya modultvillingar för enheter som inte redan har en modultvilling för säkerhet. 

Se [agentkonfiguration](how-to-agent-configuration.md) information om hur du ändrar eller ändra konfigurationen för en befintlig modultvilling. 

Att manuellt skapa en ny _azureiotsecurity_ modultvillingen för en enhet gör på följande sätt: 

1. Leta upp och välj den enhet som du vill skapa en security modultvillingen för i din IoT-hubb i din IoT-hubb.
1. Klicka på din enhet och sedan på **Lägg till modulen identitet**.
1. I den **identitet Modulnamn** anger **azureiotsecurity**.

1. Klicka på **Spara**. 

## <a name="verify-creation-of-a-module-twin"></a>Kontrollera skapandet av en modultvilling

Kontrollera om det finns en modultvilling för säkerhet för en specifik enhet:

1. I Azure IoT Hub, Välj **IoT-enheter** från den **Utforskare** menyn.    
1. Ange enhets-ID eller välj ett alternativ i den **enheten frågefält** och klicka på **fråga enheter**. 
    ![Fråga enheter](./media/quickstart/verify-security-module-twin.png)
1. Välj enheten eller dubbelklicka på den för att öppna sidan med enhetsinformation. 
1. Välj den **modulen identiteter** -menyn och bekräfta förekomsten av den **azureiotsecurity** modul i listan över modulen identiteter som hör till enheten. 
    ![Moduler som är associerade med en enhet](./media/quickstart/verify-security-module-twin-3.png)


Mer information om hur du anpassar egenskaperna för ASC för IoT-modultvillingar finns [agentkonfiguration](how-to-agent-configuration.md).

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel om du vill lära dig hur du konfigurerar anpassade aviseringar...

> [!div class="nextstepaction"]
> [Konfigurera anpassade aviseringar](quickstart-create-custom-alerts.md)
