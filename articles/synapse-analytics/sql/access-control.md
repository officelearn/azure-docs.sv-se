---
title: Hantera åtkomst till arbetsytor, data och pipelines
description: Lär dig hur du hanterar åtkomstkontroll till arbetsytor, data och pipelines på en Azure Synapse Analytics-arbetsyta (förhandsversion).
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 89d2105ab080309639c4341072c3f5f36608dfce
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424771"
---
# <a name="manage-access-to-workspaces-data-and-pipelines"></a>Hantera åtkomst till arbetsytor, data och pipelines

Lär dig hur du hanterar åtkomstkontroll till arbetsytor, data och pipelines på en Azure Synapse Analytics-arbetsyta (förhandsversion).

> [!NOTE]
> För GA kommer RBAC att utvecklas mer genom införandet av Synapse-specifika Azure RBAC-roller

## <a name="access-control-for-workspace"></a>Åtkomstkontroll för arbetsyta

För en produktionsdistribution till en Azure Synapse-arbetsyta föreslår vi att du organiserar din miljö så att den blir enkel att etablera användare och administratörer.

> [!NOTE]
> Den metod som används här är att skapa flera säkerhetsgrupper och sedan konfigurera arbetsytan för att använda dem konsekvent. När grupperna har konfigurerats behöver en administratör bara hantera medlemskap inom säkerhetsgrupperna.

### <a name="step-1-set-up-security-groups-with-names-following-this-pattern"></a>Steg 1: Konfigurera säkerhetsgrupper med namn som följer det här mönstret

1. Skapa säkerhetsgrupp som kallas`Synapse_WORKSPACENAME_Users`
2. Skapa säkerhetsgrupp som kallas`Synapse_WORKSPACENAME_Admins`
3. Lägg till `Synapse_WORKSPACENAME_Admins` i `ProjectSynapse_WORKSPACENAME_Users`

### <a name="step-2-prepare-the-default-adls-gen2-account"></a>Steg 2: Förbereda adls-gen2-standardkontot

När du etablerade arbetsytan var du tvungen att välja ett ADLSGEN2-konto och en behållare för det filsystem som arbetsytan skulle använda.

1. Öppna [Azure-portalen](https://portal.azure.com)
2. Navigera till ADLSGEN2-kontot
3. Navigera till behållaren (filsystem) som du valde för Azure Synapse-arbetsytan
4. Klicka på **Åtkomstkontroll (IAM)**
5. Tilldela följande roller:
   1. **Roll för läsare:**`Synapse_WORKSPACENAME_Users`
   2. **Ägarerollen Storage Blob Data:**`Synapse_WORKSPACENAME_Admins`
   3. **Rollen Storage Blob Data Contributor:**`Synapse_WORKSPACENAME_Users`
   4. **Ägarerollen Storage Blob Data:**`WORKSPACENAME`
  
### <a name="step-3-configure-the-workspace-admin-list"></a>Steg 3: Konfigurera administratörslistan för arbetsytan

1. Gå till [ **webbgränssnittet för Azure Synapse**](https://web.azuresynapse.net)
2. Gå till **Hantera**  > **säkerhetsåtkomstkontrollen** **Security** > 
3. Klicka på **Lägg till administratör**och välj`Synapse_WORKSPACENAME_Admins`

### <a name="step-4-configure-sql-admin-access-for-the-workspace"></a>Steg 4: Konfigurera SQL Admin Access för arbetsytan

1. Gå till [Azure Portal](https://portal.azure.com)
2. Navigera till arbetsytan
3. Gå till**Active Directory-administratör för** **Inställningar** > 
4. Klicka på **Ange administratör**
5. Välj `Synapse_WORKSPACENAME_Admins`
6. Klicka på **Välj**
7. Klicka på **Spara**

### <a name="step-5-add-and-remove-users-and-admins-to-security-groups"></a>Steg 5: Lägga till och ta bort användare och administratörer i säkerhetsgrupper

1. Lägg till användare som behöver administrativ åtkomst till`Synapse_WORKSPACENAME_Admins`
2. Lägg till alla andra användare i`Synapse_WORKSPACENAME_Users`

## <a name="access-control-to-data"></a>Åtkomstkontroll till data

Åtkomstkontroll till underliggande data är uppdelad i tre delar:

- Data-plan åtkomst till lagringskontot (redan konfigurerat ovan i steg 2)
- Data-plan åtkomst till SQL-databaser (för både SQL-pooler och SQL on-demand)
- Skapa en autentiseringsinformation för SQL on-demand-databaser över lagringskontot

## <a name="access-control-to-sql-databases"></a>Åtkomstkontroll till SQL-databaser

> [!TIP]
> Nedanstående steg måste köras för **varje** SQL-databas för att ge användaren åtkomst till alla SQL-databaser.

### <a name="sql-on-demand"></a>SQL på begäran

Så här beviljar du åtkomst till en användare till en **enda** SQL-databas på begäran:

1. Skapa inloggning

    ```sql
    use master
    go
    CREATE LOGIN [John.Thomas@microsoft.com] FROM EXTERNAL PROVIDER;
    go
    ```

2. Skapa ANVÄNDARE

    ```sql
    use yourdb -- Use your DB name
    go
    CREATE USER john FROM LOGIN [John.Thomas@microsoft.com];
    ```

3. Lägga till ANVÄNDARE till medlemmar i den angivna rollen

    ```sql
    use yourdb -- Use your DB name
    go
    alter role db_owner Add member john -- Type USER name from step 2
    ```

### <a name="sql-pools"></a>SQL-pooler

Så här beviljar du åtkomst till en användare till en **enda** SQL-databas:

1. Skapa användaren i databasen genom att köra följande kommando som riktar sig till önskad databas i kontextväljaren (listruta för att välja databaser):

    ```sql
    --Create user in SQL DB
    CREATE USER [<alias@domain.com>] FROM EXTERNAL PROVIDER;
    ```

2. Ge användaren en roll för att komma åt databasen:

    ```sql
    --Create user in SQL DB
    EXEC sp_addrolemember 'db_owner', '<alias@domain.com>';
    ```

> [!IMPORTANT]
> *db_datareader* och *db_datawriter* kan fungera för läs-/skrivbehörighet om det inte är önskvärt att bevilja *db_owner* behörighet.
> För att en Spark-användare ska kunna läsa och skriva direkt från Spark till/från en SQL-pool krävs *db_owner* behörighet.

När du har skapat användarna, verifiera att SQL on-demand kan fråga lagringskontot:

- Kör följande kommando som riktar sig till **huvuddatabasen** för SQL på begäran:

    ```sql
    CREATE CREDENTIAL [https://<storageaccountname>.dfs.core.windows.net]
    WITH IDENTITY='User Identity';
    ```

## <a name="access-control-to-workspace-pipeline-runs"></a>Åtkomstkontroll till arbetsytepipelgångskörningar

### <a name="workspace-managed-identity"></a>Arbetsytehanterad identitet

> [!IMPORTANT]
> Om du vill köra pipelines som innehåller datauppsättningar eller aktiviteter som refererar till en SQL-pool måste arbetsytans identitet beviljas åtkomst till SQL-poolen direkt.

Kör följande kommandon i varje SQL-pool så att den hanterade arbetsytsidentiteten kan köra pipelines i SQL-pooldatabasen:

```sql
--Create user in DB
CREATE USER [<workspacename>] FROM EXTERNAL PROVIDER;

--Granting permission to the identity
GRANT CONTROL ON DATABASE::<SQLpoolname> TO <workspacename>;
```

Den här behörigheten kan tas bort genom att köra följande skript i samma SQL-pool:

```sql
--Revoking permission to the identity
REVOKE CONTROL ON DATABASE::<SQLpoolname> TO <workspacename>;

--Deleting the user in the DB
DROP USER [<workspacename>];
```

## <a name="next-steps"></a>Nästa steg

En översikt över åtkomst och kontroll i Synapse SQL finns i [Synapse SQL-åtkomstkontroll](../sql/access-control.md). Mer information om databasens huvudnamn finns i [Huvudnamn](https://msdn.microsoft.com/library/ms181127.aspx). Ytterligare information om databasroller finns i artikeln [Databasroller.](https://msdn.microsoft.com/library/ms189121.aspx)
