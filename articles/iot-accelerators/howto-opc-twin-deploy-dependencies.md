---
title: Så här distribuerar du Azure IoT OPC UA device management molnet beroenden | Microsoft Docs
description: Så här distribuerar OPC Twin Azure beroenden.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: cdd89293328da47c8e338bb6fca8c7b93dd84f97
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2019
ms.locfileid: "58759659"
---
# <a name="deploying-dependencies-for-local-development"></a>Distribution av beroenden för lokal utveckling

Den här artikeln förklarar hur du distribuerar Azure-plattformstjänster behöver göra lokal utveckling och felsökning.   I slutet har du en resursgrupp som distribueras som innehåller allt du behöver för lokal utveckling och felsökning.

## <a name="deploy-azure-platform-services"></a>Distribuera Azure-plattformstjänsterna

1. Kontrollera att du har PowerShell och [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-1.1.0) installerade tillägg.  Öppna en kommandotolk eller terminal och kör:

   ```bash
   git clone https://github.com/Azure/azure-iiot-components
   cd azure-iiot-components
   ```

   ```bash
   deploy -type local
   ```

2. Följ anvisningarna för att tilldela ett namn till resursgruppen för din distribution.  Skriptet distribuerar endast beroenden till den här resursgruppen i din Azure-prenumeration, men inte mikrotjänster.  Skriptet kan du även registrerar ett program i Azure Active Directory.  Detta behövs för att stödja OAUTH-baserad autentisering.  Distributionen kan ta flera minuter.

3. När skriptet har körts kan du välja för att spara .env-fil.  Miljöfil .env är konfigurationsfilen för alla tjänster och verktyg som du vill köra på utvecklingsdatorn.  

## <a name="troubleshooting-deployment-failures"></a>Felsöka fel vid distribution

### <a name="resource-group-name"></a>Namn på resursgrupp

Kontrollera att du använder ett kort och enkelt resursgruppens namn.  Namnet används också namn resurser så det måste vara kompatibel med resursen som krav för namngivning.  

### <a name="azure-active-directory-aad-registration"></a>Azure Active Directory (AAD)-registrering

Distributionsskriptet försöker registrera AAD-program i Azure Active Directory.  Beroende på dina rättigheter till de valda AAD-klienten kan det misslyckas.   Det finns 3 alternativ:

1. Om du väljer en AAD-klient från en lista över klienter, starta om skriptet och välja ett annat i listan.
2. Du kan också distribuera en privat AAD-klient, startar du om och väljer för att använda den.
3. Fortsätt utan autentisering.  Eftersom du kör dina mikrotjänster lokalt, detta är acceptabelt, men efterlikna inte produktionsmiljöer.  

## <a name="next-steps"></a>Nästa steg

Nu när OPC-enhetshantering tjänster har distribuerats till ett befintligt projekt, är här nästa föreslagna steg:

> [!div class="nextstepaction"]
> [Lär dig mer om hur du distribuerar OPC-enhetshantering moduler](howto-opc-twin-deploy-modules.md)
