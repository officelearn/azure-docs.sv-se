---
title: Om MARS-agenten
description: Lär dig hur MARS-agenten stöder säkerhetskopieringsscenarier
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: d2cc8e32152f6930c9c250e2811668cc2c924616
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673280"
---
# <a name="about-the-microsoft-azure-recovery-services-mars-agent"></a>Om MARS-agenten (Microsoft Azure Recovery Services)

I den här artikeln beskrivs hur Azure Backup-tjänsten använder MARS-agenten (Microsoft Azure Recovery Services) för att säkerhetskopiera och återställa filer, mappar och volym- eller systemtillståndet från en lokal dator till Azure.

MARS-agenten stöder följande säkerhetskopieringsscenarier:

![Scenarier för MARS-säkerhetskopiering](./media/backup-try-azure-backup-in-10-mins/backup-scenarios.png)

- **Filer och mappar:** Selektivt skydda Windows-filer och mappar.
- **Volymnivå:** Skydda en hel Windows-volym på din dator.
- **Systemnivå:** Skydda ett helt Windows-systemtillstånd.

MARS-agenten stöder följande återställningsscenarier:

![MARS-återställningsscenarier](./media/backup-try-azure-backup-in-10-mins/restore-scenarios.png)

- **Samma server:** Den server där säkerhetskopian ursprungligen skapades.
  - **Filer och mappar:** Välj de enskilda filer och mappar som du vill återställa.
  - **Volymnivå:** Välj den volym och återställningspunkt som du vill återställa och återställ den sedan till samma plats eller en annan plats på samma dator.  Skapa en kopia av befintliga filer, skriv över befintliga filer eller hoppa över återställning av befintliga filer.
  - **Systemnivå:** Välj systemtillstånd och återställningspunkt för att återställa till samma dator på en angiven plats.

- **Alternativ server:** En annan server än den server där säkerhetskopian togs.
  - **Filer och mappar:** Välj de enskilda filer och mappar vars återställningspunkt du vill återställa till en måldator.
  - **Volymnivå:** Välj den volym och återställningspunkt som du vill återställa till en annan plats. Skapa en kopia av befintliga filer, skriv över befintliga filer eller hoppa över återställning av befintliga filer.
  - **Systemnivå:** Välj systemtillstånd och återställningspunkt för att återställa som en systemtillståndsfil till en alternativ dator.

## <a name="backup-process"></a>Säkerhetskopieringsprocessen

1. Skapa ett [Recovery Services-valv](install-mars-agent.md#create-a-recovery-services-vault)från Azure-portalen och välj filer, mappar och systemtillståndet från säkerhetskopieringsmålen.
2. [Hämta autentiseringsuppgifterna för Recovery Services-valvet och agentinstallationsprogrammet](https://docs.microsoft.com/azure/backup/install-mars-agent#download-the-mars-agent) till en lokal dator.

    Om du vill skydda den lokala datorn genom att välja alternativet Säkerhetskopiering väljer du filer, mappar och systemtillståndet och hämtar sedan MARS-agenten.

3. Förbered infrastrukturen:

    a. Kör installationsprogrammet för att [installera agenten](https://docs.microsoft.com/azure/backup/install-mars-agent#install-and-register-the-agent).

    b. Använd dina hämtade autentiseringsuppgifter för att registrera datorn i Recovery Services-valvet.
4. [Konfigurera säkerhetskopian](https://docs.microsoft.com/azure/backup/backup-windows-with-mars-agent#create-a-backup-policy)från agentkonsolen på klienten . Ange bevarandeprincipen för dina säkerhetskopierade data för att börja skydda dem.

![Azure Backup agent diagram](./media/backup-try-azure-backup-in-10-mins/backup-process.png)

### <a name="additional-scenarios"></a>Fler scenarier

- **Säkerhetskopiera specifika filer och mappar i virtuella Azure-datorer:** Den primära metoden för säkerhetskopiering av virtuella Azure-datorer (VMs) är att använda ett Azure Backup-tillägg på den virtuella datorn. Tillägget säkerhetskopierar hela den virtuella datorn. Om du vill säkerhetskopiera specifika filer och mappar i en virtuell dator kan du installera MARS-agenten i virtuella Azure-datorer. Mer information finns [i Arkitektur: Inbyggd Azure VM-säkerhetskopiering](https://docs.microsoft.com/azure/backup/backup-architecture#architecture-built-in-azure-vm-backup).

- **Offlinesådning:** Inledande fullständiga säkerhetskopior av data till Azure överför vanligtvis stora mängder data och kräver mer nätverksbandbredd. Efterföljande säkerhetskopior överför endast delta- eller inkrementell mängden data. Azure Backup komprimerar de första säkerhetskopieringarna. Genom processen *för offline seedning*kan Azure Backup använda diskar för att överföra komprimerade initiala säkerhetskopieringsdata offline till Azure. Mer information finns i [Azure Backup offline-säkerhetskopiering med Azure Data Box](offline-backup-azure-data-box.md).

## <a name="next-steps"></a>Nästa steg

[Stödmatris för MARS-agenten](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent)

[Vanliga frågor om MARS-agent](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq)
