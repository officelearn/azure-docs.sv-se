---
title: 'Anpassade roller: Sql Server online till SQL-tjänstringar för hanterade instanser'
titleSuffix: Azure Database Migration Service
description: Lär dig att använda de anpassade rollerna för SQL Server till Azure SQL Database hanterade instansanpassningar online.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 10/25/2019
ms.openlocfilehash: e9a1024ca3ab68841474ab051c029042df4915b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78254947"
---
# <a name="custom-roles-for-sql-server-to-sql-database-managed-instance-online-migrations"></a>Anpassade roller för SQL Server till SQL Database-hanterade instanseringstjänsttjänst för onlinemigreringar

Azure Database Migration Service använder ett APP-ID för att interagera med Azure Services. APP-ID kräver antingen deltagarrollen på prenumerationsnivå (vilket många företagssäkerhetsavdelningar inte tillåter) eller skapandet av anpassade roller som ger de specifika behörigheter som Azure-databasmigreringstjänsten kräver. Eftersom det finns en gräns på 2 000 anpassade roller i Azure Active Directory, kanske du vill kombinera alla behörigheter som krävs specifikt av APP-ID till en eller två anpassade roller och sedan ge APP-ID:t den anpassade rollen för specifika objekt eller resursgrupper (jämfört med i app-ID:t) abonnemangsnivå). Om antalet anpassade roller inte är ett problem kan du dela upp de anpassade rollerna efter resurstyp för att skapa totalt tre anpassade roller enligt beskrivningen nedan.

Med avsnittet AssignableScopes i rolldefinitionsjisonsträngen kan du styra var behörigheterna visas i användargränssnittet **Lägg till rolltilldelning** i portalen. Du vill förmodligen definiera rollen på resursgruppen eller till och med resursnivå för att undvika att användargränssnittet störs med extra roller. Observera att detta inte utför den faktiska rolltilldelningen.

## <a name="minimum-number-of-roles"></a>Minsta antal roller

Vi rekommenderar för närvarande att skapa minst två anpassade roller för APP-ID, en på resursnivå och den andra på prenumerationsnivå.

> [!NOTE]
> Det senaste anpassade rollkravet kan så småningom tas bort, eftersom ny SQL Database-hanterad instanskod distribueras till Azure.

**Anpassad roll för APP-ID**. Den här rollen krävs för migrering av Azure Database Migration Service på *resurs-* eller *resursgruppsnivå* (mer information om APP-ID finns i artikeln [Använd portalen för att skapa ett Azure AD-program och tjänsthuvudnamn som kan komma åt resurser](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)).

```json
{
  "Name": "DMS Role - App ID",
  "IsCustom": true,
  "Description": "DMS App ID access to complete MI migrations",
  "Actions": [
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageaccounts/blobservices/read",
        "Microsoft.Storage/storageaccounts/blobservices/write",
        "Microsoft.Sql/managedInstances/read",
        "Microsoft.Sql/managedInstances/write",
        "Microsoft.Sql/managedInstances/databases/read",
        "Microsoft.Sql/managedInstances/databases/write",
        "Microsoft.Sql/managedInstances/databases/delete",
        "Microsoft.Sql/managedInstances/metrics/read",
        "Microsoft.DataMigration/locations/*",
        "Microsoft.DataMigration/services/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>/ResourceGroups/<StorageAccount_rg_name>",
    "/subscriptions/<subscription_id>/ResourceGroups/<ManagedInstance_rg_name>",
    "/subscriptions/<subscription_id>/ResourceGroups/<DMS_rg_name>",
  ]
}
```

**Anpassad roll för APP-ID - prenumeration**. Den här rollen krävs för Migrering av Azure Database Migration Service på *prenumerationsnivå.*

```json
{
  "Name": "DMS Role - App ID - Sub",
  "IsCustom": true,
  "Description": "DMS App ID access at subscription level to complete MI migrations",
  "Actions": [
        "Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>"
  ]
}
```

Json ovan måste lagras i tre textfiler och du kan använda antingen AzureRM, AZ PowerShell-cmdlets eller Azure CLI för att skapa rollerna med hjälp av **New-AzureRmRoleDefinition (AzureRM)** eller **New-AzRoleDefinition (AZ)**.

Mer information finns i artikeln [Anpassade roller för Azure-resurser](https://docs.microsoft.com/azure/role-based-access-control/custom-roles).

När du har skapat dessa anpassade roller måste du lägga till rolltilldelningar för användare och APP-ID:n i lämpliga resurser eller resursgrupper:

* Rollen "DMS-roll - App-ID" måste beviljas till APP-ID:t som ska användas för migreringarna och även på lagringskontot, Azure Database Migration Service-instansen och SQL Database-hanterade instansresursnivåer.
* Rollen "DMS-roll - App-ID - Sub" måste beviljas APP-ID på prenumerationsnivå (beviljandet på resursen eller resursgruppen misslyckas). Det här kravet är tillfälligt tills en koduppdatering har distribuerats.

## <a name="expanded-number-of-roles"></a>Utökat antal roller

Om antalet anpassade roller i din Azure Active Directory inte är ett problem rekommenderar vi att du skapar totalt tre roller. Du behöver fortfarande rollen "DMS-roll – App-ID – Sub", men rollen "DMS-roll – App-ID" ovan delas upp efter resurstyp i två olika roller.

**Anpassad roll för APP-ID för SQL Database-hanterad instans**

```json
{
  "Name": "DMS Role - App ID - SQL MI",
  "IsCustom": true,
  "Description": "DMS App ID access to complete MI migrations",
  "Actions": [
        "Microsoft.Sql/managedInstances/read",
        "Microsoft.Sql/managedInstances/write",
        "Microsoft.Sql/managedInstances/databases/read",
        "Microsoft.Sql/managedInstances/databases/write",
        "Microsoft.Sql/managedInstances/databases/delete",
        "Microsoft.Sql/managedInstances/metrics/read"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>/resourceGroups/<ManagedInstance_rg_name>"
  ]
}
```

**Anpassad roll för APP-ID för lagring**

```json
{
  "Name": "DMS Role - App ID - Storage",
  "IsCustom": true,
  "Description": "DMS App ID storage access to complete MI migrations",
  "Actions": [
"Microsoft.Storage/storageAccounts/read",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageaccounts/blobservices/read",
        "Microsoft.Storage/storageaccounts/blobservices/write"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>/resourceGroups/<StorageAccount_rg_name>"
  ]
}
```

## <a name="role-assignment"></a>Rolltilldelning

Om du vill tilldela en roll till användare/APP-ID öppnar du Azure-portalen:

1. Navigera till resursgruppen eller resursen (förutom den roll som måste beviljas för prenumerationen), gå till **Åtkomstkontroll**och bläddra sedan för att hitta de anpassade roller som du just skapade.

2. Välj lämplig roll, välj APP-ID och spara ändringarna.

  App-ID:t visas nu på fliken **Rolltilldelningar.**

## <a name="next-steps"></a>Nästa steg

* Läs migreringsvägledningen för ditt scenario i [Migreringsguiden för](https://datamigration.microsoft.com/)Microsoft Database .
