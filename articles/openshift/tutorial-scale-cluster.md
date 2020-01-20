---
title: Självstudie – skala ett kluster med ett rött hat OpenShift-kluster
description: Lär dig hur du skalar ett Microsoft Azure Red Hat OpenShift-kluster med Azure CLI
author: jimzim
ms.author: jzim
ms.topic: tutorial
ms.service: container-service
ms.date: 05/06/2019
ms.openlocfilehash: bf9172f0c84834c951446520ff0bfcc3ef756c9c
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278309"
---
# <a name="tutorial-scale-an-azure-red-hat-openshift-cluster"></a>Självstudie: skala ett Azure Red Hat OpenShift-kluster

Den här självstudien är del två i en serie. Du lär dig hur du skapar ett Microsoft Azure Red Hat OpenShift-kluster med hjälp av Azure CLI, skalar det och sedan tar bort det för att rensa resurser.

I del två i serien lär du dig hur du:

> [!div class="checklist"]
> * Skala ett rött hat OpenShift-kluster

I den här självstudieserien får du lära du dig att:
> [!div class="checklist"]
> * [Skapa ett Azure Red Hat OpenShift-kluster](tutorial-create-cluster.md)
> * Skala ett kluster med ett rött hat OpenShift-kluster
> * [Ta bort ett Azure Red Hat OpenShift-kluster](tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Krav

Innan du börjar den här självstudien:

* Skapa ett kluster genom att följa själv studie kursen [skapa en Azure Red Hat OpenShift-kluster](tutorial-create-cluster.md) .

## <a name="step-1-sign-in-to-azure"></a>Steg 1: Logga in på Azure

Om du kör Azure CLI lokalt kan du köra `az login` för att logga in på Azure.

```bash
az login
```

Om du har åtkomst till flera prenumerationer kör du `az account set -s {subscription ID}` ersätter `{subscription ID}` med den prenumeration som du vill använda.

## <a name="step-2-scale-the-cluster-with-additional-nodes"></a>Steg 2: skala klustret med ytterligare noder

Från en bash-Terminal ställer du in variabeln CLUSTER_NAME till namnet på klustret:

```bash
CLUSTER_NAME=yourclustername
```

Nu ska vi skala klustret till fem noder med Azure CLI:

```bash
az openshift scale --resource-group $CLUSTER_NAME --name $CLUSTER_NAME --compute-count 5
```

Efter några minuter kommer `az openshift scale` att slutföras och returnera ett JSON-dokument som innehåller den skalade kluster informationen.

## <a name="next-steps"></a>Nästa steg

I den här självstudiedelen lärde du dig att:

> [!div class="checklist"]
> * Skala ett kluster med ett rött hat OpenShift-kluster

Gå vidare till nästa kurs:
> [!div class="nextstepaction"]
> [Ta bort ett Azure Red Hat OpenShift-kluster](tutorial-delete-cluster.md)
