---
title: Självstudie – skala ett kluster i Azure Red Hat OpenShift | Microsoft Docs
description: Lär dig hur du skalar ett Microsoft Azure Red Hat OpenShift-kluster med Azure CLI
services: container-service
author: jimzim
ms.author: jzim
manager: jeconnoc
ms.topic: tutorial
ms.service: openshift
ms.date: 05/06/2019
ms.openlocfilehash: b25e17e7064006a1421142dfcd32997cb4426e8e
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66305970"
---
# <a name="tutorial-scale-an-azure-red-hat-openshift-cluster"></a>Självstudier: Skala ett Azure Red Hat OpenShift-kluster

Den här självstudien är del två i en serie. Du lär dig att skapa ett Microsoft Azure Red Hat OpenShift-kluster med Azure CLI, skalning och sedan ta bort den för att rensa resurser.

I del två i serien lär du dig hur du:

> [!div class="checklist"]
> * Skala ett kluster med Red Hat OpenShift

I den här självstudieserien får du lära du dig att:
> [!div class="checklist"]
> * [Skapa ett Azure Red Hat OpenShift-kluster](tutorial-create-cluster.md)
> * Skala ett Azure Red Hat OpenShift-kluster
> * [Ta bort ett Azure Red Hat OpenShift-kluster](tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du börjar den här självstudien:

* Skapa ett kluster genom att följa den [skapa ett kluster i Azure Red Hat OpenShift](tutorial-create-cluster.md) självstudien.

## <a name="step-1-sign-in-to-azure"></a>Steg 1: Logga in till Azure

Om du använder Azure CLI lokalt kan köra `az login` att logga in på Azure.

```bash
az login
```

Om du har åtkomst till flera prenumerationer kan köra `az account set -s {subscription ID}` ersätter `{subscription ID}` med den prenumeration som du vill använda.

## <a name="step-2-scale-the-cluster-with-additional-nodes"></a>Steg 2: Skala kluster med ytterligare noder

Ange variabeln klusternamn till namnet på ditt kluster från en terminal Bash:

```bash
CLUSTER_NAME=yourclustername
```

Nu ska vi skala klustret till fem noder som använder Azure CLI:

```bash
az openshift scale --resource-group $CLUSTER_NAME --name $CLUSTER_NAME --compute-count 5
```

Efter ett par minuter `az openshift scale` ska slutföras och returnerar ett JSON-dokument som innehåller information om skalade kluster.

## <a name="next-steps"></a>Nästa steg

I den här självstudiedelen lärde du dig att:

> [!div class="checklist"]
> * Skala ett Azure Red Hat OpenShift-kluster

Gå vidare till nästa kurs:
> [!div class="nextstepaction"]
> [Ta bort ett Azure Red Hat OpenShift-kluster](tutorial-delete-cluster.md)