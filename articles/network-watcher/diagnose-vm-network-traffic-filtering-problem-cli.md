---
title: Diagnostisera problem med filtreringen av nätverkstrafik på virtuella datorer – snabbstart – Azure CLI | Microsoft Docs
description: I den här snabbstarten lär du dig hur du diagnostiserar problem med filtreringen av nätverkstrafik på en virtuell dator med hjälp av funktionen Kontrollera IP-flöde i Azure Network Watcher.
services: network-watcher
documentationcenter: network-watcher
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I need to diagnose a virtual machine (VM) network traffic filter problem that prevents communication to and from a VM.
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: 8b40a4fbee1e6ee501178039e87f39d47d77fcab
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54855983"
---
# <a name="quickstart-diagnose-a-virtual-machine-network-traffic-filter-problem---azure-cli"></a>Snabbstart: Diagnostisera problem med filtreringen av nätverkstrafik på virtuella datorer – Azure CLI

I den här snabbstarten ska du distribuera en virtuell dator (VM) och kontrollera kommunikationen till en IP-adress och URL och från en IP-adress. Du lär dig också hur du fastställer orsaken till ett kommunikationsfel och hur du löser problemet.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0.28 eller senare under den här snabbstarten. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli). När du har verifierat CLI-versionen kör du `az login` för att skapa en anslutning till Azure. CLI-kommandona i den här snabbstarten är utformade att köras i ett Bash-gränssnitt.

## <a name="create-a-vm"></a>Skapa en virtuell dator

Innan du kan skapa en virtuell dator måste du skapa en resursgrupp som innehåller den virtuella datorn. Skapa en resursgrupp med [az group create](/cli/azure/group#az_group_create). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Skapa en virtuell dator med [az vm create](/cli/azure/vm#az_vm_create). Om det inte redan finns SSH-nycklar på en standardnyckelplats skapar kommandot dem. Om du vill använda en specifik uppsättning nycklar använder du alternativet `--ssh-key-value`. I följande exempel skapas en virtuell dator med namnet *myVm*:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image UbuntuLTS \
  --generate-ssh-keys
```

Det tar några minuter att skapa den virtuella datorn. Fortsätt inte med de återstående stegen förrän den virtuella datorn har skapats och CLI returnerar utdata.

## <a name="test-network-communication"></a>Testa nätverkskommunikationen

För att testa nätverkskommunikation med Network Watcher måste du först aktivera en nätverksbevakare i den region där den virtuella dator du vill testa finns i, och sedan använda Kontrollera IP-flöde i Network Watcher för att testa kommunikationen.

### <a name="enable-network-watcher"></a>Aktivera nätverksbevakare

Om du redan har aktiverat en nätverksbevakare i regionen Östra USA går du vidare till [Använda Kontrollera IP-flöde](#use-ip-flow-verify). Använd kommandot [az network watcher configure](/cli/azure/network/watcher#az-network-watcher-configure) till att skapa en nätverksbevakare i regionen Östra USA:

```azurecli-interactive
az network watcher configure \
  --resource-group NetworkWatcherRG \
  --locations eastus \
  --enabled
```

### <a name="use-ip-flow-verify"></a>Använda Kontrollera IP-flöde

När du skapar en virtuell dator tillåter och nekar Azure nätverkstrafik till och från den virtuella datorn som standard. Om du vill kan du åsidosätta standardinställningarna i Azure och tillåta eller neka andra typer av trafik. Testa om trafik tillåts eller nekas på olika mål och från en käll-IP-adress använder du kommandot [az network watcher test-ip-flow](/cli/azure/network/watcher#az-network-watcher-test-ip-flow).

Testa utgående kommunikation från den virtuella datorn till någon av IP-adresserna för www.bing.com:

```azurecli-interactive
az network watcher test-ip-flow \
  --direction outbound \
  --local 10.0.0.4:60000 \
  --protocol TCP \
  --remote 13.107.21.200:80 \
  --vm myVm \
  --nic myVmVMNic \
  --resource-group myResourceGroup \
  --out table
```

Resultatet visas efter flera sekunder och anger att åtkomsten tillåts av en säkerhetsregel med namnet **AllowInternetOutbound**.

Testa utgående kommunikation från den virtuella datorn till 172.31.0.100:

```azurecli-interactive
az network watcher test-ip-flow \
  --direction outbound \
  --local 10.0.0.4:60000 \
  --protocol TCP \
  --remote 172.31.0.100:80 \
  --vm myVm \
  --nic myVmVMNic \
  --resource-group myResourceGroup \
  --out table
```

Resultatet som returneras anger att åtkomsten nekas av en säkerhetsregel med namnet **DefaultOutboundDenyAll**.

Testa inkommande kommunikation till den virtuella datorn från 172.31.0.100:

```azurecli-interactive
az network watcher test-ip-flow \
  --direction inbound \
  --local 10.0.0.4:80 \
  --protocol TCP \
  --remote 172.31.0.100:60000 \
  --vm myVm \
  --nic myVmVMNic \
  --resource-group myResourceGroup \
  --out table
```

Resultatet som returneras anger att åtkomsten nekas på grund av en säkerhetsregel med namnet **DefaultInboundDenyAll**. Nu när du vet vilka säkerhetsregler som tillåter eller nekar trafik till eller från en virtuell dator kan du lättare avgöra hur du ska lösa problemet.

## <a name="view-details-of-a-security-rule"></a>Visa information om en säkerhetsregel

Om du vill fastställa varför reglerna i [Använda Kontrollera IP-flöde](#use-ip-flow-verify) tillåter eller förhindrar kommunikation granskar du de gällande säkerhetsreglerna för nätverksgränssnittet med kommandot [az network nic list-effective-nsg](/cli/azure/network/nic#az-network-nic-list-effective-nsg):

```azurecli-interactive
az network nic list-effective-nsg \
  --resource-group myResourceGroup \
  --name myVmVMNic
```

Returnerade utdata innehåller följande text för regeln **AllowInternetOutbound** som tillåter utgående åtkomst till www.bing.com i ett tidigare steg under [Använda Kontrollera IP-flöde](#use-ip-flow-verify):

```azurecli
{
 "access": "Allow",
 "additionalProperties": {},
 "destinationAddressPrefix": "Internet",
 "destinationAddressPrefixes": [
  "Internet"
 ],
 "destinationPortRange": "0-65535",
 "destinationPortRanges": [
  "0-65535"
 ],
 "direction": "Outbound",
 "expandedDestinationAddressPrefix": [
  "1.0.0.0/8",
  "2.0.0.0/7",
  "4.0.0.0/6",
  "8.0.0.0/7",
  "11.0.0.0/8",
  "12.0.0.0/6",
  ...
 ],
 "expandedSourceAddressPrefix": null,
 "name": "defaultSecurityRules/AllowInternetOutBound",
 "priority": 65001,
 "protocol": "All",
 "sourceAddressPrefix": "0.0.0.0/0",
 "sourceAddressPrefixes": [
  "0.0.0.0/0"
 ],
 "sourcePortRange": "0-65535",
 "sourcePortRanges": [
  "0-65535"
 ]
},
```

Du kan se i tidigare utdata att **destinationAddressPrefix** är **Internet**. Men det är oklart hur 13.107.21.200 relaterar till **Internet**. Du ser flera adressprefix listade under **expandedDestinationAddressPrefix**. Ett av prefixen i listan är **12.0.0.0/6**, vilken omfattar IP-adressintervallet 12.0.0.1–15.255.255.254. Eftersom 13.107.21.200 ligger inom det adressintervallet tillåter regeln **AllowInternetOutBound** den utgående trafiken. Dessutom finns det inga regler med högre prioritet (lägre nummer) som visas i tidigare utdata som åsidosätter den här regeln. Om du vill neka utgående kommunikation till en IP-adress kan du lägga till en säkerhetsregel med högre prioritet, som nekar utgående trafik på port 80 till IP-adressen.

När du körde kommandot `az network watcher test-ip-flow` för att testa utgående kommunikation till 172.131.0.100 i [Använda Kontrollera IP-flöde](#use-ip-flow-verify) angavs i utdata att regeln **DefaultOutboundDenyAll** nekad kommunikationen. Regeln **DefaultOutboundDenyAll** är lika med regeln **DenyAllOutBound** som visas i följande utdata från kommandot `az network nic list-effective-nsg`:

```azurecli
{
 "access": "Deny",
 "additionalProperties": {},
 "destinationAddressPrefix": "0.0.0.0/0",
 "destinationAddressPrefixes": [
  "0.0.0.0/0"
 ],
 "destinationPortRange": "0-65535",
 "destinationPortRanges": [
  "0-65535"
 ],
 "direction": "Outbound",
 "expandedDestinationAddressPrefix": null,
 "expandedSourceAddressPrefix": null,
 "name": "defaultSecurityRules/DenyAllOutBound",
 "priority": 65500,
 "protocol": "All",
 "sourceAddressPrefix": "0.0.0.0/0",
 "sourceAddressPrefixes": [
  "0.0.0.0/0"
 ],
 "sourcePortRange": "0-65535",
 "sourcePortRanges": [
  "0-65535"
 ]
}
```

Regeln listar **0.0.0.0/0** som **destinationAddressPrefix**. Regeln nekar utgående kommunikation till 172.131.0.100, eftersom adressen inte är inom **destinationAddressPrefix** för någon av de andra reglerna för utgående trafik i utdata från kommandot `az network nic list-effective-nsg`. Om du vill tillåta den utgående kommunikationen kan du lägga till en säkerhetsregel med högre prioritet, som tillåter utgående trafik på port 80 på 172.131.0.100.

När du körde kommandot `az network watcher test-ip-flow` i [Använda Kontrollera IP-flöde](#use-ip-flow-verify) för att testa inkommande kommunikation från 172.131.0.100 angavs i utdata att regeln **DefaultOutboundDenyAll** nekade kommunikationen. Regeln **DefaultInboundDenyAll** är lika med regeln **DenyAllInBound** som visas i följande utdata från kommandot `az network nic list-effective-nsg`:

```azurecli
{
 "access": "Deny",
 "additionalProperties": {},
 "destinationAddressPrefix": "0.0.0.0/0",
 "destinationAddressPrefixes": [
  "0.0.0.0/0"
 ],
 "destinationPortRange": "0-65535",
 "destinationPortRanges": [
  "0-65535"
 ],
 "direction": "Inbound",
 "expandedDestinationAddressPrefix": null,
 "expandedSourceAddressPrefix": null,
 "name": "defaultSecurityRules/DenyAllInBound",
 "priority": 65500,
 "protocol": "All",
 "sourceAddressPrefix": "0.0.0.0/0",
 "sourceAddressPrefixes": [
  "0.0.0.0/0"
 ],
 "sourcePortRange": "0-65535",
 "sourcePortRanges": [
  "0-65535"
 ]
},
```

Regeln **DenyAllInBound** används eftersom, som du ser i utdata, det inte finns någon regeln med högre prioritet i utdata från kommandot `az network nic list-effective-nsg` som tillåter port 80-inkommande till den virtuella datorn från 172.131.0.100. Om du vill tillåta den inkommande kommunikationen kan du lägga till en säkerhetsregel med högre prioritet, som tillåter inkommande trafik på port 80 från 172.131.0.100.

Kontrollerna i den här snabbstarten testade Azure-konfigurationen. Om kontrollerna returnerar förväntat resultat och du fortfarande har problem med nätverket kontrollerar du att det inte finns en brandvägg mellan den virtuella datorn och den slutpunkt som du kommunicerar med, samt att operativsystemet på den virtuella datorn inte har en brandvägg som tillåter eller nekar kommunikationen.

## <a name="clean-up-resources"></a>Rensa resurser

När resurserna inte behövs längre kan du använda [az group delete](/cli/azure/group) för att ta bort resursgruppen och alla relaterade resurser den innehåller:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en virtuell dator och diagnostiserade filter för inkommande och utgående nätverkstrafik. Du lärde dig att regler för nätverkssäkerhetsgrupper tillåter eller nekar trafik till och från en virtuell dator. Lär dig mer om [säkerhetsregler](../virtual-network/security-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) och hur du [skapar säkerhetsregler](../virtual-network/manage-network-security-group.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-security-rule).

Kommunikationen till en virtuell dator kan misslyckas även om rätt trafikfilter används på grund av routningskonfigurationen. Mer information om hur du diagnostiserar problem med VM-routning finns i [Diagnostisera problem med VM-routning](diagnose-vm-network-routing-problem-cli.md). Information om hur du diagnostiserar problem med utgående routning, svarstider och trafikfiltrering med ett verktyg finns i avsnittet om hur du [felsöker anslutningar](network-watcher-connectivity-cli.md).
