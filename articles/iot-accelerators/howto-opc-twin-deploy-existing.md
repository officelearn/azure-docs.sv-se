---
title: Distribuera en OPC Twin-modul till ett befintligt Azure-projekt | Microsoft-dokument
description: I den här artikeln beskrivs hur du distribuerar OPC Twin till ett befintligt projekt. Du kan också läsa om hur du felsöker distributionsfel.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: b971ec13c71ccfd7d28ae6987593d09201b9b764
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73824124"
---
# <a name="deploy-opc-twin-to-an-existing-project"></a>Distribuera OPC Twin till ett befintligt projekt

OPC Twin-modulen körs på IoT Edge och tillhandahåller flera edge-tjänster till OPC Twin- och Registry-tjänsterna.

OPC Twin-mikrotjänsten underlättar kommunikationen mellan fabriksoperatörer och OPC UA-serverenheter på fabriksgolvet via en OPC Twin IoT Edge-modul. Mikrotjänsten exponerar OPC UA-tjänster (Bläddra, Läsa, Skriva och kör) via dess REST API. 

OPC UA-enhetsregistermikroservice ger åtkomst till registrerade UA-program för OPC och deras slutpunkter. Operatörer och administratörer kan registrera och avregistrera nya OPC UA-program och bläddra bland befintliga, inklusive deras slutpunkter. Förutom hantering av program och slutpunkter katalogerar registertjänsten även registrerade OPC Twin IoT Edge-moduler. Tjänst-API:et ger dig kontroll över edge-modulfunktioner, till exempel starta eller stoppa serveridentifiering (skanningstjänster) eller aktivera nya slutpunktstvillingar som kan nås med hjälp av OPC Twin-mikrotjänsten.

Kärnan i modulen är handledaridentiteten. Övervakaren hanterar slutpunktstvilling, vilket motsvarar OPC UA-serverslutpunkter som aktiveras med motsvarande OPC UA-register-API. Den här slutpunktstvillingarna översätter OPC UA JSON som tas emot från OPC Twin-mikrotjänsten som körs i molnet till binära OPC UA-meddelanden, som skickas över en tillståndskänslig säker kanal till den hanterade slutpunkten. Övervakaren tillhandahåller också identifieringstjänster som skickar enhetsidentifieringshändelser till OPC UA-enhetens introduktionstjänst för bearbetning, där dessa händelser resulterar i uppdateringar av OPC UA-registret.  Den här artikeln visar hur du distribuerar OPC Twin-modulen till ett befintligt projekt.

> [!NOTE]
> Mer information om distributionsinformation och instruktioner finns i [GitHub-databasen](https://github.com/Azure/azure-iiot-opc-twin-module).

## <a name="prerequisites"></a>Krav

Kontrollera att du har PowerShell- och [AzureRM PowerShell-tillägg](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) installerade. Om du inte redan har gjort det klonar du den här GitHub-databasen. Kör följande kommandon i PowerShell:

```powershell
git clone --recursive https://github.com/Azure/azure-iiot-components.git
cd azure-iiot-components
```

## <a name="deploy-industrial-iot-services-to-azure"></a>Distribuera industriella IoT-tjänster till Azure

1. Kör i PowerShell-sessionen:

    ```powershell
    set-executionpolicy -ExecutionPolicy Unrestricted -Scope Process
    .\deploy.cmd
    ```

2. Följ anvisningarna om du vill tilldela ett namn till resursgruppen för distributionen och ett namn till webbplatsen.   Skriptet distribuerar mikrotjänsterna och deras Azure-plattformsberoenden till resursgruppen i din Azure-prenumeration.  Skriptet registrerar också ett program i din Azure Active Directory (AAD) klient för att stödja OAUTH-baserad autentisering.  Distributionen tar flera minuter.  Ett exempel på vad du skulle se när lösningen har distribuerats:

   ![Industriell IoT OPC Twin distribuera till befintliga projekt](media/howto-opc-twin-deploy-existing/opc-twin-deploy-existing1.png)

   Utdata innehåller URL:en för den offentliga slutpunkten. 

3. När skriptet har slutförts väljer du om `.env` du vill spara filen.  Du behöver `.env` miljöfilen om du vill ansluta till molnslutpunkten med verktyg som konsolen eller distribuera moduler för utveckling och felsökning.

## <a name="troubleshooting-deployment-failures"></a>Felsöka distributionsfel

### <a name="resource-group-name"></a>Namn på resursgrupp

Se till att du använder ett kort och enkelt resursgruppsnamn.  Namnet används också för att namnge resurser som sådana måste det uppfylla resursnamnkraven.  

### <a name="website-name-already-in-use"></a>Webbplatsnamn som redan används

Det är möjligt att namnet på webbplatsen redan används.  Om du stöter på det här felet måste du använda ett annat programnamn.

### <a name="azure-active-directory-aad-registration"></a>AAD-registrering (Azure Active Directory)

Distributionsskriptet försöker registrera två AAD-program i Azure Active Directory.  Beroende på dina rättigheter till den valda AAD-klienten kan distributionen misslyckas. Det finns två alternativ:

1. Om du väljer en AAD-klient från en lista över klienter startar du om skriptet och väljer en annan från listan.
2. Du kan också distribuera en privat AAD-klient i en annan prenumeration, starta om skriptet och välja att använda det.

> [!WARNING]
> Fortsätt ALDRIG utan autentisering.  Om du väljer att göra det kan vem som helst komma åt dina OPC Twin-slutpunkter från Internet som inte är oautentiserade.   Du kan alltid välja [det "lokala" distributionsalternativet](howto-opc-twin-deploy-dependencies.md) för att sparka däcken.

## <a name="deploy-an-all-in-one-industrial-iot-services-demo"></a>Distribuera en allt-i-ett-demonstration för industriella IoT-tjänster

I stället för bara tjänster och beroenden kan du också distribuera en allt-i-ett-demo.  Allt i en demo innehåller tre OPC UA-servrar, OPC Twin-modulen, alla mikrotjänster och ett exempel på webbprogram.  Den är avsedd för demonstrationsändamål.

1. Kontrollera att du har en klon av databasen (se ovan). Öppna en PowerShell-prompt i databasens rot och kör:

    ```powershell
    set-executionpolicy -ExecutionPolicy Unrestricted -Scope Process
    .\deploy -type demo
    ```

2. Följ anvisningarna om du vill tilldela ett nytt namn till resursgruppen och ett namn till webbplatsen.  När du har distribuerats visas webbprogrammets slutpunkts URL.Once deployed successfully, the script will display the URL of the web application endpoint.

## <a name="deployment-script-options"></a>Alternativ för distributionsskript

Skriptet tar följande parametrar:

```powershell
-type
```

Typ av distribution (vm, lokal, demo)

```powershell
-resourceGroupName
```

Kan vara namnet på en befintlig eller en ny resursgrupp.

```powershell
-subscriptionId
```

Valfritt, prenumerations-ID där resurser ska distribueras.

```powershell
-subscriptionName
```

Eller prenumerationsnamnet.

```powershell
-resourceGroupLocation
```

Valfri, en resursgruppsplats. Om det anges försöker du skapa en ny resursgrupp på den här platsen.

```powershell
-aadApplicationName
```

Ett namn för AAD-ansökan att registrera sig under.

```powershell
-tenantId
```

AAD-klient att använda.

```powershell
-credentials
```

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du distribuerar OPC Twin till ett befintligt projekt, här är det föreslagna nästa steget:

> [!div class="nextstepaction"]
> [Säker kommunikation av OPC UA Client och OPC UA PLC](howto-opc-vault-secure.md)
