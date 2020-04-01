---
title: Självstudiekurs - Skala ett Azure Red Hat OpenShift-kluster
description: Lär dig hur du skalar ett Microsoft Azure Red Hat OpenShift-kluster med Hjälp av Azure CLI
author: jimzim
ms.author: jzim
ms.topic: tutorial
ms.service: container-service
ms.date: 05/06/2019
ms.openlocfilehash: c6334aa20b543dfbf87fedcfe45d54bbcf7a219a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79477025"
---
# <a name="tutorial-scale-an-azure-red-hat-openshift-cluster"></a>Självstudiekurs: Skala ett Azure Red Hat OpenShift-kluster

Den här självstudien är del två i en serie. Du får lära dig hur du skapar ett Microsoft Azure Red Hat OpenShift-kluster med Azure CLI, skala det och sedan ta bort det för att rensa resurser.

I del två i serien lär du dig hur du:

> [!div class="checklist"]
> * Skala ett OpenShift-kluster med röd hatt

I den här självstudieserien får du lära du dig att:
> [!div class="checklist"]
> * [Skapa ett Azure Red Hat OpenShift-kluster](tutorial-create-cluster.md)
> * Skala ett Azure Red Hat OpenShift-kluster
> * [Ta bort ett Azure Red Hat OpenShift-kluster](tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Krav

Innan du börjar den här självstudien:

* Skapa ett kluster genom att följa [självstudien Skapa ett Azure Red Hat OpenShift-kluster.](tutorial-create-cluster.md)

## <a name="step-1-sign-in-to-azure"></a>Steg 1: Logga in på Azure

Om du kör Azure CLI lokalt `az login` kan du köra för att logga in på Azure.

```azurecli
az login
```

Om du har åtkomst till `az account set -s {subscription ID}` flera `{subscription ID}` prenumerationer kör du ersättning med den prenumeration du vill använda.

## <a name="step-2-scale-the-cluster-with-additional-nodes"></a>Steg 2: Skala klustret med ytterligare noder

Från en Bash-terminal ställer du in variabeln CLUSTER_NAME till namnet på klustret:

```bash
CLUSTER_NAME=yourclustername
```

Nu ska vi skala klustret till fem noder med hjälp av Azure CLI:

```azurecli
az openshift scale --resource-group $CLUSTER_NAME --name $CLUSTER_NAME --compute-count 5
```

Efter några minuter `az openshift scale` slutförs och returneras ett JSON-dokument som innehåller den skalade klusterinformationen.

## <a name="next-steps"></a>Nästa steg

I den här självstudiedelen lärde du dig att:

> [!div class="checklist"]
> * Skala ett Azure Red Hat OpenShift-kluster

Gå vidare till nästa kurs:
> [!div class="nextstepaction"]
> [Ta bort ett Azure Red Hat OpenShift-kluster](tutorial-delete-cluster.md)
