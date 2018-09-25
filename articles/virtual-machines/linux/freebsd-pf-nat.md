---
title: Använd paketfilter från FreeBSD för att skapa en brandvägg i Azure | Microsoft Docs
description: Lär dig hur du distribuerar en NAT-brandvägg med hjälp av från FreeBSD PF i Azure.
services: virtual-machines-linux
documentationcenter: ''
author: KylieLiang
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/20/2017
ms.author: kyliel
ms.openlocfilehash: 8400fa12e8776834f71740f809df18753291601f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46992322"
---
# <a name="how-to-use-freebsds-packet-filter-to-create-a-secure-firewall-in-azure"></a>Hur du använder paketfilter från FreeBSD för att skapa en säker brandvägg i Azure
Den här artikeln beskrivs hur du distribuerar en NAT-brandvägg med från FreeBSD Packer Filter via Azure Resource Manager-mall för web server ovanligt.

## <a name="what-is-pf"></a>Vad är PF?
PF (ett paketfilter, också skrivit pf) är ett BSD licensierade tillståndskänsliga paketfilter, en central del av programvaran för nätverksfunktion. PF eftersom har utvecklats snabbt och nu har flera fördelar jämfört med andra tillgängliga brandväggar. Network Address Translation (NAT) är i PF ända sedan paketschemaläggaren och aktiva köhantering har integrerats i PF, genom att integrera ALTQ och göra den kan konfigureras via PFS konfiguration. Funktioner som pfsync och KARP för växling vid fel och redundans, authpf för sessionsautentisering och ftp-proxy för att underlätta brandväggsfunktioner svårt FTP-protokollet har även utökat PF. Kortfattat är PF en kraftfull och funktionsrik brandvägg. 

## <a name="get-started"></a>Kom igång
Om du är intresserad av att ställa in en säker brandvägg i molnet för dina webbservrar, nu sätter vi igång. Du kan också använda de skript som används i den här Azure Resource Manager-mallen för att konfigurera din nätverkets topologi.
Azure Resource Manager-mallen ställer in en FreeBSD virtuell dator som utför NAT /redirection med PF och två FreeBSD-virtuella datorer med Nginx-webbserver installeras och konfigureras. Förutom att utföra NAT för två web servrar utgående trafiken kan NAT/omdirigering av virtuell dator spärras HTTP-begäranden och omdirigerar dem till två webbservrarna i resursallokering. Det virtuella nätverket använder den privata icke-dirigerbara IP-adress utrymme 10.0.0.2/24 och du kan ändra parametrarna i mallen. Azure Resource Manager-mall definierar också en routningstabell för hela VNet, som är en samling individuella vägare som används för att åsidosätta Azure standardvägar baserat på IP-måladressen. 

![pf_topology](./media/freebsd-pf-nat/pf_topology.jpg)
    
### <a name="deploy-through-azure-cli"></a>Distribuera via Azure CLI
Du behöver senast [Azure CLI](/cli/azure/install-az-cli2) installerat och loggat in till en Azure-konto med hjälp av [az-inloggning](/cli/azure/reference-index#az_login). Skapa en resursgrupp med [az group create](/cli/azure/group#az_group_create). I följande exempel skapas ett Resursgruppsnamn `myResourceGroup` i den `West US` plats.

```azurecli
az group create --name myResourceGroup --location westus
```

Distribuera mallen [pf-freebsd-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/pf-freebsd-setup) med [az group deployment skapa](/cli/azure/group/deployment#az_group_deployment_create). Ladda ned [azuredeploy.parameters.json](https://github.com/Azure/azure-quickstart-templates/blob/master/pf-freebsd-setup/azuredeploy.parameters.json) under samma sökväg och definiera dina egna värden för resurs, till exempel `adminPassword`, `networkPrefix`, och `domainNamePrefix`. 

```azurecli
az group deployment create --resource-group myResourceGroup --name myDeploymentName \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/pf-freebsd-setup/azuredeploy.json \
    --parameters '@azuredeploy.parameters.json' --verbose
```

Efter fem minuter får du information om `"provisioningState": "Succeeded"`. Sedan kan du ssh till klientdelens VM (NAT) eller åtkomst Nginx-webbserver i en webbläsare med den offentliga IP-adress eller FQDN för klientdelens VM (NAT). I följande exempel visas FQDN- och offentlig IP-adress som tilldelats klientdelens VM (NAT) i den `myResourceGroup` resursgrupp. 

```azurecli
az network public-ip list --resource-group myResourceGroup
```
    
## <a name="next-steps"></a>Nästa steg
Vill du ställa in dina egna NAT i Azure? Öppen källkod, kostnadsfritt men kraftfulla? Sedan är PF ett bra alternativ. Med hjälp av mallen [pf-freebsd-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/pf-freebsd-setup), behöver du bara fem minuter att ställa in en NAT-brandvägg med belastningsutjämning för resursallokering med FreeBSD s PF i Azure för web server ovanligt. 

Om du vill lära dig att erbjuda FreeBSD i Azure kan referera till [introduktion till FreeBSD på Azure](freebsd-intro-on-azure.md).

Om du vill veta mer om PF Se [FreeBSD handboken](https://www.freebsd.org/doc/handbook/firewalls-pf.html) eller [PF-Användarhandbok](https://www.freebsd.org/doc/handbook/firewalls-pf.html).
