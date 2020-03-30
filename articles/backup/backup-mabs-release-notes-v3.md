---
title: Viktig information för Microsoft Azure Backup Server v3
description: Den här artikeln innehåller information om kända problem och lösningar för Microsoft Azure Backup Server (MABS) v3.
ms.topic: conceptual
ms.date: 11/22/2018
ms.asset: 0c4127f2-d936-48ef-b430-a9198e425d81
ms.openlocfilehash: b47d83e0e3714f3f035397acaadeac9cda39d12a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74172268"
---
# <a name="release-notes-for-microsoft-azure-backup-server"></a>Viktig information för Microsoft Azure Backup Server

Den här artikeln innehåller kända problem och lösningar för MICROSOFT Azure Backup Server (MABS) V3.

## <a name="backup-and-recovery-fails-for-clustered-workloads"></a>Säkerhetskopiering och återställning misslyckas för klustrade arbetsbelastningar

**Beskrivning:** Säkerhetskopiering/återställning misslyckas för klustrade datakällor som Hyper-V-kluster eller SQL-kluster (SQL Always On) eller Exchange i databastillgänglighetsgruppen (DAG) efter uppgradering av MABS V2 till MABS V3.

**Gå runt:** För att förhindra detta, öppna SQL Server Management Studio (SSMS)) och köra följande SQL-skript på DPM DB:

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

## <a name="upgrade-to-mabs-v3-fails-in-russian-locale"></a>Uppgradering till MABS V3 misslyckas i ryska lokalen

**Beskrivning:** Uppgradering från MABS V2 till MABS V3 i ryska språk misslyckas med en felkod **4387**.

**Gå runt:** Gör följande steg för att uppgradera till MABS V3 med ryska installationspaketet:

1. [Säkerhetskopiera](https://docs.microsoft.com/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server?view=sql-server-2017#SSMSProcedure) SQL-databasen och avinstallera MABS V2 (välj att behålla skyddade data under avinstallationen).
2. Uppgradera till SQL 2017 (Enterprise) och avinstallera rapportering som en del av uppgraderingen.
3. [Installera](https://docs.microsoft.com/sql/reporting-services/install-windows/install-reporting-services?view=sql-server-2017#install-your-report-server) SQL Server Reporting Services (SSRS).
4. [Installera](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) SQL Server Management Studio (SSMS).
5. Konfigurera rapportering med hjälp av parametrarna som dokumenteras i [SSRS-konfigurationen med SQL 2017](https://docs.microsoft.com/azure/backup/backup-azure-microsoft-azure-backup#upgrade-mabs).
6. [Installera](backup-azure-microsoft-azure-backup.md) MABS V3.
7. [Återställ](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms?view=sql-server-2017) SQL med SSMS och kör DPM-Sync-verktyget enligt beskrivningen [här](https://docs.microsoft.com/previous-versions/system-center/data-protection-manager-2010/ff634215(v=technet.10)).
8. Uppdatera egenskapen "DataBaseVersion" i tabellen dbo.tbl_DLS_GlobalSetting med följande kommando:

```sql
        UPDATE dbo.tbl_DLS_GlobalSetting
        set PropertyValue = '13.0.415.0'
        where PropertyName = 'DatabaseVersion'
```

9. Starta MSDPM-tjänsten.

## <a name="next-steps"></a>Nästa steg

[Nyheter i MABS V3](backup-mabs-whats-new-mabs.md)
