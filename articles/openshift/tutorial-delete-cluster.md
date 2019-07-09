---
title: Självstudie – ta bort ett kluster i Azure Red Hat OpenShift | Microsoft Docs
description: I de här självstudierna lär du dig hur du tar bort ett Azure Red Hat OpenShift-kluster med Azure CLI
services: container-service
author: jimzim
ms.author: jzim
manager: jeconnoc
ms.topic: tutorial
ms.service: container-service
ms.date: 05/06/2019
ms.openlocfilehash: 0ad70f4c3681705377a350fee8b02a55c526f26c
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67669341"
---
# <a name="tutorial-delete-an-azure-red-hat-openshift-cluster"></a>Självstudier: Ta bort ett kluster i Azure Red Hat OpenShift

Det här är slutet av självstudiekursen. När du är klar med att testa exemplet klustret, är här att ta bort den och dess kopplade resurser så att du inte betalar för vad du inte använder.

I den tredje delen i serien får du lära dig att:

> [!div class="checklist"]
> * Ta bort ett kluster i Azure Red Hat OpenShift

I den här självstudieserien får du lära du dig att:
> [!div class="checklist"]
> * [Skapa ett Azure Red Hat OpenShift-kluster](tutorial-create-cluster.md)
> * [Skala ett Azure Red Hat OpenShift-kluster](tutorial-scale-cluster.md)
> * Ta bort ett kluster i Azure Red Hat OpenShift

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar den här självstudien:

* Skapa ett kluster genom att följa den [skapa ett kluster i Azure Red Hat OpenShift](tutorial-create-cluster.md) självstudien.

## <a name="step-1-sign-in-to-azure"></a>Steg 1: Logga in på Azure

Om du använder Azure CLI lokalt kan köra `az login` att logga in på Azure.

```bash
az login
```

Om du har åtkomst till flera prenumerationer kan köra `az account set -s {subscription ID}` ersätter `{subscription ID}` med den prenumeration som du vill använda.

## <a name="step-2-delete-the-cluster"></a>Steg 2: Ta bort klustret

Öppna en Bash-terminal och ange variabeln klusternamn till namnet på klustret:

```bash
CLUSTER_NAME=yourclustername
```

Nu ska du ta bort klustret:

```bash
az openshift delete --resource-group $CLUSTER_NAME --name $CLUSTER_NAME
```

Du uppmanas om du vill ta bort klustret. När du har bekräftat med `y`, det tar flera minuter att ta bort klustret. När kommandot har slutförts tas hela resursgruppen och alla resurser, inklusive klustret bort.

## <a name="deleting-a-cluster-using-the-azure-portal"></a>Tar bort ett kluster med Azure portal

Alternativt kan du ta bort den associerade resursgruppen på klustret via online Azure-portalen. Namnet på resursgruppen är samma som klusternamnet.

För närvarande den `Microsoft.ContainerService/openShiftManagedClusters` resurs som skapas när du skapar klustret är dold i Azure-portalen. I den `Resource group` vyn, kontrollera `Show hidden types` att visa resursgruppen.

![Skärmbild av kryssrutan dolda typ](./media/aro-portal-hidden-type.png)

Ta bort resursgruppen raderas alla relaterade resurser som skapas när du skapar ett Azure Red Hat OpenShift-kluster.

## <a name="next-steps"></a>Nästa steg

I den här självstudiedelen lärde du dig att:
> [!div class="checklist"]
> * Ta bort ett kluster i Azure Red Hat OpenShift

Läs mer om hur du använder OpenShift med officiellt [Red Hat OpenShift-dokumentation](https://docs.openshift.com/aro/welcome/index.html)