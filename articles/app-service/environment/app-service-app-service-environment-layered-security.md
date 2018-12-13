---
title: Lager Säkerhetsarkitektur med App Service Environment - Azure
description: Implementera en arkitektur med flernivåsäkerhet med App Service-miljöer.
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
ms.custom: seodec18
ms.openlocfilehash: 5e25de1ad2042ac978c3698165b9d9baba20e816
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53274175"
---
# <a name="implementing-a-layered-security-architecture-with-app-service-environments"></a>Implementera en arkitektur med flernivåsäkerhet med App Service-miljöer
## <a name="overview"></a>Översikt
Eftersom App Service-miljöer ger en isolerad körningsmiljö som distribuerats i ett virtuellt nätverk, kan utvecklare skapa en arkitektur med flernivåsäkerhet att tillhandahålla olika nivåer av nätverksåtkomst för varje fysisk programnivå.

En vanligt önskemål är att dölja API-servrar från Allmänt Internetåtkomst och endast tillåta API: er anropas av överordnade webbappar.  [Nätverkssäkerhetsgrupper (NSG)] [ NetworkSecurityGroups] kan användas på undernät som innehåller App Service-miljöer för att begränsa offentlig åtkomst till API-program.

Diagrammet nedan visar en exempel-arkitektur med en WebAPI-baserad app som distribuerats på en App Service Environment.  Tre separata web app-instanserna, distribueras på tre separata App Service-miljöer, göra backend-anrop till samma WebAPI-app.

![Konceptuell arkitektur][ConceptualArchitecture] 

Grönt plustecken tyda på att nätverkssäkerhetsgruppen på undernätet som innehåller ”apiase” tillåter inkommande anrop från de överordnade webbapparna som väl anrop från själva.  Men har samma nätverkssäkerhetsgruppen uttryckligen nekar åtkomst till allmänna inkommande trafik från Internet. 

Resten av den här artikeln beskriver de steg som krävs för att konfigurera nätverkssäkerhetsgruppen på undernätet som innehåller ”apiase”.

## <a name="determining-the-network-behavior"></a>Bestämma nätverksbeteendet
Om du vill veta vilka Nätverkssäkerhetsregler krävs, måste du fastställa vilka nätverksklienter ska kunna nå App Service Environment som innehåller API-appen och vilka klienter kommer att blockeras.

Eftersom [nätverkssäkerhetsgrupper (NSG)] [ NetworkSecurityGroups] tillämpas på undernät, och App Service-miljöer distribueras i undernät gäller reglerna i en NSG till **alla** appar körs på en App Service Environment.  Med hjälp av exempel arkitekturen i den här artikeln när en nätverkssäkerhetsgrupp som tillämpas på undernätet som innehåller ”apiase”, kommer alla appar som körs på ”apiase” App Service Environment att skyddas av samma uppsättning säkerhetsregler. 

* **Fastställa överordnade anropare utgående IP-adress:**  Vad är de IP-adressen eller adresserna till de överordnade anropare?  Dessa adresser måste du uttryckligen tillåts åtkomst i NSG: N.  Eftersom anrop mellan App Service-miljöer betraktas som ”Internet”-anrop, tilldelas utgående IP-adressen var och en av tre överordnade App Service Environment måste kunna komma i NSG för undernätet ”apiase”.   Mer information om hur du bestämmer utgående IP-adressen för appar som körs i en App Service Environment finns i den [nätverksarkitektur] [ NetworkArchitecture] översikten.
* **Backend-API-app måste anropa själva?**  En ibland förbises och diskret punkt är ett scenario där backend-programmet måste anropa sig själv.  Om en backend-API-program på en App Service Environment måste anropa sig själv, också behandlas som en ”Internet”-anrop.  I exemplet-arkitekturen kräver detta att tillåta åtkomst från den ”apiase” App Service Environment samt utgående IP-adress.

## <a name="setting-up-the-network-security-group"></a>Konfigurera Nätverkssäkerhetsgruppen
När uppsättningen med utgående IP-adresser är kända, är nästa steg att skapa en nätverkssäkerhetsgrupp.  Nätverkssäkerhetsgrupper kan skapas för båda Resource Manager-baserade virtuella nätverk, samt klassiska virtuella nätverk.  Exemplen nedan visar hur du skapar och konfigurerar en NSG på ett klassiskt virtuellt nätverk med hjälp av Powershell.

För exempelarkitektur finns miljöer i södra centrala USA, så en tom NSG har skapats i regionen:

    New-AzureNetworkSecurityGroup -Name "RestrictBackendApi" -Location "South Central US" -Label "Only allow web frontend and loopback traffic"

Först en explicit tillåta regel har lagts till för av Azure-hanteringsinfrastrukturen enligt vad som anges i artikeln på [inkommande trafik] [ InboundTraffic] för App Service-miljöer.

    #Open ports for access by Azure management infrastructure
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET' -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP

Därefter två regler har lagts till för att tillåta HTTP och HTTPS-anrop från den första överordnade App Service Environment (”fe1ase”).

    #Grant access to requests from the first upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe1ase" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe1ase" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Spola och upprepa för den andra och tredje överordnade App Service-miljöer (”fe2ase” och ”fe3ase”).

    #Grant access to requests from the second upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe2ase" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe2ase" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

    #Grant access to requests from the third upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe3ase" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe3ase" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Till sist bevilja åtkomst till backend-API-App Service Environment utgående IP-adress så att den kan anropa tillbaka till sig själv.

    #Allow apps on the apiase environment to call back into itself
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP apiase" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS apiase" -Type Inbound -Priority 900 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Inga andra Nätverkssäkerhetsregler krävs, eftersom varje NSG har en uppsättning standardregler som blockerar inkommande åtkomst från Internet, som standard.

En fullständig lista över regler i nätverkssäkerhetsgruppen visas nedan.  Observera hur den senaste regel som är markerad, blockerar inkommande åtkomst från alla anropare än anropare som uttryckligen beviljats åtkomst.

![NSG-konfiguration][NSGConfiguration] 

Det sista steget är att använda NSG på undernätet som innehåller ”apiase” App Service Environment.

     #Apply the NSG to the backend API subnet
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'yourvnetnamehere' -SubnetName 'API-ASE-Subnet'

Med NSG tillämpad på undernätet, ska bara tre överordnade App Service Environments och App Service Environment som innehåller API serverdelen kunna anropa ”apiase”-miljö.

## <a name="additional-links-and-information"></a>Ytterligare länkar och Information
Information om [nätverkssäkerhetsgrupper](../../virtual-network/security-overview.md).

Förstå [utgående IP-adresser] [ NetworkArchitecture] och App Service-miljöer.

[Nätverksportar] [ InboundTraffic] används av App Service-miljöer.

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[NetworkArchitecture]:  app-service-app-service-environment-network-architecture-overview.md
[InboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-layered-security/ConceptualArchitecture-1.png
[NSGConfiguration]:  ./media/app-service-app-service-environment-layered-security/NSGConfiguration-1.png
