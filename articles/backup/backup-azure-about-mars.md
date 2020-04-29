---
title: Om MARS-agenten
description: Lär dig hur MARS-agenten stöder säkerhets kopierings scenarier
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: d2cc8e32152f6930c9c250e2811668cc2c924616
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "78673280"
---
# <a name="about-the-microsoft-azure-recovery-services-mars-agent"></a>Om Microsoft Azure Recovery Services (MARS)-agenten

I den här artikeln beskrivs hur Azure Backup tjänsten använder MARS-agenten (Microsoft Azure Recovery Services) för att säkerhetskopiera och återställa filer, mappar och volym eller system tillstånd från en lokal dator till Azure.

MARS-agenten stöder följande säkerhets kopierings scenarier:

![Scenarier med MARS-säkerhetskopiering](./media/backup-try-azure-backup-in-10-mins/backup-scenarios.png)

- **Filer och mappar**: selektivt skydda Windows-filer och mappar.
- **Volym nivå**: skydda en hel Windows-volym på din dator.
- **System nivå**: skydda ett helt Windows-system tillstånd.

MARS-agenten stöder följande återställnings scenarier:

![Scenarier för MARS-återställning](./media/backup-try-azure-backup-in-10-mins/restore-scenarios.png)

- **Samma server**: den server där säkerhets kopian skapades.
  - **Filer och mappar**: Välj de enskilda filer och mappar som du vill återställa.
  - **Volym nivå**: Välj den volym och återställnings punkt som du vill återställa och Återställ den sedan till samma plats eller en annan plats på samma dator.  Skapa en kopia av befintliga filer, Skriv över befintliga filer eller hoppa över att återskapa befintliga filer.
  - **System nivå**: Välj system tillstånd och återställnings punkt för att återställa till samma dator på en angiven plats.

- **Alternativ server**: en annan server än den server där säkerhets kopian gjordes.
  - **Filer och mappar**: Välj de enskilda filerna och mapparna vars återställnings punkt du vill återställa till mål datorn.
  - **Volym nivå**: Välj den volym och återställnings punkt som du vill återställa till en annan plats. Skapa en kopia av befintliga filer, Skriv över befintliga filer eller hoppa över att återskapa befintliga filer.
  - **System nivå**: Välj system tillstånd och återställnings punkt för att återställa som en system tillstånds fil till en annan dator.

## <a name="backup-process"></a>Säkerhetskopieringsprocessen

1. Skapa ett [Recovery Services-valv](install-mars-agent.md#create-a-recovery-services-vault)från Azure Portal och välj filer, mappar och system tillstånd från säkerhets kopierings målen.
2. [Ladda ned autentiseringsuppgifterna för Recovery Services valvet och agent installations programmet](https://docs.microsoft.com/azure/backup/install-mars-agent#download-the-mars-agent) till en lokal dator.

    Om du vill skydda den lokala datorn genom att välja alternativet säkerhets kopiering väljer du filer, mappar och system tillstånd och laddar sedan MARS-agenten.

3. Förbered infrastrukturen:

    a. Kör installations programmet för att [Installera agenten](https://docs.microsoft.com/azure/backup/install-mars-agent#install-and-register-the-agent).

    b. Använd de hämtade autentiseringsuppgifterna för valvet för att registrera datorn i Recovery Services-valvet.
4. [Konfigurera säkerhets kopieringen](https://docs.microsoft.com/azure/backup/backup-windows-with-mars-agent#create-a-backup-policy)från agent konsolen på klienten. Ange bevarande principen för dina säkerhets kopierings data för att börja skydda den.

![Azure Backup Agent diagram](./media/backup-try-azure-backup-in-10-mins/backup-process.png)

### <a name="additional-scenarios"></a>Fler scenarier

- **Säkerhetskopiera vissa filer och mappar i Azure Virtual Machines**: den primära metoden för att säkerhetskopiera virtuella datorer i Azure är att använda ett Azure Backup-tillägg på den virtuella datorn. Tillägget säkerhetskopierar hela den virtuella datorn. Om du vill säkerhetskopiera vissa filer och mappar i en virtuell dator kan du installera MARS-agenten på de virtuella Azure-datorerna. Mer information finns i [arkitektur: inbyggd virtuell Azure-säkerhetskopiering](https://docs.microsoft.com/azure/backup/backup-architecture#architecture-built-in-azure-vm-backup).

- **Offline-seeding**: inledande fullständiga säkerhets kopieringar av data till Azure överför vanligt vis stora mängder data och kräver mer nätverks bandbredd. Vid efterföljande säkerhets kopieringar överförs endast delta eller stegvis mängd data. Azure Backup komprimerar de första säkerhets kopiorna. Genom processen för *dirigering av dirigering*kan Azure Backup använda diskar för att ladda upp komprimerade första säkerhets kopierings data offline till Azure. Mer information finns i [Azure Backup offline-säkerhetskopiering med Azure Data Box](offline-backup-azure-data-box.md).

## <a name="next-steps"></a>Nästa steg

[Stödmatris för MARS-agenten](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent)

[Vanliga frågor och svar om MARS-agent](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq)
