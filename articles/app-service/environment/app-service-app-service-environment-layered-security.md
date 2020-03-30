---
title: Säkerhet i lager v1
description: Lär dig hur du implementerar en säkerhetsarkitektur i lager i apptjänstmiljön. Det här dokumentet tillhandahålls endast för kunder som använder den äldre v1 ASE.
author: stefsch
ms.assetid: 73ce0213-bd3e-4876-b1ed-5ecad4ad5601
ms.topic: article
ms.date: 08/30/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: a8920e97d315dc7bfd0ba22386b8b637afb7c05e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74688789"
---
# <a name="implementing-a-layered-security-architecture-with-app-service-environments"></a>Implementera en säkerhetsarkitektur i flera lager med apptjänstmiljöer
Eftersom App Service-miljöer tillhandahåller en isolerad körningsmiljö som distribueras till ett virtuellt nätverk kan utvecklare skapa en säkerhetsarkitektur i flera lager som ger olika nivåer av nätverksåtkomst för varje fysisk programnivå.

En vanlig önskan är att dölja API-back-ends från allmän Internet-åtkomst och endast tillåta api:er som ska anropas av uppströms webbappar.  [Nätverkssäkerhetsgrupper (NSG)][NetworkSecurityGroups] kan användas i undernät som innehåller App Service-miljöer för att begränsa allmänhetens åtkomst till API-program.

Diagrammet nedan visar en exempelarkitektur med en WebAPI-baserad app som distribueras i en App Service-miljö.  Tre separata webbappinstanser som distribueras på tre separata apptjänstmiljöer, gör backend-samtal till samma WebAPI-app.

![Konceptuell arkitektur][ConceptualArchitecture] 

De gröna plusskyltarna indikerar att nätverkssäkerhetsgruppen i undernätet som innehåller "apiase" tillåter inkommande samtal från uppströmswebbapparna samt anrop från sig själv.  Samma nätverkssäkerhetsgrupp nekar dock uttryckligen åtkomst till allmän inkommande trafik från Internet. 

Resten av den här artikeln går igenom de steg som krävs för att konfigurera nätverkssäkerhetsgruppen i undernätet som innehåller "apiase".

## <a name="determining-the-network-behavior"></a>Bestämma nätverksbeteendet
För att veta vilka nätverkssäkerhetsregler som behövs måste du bestämma vilka nätverksklienter som ska tillåtas nå apptjänstmiljön som innehåller API-appen och vilka klienter som ska blockeras.

Eftersom [NSG-grupper (Network Security Groups)][NetworkSecurityGroups] tillämpas på undernät och App Service-miljöer distribueras till undernät gäller reglerna i en NSG för **alla** appar som körs i en App Service-miljö.  Med hjälp av exempelarkitekturen för den här artikeln, när en nätverkssäkerhetsgrupp tillämpas på undernätet som innehåller "apiase", kommer alla appar som körs på App Service-miljön för apiase att skyddas av samma uppsättning säkerhetsregler. 

* **Bestäm den utgående IP-adressen för uppströms anropare:**  Vad är IP-adressen eller adresserna för uppströmssamtalen?  Dessa adresser måste uttryckligen tillåtas åtkomst i NSG.  Eftersom anrop mellan App Service-miljöer betraktas som "Internet"-anrop måste den utgående IP-adressen som tilldelats var och en av de tre apptjänstmiljöerna i föregående led tillåtas åtkomst i NSG för undernätet "apiase".   Mer information om hur du fastställer den utgående IP-adressen för appar som körs i en App Service-miljö finns i artikeln Översikt över [nätverksarkitektur.][NetworkArchitecture]
* **Kommer backend API-appen att behöva anropa sig själv?**  En ibland förbisedd och subtil punkt är det scenario där backend-programmet måste anropa sig själv.  Om ett backend-API-program i en App Service-miljö måste anropa sig själv behandlas det också som ett "Internet"-anrop.  I exempelarkitekturen kräver detta att du tillåter åtkomst från den utgående IP-adressen för "apiase" App Service Environment också.

## <a name="setting-up-the-network-security-group"></a>Ställa in nätverkssäkerhetsgruppen
När uppsättningen utgående IP-adresser är kända är nästa steg att skapa en nätverkssäkerhetsgrupp.  Nätverkssäkerhetsgrupper kan skapas för både Resource Manager-baserade virtuella nätverk och klassiska virtuella nätverk.  Exemplen nedan visar hur du skapar och konfigurerar en NSG i ett klassiskt virtuellt nätverk med Powershell.

För exempelarkitekturen finns miljöerna i södra centrala USA, så en tom NSG skapas i den regionen:

    New-AzureNetworkSecurityGroup -Name "RestrictBackendApi" -Location "South Central US" -Label "Only allow web frontend and loopback traffic"

Först läggs en explicit tillåtregel till för Azure-hanteringsinfrastrukturen som anges i artikeln om [inkommande trafik][InboundTraffic] för App Service-miljöer.

    #Open ports for access by Azure management infrastructure
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET' -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP

Därefter läggs två regler till för att tillåta HTTP- och HTTPS-anrop från den första uppströms App Service Environment ("fe1ase").

    #Grant access to requests from the first upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe1ase" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe1ase" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Skölj och upprepa för andra och tredje uppströms App Service Environments ("fe2ase" och "fe3ase").

    #Grant access to requests from the second upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe2ase" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe2ase" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

    #Grant access to requests from the third upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe3ase" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe3ase" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Slutligen bevilja åtkomst till den utgående IP-adressen för backend-API:ets App Service-miljö så att den kan anropa tillbaka till sig själv.

    #Allow apps on the apiase environment to call back into itself
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP apiase" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS apiase" -Type Inbound -Priority 900 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Inga andra nätverkssäkerhetsregler krävs, eftersom varje NSG har en uppsättning standardregler som blockerar inkommande åtkomst från Internet som standard.

Den fullständiga listan över regler i nätverkssäkerhetsgruppen visas nedan.  Observera hur den sista regeln, som är markerad, blockerar inkommande åtkomst från alla anropare, förutom anropare som uttryckligen har beviljats åtkomst.

![NSG-konfiguration][NSGConfiguration] 

Det sista steget är att tillämpa NSG på undernätet som innehåller "apiase" App Service Environment.

     #Apply the NSG to the backend API subnet
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'yourvnetnamehere' -SubnetName 'API-ASE-Subnet'

Med NSG tillämpas på undernätet, endast de tre uppströms App Service-miljöer, och App Service-miljön som innehåller API back-end, får anropa till "apiase" miljö.

## <a name="additional-links-and-information"></a>Ytterligare länkar och information
Information om [nätverkssäkerhetsgrupper](../../virtual-network/security-overview.md).

Förstå [utgående IP-adresser][NetworkArchitecture] och App Service-miljöer.

[Nätverksportar][InboundTraffic] som används av App Service-miljöer.

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[NetworkArchitecture]:  app-service-app-service-environment-network-architecture-overview.md
[InboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-layered-security/ConceptualArchitecture-1.png
[NSGConfiguration]:  ./media/app-service-app-service-environment-layered-security/NSGConfiguration-1.png
