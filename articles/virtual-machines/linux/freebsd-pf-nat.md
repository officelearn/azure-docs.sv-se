---
title: Använd Freebsds paketfilter för att skapa en brandvägg i Azure | Microsoft Docs
description: Lär dig hur du distribuerar en NAT-brandvägg med Freebsd's PF i Azure.
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
ms.openlocfilehash: 42312522381529075f544a6c277f66a4ef1982d2
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
ms.locfileid: "30904261"
---
# <a name="how-to-use-freebsds-packet-filter-to-create-a-secure-firewall-in-azure"></a>Hur du använder Freebsds paketfilter för att skapa en säker brandvägg i Azure
Den här artikeln beskriver hur du distribuerar en NAT-brandvägg med Freebsds förpackaren Filter via Azure Resource Manager-mall för web server ovanligt.

## <a name="what-is-pf"></a>Vad är PF?
PF (paketfilter, också skrivs pf) är ett BSD licensierade tillståndskänslig paketfilter, en central del av programvaran för firewalling. PF sedan har utvecklats snabbt och nu har flera fördelar jämfört med andra tillgängliga brandväggar. NAT (Network Address Translation) är i PF ända sedan paketschemaläggaren och aktiva köhantering har integrerats i PF, genom att integrera ALTQ och göra den konfigureras via PFS konfiguration. Funktioner som pfsync och KARP för växling vid fel och redundans, authpf för sessionsautentisering och ftp-proxy för att underlätta firewalling svårt FTP-protokollet har också utökat PF. Kort sagt: PF är en kraftfull och funktionsrika brandvägg. 

## <a name="get-started"></a>Kom igång
Om du är intresserad av att skapa en säker brandvägg i molnet för webbservrar, nu sätter vi igång. Du kan också använda de skript som används i den här Azure Resource Manager-mallen för att konfigurera din nätverkstopologin.
Azure Resource Manager-mallen ställa in en FreeBSD virtuell dator som utför NAT /redirection med PF och två FreeBSD-virtuella datorer med webbservern Nginx installeras och konfigureras. Förutom att utföra NAT för de två servrarna utgång webbtrafik virtuella NAT-omdirigering spärras HTTP-begäranden och omdirigerar dem till två webbservrar på resursallokering sätt. VNet använder den privata icke-dirigerbara IP-adressutrymme 10.0.0.2/24 och du kan ändra parametrarna för mallen. Azure Resource Manager-mall definierar också en routningstabell för hela VNet, som är en samling individuella vägare som används för att åsidosätta Azure standardvägar baserat på mål-IP-adress. 

![pf_topology](./media/freebsd-pf-nat/pf_topology.jpg)
    
### <a name="deploy-through-azure-cli"></a>Distribuera via Azure CLI
Du behöver senast [Azure CLI 2.0](/cli/azure/install-az-cli2) installerad och inloggad till en Azure-konto med hjälp av [az inloggningen](/cli/azure/reference-index#az_login). Skapa en resursgrupp med [az group create](/cli/azure/group#az_group_create). I följande exempel skapas ett Resursgruppsnamn `myResourceGroup` i den `West US` plats.

```azurecli
az group create --name myResourceGroup --location westus
```

Distribuera mallen [pf-freebsd-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/pf-freebsd-setup) med [az distribution skapa](/cli/azure/group/deployment#az_group_deployment_create). Hämta [azuredeploy.parameters.json](https://github.com/Azure/azure-quickstart-templates/blob/master/pf-freebsd-setup/azuredeploy.parameters.json) under samma sökväg och definiera en egen resurs-värden som `adminPassword`, `networkPrefix`, och `domainNamePrefix`. 

```azurecli
az group deployment create --resource-group myResourceGroup --name myDeploymentName \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/pf-freebsd-setup/azuredeploy.json \
    --parameters '@azuredeploy.parameters.json' --verbose
```

Efter cirka fem minuter visas information för `"provisioningState": "Succeeded"`. Sedan kan du ssh till klientdel VM (NAT) eller åtkomst Nginx webbserver i en webbläsare som använder offentliga IP-adress eller FQDN för klientdel VM (NAT). I följande exempel visar FQDN och offentliga IP-adress som tilldelats klientdel VM (NAT) i den `myResourceGroup` resursgruppen. 

```azurecli
az network public-ip list --resource-group myResourceGroup
```
    
## <a name="next-steps"></a>Nästa steg
Vill du konfigurera din egen NAT i Azure? Öppna datakällan, kostnadsfritt men kraftfulla? Sedan är PF ett bra alternativ. Med hjälp av mallen [pf-freebsd-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/pf-freebsd-setup), behöver du bara fem minuter har PF i Azure för web server ovanligt att konfigurera en NAT-brandvägg med belastningsutjämning för resursallokering med FreeBSD. 

Om du vill lära dig att erbjuda FreeBSD i Azure finns [introduktion till FreeBSD på Azure](freebsd-intro-on-azure.md).

Om du vill veta mer om PF avser [FreeBSD handboken](https://www.freebsd.org/doc/handbook/firewalls-pf.html) eller [PF-Användarhandbok](https://www.freebsd.org/doc/handbook/firewalls-pf.html).
