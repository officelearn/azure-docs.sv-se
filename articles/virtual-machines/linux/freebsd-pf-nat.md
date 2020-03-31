---
title: Använda FreeBSD:s paketfilter för att skapa en brandvägg i Azure
description: Lär dig hur du distribuerar en NAT-brandvägg med FreeBSD:s PF i Azure.
author: KylieLiang
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/20/2017
ms.author: kyliel
ms.openlocfilehash: 9b78c0d93b57a3e3f4963088d0b93f121f57483c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945109"
---
# <a name="how-to-use-freebsds-packet-filter-to-create-a-secure-firewall-in-azure"></a>Så här använder du FreeBSD:s paketfilter för att skapa en säker brandvägg i Azure
Den här artikeln introducerar hur du distribuerar en NAT-brandvägg med FreeBSD:s Packer-filter via Azure Resource Manager-mallen för vanligt webbserverscenario.

## <a name="what-is-pf"></a>Vad är PF?
PF (Packet Filter, även skriven pf) är ett BSD-licensierat tillståndskänsligt paketfilter, en central programvara för brandvägg. PF har sedan utvecklats snabbt och har nu flera fördelar jämfört med andra tillgängliga brandväggar. Nat (Network Address Translation) finns i PF sedan dag ett, sedan paketschemaläggare och aktiv köhantering har integrerats i PF, genom att integrera ALTQ och göra den konfigurerbar genom PF:s konfiguration. Funktioner som pfsync och CARP för redundans och redundans, authpf för sessionsautentisering och ftp-proxy för att underlätta brandväggen för det svåra FTP-protokollet har också utökat PF. Kort sagt, PF är en kraftfull och funktionsrik brandvägg. 

## <a name="get-started"></a>Komma igång
Om du är intresserad av att konfigurera en säker brandvägg i molnet för dina webbservrar, så låt oss komma igång. Du kan också använda skripten som används i den här Azure Resource Manager-mallen för att konfigurera nätverkstopologin.
Azure Resource Manager-mallen konfigurerar en virtuell FreeBSD-dator som utför NAT /redirection med PF och två virtuella FreeBSD-datorer med Nginx-webbservern installerad och konfigurerad. Förutom att utföra NAT för de två webbservrarnas utgående trafik, avlyssnar den virtuella nat/redirection-datorn HTTP-begäranden och omdirigerar dem till de två webbservrarna på round robin-sätt. Det virtuella nätverket använder det privata ip-adressutrymmet för icke-dirigerbara 10.0.0.2/24 och du kan ändra parametrarna för mallen. Azure Resource Manager-mallen definierar också en vägtabell för hela virtuella nätverk, som är en samling enskilda vägar som används för att åsidosätta Azure-standardvägar baserat på mål-IP-adressen. 

![pf_topology](./media/freebsd-pf-nat/pf_topology.jpg)
    
### <a name="deploy-through-azure-cli"></a>Distribuera via Azure CLI
Du behöver den senaste [Azure CLI](/cli/azure/install-az-cli2) installerat och inloggad på ett Azure-konto med [az login](/cli/azure/reference-index). Skapa en resursgrupp med [az group create](/cli/azure/group). I följande exempel skapas `myResourceGroup` ett `West US` resursgruppnamn på platsen.

```azurecli
az group create --name myResourceGroup --location westus
```

Distribuera sedan mallen [pf-freebsd-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/pf-freebsd-setup) med [az-gruppdistribution skapa](/cli/azure/group/deployment). Hämta [azuredeploy.parameters.json](https://github.com/Azure/azure-quickstart-templates/blob/master/pf-freebsd-setup/azuredeploy.parameters.json) under samma sökväg och definiera `adminPassword`dina `networkPrefix`egna `domainNamePrefix`resursvärden, till exempel , och . 

```azurecli
az group deployment create --resource-group myResourceGroup --name myDeploymentName \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/pf-freebsd-setup/azuredeploy.json \
    --parameters '@azuredeploy.parameters.json' --verbose
```

Efter cirka fem minuter får du `"provisioningState": "Succeeded"`information om . Sedan kan du ssh till frontend VM (NAT) eller komma åt Nginx webbserver i en webbläsare med hjälp av den offentliga IP-adressen eller FQDN för frontend VM (NAT). I följande exempel visas FQDN och offentlig IP-adress som tilldelats `myResourceGroup` NAT (Frontend VM) i resursgruppen. 

```azurecli
az network public-ip list --resource-group myResourceGroup
```
    
## <a name="next-steps"></a>Nästa steg
Vill du konfigurera din egen NAT i Azure? Öppen källkod, gratis men kraftfull? Då pf är ett bra val. Genom att använda mallen [pf-freebsd-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/pf-freebsd-setup)behöver du bara fem minuter för att konfigurera en NAT-brandvägg med lastutjämningsutjämning av round-robin med FreeBSD:s PF i Azure för vanligt webbserverscenario. 

Om du vill lära dig erbjudandet om FreeBSD i Azure läser du [introduktionen till FreeBSD på Azure](freebsd-intro-on-azure.md).

Om du vill veta mer om PF, se [FreeBSD handbok](https://www.freebsd.org/doc/handbook/firewalls-pf.html) eller [PF-User's Guide](https://www.freebsd.org/doc/handbook/firewalls-pf.html).
