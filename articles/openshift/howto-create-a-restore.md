---
title: Skapa en Azure Red Hat OpenShift 4-kluster program återställning med Velero
description: Lär dig hur du skapar en återställning av dina Azure Red Hat OpenShift-kluster program med Velero
ms.service: container-service
ms.topic: article
ms.date: 06/22/2020
author: troy0820
ms.author: b-trconn
keywords: Aro, OpenShift, AZ Aro, Red Hat, CLI
ms.custom: mvc
ms.openlocfilehash: 0cd6797bcdfadca807e25f8b3decf34bd553fc56
ms.sourcegitcommit: 4feb198becb7a6ff9e6b42be9185e07539022f17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2020
ms.locfileid: "89470059"
---
# <a name="create-an-azure-red-hat-openshift-4-cluster-application-restore"></a>Skapa en Azure Red Hat OpenShift 4-kluster program återställning

I den här artikeln förbereder du din miljö för att skapa en Azure Red Hat OpenShift 4-kluster program återställning. Du lär dig följande:

> [!div class="checklist"]
> * Konfigurera förutsättningarna och installera nödvändiga verktyg
> * Skapa en Azure Red Hat OpenShift 4-program återställning

Om du väljer att installera och använda CLI lokalt kräver den här självstudien att du kör Azure CLI-version 2.6.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="before-you-begin"></a>Innan du börjar

### <a name="create-an-azure-red-hat-openshift-4-application-backup"></a>Skapa en Azure Red Hat OpenShift 4 program säkerhets kopiering

Om du vill skapa en Azure Red Hat OpenShift 4-programsäkerhets kopiering, se [skapa en Azure Red Hat OpenShift 4-säkerhetskopiering](./howto-create-a-backup.md)

## <a name="restore-an-azure-red-hat-openshift-4-application"></a>Återställa ett Azure Red Hat OpenShift 4-program

Med de här stegen kan du återställa ett program som tidigare har säkerhetskopierats med Velero.
Du kan kontrol lera listan över säkerhets kopior som för närvarande identifieras av klustret för att se vilka säkerhets kopior som är tillgängliga för återställning.  För att göra det här steget måste du köra följande kommando:

_(Det här steget förutsätter att du har installerat Velero i ett projekt med namnet "Velero")_

```bash
oc get backups -n velero
```

När du har den säkerhets kopia som du vill återställa måste du utföra återställningen med följande kommando:

```bash
velero restore create --from-backup <name of backup from above output list>
```

I det här steget skapar du Kubernetes-objekt som har säkerhetskopierats från föregående steg när du skapade en säkerhets kopia.

Kör följande steg för att se status för återställningen:

```bash
oc get restore -n velero <name of restore created previously> -o yaml
```
När fasen säger att `Completed` ditt Azure Red Hat 4-program ska återställas.

## <a name="next-steps"></a>Nästa steg

I den här artikeln återställdes ett kluster program för Azure Red Hat OpenShift 4. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa en OpenShift v4-kluster program återställning med Velero


Gå vidare till nästa artikel om du vill lära dig mer om resurser som stöds i Azure Red Hat OpenShift 4.

* [Azure Red Hat OpenShift v4-resurser som stöds](supported-resources.md)