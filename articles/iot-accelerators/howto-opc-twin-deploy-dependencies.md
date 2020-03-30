---
title: Distribuera OPC Twin-molnberoenden i Azure | Microsoft-dokument
description: I den här artikeln beskrivs hur du distribuerar OPC Twin Azure-beroenden som behövs för att göra lokal utveckling och felsökning.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 231d1efa02ec80e8ad56a8895d4262d774480111
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73824106"
---
# <a name="deploying-dependencies-for-local-development"></a>Distribuera beroenden för lokal utveckling

I den här artikeln beskrivs hur du distribuerar endast Azure Platform Services som behövs för att göra lokal utveckling och felsökning.   I slutet kommer du att ha en resursgrupp distribuerad som innehåller allt du behöver för lokal utveckling och felsökning.

## <a name="deploy-azure-platform-services"></a>Distribuera Azure-plattformstjänster

1. Kontrollera att du har PowerShell- och [AzureRM PowerShell-tillägg](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) installerade.  Öppna en kommandotolk eller terminal och kör:

   ```bash
   git clone https://github.com/Azure/azure-iiot-components
   cd azure-iiot-components
   ```

   ```bash
   deploy -type local
   ```

2. Följ anvisningarna om du vill tilldela ett namn till resursgruppen för distributionen.  Skriptet distribuerar endast beroenden till den här resursgruppen i din Azure-prenumeration, men inte mikrotjänsterna.  Skriptet registrerar också ett program i Azure Active Directory.  Detta behövs för att stödja OAUTH-baserad autentisering.  Distributionen kan ta flera minuter.

3. När skriptet är klart kan du välja att spara .env-filen.  Miljöfilen .env är konfigurationsfilen för alla tjänster och verktyg som du vill köra på utvecklingsdatorn.  

## <a name="troubleshooting-deployment-failures"></a>Felsöka distributionsfel

### <a name="resource-group-name"></a>Namn på resursgrupp

Se till att du använder ett kort och enkelt resursgruppsnamn.  Namnet används också för att namnge resurser som sådana måste det uppfylla resursnamnkraven.  

### <a name="azure-active-directory-aad-registration"></a>AAD-registrering (Azure Active Directory)

Distributionsskriptet försöker registrera AAD-program i Azure Active Directory.  Beroende på dina rättigheter till den valda AAD-klienten kan detta misslyckas.   Det finns tre alternativ:

1. Om du väljer en AAD-klient från en lista över klienter startar du om skriptet och väljer en annan från listan.
2. Du kan också distribuera en privat AAD-klient, starta om skriptet och välja att använda det.
3. Fortsätt utan autentisering.  Eftersom du kör dina mikrotjänster lokalt är detta acceptabelt, men efterliknar inte produktionsmiljöer.  

## <a name="next-steps"></a>Nästa steg

Nu när du har distribuerat OPC Twin-tjänster till ett befintligt projekt är detta det föreslagna nästa steget:

> [!div class="nextstepaction"]
> [Lär dig mer om hur du distribuerar OPC Twin-moduler](howto-opc-twin-deploy-modules.md)
