---
title: Ta bort data styrenhet för Azure-båge
description: Ta bort data styrenhet för Azure-båge
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 2bae661218989d49b74ed8ca3f694ccb912ef912
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90941557"
---
# <a name="delete-azure-arc-data-controller"></a>Ta bort data styrenhet för Azure-båge

I följande artikel beskrivs hur du tar bort en Azure Arc-datakontrollant.

Innan du fortsätter, se till att alla data tjänster som har skapats på data styrenheten tas bort på följande sätt:

## <a name="log-in-to-the-data-controller"></a>Logga in på data styrenheten

Logga in på den data enhet som du vill ta bort:

```
azdata login
```

## <a name="list--delete-existing-data-services"></a>Lista & ta bort befintliga data tjänster

Kör följande kommando för att kontrol lera om några SQL-hanterade instanser har skapats:

```
azdata arc sql mi list
```

För varje SQL-hanterad instans från listan ovan kör du kommandot DELETE enligt följande:

```
azdata arc sql mi delete -n <name>
# for example: azdata arc sql mi delete -n sqlinstance1
```

På samma sätt kan du kontrol lera om det finns PostgreSQL för storskaliga instanser genom att köra:

```
azdata arc postgres server list
```

Och för varje PostgreSQL-instans kör du kommandot DELETE enligt följande:
```
azdata arc postgres server delete -n <name>
# for example: azdata arc postgres server delete -n pg1
```

## <a name="delete-controller"></a>Ta bort styrenhet

När alla SQL-hanterade instanser och PostgreSQL-instanser har tagits bort kan datakontrollanten tas bort på följande sätt:

```
azdata arc dc delete -n <name> -ns <namespace>
# for example: azdata arc dc delete -ns arc -n arcdc
```

### <a name="remove-sccs-red-hat-openshift-only"></a>Ta bort SCCs (endast Red Hat OpenShift)

```console
oc adm policy remove-scc-from-user privileged -z default -n arc
oc adm policy remove-scc-from-user anyuid     -z default -n arc
```

### <a name="optionally-delete-the-azure-arc-data-controller-namespace"></a>Du kan också ta bort namn området för Azure Arc data Controller


```console
kubectl delete ns <nameSpecifiedDuringCreation>
# for example kubectl delete ns arc
```

## <a name="next-steps"></a>Nästa steg

[Vad är Azure Arc-aktiverade datatjänster?](overview.md)