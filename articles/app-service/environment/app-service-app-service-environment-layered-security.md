---
title: Överlappande säkerhetsarkitekturen med Apptjänstmiljöer
description: Implementera en skiktad säkerhetsarkitekturen med Apptjänstmiljöer.
services: app-service
documentationcenter: ''
author: stefsch
manager: erikre
editor: ''
ms.assetid: 73ce0213-bd3e-4876-b1ed-5ecad4ad5601
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2016
ms.author: stefsch
ms.openlocfilehash: 29c928c7d81eb3a2532f735be9132b49db5da373
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="implementing-a-layered-security-architecture-with-app-service-environments"></a>Implementera en skiktad säkerhetsarkitekturen med Apptjänstmiljöer
## <a name="overview"></a>Översikt
Utvecklare kan skapa en skiktad säkerhetsarkitekturen att tillhandahålla olika nivåer av nätverksåtkomst för varje nivå i Tillämpningsprogrammets fysiska eftersom Apptjänstmiljöer ger en isolerad körningsmiljö har distribuerats till ett virtuellt nätverk.

En gemensam önskan är att dölja API-servrar från allmän tillgång till Internet och endast låta API: er anropas av överordnad webbprogram.  [Nätverkssäkerhetsgrupper (NSG: er)] [ NetworkSecurityGroups] kan användas på undernät som innehåller Apptjänstmiljöer för att begränsa offentlig åtkomst till API-program.

Diagrammet nedan illustrerar ett exempel arkitekturen med en WebAPI baserat app som har distribuerats på en Apptjänst-miljö.  Tre separata web app-instanser, distribueras på tre separata Apptjänstmiljöer, gör backend-anrop med samma WebAPI-App.

![Konceptuell arkitektur][ConceptualArchitecture] 

Grönt plustecken indikera att nätverkssäkerhetsgruppen i undernät som innehåller ”apiase” tillåter inkommande anrop från de överordnade webbprogram som korrekt anrop från sig själv.  Men samma nätverkssäkerhetsgruppen nekar uttryckligen åtkomst till allmän inkommande trafik från Internet. 

Resten av den här artikeln innehåller stegvisa instruktioner för att konfigurera nätverkssäkerhetsgruppen för undernätet som innehåller ”apiase”.

## <a name="determining-the-network-behavior"></a>Bestämma nätverksbeteendet
För att kunna veta vilka Nätverkssäkerhetsregler krävs, måste du bestämma vilka nätverksklienter ska kunna nå Apptjänst-miljön som innehåller API-appen och vilka klienter kommer att blockeras.

Eftersom [nätverkssäkerhetsgrupper (NSG: er)] [ NetworkSecurityGroups] tillämpas på undernät, och Apptjänstmiljöer har distribuerats till undernät gäller reglerna i en NSG för **alla** appar som körs på en Apptjänst-miljö.  Med hjälp av exempel arkitekturen för den här artikeln när en nätverkssäkerhetsgrupp tillämpas på undernät som innehåller ”apiase”, kommer alla appar som körs på ”apiase” Apptjänstmiljö att skyddas av samma uppsättning säkerhetsregler. 

* **Fastställa utgående IP-adressen för överordnade anropare:** vad är IP-adressen eller adresserna för de överordnade anropare?  Dessa adresser måste du uttryckligen tillåts åtkomst i NSG: N.  Eftersom anrop mellan Apptjänstmiljöer betraktas som ”Internet” anrop, tilldelas utgående IP-adress varje tre överordnade Apptjänstmiljöer måste ha behörighet i NSG för undernätet ”apiase”.   Mer information om hur du fastställer utgående IP-adressen för appar som körs i en Apptjänst-miljö finns i [nätverksarkitektur] [ NetworkArchitecture] översiktsartikel.
* **Backend-API-app måste anropa sig själv?**  En ibland förbises och diskret punkt är ett scenario där backend-programmet behöver anropa sig själv.  Om en backend-API-program på en Apptjänst-miljö måste anropa sig själv, även behandlas som ett anrop för ”Internet”.  I exempelarkitektur kräver detta att tillåta åtkomst från det ”apiase” Apptjänstmiljö samt utgående IP-adress.

## <a name="setting-up-the-network-security-group"></a>Konfigurera Nätverkssäkerhetsgruppen
När en uppsättning utgående IP-adresser är kända, är nästa steg att skapa en nätverkssäkerhetsgrupp.  Du kan skapa nätverkssäkerhetsgrupper för båda Resource Manager-baserade virtuella nätverk, samt klassiska virtuella nätverk.  Exemplen nedan visar hur du skapar och konfigurerar en NSG på ett klassiskt virtuellt nätverk med hjälp av Powershell.

För exempel-arkitekturen finns i miljöer i södra centrala USA, så skapas en tom NSG i regionen:

    New-AzureNetworkSecurityGroup -Name "RestrictBackendApi" -Location "South Central US" -Label "Only allow web frontend and loopback traffic"

Ett explicit Låt först lägga till regeln för Azure hanteringsinfrastrukturen enligt beskrivningen i artikeln på [inkommande trafik] [ InboundTraffic] för Apptjänstmiljöer.

    #Open ports for access by Azure management infrastructure
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET' -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP

Därefter två regler har lagts till kan HTTP och HTTPS-anrop från den första överordnade Apptjänst-miljö (”fe1ase”).

    #Grant access to requests from the first upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe1ase" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe1ase" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Spola och upprepa för den andra och tredje överordnade Apptjänstmiljöer (”fe2ase” och ”fe3ase”).

    #Grant access to requests from the second upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe2ase" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe2ase" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

    #Grant access to requests from the third upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe3ase" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe3ase" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Till sist ska bevilja åtkomst till backend-API Apptjänstmiljö utgående IP-adress så att den kan anropa tillbaka till sig själv.

    #Allow apps on the apiase environment to call back into itself
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP apiase" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS apiase" -Type Inbound -Priority 900 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Inga andra Nätverkssäkerhetsregler krävs, eftersom varje NSG innehåller en uppsättning standardregler som blockerar inkommande åtkomst från Internet, som standard.

En fullständig lista över regler i nätverkssäkerhetsgruppen visas nedan.  Observera hur den sista regeln är markerat blockerar inkommande åtkomst från alla anropare än anropare som uttryckligen getts åtkomst.

![NSG-konfiguration][NSGConfiguration] 

Det sista steget är att tillämpa NSG: N på det undernät som innehåller ”apiase” Apptjänst-miljö.

     #Apply the NSG to the backend API subnet
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'yourvnetnamehere' -SubnetName 'API-ASE-Subnet'

Med NSG tillämpad på undernätet, tillåts endast de tre överordnade Apptjänstmiljöer och Apptjänst-miljön som innehåller API serverdel att anropa ”apiase”-miljö.

## <a name="additional-links-and-information"></a>Information och ytterligare länkar
Information om [nätverkssäkerhetsgrupper](../../virtual-network/security-overview.md).

Förstå [utgående IP-adresser] [ NetworkArchitecture] och Apptjänstmiljöer.

[Nätverksportar] [ InboundTraffic] används av Apptjänstmiljöer.

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[NetworkArchitecture]:  app-service-app-service-environment-network-architecture-overview.md
[InboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-layered-security/ConceptualArchitecture-1.png
[NSGConfiguration]:  ./media/app-service-app-service-environment-layered-security/NSGConfiguration-1.png
