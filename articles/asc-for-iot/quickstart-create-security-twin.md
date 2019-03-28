---
title: Skapa en security modultvillingen för ASC för förhandsversionen av IoT | Microsoft Docs
description: Lär dig hur du skapar en ASC för IoT-modultvilling för användning med ASC för IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: c782692e-1284-4c54-9d76-567bc13787cc
ms.service: ascforiot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 89802a638944ec220186e943d5fdc33524b2d4e9
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541674"
---
# <a name="quickstart-create-an-asc-for-iot-module-twin"></a>Snabbstart: Skapa en ASC för IoT-modultvilling

> [!IMPORTANT]
> ASC för IoT är för närvarande i offentlig förhandsversion. Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Den här snabbstarten förklaringar av hur du skapar enskilda ASC för IoT modultvillingar för nya enheter eller batch-skapa modultvillingar för alla enheter i en IoT-hubb.  

## <a name="understanding-asc-for-iot-module-twins"></a>Så här fungerar ASC för IoT-modultvillingar 

För IoT-lösningar som skapats i Azure, spela enhetstvillingar en viktig roll i både enhetshantering och automatisering av affärsprocesser. 

ASC för IoT erbjuder fullständig integrering med din befintliga IoT plattform för enhetshantering, så att du kan hantera säkerhetsstatusen enheten samt kontrollera användning av befintliga funktioner för kontroll av enheter. ASC för IoT-integrering uppnås genom att använda IoT-hubben twin mekanism.  

Se [IoT Hub modultvillingar](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) mer information om allmänna begrepp för modultvillingar i Azure IoT Hub. 
 
ASC för IoT gör använda mekanismen för modulen twin och underhåller en security modultvillingen för var och en av dina enheter. Modultvilling security innehåller all information som rör enhetssäkerhet för var och en av dina enheter. 
 
Om du vill dra full nytta av ASC för IoT-funktioner, måste du skapa, konfigurera och använda dessa security modultvillingar för varje enhet i tjänsten.  

## <a name="create-asc-for-iot-module-twin"></a>Skapa ASC för IoT-modultvilling 

ASC för IoT-modultvillingar kan skapas i batchläge använder standardkonfigurationen, eller individuellt med specifika konfigurationer för varje enhet. I batch skapar för nya enheter eller enheter utan en modultvilling, använder den [modulen batchskript](https://aka.ms/iot-security-github-create-module). 

>[!NOTE] 
> Med batch-metoden inte skriver över befintliga modultvillingar. Med batch-metod skapar endast nya modultvillingar för enheter som inte redan har en modultvilling. 

Se [ändra en security modultvilling](how-to-modify-security-module-twin.md) information om hur du ändrar eller ändra konfigurationen för en befintlig modultvilling. 

Använd följande instruktioner för att skapa en ny ASC för IoT modultvilling för en enhet: 

1. Leta upp och välj den enhet som du vill skapa en security modultvillingen för i din IoT-hubb i din IoT-hubb. 
1. I den **Microsoft identitetsnamn** anger **ascforiotsecurity**.
1. Klicka på **Spara**. 

## <a name="verify-creation-of-a-module-twin"></a>Kontrollera skapandet av en modultvilling

Kontrollera om det finns en modultvilling för säkerhet för en specifik enhet:

1. I Azure IoT Hub, Välj **IoT-enheter** från den **Utforskare** menyn.    
1. Ange enhets-ID eller välj ett alternativ i den **enheten frågefält** och klicka på **fråga enheter**. 
    ![Fråga enheter](./media/quickstart/verify-security-module-twin.png)
1. Välj enheten eller dubbelklicka på den för att öppna sidan med enhetsinformation. 
1. Välj den **modulen identiteter** -menyn och bekräfta förekomsten av den **ascforiotsecurity** modulen och en **anslutningsläge** av **ansluten**i listan över modulen identiteter som hör till enheten. 
    ![Moduler som är associerade med en enhet](./media/quickstart/verify-security-module-twin-2.png)


Mer information om hur du anpassar egenskaperna för ASC för IoT-modultvillingar finns [agentkonfiguration](concept-agent-configuration.md).

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel om du vill lära dig hur du konfigurerar anpassade aviseringar...

> [!div class="nextstepaction"]
> [Konfigurera anpassade aviseringar](quickstart-create-custom-alerts.md)
