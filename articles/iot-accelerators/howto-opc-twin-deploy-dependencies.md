---
title: Så här distribuerar du OPCs dubbla moln beroenden i Azure | Microsoft Docs
description: Den här artikeln beskriver hur du distribuerar OPC-dubbla Azure-beroenden som krävs för att utföra lokal utveckling och fel sökning.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: ae0e18a2dfbce52e50b4a8b58d478a46dff12922
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91282110"
---
# <a name="deploying-dependencies-for-local-development"></a>Distribuera beroenden för lokal utveckling

> [!IMPORTANT]
> Medan vi uppdaterar den här artikeln kan du läsa mer i [Azures industriella IoT](https://azure.github.io/Industrial-IoT/) .

Den här artikeln beskriver hur du endast distribuerar de Azure Platform-tjänster som krävs för lokal utveckling och fel sökning.   När du är klar har du en resurs grupp som innehåller allt du behöver för lokal utveckling och fel sökning.

## <a name="deploy-azure-platform-services"></a>Distribuera Azures plattforms tjänster

1. Kontrol lera att PowerShell-och [AzureRM PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) -tillägg är installerade.  Öppna en kommando tolk eller Terminal och kör:

   ```bash
   git clone https://github.com/Azure/azure-iiot-components
   cd azure-iiot-components
   ```

   ```bash
   deploy -type local
   ```

2. Följ anvisningarna för att tilldela resurs gruppen ett namn för din distribution.  Skriptet distribuerar bara beroenden till den här resurs gruppen i din Azure-prenumeration, men inte Micro-tjänsterna.  Skriptet registrerar också ett program i Azure AD.  Detta krävs för att ge stöd för OAUTH-baserad autentisering.  Distributionen kan ta flera minuter.

3. När skriptet har slutförts kan du välja att spara. miljö filen.  Miljö filen. miljö är konfigurations filen för alla tjänster och verktyg som du vill köra på utvecklings datorn.  

## <a name="troubleshooting-deployment-failures"></a>Felsöka distributions fel

### <a name="resource-group-name"></a>Namn på resursgrupp

Se till att du använder ett kort och enkelt resurs grupp namn.  Namnet används också för att ge resurser samma namn som de måste uppfylla kraven på resurs namn.  

### <a name="azure-active-directory-ad-registration"></a>Azure Active Directory (AD)-registrering

Distributions skriptet försöker registrera Azure AD-program i Azure AD.  Beroende på dina rättigheter till den valda Azure AD-klienten kan detta Miss förväntat. Det finns tre alternativ:

1. Om du väljer en Azure AD-klient från en lista över klienter startar du om skriptet och väljer ett annat i listan.
2. Du kan också distribuera en privat Azure AD-klient, starta om skriptet och välja att använda det.
3. Fortsätt utan autentisering.  Eftersom du kör dina mikrotjänster lokalt, är detta acceptabelt, men den efterliknar inte produktions miljöer.  

## <a name="next-steps"></a>Nästa steg

Nu när du har distribuerat OPC-dubbla tjänster till ett befintligt projekt är det det föreslagna nästa steg:

> [!div class="nextstepaction"]
> [Lär dig hur du distribuerar OPC-sammanflätade moduler](howto-opc-twin-deploy-modules.md)
