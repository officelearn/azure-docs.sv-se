---
title: Integrering av programgateway med tjänstslutpunkter – Azure App Service | Microsoft-dokument
description: Beskriver hur Application Gateway integreras med Azure App Service skyddad med tjänstslutpunkter.
services: app-service
documentationcenter: ''
author: madsd
manager: ccompy
editor: ''
ms.assetid: 073eb49c-efa1-4760-9f0c-1fecd5c251cc
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/09/2019
ms.author: madsd
ms.custom: seodec18
ms.openlocfilehash: 5e32baa10e98f0f57a861f8cebfb7506ad615631
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74980067"
---
# <a name="application-gateway-integration-with-service-endpoints"></a>Integrering av programgateway med tjänstslutpunkter
Det finns tre varianter av App Service som kräver något annorlunda konfiguration av integreringen med Azure Application Gateway. Variationerna inkluderar regelbunden apptjänst – även känd som ILB-miljö (Internal Load Balancer) och Extern ASE. Den här artikeln kommer att gå igenom hur du konfigurerar den med App Service (flera innehavare) och diskutera överväganden om ILB och extern ASE.

## <a name="integration-with-app-service-multi-tenant"></a>Integrering med App Service (flera innehavare)
App Service (flera innehavare) har en offentlig internetvänd slutpunkt. Med hjälp av [tjänstslutpunkter](../../virtual-network/virtual-network-service-endpoints-overview.md) kan du bara tillåta trafik från ett visst undernät i ett Virtuellt Azure-nätverk och blockera allt annat. I följande scenario använder vi den här funktionen för att säkerställa att en App Service-instans endast kan ta emot trafik från en viss Application Gateway-instans.

![Integrering av programgateway med App Service](./media/app-gateway-with-service-endpoints/service-endpoints-appgw.png)

Det finns två delar till den här konfigurationen förutom att skapa App-tjänsten och Application Gateway. Den första delen är att aktivera tjänstslutpunkter i undernätet i det virtuella nätverket där Programgateway distribueras. Tjänstslutpunkter kontrollerar att all nätverkstrafik som lämnar undernätet mot App-tjänsten kommer att märkas med det specifika undernäts-ID:t. Den andra delen är att ange en åtkomstbegränsning för den specifika webbappen för att säkerställa att endast trafik som taggats med det här specifika undernäts-ID:t tillåts. Du kan konfigurera den med olika verktyg beroende på inställningarna.

## <a name="using-azure-portal"></a>Använda Azure Portal
Med Azure-portalen följer du fyra steg för att etablera och konfigurera installationen. Om du har befintliga resurser kan du hoppa över de första stegen.
1. Skapa en apptjänst med hjälp av en av snabbstarterna i App Service-dokumentationen, till exempel [.Net Core Quickstart](../../app-service/app-service-web-get-started-dotnet.md)
2. Skapa en programgateway med hjälp av [portalen Quickstart,](../../application-gateway/quick-create-portal.md)men hoppa över avsnittet Lägg till backend-mål.
3. Konfigurera [App-tjänsten som serverdel i Application Gateway](../../application-gateway/configure-web-app-portal.md), men hoppa över avsnittet Begränsa åtkomst.
4. Skapa slutligen [åtkomstbegränsningen med hjälp av tjänstslutpunkter](../../app-service/app-service-ip-restrictions.md#service-endpoints).

Du kan nu komma åt App-tjänsten via Application Gateway, men om du försöker komma åt App-tjänsten direkt bör du få ett 403 HTTP-fel som anger att webbplatsen har stoppats.

![Integrering av programgateway med App Service](./media/app-gateway-with-service-endpoints/web-site-stopped.png)

## <a name="using-azure-resource-manager-template"></a>Använda Azure Resource Manager-mall
[Distributionsmallen för Resurshanteraren][template-app-gateway-app-service-complete] etablerar ett fullständigt scenario. Scenariot består av en App Service-instans som är låst med tjänstslutpunkter och åtkomstbegränsning för att endast ta emot trafik från Application Gateway. Mallen innehåller många smarta standardvärden och unika postfix som lagts till i resursnamnen för att det ska vara enkelt. Om du vill åsidosätta dem måste du klona repo- eller hämtningsmallen och redigera den. 

Om du vill använda mallen kan du använda knappen Distribuera till Azure som finns i beskrivningen av mallen, eller så kan du använda lämpliga PowerShell/CLI.

## <a name="using-azure-command-line-interface"></a>Använda Azure Command Line-gränssnitt
[Azure CLI-exemplet](../../app-service/scripts/cli-integrate-app-service-with-application-gateway.md) etablerar en App-tjänst som är låst med tjänstslutpunkter och åtkomstbegränsning för att endast ta emot trafik från Application Gateway. Om du bara behöver isolera trafik till en befintlig App-tjänst från en befintlig programgateway är följande kommando tillräckligt.

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --rule-name AppGwSubnet --priority 200 --subnet mySubNetName --vnet-name myVnetName
```

I standardkonfigurationen säkerställer kommandot både inställningarna för tjänstslutpunktskonfigurationen i undernätet och åtkomstbegränsningen i App-tjänsten.

## <a name="considerations-for-ilb-ase"></a>Överväganden för ILB ASE
ILB ASE är inte utsatt för internet och trafiken mellan instansen och en Application Gateway är därför redan isolerad till det virtuella nätverket. Följande [instruktioner konfigurerar](../environment/integrate-with-application-gateway.md) en ILB ASE och integrerar den med en Application Gateway med Azure-portalen. 

Om du vill vara säker på att endast trafik från undernätet Application Gateway når ASE kan du konfigurera en NSG (Network Security Group) som påverkar alla webbappar i ASE. För NSG kan du ange ip-intervallet för undernätet och eventuellt portarna (80/443). Se till att du inte åsidosätter de [NSG-regler som krävs](../environment/network-info.md#network-security-groups) för att ASE ska fungera korrekt.

Om du vill isolera trafik till en enskild webbapp måste du använda ip-baserade åtkomstbegränsningar eftersom tjänstslutpunkter inte fungerar för ASE. IP-adressen ska vara den privata IP-adressen för Application Gateway-instansen.

## <a name="considerations-for-external-ase"></a>Överväganden för extern ASE
Extern ASE har en offentlig inför belastningsutjämnare som App Service för flera innehavare. Tjänstslutpunkter fungerar inte för ASE, och det är därför du måste använda IP-baserade åtkomstbegränsningar med hjälp av den offentliga IP-adressen för Application Gateway-instansen. Om du vill skapa en extern ASE med Azure-portalen kan du följa den här [snabbstarten](../environment/create-external-ase.md)

[template-app-gateway-app-service-complete]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-app-gateway-v2/ "Azure Resource Manager-mall för fullständigt scenario"

## <a name="considerations-for-kuduscm-site"></a>Överväganden för kudu / scm webbplats
SCM webbplats, även känd som kudu, är en admin webbplats, som finns för varje webbapp. Det är inte möjligt att vända proxy scm webbplats och du troligen också vill låsa den till enskilda IP-adresser eller ett visst undernät.

Om du vill använda samma åtkomstbegränsningar som huvudwebbplatsen kan du ärva inställningarna med följande kommando.

```azurecli-interactive
az webapp config access-restriction set --resource-group myRG --name myWebApp --use-same-restrictions-for-scm-site
```

Om du vill ange enskilda åtkomstbegränsningar för scm-webbplatsen kan du lägga till åtkomstbegränsningar med flaggan --scm-site som visas nedan.

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --scm-site --rule-name KudoAccess --priority 200 --ip-address 208.130.0.0/16
```

## <a name="next-steps"></a>Nästa steg
Mer information om App Service-miljön finns i [dokumentationen till App Service Environment](https://docs.microsoft.com/azure/app-service/environment).

För att skydda din webbapp ytterligare finns information om brandvägg för webbprogram i Application Gateway i [dokumentationen till Azure Web Application Firewall](../../web-application-firewall/ag/ag-overview.md).