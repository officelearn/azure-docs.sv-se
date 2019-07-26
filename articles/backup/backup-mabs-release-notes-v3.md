---
title: Viktig information för Microsoft Azure Backup Server v3
description: Den här artikeln innehåller information om kända problem och lösningar för MABS v3.
author: JYOTHIRMAISURI
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 11/22/2018
ms.author: v-jysur
ms.asset: 0c4127f2-d936-48ef-b430-a9198e425d81
ms.openlocfilehash: e0bcdeeda2f8517b85059817669c19d66c723902
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465074"
---
# <a name="release-notes-for-microsoft-azure-backup-server"></a>Viktig information för Microsoft Azure Backup Server
Den här artikeln innehåller kända problem och lösningar för Microsoft Azure Backup Server (MABS) v3.

##  <a name="backup-and-recovery-fails-for-clustered-workloads"></a>Säkerhets kopiering och återställning Miss lyckas för klustrade arbets belastningar

**Beskrivning:** Säkerhets kopiering/återställning Miss lyckas för klustrade data källor som Hyper-V-kluster eller SQL-kluster (alltid SQL Always on) eller Exchange i databasen tillgänglighets grupp (DAG) efter uppgradering av MABS v2 till MABS v3.

**Lösning:** Förhindra detta genom att öppna SQL Server Management Studio (SSMS)) och köra följande SQL-skript på DPM-databasen:


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


##  <a name="upgrade-to-mabs-v3-fails-in-russian-locale"></a>Det går inte att uppgradera till MABS v3 på ryskt språk

**Beskrivning:** Det går inte att uppgradera från MABS v2 till MABS v3 på den ryska språkvarianten med felkod **4387**.

**Lösning:** Utför följande steg för att uppgradera till MABS v3 med installations paketet för ryska:

1.  [Säkerhetskopiera](https://docs.microsoft.com/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server?view=sql-server-2017#SSMSProcedure) SQL-databasen och avinstallera Mabs v2 (Välj att behålla skyddade data under avinstallationen).
2.  Uppgradera till SQL 2017 (Enterprise) och avinstallera rapportering som en del av uppgraderingen.
3. [Installera](https://docs.microsoft.com/sql/reporting-services/install-windows/install-reporting-services?view=sql-server-2017#install-your-report-server) SQL Server Reporting Services (SSRS).
4.  [Installera](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms#download-ssms-181) SQL Server Management Studio (SSMS).
5.  Konfigurera rapportering med parametrarna enligt beskrivningen i [SSRS-konfigurationen med SQL 2017](https://docs.microsoft.com/azure/backup/backup-azure-microsoft-azure-backup#upgrade-mabs).
6.  [Installera](backup-azure-microsoft-azure-backup.md) MABS V3.
7. [Återställ](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms?view=sql-server-2017) SQL med SSMS och kör verktyget DPM-Sync enligt beskrivningen [här](https://docs.microsoft.com/previous-versions/system-center/data-protection-manager-2010/ff634215(v=technet.10)).
8.  Uppdatera egenskapen ' DataBaseVersion ' i tabellen dbo. TBL _DLS_GlobalSetting med hjälp av följande kommando:

        UPDATE dbo.tbl_DLS_GlobalSetting
        set PropertyValue = '13.0.415.0'
        where PropertyName = 'DatabaseVersion'


9.  Starta MSDPM-tjänsten.


## <a name="next-steps"></a>Nästa steg

[Vad är nytt i MABS v3](backup-mabs-whats-new-mabs.md)
