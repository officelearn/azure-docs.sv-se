---
title: Affärskontinuitet och haveriberedskap
services: azure-dev-spaces
author: lisaguthrie
ms.author: lcozzens
ms.date: 01/28/2019
ms.topic: conceptual
description: Lär dig att använda Azure Dev Spaces och Azure Kubernetes Services för att ge affärskontinuitet och förbereda för haveriberedskap
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, behållare, Helm, servicenät, routning av tjänstnät, kubectl, k8s '
manager: gwallace
ms.openlocfilehash: 37c0048bfa7e72b25eb56603fc027045eba25cea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78295835"
---
# <a name="business-continuity-and-disaster-recovery-in-azure-dev-spaces"></a>Kontinuitet i verksamheten och haveriberedskap i Azure Dev Spaces

## <a name="review-disaster-recovery-guidance-for-azure-kubernetes-service-aks"></a>Granska katastrofåterställningsvägledning för Azure Kubernetes Service (AKS)

Azure Dev Spaces är en funktion i Azure Kubernetes Service (AKS). Du bör vara medveten om riktlinjer för haveriberedskap i AKS och överväga om de gäller för AKS-kluster som du använder för dev spaces. Mer information finns i [Metodtips för affärskontinuitet och haveriberedskap i Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region)

## <a name="enable-dev-spaces-on-aks-clusters-in-different-regions"></a>Aktivera dev spaces i AKS-kluster i olika regioner

Om du aktiverar dev spaces i AKS-kluster i olika regioner kan du återuppta med dev spaces direkt efter ett Azure-regionfel.

Allmän information om distributioner med flera regioner av AKS finns i [Planera för distribution med flera regioner](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region#plan-for-multiregion-deployment)

### <a name="enable-dev-spaces-via-the-azure-portal"></a>Aktivera dev spaces via Azure-portalen

Välj menyalternativet **Dev Spaces** under inställningarna för varje kluster i Azure-portalen. Välj sedan alternativet för att aktivera Dev Spaces och spara.

![Aktivera Dev Spaces via Azure Portal](../media/common/enable-dev-spaces.jpg)

Upprepa den här processen för varje kluster.

### <a name="enable-dev-spaces-via-the-azure-cli"></a>Aktivera dev spaces via Azure CLI

Du kan också aktivera Dev Spaces på kommandoraden:

```azurecli
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

## <a name="deploy-your-teams-baseline-to-each-cluster"></a>Distribuera teamets baslinje till varje kluster

När du arbetar med Dev Spaces distribuerar du vanligtvis hela programmet till ett överordnat utvecklingsutrymme i Kubernetes-klustret. Som standard `default` används utrymmet. Den första distributionen omfattar alla tjänster samt externa resurser som dessa tjänster är beroende av, till exempel databaser eller köer. Detta kallas *baslinjen*. När du har ställt in en baslinje i det överordnade utvecklingsutrymmet itererar du på och felsöker enskilda tjänster i underordnade utvecklingsutrymmen.

Du bör distribuera de senaste versionerna av baslinjeuppsättningen av tjänster till kluster i flera regioner. Genom att uppdatera originalplanens tjänster på det här sättet kan du fortsätta att använda dev spaces om det finns ett Azure-regionfel. Om du till exempel distribuerar originalplanen via en CI/CD-pipeline ändrar du pipelinen så att den distribueras till flera kluster i olika regioner.

## <a name="select-the-correct-aks-cluster-to-use-for-dev-spaces"></a>Välj rätt AKS-kluster som ska användas för Dev Spaces

När du har konfigurerat ett säkerhetskopieringskluster som kör teamets baslinje på rätt sätt kan du snabbt växla över till säkerhetskopieringsklustret när som helst. Sedan kan du köra de enskilda tjänster som du arbetar med i underordnade utvecklingsutrymmen.

Välj ett annat kluster med följande CLI-kommando:

```azurecli
az aks use-dev-spaces -g <new resource group name> -n <new cluster name>
```

Du kan lista tillgängliga utvecklingsutrymmen i det nya klustret med följande kommando:

```cmd
azds space list
```

Du kan skapa ett nytt utvecklingsutrymme att arbeta i eller välja ett befintligt utvecklingsutrymme med följande kommando:

```cmd
azds space select -n <space name>
```

När du har kört dessa kommandon används det valda klustret och utvecklingsutrymmet för efterföljande CLI-åtgärder och för felsökning av projekt med hjälp av Visual Studio-kodtillägget för Azure Dev Spaces.

Om du använder Visual Studio kan du växla klustret som används av ett befintligt projekt genom följande steg:

1. Öppna projektet i Visual Studio.
1. Högerklicka på projektnamnet i Lösningsutforskaren och klicka på **Egenskaper**
1. Klicka på **Felsök i** den vänstra rutan
1. På sidan Felsökningsegenskaper klickar du på listrutan **Profil** och väljer **Azure Dev Spaces**.
1. Klicka på knappen **Ändra.**
1. I dialogrutan som visas väljer du det AKS-kluster som du vill använda. Om du vill kan du välja ett annat utvecklingsutrymme att arbeta i eller skapa ett nytt utvecklingsutrymme genom att välja lämpligt alternativ i listrutan **Utrymme.**

När du har valt rätt kluster och utrymme kan du trycka på F5 för att köra tjänsten i Dev Spaces.

Upprepa dessa steg för alla andra projekt som konfigurerats för att använda det ursprungliga klustret.

## <a name="access-a-service-on-a-backup-cluster"></a>Komma åt en tjänst i ett säkerhetskopieringskluster

Om du har konfigurerat tjänsten så att den använder ett offentligt DNS-namn har tjänsten en annan URL om du kör den i ett säkerhetskopieringskluster. Offentliga DNS-namn är `<space name>.s.<root space name>.<service name>.<cluster GUID>.<region>.azds.io`alltid i formatet . Om du växlar till ett annat kluster ändras klustrets GUID och eventuellt regionen.

Dev Spaces visar alltid rätt URL `azds up`för tjänsten när du kör eller i utdatafönstret i Visual Studio under **Azure Dev Spaces**.

Du kan också hitta WEBBADRESSEN genom att köra kommandot: `azds list-uris`
```
$ azds list-uris
Uri                                                     Status
------------------------------------------------------  ---------
http://default.mywebapi.d05afe7e006a4fddb73c.eus.azds.io/  Available
```

Använd den här URL:en när du öppnar tjänsten.
