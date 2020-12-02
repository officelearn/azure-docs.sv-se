---
title: Hantera åtkomst till arbets ytor, data och pipelines
description: Lär dig hur du hanterar åtkomst kontroll till arbets ytor, data och pipelines i Azure Synapse Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: c4304aeadf2950c1a91ee50ba9ecd895b2561b41
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461358"
---
# <a name="manage-access-to-workspaces-data-and-pipelines"></a>Hantera åtkomst till arbets ytor, data och pipelines

Lär dig hur du hanterar åtkomst kontroll till arbets ytor, data och pipelines i Azure Synapse Analytics.

> [!NOTE]
> För GA är Azure RBAC mer utvecklad genom introduktionen av Synapse Azure-roller

## <a name="access-control-for-workspace"></a>Access Control för arbets yta

För en produktions distribution till en Azure Synapse-arbetsyta föreslår vi att organisera din miljö för att göra det enkelt att etablera användare och administratörer.

> [!NOTE]
> Metoden som tas hit är att skapa flera säkerhets grupper och sedan konfigurera arbets ytan så att de använder dem konsekvent. När grupperna har kon figurer ATS behöver administratören bara hantera medlemskap i säkerhets grupperna.

### <a name="step-1-set-up-security-groups-with-names-following-this-pattern"></a>Steg 1: Konfigurera säkerhets grupper med namn som följer detta mönster

1. Skapa säkerhets grupp med namnet `Synapse_WORKSPACENAME_Users`
2. Skapa säkerhets grupp med namnet `Synapse_WORKSPACENAME_Admins`
3. Lägg till `Synapse_WORKSPACENAME_Admins` i `Synapse_WORKSPACENAME_Users`

> [!NOTE]
> Lär dig hur du skapar en säkerhets grupp i [den här artikeln](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).
>
> Lär dig hur du lägger till en säkerhets grupp från en annan säkerhets grupp i [den här artikeln](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-membership-azure-portal).
>
> WORKSPACENAME – du bör ersätta den här delen med din faktiska arbets ytans namn.

### <a name="step-2-prepare-the-default-adls-gen2-account"></a>Steg 2: Förbered standard ADLS Gen2s kontot

När du etablerade arbets ytan var du tvungen att välja ett [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction) konto och en behållare för det fil system som ska användas för arbets ytan.

1. Öppna [Azure-portalen](https://portal.azure.com)
2. Navigera till Azure Data Lake Storage Gen2 kontot
3. Navigera till container (FileSystem) som du har valt för Azure Synapse-arbetsytan
4. Välj **Access Control (IAM)**
5. Tilldela följande roller:
   1. Rollen **läsare** för att:`Synapse_WORKSPACENAME_Users`
   2. Rollen **Storage BLOB data-ägare** till:`Synapse_WORKSPACENAME_Admins`
   3. Rollen **Storage BLOB data Contributor** till:`Synapse_WORKSPACENAME_Users`
   4. Rollen **Storage BLOB data-ägare** till:`WORKSPACENAME`

> [!NOTE]
> WORKSPACENAME – du bör ersätta den här delen med din faktiska arbets ytans namn.

### <a name="step-3-configure-the-workspace-admin-list"></a>Steg 3: Konfigurera arbets ytans administratörs lista

1. Gå till [ **webb gränssnittet för Azure Synapse Web**](https://web.azuresynapse.net)
2. Gå till **Hantera**   >  **säkerhets**  >  **åtkomst kontroll**
3. Välj **Lägg till administratör** och välj `Synapse_WORKSPACENAME_Admins`

### <a name="step-4-configure-sql-admin-access-for-the-workspace"></a>Steg 4: Konfigurera SQL admin-åtkomst för arbets ytan

1. Gå till [Azure Portal](https://portal.azure.com)
2. Navigera till din arbets yta
3. Gå till **Inställningar**  >  **Active Directory admin**
4. Välj **Ange administratör**
5. Välj `Synapse_WORKSPACENAME_Admins`
6. Välj **Välj**
7. Välj **Spara**

> [!NOTE]
> WORKSPACENAME – du bör ersätta den här delen med din faktiska arbets ytans namn.

### <a name="step-5-add-and-remove-users-and-admins-to-security-groups"></a>Steg 5: lägga till och ta bort användare och administratörer i säkerhets grupper

1. Lägg till användare som behöver administrativ åtkomst till `Synapse_WORKSPACENAME_Admins`
2. Lägg till alla andra användare i `Synapse_WORKSPACENAME_Users`

> [!NOTE]
> Lär dig hur du lägger till användare som en medlem i en säkerhets grupp i [den här artikeln](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-members-azure-portal)
> 
> WORKSPACENAME – du bör ersätta den här delen med din faktiska arbets ytans namn.

## <a name="access-control-to-data"></a>Access Control till data

Åtkomst kontroll till underliggande data delas upp i tre delar:

- Data-plan åtkomst till lagrings kontot (redan konfigurerat ovan i steg 2)
- Data Plans åtkomst till SQL-databaserna (för både dedikerade SQL-pooler och SQL-pool utan server)
- Skapa en autentiseringsuppgift för databaser utan server med SQL-pooler över lagrings kontot

## <a name="access-control-to-sql-databases"></a>Åtkomst kontroll till SQL-databaser

> [!TIP]
> Stegen nedan måste köras för **varje** SQL-databas för att ge användarna åtkomst till alla SQL-databaser, förutom i avsnittet [Server nivå behörighet](#server-level-permission) där du kan tilldela användare en sysadmin-roll.

### <a name="serverless-sql-pool"></a>Serverlös SQL-pool

I det här avsnittet hittar du exempel på hur du ger användaren behörighet till en viss databas eller fullständiga Server behörigheter.

#### <a name="database-level-permission"></a>Behörighet på databas nivå

Om du vill bevilja åtkomst till en användare till en **enskild** server utan server för SQL-pool följer du stegen i det här exemplet:

1. Skapa inloggning

    ```sql
    use master
    go
    CREATE LOGIN [alias@domain.com] FROM EXTERNAL PROVIDER;
    go
    ```

2. Skapa användare

    ```sql
    use yourdb -- Use your DB name
    go
    CREATE USER alias FROM LOGIN [alias@domain.com];
    ```

3. Lägg till användare till medlemmar i den angivna rollen

    ```sql
    use yourdb -- Use your DB name
    go
    alter role db_owner Add member alias -- Type USER name from step 2
    ```

> [!NOTE]
> Ersätt alias med alias för den användare som du vill ge åtkomst och domän med den företags domän som du använder.

#### <a name="server-level-permission"></a>Behörighet på server nivå

Följ stegen i det här exemplet om du vill ge fullständig åtkomst till en användare till **alla** databaser i SQL-poolen utan server:

```sql
CREATE LOGIN [alias@domain.com] FROM EXTERNAL PROVIDER;
ALTER SERVER ROLE  sysadmin  ADD MEMBER [alias@domain.com];
```

### <a name="dedicated-sql-pool"></a>Dedikerad SQL-pool

Följ dessa steg om du vill bevilja åtkomst till en användare till en **enskild** SQL-databas:

1. Skapa användaren i databasen genom att köra följande kommando som mål för önskad databas i kontext väljaren (listruta för att välja databaser):

    ```sql
    --Create user in SQL DB
    CREATE USER [<alias@domain.com>] FROM EXTERNAL PROVIDER;
    ```

2. Ge användaren en roll för att få åtkomst till databasen:

    ```sql
    --Create user in SQL DB
    EXEC sp_addrolemember 'db_owner', '<alias@domain.com>';
    ```

> [!IMPORTANT]
> *db_datareader* och *db_datawriter* kan arbeta med Läs-/Skriv behörighet om beviljandet *db_owner* behörighet är oönskade.
> För att en spark-användare ska kunna läsa och skriva direkt från Spark till/från en dedikerad SQL-pool krävs *db_owner* behörighet.

När du har skapat användarna kontrollerar du att du kan fråga lagrings kontot med hjälp av SQL-poolen utan server.

## <a name="access-control-to-workspace-pipeline-runs"></a>Åtkomstkontroll till arbetsytans pipeline-körningar

### <a name="workspace-managed-identity"></a>Hanterad identitet för arbets yta

> [!IMPORTANT]
> För att kunna köra pipelines som innehåller data uppsättningar eller aktiviteter som refererar till en dedikerad SQL-pool, måste arbets ytans identitet beviljas åtkomst till SQL-poolen direkt.

Kör följande kommandon på varje dedikerad SQL-pool för att tillåta att den arbetsytans hanterade identiteten kör pipelines på SQL-poolens databas:

```sql
--Create user in DB
CREATE USER [<workspacename>] FROM EXTERNAL PROVIDER;

--Granting permission to the identity
GRANT CONTROL ON DATABASE::<SQLpoolname> TO <workspacename>;
```

Du kan ta bort den här behörigheten genom att köra följande skript i samma SQL-pool:

```sql
--Revoking permission to the identity
REVOKE CONTROL ON DATABASE::<SQLpoolname> TO <workspacename>;

--Deleting the user in the DB
DROP USER [<workspacename>];
```

## <a name="next-steps"></a>Nästa steg

En översikt över Synapse-hanterad identitet i arbets ytan finns i [hanterad identitet för Azure Synapse-arbetsytan](../security/synapse-workspace-managed-identity.md). Mer information om databasens huvud namn finns i [huvud konton](https://msdn.microsoft.com/library/ms181127.aspx). Mer information om databas roller finns i artikeln [databas roller](https://msdn.microsoft.com/library/ms189121.aspx) .
