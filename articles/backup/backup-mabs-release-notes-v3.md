---
title: Viktig information för Microsoft Azure Backup Server v3
description: Den här artikeln innehåller information om kända problem och lösningar för MABS v3.
services: backup
author: JYOTHIRMAISURI
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 11/22/2018
ms.author: v-jysur
ms.asset: 0c4127f2-d936-48ef-b430-a9198e425d81
ms.openlocfilehash: d37245d7eed39ee9d219578db9e0a50d758ba9a2
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2019
ms.locfileid: "55660875"
---
# <a name="release-notes-for-microsoft-azure-backup-server"></a>Viktig information för Microsoft Azure Backup Server
Den här artikeln innehåller kända problem och lösningar för Microsoft Azure Backup Server (MABS) V3.

##  <a name="backup-and-recovery-fails-for-clustered-workloads"></a>Säkerhetskopiering och återställning misslyckas av klustrade arbetsbelastningar

**Beskrivning:** Säkerhetskopiering/återställning misslyckas för klustrade datakällor, till exempel Hyper-V-kluster eller SQL-kluster (SQL Always On) eller Exchange i grupp för databastillgänglighet (DAG) när du har uppgraderat MABS V2 till V3 MABS.

**Runt:** Öppna SQL Server Management Studio (SSMS) för att förhindra detta) och kör följande SQL-skript på DPM-databasen:


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


##  <a name="upgrade-to-mabs-v3-fails-in-russian-locale"></a>Det inte går att uppgradera till MABS V3 ryska språk

**Beskrivning:** Uppgradera från MABS V2 till V3 MABS ryska språk misslyckas med felkoden **4387 när**.

**Runt:** Utför följande steg för att uppgradera till MABS V3 med ryska installera paketet:

1.  [Backup](https://docs.microsoft.com/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server?view=sql-server-2017#SSMSProcedure) din SQL-databas och avinstallera MABS V2 (Välj att behålla skyddade data under avinstallationen).
2.  Uppgradera till SQL-2017 (företag) och avinstallera reporting som en del av uppgraderingen.
3. [Installera](https://docs.microsoft.com/sql/reporting-services/install-windows/install-reporting-services?view=sql-server-2017#install-your-report-server) SQLServer Reporting Services (SSRS).
4.  [Installera](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017#ssms-installation-tips-and-issues-ssms-1791) SQL Server Management Studio (SSMS).
5.  Konfigurera rapportering med parametrar som beskrivs i [SSRS-konfiguration med SQL 2017](https://docs.microsoft.com/azure/backup/backup-azure-microsoft-azure-backup#upgrade-mabs).
6.  [Installera](backup-azure-microsoft-azure-backup.md) MABS V3.
7. [Återställa](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms?view=sql-server-2017) SQL med hjälp av SSMS och kör DPM-synkroniseringsverktyget enligt [här](https://docs.microsoft.com/previous-versions/system-center/data-protection-manager-2010/ff634215(v=technet.10)).
8.  Uppdatera egenskapen 'DataBaseVersion' i dbo.tbl_DLS_GlobalSetting tabell med följande kommando:

        UPDATE dbo.tbl_DLS_GlobalSetting
        set PropertyValue = '13.0.415.0'
        where PropertyName = 'DatabaseVersion'


9.  Starta MSDPM-tjänsten.


## <a name="next-steps"></a>Nästa steg

[Vad är nytt i MABS V3](backup-mabs-whats-new-mabs.md)
