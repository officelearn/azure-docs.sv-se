---
title: Viktig information för Microsoft Azure Backup Server v3
description: Den här artikeln innehåller information om kända problem och lösningar för Microsoft Azure Backup Server (MABS) v3.
ms.topic: conceptual
ms.date: 11/22/2018
ms.asset: 0c4127f2-d936-48ef-b430-a9198e425d81
ms.openlocfilehash: a5c99bcb95fde39bddc9e9db9ab000881c89081a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82185633"
---
# <a name="release-notes-for-microsoft-azure-backup-server"></a>Viktig information för Microsoft Azure Backup Server

Den här artikeln innehåller kända problem och lösningar för Microsoft Azure Backup Server (MABS) v3.

## <a name="backup-and-recovery-fails-for-clustered-workloads"></a>Säkerhets kopiering och återställning Miss lyckas för klustrade arbets belastningar

**Beskrivning:** Säkerhets kopiering/återställning Miss lyckas för klustrade data källor som Hyper-V-kluster eller SQL-kluster (alltid SQL Always on) eller Exchange i databasen tillgänglighets grupp (DAG) efter uppgradering av MABS v2 till MABS v3.

Lösning **:** Förhindra detta genom att öppna SQL Server Management Studio (SSMS)) och köra följande SQL-skript på DPM-databasen:

```sql
    IF EXISTS (SELECT * FROM dbo.sysobjects
        WHERE id = OBJECT_ID(N'[dbo].[tbl_PRM_DatasourceLastActiveServerMap]')
        AND OBJECTPROPERTY(id, N'IsUserTable') = 1)
        DROP TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap]
        GO

        CREATE TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap] (
            [DatasourceId]          [GUID]          NOT NULL,
            [ActiveNode]            [nvarchar](256) NULL,
            [IsGCed]                [bit]           NOT NULL
            ) ON [PRIMARY]
        GO

        ALTER TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap] ADD
    CONSTRAINT [pk__tbl_PRM_DatasourceLastActiveServerMap__DatasourceId] PRIMARY KEY NONCLUSTERED
        (
            [DatasourceId]
        )  ON [PRIMARY],

    CONSTRAINT [DF_tbl_PRM_DatasourceLastActiveServerMap_IsGCed] DEFAULT
        (
            0
        ) FOR [IsGCed]
    GO
```

## <a name="upgrade-to-mabs-v3-fails-in-russian-locale"></a>Det går inte att uppgradera till MABS v3 på ryskt språk

**Beskrivning:** Det går inte att uppgradera från MABS v2 till MABS v3 på den ryska språkvarianten med felkod **4387**.

Lösning **:** Utför följande steg för att uppgradera till MABS v3 med installations paketet för ryska:

1. [Säkerhetskopiera](https://docs.microsoft.com/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server?view=sql-server-2017#SSMSProcedure) SQL-databasen och avinstallera Mabs v2 (Välj att behålla skyddade data under avinstallationen).
2. Uppgradera till SQL 2017 (Enterprise) och avinstallera rapportering som en del av uppgraderingen.
3. [Installera](https://docs.microsoft.com/sql/reporting-services/install-windows/install-reporting-services?view=sql-server-2017#install-your-report-server) SQL Server Reporting Services (SSRS).
4. [Installera](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) SQL Server Management Studio (SSMS).
5. Konfigurera rapportering med parametrarna enligt beskrivningen i [SSRS-konfigurationen med SQL 2017](https://docs.microsoft.com/azure/backup/backup-azure-microsoft-azure-backup#upgrade-mabs).
6. [Installera](backup-azure-microsoft-azure-backup.md) MABS V3.
7. [Återställ](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms?view=sql-server-2017) SQL med SSMS och kör verktyget DPM-Sync enligt beskrivningen [här](https://docs.microsoft.com/system-center/dpm/back-up-the-dpm-server?view=sc-dpm-2019#using-dpmsync).
8. Uppdatera egenskapen ' DataBaseVersion ' i dbo. tbl_DLS_GlobalSetting-tabellen med hjälp av följande kommando:

    ```sql
            UPDATE dbo.tbl_DLS_GlobalSetting
            set PropertyValue = '13.0.415.0'
            where PropertyName = 'DatabaseVersion'
    ```

9. Starta MSDPM-tjänsten.

## <a name="next-steps"></a>Nästa steg

[Vad är nytt i MABS v3](backup-mabs-whats-new-mabs.md)
