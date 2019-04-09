---
title: Hur du distribuerar Azure IoT OPC UA device management-modulen till ett befintligt projekt | Microsoft Docs
description: Så här distribuerar OPC-Twin till ett befintligt projekt.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: dcf6acca344fe2a34fdc48fe89c5a1ee62b10b23
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59255894"
---
# <a name="deploy-opc-twin-to-an-existing-project"></a>Distribuera OPC-Twin till ett befintligt projekt

Enhetstvillingen för OPC-modulen körs på IoT Edge och tillhandahåller flera edge-tjänster till OPC-Enhetstvillingen och registertjänster. 

Tjänsten OPC Enhetstvillingen micro underlättar kommunikationen mellan factory operatorer och OPC UA-serverenheter på fabriksgolvet via en OPC Twin IoT Edge-modul. Tjänsten micro exponerar OPC UA-tjänster (Bläddra, Läs, Skriv- och körningsbehörighet) via dess REST-API. 

Tjänsten OPC UA-enhetsregistret micro ger åtkomst till registrerade OPC UA-program och deras slutpunkter. Operatörer och administratörer kan registrera och avregistrera nya OPC UA-program och bläddra bland befintliga, inklusive slutpunkter. Förutom hantering av slutpunkten och programmet katalogiserar registry-tjänsten också registrerade OPC Device Twin IoT Edge-moduler. Tjänst-API ger dig kontroll över edge-modul-funktionerna, till exempel starta eller stoppa serveridentifiering (genomsökning services) eller aktivera nya endpoint twins som kan nås med hjälp av tjänsten OPC-Twin micro.

Kärnan i modulen är den överordnade identitet. Övervakaren hanterar endpoint twin, vilket motsvarar OPC UA-serverslutpunkter som aktiveras med hjälp av motsvarande OPC UA-registret API. Den här slutpunkten twins översätta OPC UA JSON som tagits emot från OPC-Twin micro tjänsten som körs i molnet till binära OPC UA-meddelanden, som skickas över en tillståndskänslig säker kanal till hanterade slutpunkten. Övervakaren tillhandahåller även av identifieringstjänster som skickar händelser för identifiering av enheten till OPC UA Device Onboarding-tjänsten för bearbetning, där de här händelserna resultera i att uppdateringar till OPC UA-registret.  Den här artikeln visar hur du distribuerar OPC-Twin-modulen till ett befintligt projekt. 

> [!NOTE]
> Mer information om distributionsinformation och instruktioner finns i GitHub [databasen](https://github.com/Azure/azure-iiot-opc-twin-module).

## <a name="prerequisites"></a>Förutsättningar

Kontrollera att du har PowerShell och [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) installerade tillägg.   Om du inte har gjort det ännu, klona GitHub-lagringsplatsen.  Öppna en kommandotolk eller terminal och kör:

```bash
git clone --recursive https://github.com/Azure/azure-iiot-components 
cd azure-iiot-components
```

## <a name="deploy-industrial-iot-services-to-azure"></a>Distribuera industriella IoT-tjänster till Azure

1. På den öppna Kommandotolken eller kör terminal:

   ```bash
   deploy
   ```

2. Följ anvisningarna för att tilldela ett namn för resursgruppen för distributionen och ett namn till webbplatsen.   Skriptet distribuerar mikrotjänster och deras beroenden för Azure-plattformen i resursgrupp i Azure-prenumerationen.  Skriptet kan du även registrerar ett program i din Azure Active Directory (AAD)-klient för att stödja OAUTH-baserad autentisering.  Distributionen tar flera minuter.  Ett exempel på det alternativ som visas när lösningen har distribuerats:

   ![Industriella IoT OPC-Twin distribuera till befintligt projekt](media/howto-opc-twin-deploy-existing/opc-twin-deploy-existing1.png)

   Utdata innehåller URL: en för den offentliga slutpunkten. 

3. När skriptet har slutförts, väljer du om du vill spara .env-fil.  Du behöver miljöfil .env om du vill ansluta till molnslutpunkten med verktyg som konsolen eller distribuera moduler för utveckling och felsökning.

## <a name="troubleshooting-deployment-failures"></a>Felsöka fel vid distribution

### <a name="resource-group-name"></a>Namn på resursgrupp

Kontrollera att du använder ett kort och enkelt resursgruppens namn.  Namnet används också namn resurser så det måste vara kompatibel med resursen som krav för namngivning.  

### <a name="website-name-already-in-use"></a>Webbplatsnamnet används redan

Det är möjligt att namnet på webbplatsen används redan.  Om du får det här felet kan behöva du använda ett annat programnamn.

### <a name="azure-active-directory-aad-registration"></a>Azure Active Directory (AAD)-registrering

Distributionsskriptet försöker registrera två AAD-program i Azure Active Directory.  Beroende på dina rättigheter till de valda AAD-klienten kan distributionen misslyckas. Det finns två alternativ:

1. Om du väljer en AAD-klient från en lista över klienter, starta om skriptet och välja ett annat i listan.
2. Du kan också distribuera en privat AAD-klient i en annan prenumeration, startar du om och väljer för att använda den.

> [!WARNING]
> ALDRIG fortsätta utan autentisering.  Om du väljer att göra det, alla kan komma åt dina OPC-enhetshantering slutpunkter från Internet har inte autentiserats.   Du kan alltid välja den [”local” distributionsalternativ](howto-opc-twin-deploy-dependencies.md) bestämmer.

## <a name="deploy-an-all-in-one-industrial-iot-services-demo"></a>Distribuera en allt-i-ett industriella IoT-tjänster-demo

Du kan även distribuera en allt-i-ett-demo i stället för bara de tjänster och beroenden.  Alla i en demo innehåller tre OPC UA-servrar, OPC-Twin-modulen, alla mikrotjänster och ett exempel webbprogram.  Den är avsedd för demonstration.

1. Kontrollera att du har en klon av lagringsplatsen (se ovan). Öppna en kommandotolk eller terminal i roten av lagringsplatsen och kör:

   ```bash
   deploy -type demo
   ```

2. Följ anvisningarna för att tilldela ett nytt namn för resursgruppen och ett namn till webbplatsen.  När du har distribuerats visas skriptet URL till slutpunkten för web-program.

## <a name="deployment-script-options"></a>Distributionsalternativ för skript

Skriptet använder följande parametrar:

```bash
-type
```

Typ av distribution (virtuell dator, lokala, demonstration)

```bash
-resourceGroupName
```

Kan vara namnet på en befintlig eller en ny resursgrupp.

```bash
-subscriptionId
```

Valfritt, prenumerations-ID där resurser ska distribueras.

```bash
-subscriptionName
```

Eller prenumerationens namn.

```bash
-resourceGroupLocation
```

Valfritt, ett resursgruppsplats. Om du försöker skapa en ny resursgrupp i den här platsen.

```bash
-aadApplicationName
```

Ett namn för AAD-programmet att registrera. 

```bash
-tenantId
```

AAD-klient att använda.

```bash
-credentials
```

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du distribuerar OPC-Twin till ett befintligt projekt, är här nästa föreslagna steg:

> [!div class="nextstepaction"]
> [Säker kommunikation för OPC-klienten och OPC PLC](howto-opc-vault-deploy-existing-client-plc-communication.md)
