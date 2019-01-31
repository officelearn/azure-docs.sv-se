---
title: Företag affärskontinuitet och haveriberedskap återställning i Azure Dev blanksteg | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: lisaguthrie
ms.author: lcozzens
ms.date: 01/28/2019
ms.topic: article
description: Snabb Kubernetes-utveckling med containrar och mikrotjänster i Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
manager: jeconnoc
ms.openlocfilehash: 877d49a49333d70ac7660900e49e7c588f52756c
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55451571"
---
# <a name="business-continuity-and-disaster-recovery-in-azure-dev-spaces"></a>Företag affärskontinuitet och haveriberedskap återställning i Azure Dev blanksteg

## <a name="review-disaster-recovery-guidance-for-azure-kubernetes-service-aks"></a>Granska vägledning om haveriberedskap för Azure Kubernetes Service (AKS)

Azure Dev blanksteg är en funktion i Azure Kubernetes Service (AKS). Du bör vara medveten om riktlinjer för haveriberedskap i AKS och överväga om de tillämpas på AKS-kluster som du använder för Dev blanksteg. Mer information hittar [bästa praxis för företag affärskontinuitet och haveriberedskap i Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region)

## <a name="enable-dev-spaces-on-aks-clusters-in-different-regions"></a>Aktivera Dev blanksteg i AKS-kluster i olika regioner

Aktivera Dev blanksteg i AKS-kluster i olika regioner kan du fortsätta att använda Dev blanksteg direkt efter ett fel i Azure-region.

Allmän information om distributioner över flera regioner med AKS finns i [planera för distribution i flera regioner](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region#plan-for-multi-region-deployment)

Information om hur du distribuerar ett AKS-kluster som är kompatibel med Azure Dev blanksteg finns i [skapa ett Kubernetes-kluster med Azure Cloud Shell](https://docs.microsoft.com/azure/dev-spaces/how-to/create-cluster-cloud-shell)

### <a name="enable-dev-spaces-via-the-azure-portal"></a>Aktivera Dev blanksteg via Azure portal

Klicka på den **Dev blanksteg** navigering objekt under egenskaperna för varje kluster i Azure-portalen. Välj sedan alternativet för att aktivera Dev blanksteg.

![Aktivera Dev blanksteg via Azure-portalen](../media/common/enable-dev-spaces.jpg)

Upprepa proceduren för varje kluster.

### <a name="enable-dev-spaces-via-the-azure-cli"></a>Aktivera Dev blanksteg via Azure CLI

Du kan också aktivera Dev blanksteg på kommandoraden:

```cmd
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

## <a name="deploy-your-teams-baseline-to-each-cluster"></a>Distribuera din grupps baslinjen till varje kluster

När du arbetar med utveckling blanksteg, distribuera hela tillämpningen till överordnade dev kan vanligtvis på ett Kubernetes-kluster. Som standard den `default` utrymme används. Den första distributionen omfattar alla tjänster, samt de externa resurserna som dessa tjänster är beroende av, till exempel databaser eller köer. Detta kallas den *baslinje*. När du har konfigurerat en baslinje i överordnade dev utrymme du iterera och Felsök enskilda tjänster i underordnade dev blanksteg.

Du bör distribuera de senaste versionerna av din baslinje har ställts in av tjänster till ett kluster i flera regioner. Uppdatera dina baslinje-tjänster på detta sätt säkerställer du att du kan fortsätta att använda Dev blanksteg om det uppstår ett fel i Azure-region. Om du distribuerar din baslinje via en CI/CD-pipeline, ändra till exempel pipelinen så att distribueras till flera kluster i olika regioner.

## <a name="select-the-correct-aks-cluster-to-use-for-dev-spaces"></a>Välj rätt AKS-kluster för utveckling blanksteg

När du har korrekt konfigurerat ett säkerhetskopiering kluster som kör din grupps baslinje, kan du snabbt växla till säkerhetskopiering klustret när som helst. Du kan sedan köra enskilda tjänster som du arbetar med i Dev blanksteg.

Välj ett annat kluster med följande CLI-kommando:

```cmd
az aks use-dev-spaces -g <new resource group name> -n <new cluster name>
```

Du kan ange adressutrymmen tillgängliga utveckling på det nya klustret med följande kommando:

```cmd
azds space list
```

Du kan skapa ett nytt dev utrymme att arbeta i eller välj en befintlig dev diskutrymme, med följande kommando:

```cmd
azds space select -n <space name>
```

När du har kört dessa kommandon används valda klustret och dev utrymme för efterföljande CLI-åtgärder och för felsökning projekt med hjälp av Visual Studio Code-tillägg för Azure Dev blanksteg.

Om du använder Visual Studio kan växla du klustret används av ett befintligt projekt genom följande steg:

1. Öppna projektet i Visual Studio.
1. Högerklicka på projektnamnet i Solution Explorer och klicka på **egenskaper**
1. I den vänstra rutan klickar du på **felsöka**
1. På sidan Debug egenskaper klickar du på den **profil** nedrullningsbara listan och välj **Azure Dev blanksteg**.
1. Klicka på den **ändra** knappen.
1. I dialogrutan som visas, väljer du det AKS-kluster som du vill använda. Om du vill välja en annan dev utrymme att arbeta i eller skapa ett nytt dev-utrymme genom att välja lämpligt alternativ från den **utrymme** listrutan.

När du har valt rätt klustret och utrymme, kan du trycka på F5 för att köra tjänsten i Dev blanksteg.

Upprepa dessa steg för andra projekt som konfigurerats för att använda det ursprungliga klustret.

## <a name="access-a-service-on-a-backup-cluster"></a>Åtkomst till en tjänst i ett kluster med säkerhetskopiering

Om du har konfigurerat din tjänst om du vill använda ett offentligt DNS-namn, kommer tjänsten har en annan URL Om du kör den i ett backup-kluster. Offentliga DNS-namn är alltid i formatet `<space name>.s.<service name>.<cluster GUID>.<region>.aksapp.io`. Om du växlar till ett annat kluster, kluster GUID och eventuellt regionen kommer att ändras.

Dev blanksteg visar alltid rätt URL för tjänsten när du kör `azds up`, eller i utdatafönstret i Visual Studio under **Azure Dev blanksteg**.

Du kan också hitta URL: en genom att köra den `azds list-uris` kommando:
```
$ azds list-uris
Uri                                                     Status
------------------------------------------------------  ---------
http://mywebapi.d05afe7e006a4fddb73c.eastus.aksapp.io/  Available
```

Använd den här URL: en vid åtkomst till tjänsten.
