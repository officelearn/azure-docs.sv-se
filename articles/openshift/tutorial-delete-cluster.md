---
title: Självstudiekurs - Ta bort ett Azure Red Hat OpenShift-kluster
description: I den här självstudien kan du läsa om hur du tar bort ett Azure Red Hat OpenShift-kluster med Azure CLI
author: jimzim
ms.author: jzim
ms.topic: tutorial
ms.service: container-service
ms.date: 05/06/2019
ms.openlocfilehash: c335236a2b0b05f03bef1ebef37f1129a5d0352b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76278774"
---
# <a name="tutorial-delete-an-azure-red-hat-openshift-cluster"></a>Självstudiekurs: Ta bort ett Azure Red Hat OpenShift-kluster

Det här är slutet av självstudiekursen. När du är klar med testningen av exempelklustret gör du så här för att ta bort det och dess associerade resurser så att du inte debiteras för det du inte använder.

I den tredje delen i serien får du lära dig att:

> [!div class="checklist"]
> * Ta bort ett Azure Red Hat OpenShift-kluster

I den här självstudieserien får du lära du dig att:
> [!div class="checklist"]
> * [Skapa ett Azure Red Hat OpenShift-kluster](tutorial-create-cluster.md)
> * [Skala ett Azure Red Hat OpenShift-kluster](tutorial-scale-cluster.md)
> * Ta bort ett Azure Red Hat OpenShift-kluster

## <a name="prerequisites"></a>Krav

Innan du börjar den här självstudien:

* Skapa ett kluster genom att följa [självstudien Skapa ett Azure Red Hat OpenShift-kluster.](tutorial-create-cluster.md)

## <a name="step-1-sign-in-to-azure"></a>Steg 1: Logga in på Azure

Om du kör Azure CLI lokalt `az login` kan du köra för att logga in på Azure.

```bash
az login
```

Om du har åtkomst till `az account set -s {subscription ID}` flera `{subscription ID}` prenumerationer kör du ersättning med den prenumeration du vill använda.

## <a name="step-2-delete-the-cluster"></a>Steg 2: Ta bort klustret

Öppna en Bash-terminal och ange variabeln CLUSTER_NAME till namnet på klustret:

```bash
CLUSTER_NAME=yourclustername
```

Ta nu bort klustret:

```bash
az openshift delete --resource-group $CLUSTER_NAME --name $CLUSTER_NAME
```

Du tillfrågas om du vill ta bort klustret. När du `y`har bekräftat med det tar det flera minuter att ta bort klustret. När kommandot är klart tas hela resursgruppen och alla resurser i det, inklusive klustret, bort.

## <a name="deleting-a-cluster-using-the-azure-portal"></a>Ta bort ett kluster med Azure-portalen

Alternativt kan du ta bort den associerade resursgruppen i klustret via Azure-portalen online. Namnet på resursgruppen är detsamma som klusternamnet.

För närvarande `Microsoft.ContainerService/openShiftManagedClusters` är resursen som skapas när du skapar klustret dold i Azure-portalen. Kontrollera `Resource group` om du `Show hidden types` vill visa resursgruppen i vyn.

![Skärmbild av kryssrutan dold text](./media/aro-portal-hidden-type.png)

Om du tar bort resursgruppen tas alla relaterade resurser som skapas när du skapar ett Azure Red Hat OpenShift-kluster bort.

## <a name="next-steps"></a>Nästa steg

I den här självstudiedelen lärde du dig att:
> [!div class="checklist"]
> * Ta bort ett Azure Red Hat OpenShift-kluster

Läs mer om hur du använder OpenShift med den officiella [Red Hat OpenShift-dokumentationen](https://docs.openshift.com/aro/welcome/index.html)
