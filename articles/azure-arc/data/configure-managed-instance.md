---
title: Konfigurera Azure Arc-aktiverad SQL-hanterad instans
description: Konfigurera Azure Arc-aktiverad SQL-hanterad instans
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: f687923f1185d848c2c4f2f40d949c71a7743d65
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "90942093"
---
# <a name="configure-azure-arc-enabled-sql-managed-instance"></a>Konfigurera Azure Arc-aktiverad SQL-hanterad instans

Den här artikeln förklarar hur du konfigurerar Azure Arc-aktiverade SQL-instansen.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="configure-resources-for-azure-arc-enabled-sql-managed-instance"></a>Konfigurera resurser för Azure Arc-aktiverad SQL-hanterad instans

### <a name="configure-using-azdata"></a>Konfigurera med azdata

Du kan redigera konfigurationen av Azure Arc-aktiverade SQL-hanterade instanser med `azdata` cli. Kör följande kommando för att Visa konfigurations alternativ. 

```
azdata arc sql mi edit --help
```

I följande exempel anges processor kärnor och minnes förfrågningar och-gränser.

```
azdata arc sql mi edit --cores-limit 4 --cores-request 2 --memory-limit 4Gi --memory-request 2Gi -n <NAME_OF_SQL_MI>
```

Om du vill visa de ändringar som gjorts i SQL-hanterad instans kan du använda följande kommandon för att visa konfigurationen yaml-filen:

```
azdata arc sql mi show -n <NAME_OF_SQL_MI>
```

## <a name="configure-server-options"></a>Konfigurera Server alternativ

Du kan konfigurera server konfigurations inställningar för Azure Arc-aktiverad SQL-hanterad instans när den skapades. Den här artikeln beskriver hur du konfigurerar inställningar som att aktivera eller inaktivera MSSQL-agenten, aktivera vissa spårnings flaggor för fel söknings scenarier.

Följ dessa steg om du vill ändra någon av dessa inställningar:

1. Skapa en anpassad `mssql-custom.conf` fil som innehåller inställningar för mål. I följande exempel aktive ras SQL-agenten och spårnings flaggan 1204.:

   ```
   [sqlagent]
   enabled=true
   
   [traceflag]
   traceflag0 = 1204
   ```

1. Kopiera `mssql-custom.conf` filen till `/var/opt/mssql` i `mssql-miaa` behållaren i `master-0` pod. Ersätt `<namespaceName>` med det Big data kluster namnet.

   ```bash
   kubectl cp mssql-custom.conf master-0:/var/opt/mssql/mssql-custom.conf -c mssql-server -n <namespaceName>
   ```

1. Starta om SQL Server instans.  Ersätt `<namespaceName>` med det Big data kluster namnet.

   ```bash
   kubectl exec -it master-0  -c mssql-server -n <namespaceName> -- /bin/bash
   supervisorctl restart mssql-server
   exit
   ```


**Kända begränsningar**
- Stegen ovan kräver Kubernetes-kluster administratörs behörighet
- Detta kan ändras i för hands versionen
