---
title: Skapa ett Linux Service Fabric-kluster i Azure | Microsoft Docs
description: "Lär dig hur du distribuerar en Linux Service Fabric-kluster till ett befintligt Azure virtuellt nätverk som använder Azure CLI."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/26/2017
ms.author: ryanwi
ms.openlocfilehash: 47e023e7240cfae3553b220ebc44c95ec96d62a7
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2017
---
# <a name="deploy-a-service-fabric-linux-cluster-into-an-azure-virtual-network"></a>Distribuera ett Service Fabric Linux-kluster till ett virtuellt Azure-nätverk
Den här kursen ingår i en serie. Du kommer lära dig hur du distribuerar en Linux Service Fabric-kluster till ett befintligt Azure virtuellt nätverk (VNET) och underordnad net med Azure CLI. När du är klar kan har du ett kluster som körs i molnet som du kan distribuera program till. För att skapa ett Windows-kluster med hjälp av PowerShell Se [skapa en säker Windows-kluster i Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md).

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Skapa ett VNET i Azure med Azure CLI
> * Skapa en säker Service Fabric-kluster i Azure med Azure CLI
> * Skydda klustret med ett X.509-certifikat
> * Ansluta till klustret med Service Fabric CLI
> * Ta bort ett kluster

I den här självstudiekursen serien lär du dig hur du:
> [!div class="checklist"]
> * Skapa en säker kluster i Azure
> * [Skala ett kluster in eller ut](/service-fabric-tutorial-scale-cluster.md)
> * [Distribuera API Management med Service Fabric](service-fabric-tutorial-deploy-api-management.md)

## <a name="prerequisites"></a>Krav
Innan du börjar den här kursen:
- Om du inte har en Azure-prenumeration kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Installera den [Service Fabric CLI](service-fabric-cli.md)
- Installera den [Azure CLI 2.0](/cli/azure/install-azure-cli)

Följande procedurer skapa ett Service Fabric-kluster med fem noder. Att beräkna kostnaden genom att köra ett Service Fabric-kluster i Azure används den [Priskalkylatorn för Azure](https://azure.microsoft.com/pricing/calculator/).

## <a name="introduction"></a>Introduktion
Den här kursen distribuerar ett kluster med fem noder i en enskild nod-typ till ett virtuellt nätverk i Azure.

Ett [Service Fabric-kluster](service-fabric-deploy-anywhere.md) är en nätverksansluten uppsättning virtuella eller fysiska datorer som dina mikrotjänster distribueras till och hanteras från. Kluster kan skalas till tusentals datorer. En dator eller virtuell dator som ingår i ett kluster kallas för en nod. Varje nod har tilldelats ett nodnamn (en sträng). Noder har egenskaper som till exempel placeringsegenskaper.

En nodtyp definierar storlek, antal och egenskaper för en uppsättning virtuella datorer i klustret. Varje definierade nodtyp har ställts in som en [virtuella datorns skaluppsättning](/azure/virtual-machine-scale-sets/), en Azure compute resursen som du använder för att distribuera och hantera en samling med virtuella datorer som en uppsättning. Varje nodtyp kan sedan skalas upp eller ned separat, har olika uppsättningar av öppna portar och kan ha olika kapacitetsdata. Nodtyper används för att definiera roller för en uppsättning noder, till exempel ”klientdel” eller ”serverdel”.  Klustret kan ha fler än en nodtyp, men den primära nodtypen måste ha minst fem datorer för produktion kluster (eller minst tre virtuella datorer för testkluster).  [Service Fabric systemtjänster](service-fabric-technical-overview.md#system-services) placeras på noder av typen primära noden.

## <a name="cluster-capacity-planning"></a>Kapacitetsplanering för kluster
Den här kursen distribuerar ett kluster med fem noder i en enskild nod-typen.  För alla Produktionsdistribution av klustret är kapacitetsplanering ett viktigt steg. Här följer några saker att tänka på som en del av den här processen.

- Antalet nod av typen ditt kluster behöver 
- Egenskaperna för varje nodtyp (till exempel storlek, primära, mot internet och antal virtuella datorer)
- Tillförlitlighet och hållbarhet egenskaper i klustret

Mer information finns i [klustret kapacitetsplaneringsöverväganden](service-fabric-cluster-capacity.md).

## <a name="sign-in-to-azure-and-select-your-subscription"></a>Logga in på Azure och välja din prenumeration
Den här guiden använder Azure CLI. När du startar en ny session, logga in på ditt Azure-konto och välja din prenumeration innan du kan köra kommandon för Azure.
 
Kör följande skript för att logga in på kontot väljer din prenumeration:

```azurecli
az login
az account set --subscription <guid>
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp
Skapa en ny resursgrupp för din distribution och ge det ett namn och en plats.

```azurecli
ResourceGroupName="sflinuxclustergroup"
Location="southcentralus"
az group create --name $ResourceGroupName --location $Location
```

## <a name="deploy-the-network-topology"></a>Distribuera nätverkets topologi
Därefter konfigurera nätverkets topologi som API Management och Service Fabric-klustret kommer att distribueras. Den [network.json] [ network-arm] Resource Manager-mall har konfigurerats för att skapa ett virtuellt nätverk (VNET) och en undernät och nätverket säkerhetsgrupp (NSG) för Service Fabric och ett undernät, och NSG för API Management . Lär dig mer om Vnet, undernät och NSG: er [här](../virtual-network/virtual-networks-overview.md).

Den [network.parameters.json] [ network-parameters-arm] parameterfilen innehåller namnen på undernät och NSG: er som Service Fabric och API-hantering distribuera till.  API Management har distribuerats i det [följande kursen](service-fabric-tutorial-deploy-api-management.md). Den här guiden behöver inte parametervärden som ska ändras. Service Fabric Resource Manager-mallar använder dessa värden.  Om värdena ändras här, måste du ändra dem i de andra Resource Manager-mallar används i den här kursen och [distribuera API Management kursen](service-fabric-tutorial-deploy-api-management.md). 

Hämta följande Resource Manager-mall och parametrar filen:
- [Network.JSON][network-arm]
- [Network.parameters.JSON][network-parameters-arm]

Använd följande skript för att distribuera Resource Manager-mall och parametern-filer för installation av nätverk:

```azurecli
az group deployment create \
    --name VnetDeployment \
    --resource-group $ResourceGroupName \
    --template-file network.json \
    --parameters @network.parameters.json
```
<a id="createvaultandcert" name="createvaultandcert_anchor"></a>
## <a name="deploy-the-service-fabric-cluster"></a>Distribuera Service Fabric-kluster
När nätverksresurser är klar för distribution, är nästa steg att distribuera ett Service Fabric-kluster till VNET i undernät och NSG: N för Service Fabric-klustret. Distribuera ett kluster till ett befintligt VNET och undernät (distribuerad tidigare i den här artikeln) kräver en Resource Manager-mall.  Mer information finns i [skapa ett kluster med hjälp av Azure Resource Manager](service-fabric-cluster-creation-via-arm.md). För den här självstudiekursen serien är mallen förkonfigurerad att använda namnen på virtuella nätverk, undernät och NSG: N som du skapade i föregående steg.  

Hämta följande Resource Manager-mall och parametrar filen:
- [linuxcluster.JSON][cluster-arm]
- [linuxcluster.parameters.JSON][cluster-parameters-arm]

Du kan använda mallen för att skapa en säker kluster.  Ett certifikat för klustret är ett X.509-certifikat som används för att skydda kommunikationen nod till nod och autentisera att klustret management slutpunkter management-klienten.  Certifikat för klustret ger också en SSL för HTTPS-hanterings-API och för Service Fabric Explorer via HTTPS. Azure Key Vault används för att hantera certifikat för Service Fabric-kluster i Azure.  När ett kluster distribueras i Azure, Azure resursprovidern ansvarar för att skapa Service Fabric-kluster hämtar certifikat från Nyckelvalvet och installerar dem på klustret virtuella datorer. 

Du kan använda ett certifikat från en certifikatutfärdare (CA) som certifikatet klustret eller för testning, skapa ett självsignerat certifikat. Certifikat för klustret måste:

- innehåller en privat nyckel.
- skapas för nyckelutbyte, som kan exporteras till en Personal Information Exchange (.pfx)-fil.
- ha ett ämnesnamn som matchar den domän som du använder för att få åtkomst till Service Fabric-klustret. Den här matchar krävs SSL för att klustrets HTTPS management slutpunkter och Service Fabric Explorer. Du kan skaffa ett SSL-certifikat från en certifikatutfärdare (CA) för det. cloudapp.azure.com domän. Du måste skaffa ett anpassat domännamn för klustret. När du begär ett certifikat från en Certifikatutfärdare måste certifikatets ämnesnamn matcha det anpassade domännamnet som du använder för klustret.

Fyll i följande tomma parametrar i den *linuxcluster.parameters.json* filen för din distribution:

|Parameter|Värde|
|---|---|
|adminPassword|Lösenordet #1234|
|adminUserName|vmadmin|
|Klusternamn|mysfcluster|

Lämna den **certificateThumbprint**, **certificateUrlValue**, och **sourceVaultValue** parametrar tomt om du vill skapa ett självsignerat certifikat.  Om du vill använda ett befintligt certifikat som tidigare har överförts till ett nyckelvalv kan fylla i de parametervärdena.

Följande skript använder den [az SA kluster skapa](/cli/azure/sf/cluster?view=azure-cli-latest#az_sf_cluster_create) kommandot och mallen för att distribuera ett nytt kluster i Azure. Cmdlet även skapar ett nytt nyckelvalv i Azure, lägger till ett nytt självsignerat certifikat till nyckelvalvet och hämtar certifikatfilen lokalt. Du kan ange ett befintligt certifikat och/eller nyckelvalv med andra parametrar för den [az SA kluster skapa](/cli/azure/sf/cluster?view=azure-cli-latest#az_sf_cluster_create) kommando.

```azurecli
Password="q6D7nN%6ck@6"
Subject="mysfcluster.southcentralus.cloudapp.azure.com"
VaultName="linuxclusterkeyvault"
az group create --name $ResourceGroupName --location $Location

az sf cluster create --resource-group $ResourceGroupName --location $Location \
   --certificate-output-folder . --certificate-password $Password --certificate-subject-name $Subject \
   --vault-name $VaultName --vault-resource-group $ResourceGroupName  \
   --template-file linuxcluster.json --parameter-file linuxcluster.parameters.json

```

## <a name="connect-to-the-secure-cluster"></a>Ansluta till det säkra klustret
Anslut till klustret med hjälp av Service Fabric-CLI `sfctl cluster select` med din nyckel.  Observera använder bara den **--Nej-verifiera** alternativ för ett självsignerat certifikat.

```azurecli
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

Kontrollera att du är ansluten och att klustret är felfri med hjälp av den `sfctl cluster health` kommando.

```azurecli
sfctl cluster health
```

## <a name="clean-up-resources"></a>Rensa resurser
De andra artiklarna i den här självstudiekursen serien använda kluster som du nyss skapade. Om du inte omedelbart flytta in i nästa artikel kanske du vill ta bort klustret för att undvika kostnader. Det enklaste sättet att ta bort klustret och alla de resurser det använder är att ta bort resursgruppen.

Logga in på Azure och välj prenumerations-ID som du vill ta bort klustret.  Du hittar prenumerations-ID genom att logga in på [Azure-portalen](http://portal.azure.com). Ta bort resursgruppen och alla klusterresurser med hjälp av den [ta bort grupp az](/cli/azure/group?view=azure-cli-latest#az_group_delete) kommando.

```azurecli
az group delete --name $ResourceGroupName
```

## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa ett VNET i Azure med Azure CLI
> * Skapa en säker Service Fabric-kluster i Azure med Azure CLI
> * Skydda klustret med ett X.509-certifikat
> * Ansluta till klustret med Service Fabric CLI
> * Ta bort ett kluster

Gå sedan till följande kursen lär dig hur du skala ditt kluster.
> [!div class="nextstepaction"]
> [Skala ett kluster](service-fabric-tutorial-scale-cluster.md)


[network-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.json
[network-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.parameters.json

[cluster-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/linuxcluster.json
[cluster-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/linuxcluster.parameters.json
