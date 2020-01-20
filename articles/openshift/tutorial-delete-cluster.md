---
title: Självstudie – ta bort ett Azure Red Hat OpenShift-kluster
description: I den här självstudien får du lära dig hur du tar bort ett Azure Red Hat OpenShift-kluster med Azure CLI
author: jimzim
ms.author: jzim
ms.topic: tutorial
ms.service: container-service
ms.date: 05/06/2019
ms.openlocfilehash: c335236a2b0b05f03bef1ebef37f1129a5d0352b
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278774"
---
# <a name="tutorial-delete-an-azure-red-hat-openshift-cluster"></a>Självstudie: ta bort ett Azure Red Hat OpenShift-kluster

Det här är slutet av självstudiekursen. När du är klar med att testa exempel klustret kan du ta bort det och dess associerade resurser så att du inte debiteras för det du använder.

I den tredje delen i serien får du lära dig hur du:

> [!div class="checklist"]
> * Ta bort ett Azure Red Hat OpenShift-kluster

I den här självstudieserien får du lära du dig att:
> [!div class="checklist"]
> * [Skapa ett Azure Red Hat OpenShift-kluster](tutorial-create-cluster.md)
> * [Skala ett Azure Red Hat OpenShift-kluster](tutorial-scale-cluster.md)
> * Ta bort ett Azure Red Hat OpenShift-kluster

## <a name="prerequisites"></a>Krav

Innan du börjar den här självstudien:

* Skapa ett kluster genom att följa själv studie kursen [skapa en Azure Red Hat OpenShift-kluster](tutorial-create-cluster.md) .

## <a name="step-1-sign-in-to-azure"></a>Steg 1: Logga in på Azure

Om du kör Azure CLI lokalt kan du köra `az login` för att logga in på Azure.

```bash
az login
```

Om du har åtkomst till flera prenumerationer kör du `az account set -s {subscription ID}` ersätter `{subscription ID}` med den prenumeration som du vill använda.

## <a name="step-2-delete-the-cluster"></a>Steg 2: ta bort klustret

Öppna en bash-Terminal och ange variabeln CLUSTER_NAME till namnet på klustret:

```bash
CLUSTER_NAME=yourclustername
```

Ta nu bort klustret:

```bash
az openshift delete --resource-group $CLUSTER_NAME --name $CLUSTER_NAME
```

Du får en fråga om du vill ta bort klustret. När du har bekräftat med `y`tar det flera minuter att ta bort klustret. När kommandot har slutförts tas hela resurs gruppen och alla resurser i den, inklusive klustret, bort.

## <a name="deleting-a-cluster-using-the-azure-portal"></a>Ta bort ett kluster med hjälp av Azure Portal

Alternativt kan du ta bort den associerade resurs gruppen för klustret via Azure Portal online. Namnet på resurs gruppen är detsamma som kluster namnet.

För närvarande är `Microsoft.ContainerService/openShiftManagedClusters` resursen som skapas när du skapar klustret dold i Azure Portal. I vyn `Resource group` kontrollerar du `Show hidden types` för att Visa resurs gruppen.

![Skärm bild av kryss rutan dold typ](./media/aro-portal-hidden-type.png)

Om du tar bort resurs gruppen raderas alla relaterade resurser som skapas när du skapar ett Azure Red Hat OpenShift-kluster.

## <a name="next-steps"></a>Nästa steg

I den här självstudiedelen lärde du dig att:
> [!div class="checklist"]
> * Ta bort ett Azure Red Hat OpenShift-kluster

Lär dig mer om att använda OpenShift med den officiella [Red Hat OpenShift-dokumentationen](https://docs.openshift.com/aro/welcome/index.html)
