---
title: "Skapa en Azure Service Fabric-behållarapp på Linux | Microsoft Docs"
description: "Skapa din första Linux-behållarapp på Azure Service Fabric.  Skapa en Docker-avbildning med din app, överför avbildningen till ett behållarregister och skapa och distribuera en Service Fabric-behållarapp."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/05/2017
ms.author: ryanwi
ms.openlocfilehash: 7623c5ac5cbc52e9fc77b1f944908b07ff43fbaf
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2017
---
# <a name="deploy-an-azure-service-fabric-linux-container-application-on-azure"></a>Distribuera ett Azure Service Fabric Linux-behållarprogram i Azure
Azure Service Fabric är en plattform för distribuerade system för distribution och hantering av skalbara och tillförlitliga mikrotjänster och behållare. 

Den här snabbstarten visar hur du distribuerar Linux-behållare till ett Service Fabric-kluster. När du är klar har du ett röstningsprogram som består av en frontwebbtjänst i Python och en Redis-serverdel som körs i ett Service Fabric-kluster. 

![quickstartpic][quickstartpic]

I den här snabbstarten lär du dig att:
> [!div class="checklist"]
> * Distribuera behållare till ett Azure Linux Service Fabric-kluster
> * Skala och redundansväxla behållare i Service Fabric

## <a name="prerequisite"></a>Krav
Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/en-us/free/) innan du börjar.
  
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda kommandoradsgränssnittet (CLI) lokalt måste du köra Azure CLI version 2.0.4 eller senare. Du kan ta reda på versionen genom att köra az --version. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli).

## <a name="get-application-package"></a>Hämta programpaket
För att kunna distribuera behållare till Service Fabric behöver du en uppsättning manifestfiler (programdefinitionen), som beskriver de enskilda behållarna samt programmet.

I Cloud Shell använder du Git för att klona en kopia av programdefinitionen.

```azurecli-interactive
git clone https://github.com/Azure-Samples/service-fabric-containers.git

cd service-fabric-containers/Linux/container-tutorial/Voting
```

## <a name="deploy-the-containers-to-a-service-fabric-cluster-in-azure"></a>Distribuera behållare till ett Service Fabric-kluster i Azure
Om du vill distribuera programmet till ett kluster i Azure kan du antingen använda ett eget kluster, eller använda ett partykluster.

> [!Note]
> Programmet måste distribueras till ett kluster i Azure och inte till ett Service Fabric-kluster på din lokala utvecklingsdator. 
>

Partykluster är kostnadsfria, tidsbegränsade Service Fabric-kluster som finns på Azure. De underhålls av Service Fabric-teamet. Där kan alla distribuera program och lära sig mer om plattformen. [Följ dessa instruktioner](http://aka.ms/tryservicefabric) för att få åtkomst till ett partykluster: 

Information om hur du skapar ett eget kluster finns i [Skapa ditt första Service Fabric-kluster i Azure](service-fabric-get-started-azure-cluster.md).

> [!Note]
> Frontwebbtjänsten är konfigurerad för att lyssna efter inkommande trafik på port 80. Se till att den porten är öppen i ditt kluster. Porten är öppen om du använder ett partykluster.
>

### <a name="deploy-the-application-manifests"></a>Distribuera programmanifesten 
Installera [CLI:n för Service Fabric (sfctl)](service-fabric-cli.md) i din CLI-miljö

```azurecli-interactive
pip3 install --user sfctl 
export PATH=$PATH:~/.local/bin
```
Anslut till Service Fabric-klustret i Azure med hjälp av Azure CLI. Slutpunkten är hanteringsslutpunkten för ditt kluster, till exempel `http://linh1x87d1d.westus.cloudapp.azure.com:19080`.

```azurecli-interactive
sfctl cluster select --endpoint http://linh1x87d1d.westus.cloudapp.azure.com:19080
```

Använd installationsskriptet som medföljer för att kopiera röstningsprogrammets definition till klustret, registrera programtypen och skapa en instans av programmet.

```azurecli-interactive
./install.sh
```

Öppna en webbläsare och gå till Service Fabric Explorer på http://\<my-azure-service-fabric-cluster-url>:19080/Explorer – till exempel `http://linh1x87d1d.westus.cloudapp.azure.com:19080/Explorer`. Utöka programnoden. Nu ser du att det finns en post för röstningsprogramtypen och den instans som du har skapat.

![Service Fabric Explorer][sfx]

Anslut till den behållare som körs.  Öppna en webbläsare och gå till URL för ditt kluster, till exempel `http://linh1x87d1d.westus.cloudapp.azure.com:80`. Nu ska röstningsprogrammet visas i webbläsaren.

![quickstartpic][quickstartpic]

## <a name="fail-over-a-container-in-a-cluster"></a>Redundansväxla en behållare i ett kluster
Service Fabric ser till att dina behållarinstanser flyttas automatiskt till andra noder i klustret vid ett fel. Du kan också tomma en nod på behållare och sedan flytta dem till andra noder i klustret. Det finns flera sätt att skala tjänsterna. I det här exemplet använder vi Service Fabric Explorer.

Så här redundansväxlar du behållaren på klientsidan:

1. Öppna Service Fabric Explorer i ditt kluster, till exempel `http://linh1x87d1d.westus.cloudapp.azure.com:19080/Explorer`.
2. Klicka på noden **fabric:/Voting/azurevotefront** i trädvyn och utöka partitionsnoden (som representeras av en globalt unik identifierare). Nodnamnet i trädvyn visar vilka noder som behållaren körs på för tillfället, till exempel `_nodetype_4`
3. Visa noden **Noder** i trädvyn. Klicka på ellipsknappen (tre punkter) bredvid den nod som körs i behållaren.
4. Välj **Starta om** för att starta om noden och bekräfta omstartsåtgärden. Omstarten gör att behållaren växlar över till en annan nod i klustret.

![sfxquickstartshownodetype][sfxquickstartshownodetype]

## <a name="scale-applications-and-services-in-a-cluster"></a>Skala program och tjänster i ett kluster
Service Fabric-tjänster kan enkelt skalas över ett kluster beroende på belastningen på tjänsterna. Du kan skala en tjänst genom att ändra antalet instanser som körs i klustret.

Gör så här om du vill skala frontwebbtjänsten:

1. Öppna Service Fabric Explorer i ditt kluster, till exempel `http://linh1x87d1d.westus.cloudapp.azure.com:19080`.
2. Klicka på ellipsknappen (tre punkter) bredvid noden **fabric:/Voting/azurevotefront** i trädvyn och välj **Scale Service** (Skala tjänst).

    ![containersquickstartscale][containersquickstartscale]

  Du kan nu välja att skala antalet instanser av frontwebbtjänsten.

3. Ändra antalet till **2** och klicka på **Scale Service** (Skala tjänst).
4. Klicka på noden **fabric:/Voting/azurevotefront** i trädvyn och utöka partitionsnoden (som representeras av en globalt unik identifierare).

    ![containersquickstartscaledone][containersquickstartscaledone]

    Du kan nu se att tjänsten har två instanser. I trädvyn kan du se vilka noder instanserna körs på.

Med den här enkla hanteringsåtgärden har vi dubblerat resurserna för bearbetning av användarbelastning för frontwebbtjänsten. Det är viktigt att veta att du inte behöver flera instanser av en tjänst för att den ska köras på ett tillförlitligt sätt. Om en tjänst misslyckas ser Service Fabric till att en ny tjänstinstans körs i klustret.

## <a name="clean-up"></a>Rensa
Använd avinstallationsskriptet som medföljer mallen för att ta bort programinstansen från klustret och avregistrera programtypen. Det tar lite tid att rensa instansen med det här kommandot. Kommandot install'sh bör inte köras omedelbart efter det här skriptet. 

```bash
./uninstall.sh
```

## <a name="next-steps"></a>Nästa steg
I den här snabbstarten har du lärt dig att:
> [!div class="checklist"]
> * Distribuera ett program för Linux-behållare till Azure
> * Redundansväxla en behållare i ett Service Fabric-kluster
> * Skala en behållare i ett Service Fabric-kluster

* Mer information om hur du kör [behållare i Service Fabric](service-fabric-containers-overview.md).
* Läs om Service Fabric-[applivscykeln](service-fabric-application-lifecycle.md).
* Se [kodexempel för Service Fabric-behållare](https://github.com/Azure-Samples/service-fabric-containers) på GitHub.

[sfx]: ./media/service-fabric-quickstart-containers-linux/containersquickstartappinstance.png
[quickstartpic]: ./media/service-fabric-quickstart-containers-linux/votingapp.png
[sfxquickstartshownodetype]:  ./media/service-fabric-quickstart-containers-linux/containersquickstartrestart.png
[containersquickstartscale]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscale.png
[containersquickstartscaledone]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscaledone.png
