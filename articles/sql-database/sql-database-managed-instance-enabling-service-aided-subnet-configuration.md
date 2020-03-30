---
title: Aktivera tjänststödd undernätskonfiguration för Azure SQL Database Managed Instance
description: Aktivera tjänststödd undernätskonfiguration för Azure SQL Database Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.date: 03/12/2020
ms.openlocfilehash: efc2b8578651f68d052f227694f85348853e191f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79533274"
---
# <a name="enabling-service-aided-subnet-configuration-for-azure-sql-database-managed-instance"></a>Aktivera tjänststödd undernätskonfiguration för Azure SQL Database Managed Instance
Tjänststödd undernätskonfiguration ger automatiserad hantering av nätverkskonfiguration för undernät som är värdar för hanterade instanser. Med servicestödd undernät konfiguration användaren behåller full kontroll över åtkomst till data (TDS trafikflöden) medan hanterad instans tar ansvar för att säkerställa oavbruten flödet av hanteringstrafik för att uppfylla SLA.

Automatiskt konfigurerade nätverkssäkerhetsgrupper och vägtabellregler är synliga för kunden och kommenterade med prefixet _Microsoft.Sql-managedInstances_UseOnly__.

Tjänststödd konfiguration aktiveras automatiskt när du aktiverar [undernätsdelegering](../virtual-network/subnet-delegation-overview.md) för `Microsoft.Sql/managedInstances` resursprovidern.

> [!IMPORTANT] 
> När undernätsdelegering är aktiverat kunde du inte stänga av den förrän du tar bort det sista virtuella klustret från undernätet. Mer information om hur du tar bort virtuellt kluster finns i följande [artikel](sql-database-managed-instance-delete-virtual-cluster.md#delete-virtual-cluster-from-the-azure-portal).

> [!NOTE] 
> Eftersom konfiguration av det servicestödda undernätet är viktig för att underhålla SLA, från och med den 1 maj 2020, är det inte möjligt att distribuera hanterade instanser i undernät som inte delegeras till hanterad instansresursprovider. Den 1 juli 2020 delegeras alla undernät som innehåller hanterade instanser automatiskt till den hanterade instansresursprovidern. 

## <a name="enabling-subnet-delegation-for-new-deployments"></a>Aktivera undernätdelegering för nya distributioner
Om du vill distribuera hanterad instans i `Microsoft.Sql/managedInstances` till tomt undernät måste du delegera den till resursprovidern enligt beskrivningen i följande [artikel](../virtual-network/manage-subnet-delegation.md). _Observera att refererad `Microsoft.DBforPostgreSQL/serversv2` artikel använder resursprovidern till exempel. Du måste använda `Microsoft.Sql/managedInstances` resursleverantören i stället._

## <a name="enabling-subnet-delegation-for-existing-deployments"></a>Aktivera undernätdelegering för befintliga distributioner

För att aktivera undernätsdelegering för din befintliga hanterade instansdistribution måste du ta reda på det virtuella nätverksundernätet där det är placerat. 

Om du vill `Virtual network/subnet` veta `Overview` detta kan du kontrollera på portalbladet för din hanterade instans.

Som ett alternativ kan du köra följande PowerShell-kommandon för att lära dig detta. Ersätt **prenumerations-ID** med ditt prenumerations-ID. Ersätt även **rg-namn** med resursgruppen för den hanterade instansen och ersätt **mi-name** med namnet på den hanterade instansen.

```powershell
Install-Module -Name Az

Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount

# Use your subscription ID in place of subscription-id below

Select-AzSubscription -SubscriptionId {subscription-id}

# Replace rg-name with the resource group for your managed instance, and replace mi-name with the name of your managed instance

$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}

$mi.SubnetId
```

När du hittar hanterade instansundernät måste `Microsoft.Sql/managedInstances` du delegera det till resursprovidern enligt beskrivningen i följande [artikel](../virtual-network/manage-subnet-delegation.md). _Observera att refererad `Microsoft.DBforPostgreSQL/serversv2` artikel använder resursprovidern till exempel. Du måste använda `Microsoft.Sql/managedInstances` resursleverantören i stället._


> [!IMPORTANT]
> Om du aktiverar konfiguration med hjälp av tjänsten orsakas inte redundans eller avbrott i anslutningen för hanterade instanser som redan finns i undernätet.
