---
title: Om MARS-agenten
description: Lär dig hur MARS-agenten stöder säkerhets kopierings scenarier
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: c036d93c09195c0c330cfe86f307d28866131d9f
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2019
ms.locfileid: "74897330"
---
# <a name="about-the-microsoft-azure-recovery-services-mars-agent"></a>Om Microsoft Azure Recovery Services (MARS)-agenten

I den här artikeln beskrivs hur Azure Backup tjänsten använder MARS-agenten (Microsoft Azure Recovery Services) för att säkerhetskopiera och återställa filer/mappar, volym eller system tillstånd från den lokala datorn till Azure.

MARS-agenten stöder följande säkerhets kopierings scenarier:

![instrument panel för Recovery Services-valv](./media/backup-try-azure-backup-in-10-mins/backup-scenarios.png)

- **Filer och mappar**: selektivt skydda Windows-filer och mappar.
- **Volym nivå**: skydda en hel Windows-volym på din dator.
- **System nivå**: skydda ett helt Windows-system tillstånd.

MARS-agenten stöder följande återställnings scenarier:

![instrument panel för Recovery Services-valv](./media/backup-try-azure-backup-in-10-mins/restore-scenarios.png)

-   **Samma server**: samma server som säkerhets kopian ursprungligen skapades på.
    -    **Filer och mappar**: du kan bläddra och välja enskilda filer och mappar som du vill återställa.
    -    **Volym nivå**: du kan välja den volym och den återställnings punkt som du vill återställa och återställa den till samma plats eller en annan plats på samma dator.  Du kan antingen skapa en kopia av befintliga filer, skriva över befintliga filer eller hoppa över befintliga filer.
    -    **System nivå**: du kan välja system tillstånd och återställnings punkt för att återställa till samma dator på en angiven plats.


-   **Alternativ server**: en annan server, d.v.s. inte den server där säkerhets kopian gjordes.
    -    **Filer och mappar**: du kan bläddra och välja enskilda filer och mappar som du vill återställa återställnings punkten till mål datorn.
    -    **Volym nivå**: du kan välja den volym och den återställnings punkt som du vill återställa till en alternativ plats genom att antingen skapa en kopia av befintliga filer, skriva över befintliga filer eller hoppa över att återställa befintliga filer.
    -    **System nivå**: du kan välja system tillstånd och återställnings punkt för att återställa som system tillstånds fil till en annan dator.

## <a name="backup-process"></a>Säkerhetskopieringsprocessen

1.  Skapa ett [Recovery Service-valv](https://docs.microsoft.com/azure/backup/backup-configure-vault#create-a-recovery-services-vault) från Azure Portal och välj filer och mappar och/eller system tillstånd från säkerhets kopierings mål.
2.  [Hämta](https://docs.microsoft.com/azure/backup/backup-configure-vault#download-the-mars-agent) autentiseringsuppgifterna för Recovery Service-valvet och agent installations programmet till en lokal dator. Om du vill skydda den lokala datorn genom att välja alternativet säkerhets kopiering väljer du filer och mappar och system tillstånd och laddar ned MARS-agenten.
3.  Förbered infrastrukturen:

    a.    Kör installations programmet för att [Installera](https://docs.microsoft.com/azure/backup/backup-configure-vault#install-and-register-the-agent) agenten.

    b.  Använd hämtade autentiseringsuppgifter för valvet för att registrera datorn i Recovery Services Vault.
4.  Från agent konsolen på klienten använder du [Schemalägg säkerhets kopiering](https://docs.microsoft.com/azure/backup/backup-configure-vault#create-a-backup-policy) för att konfigurera säkerhets kopieringen. Ange bevarande principen för dina säkerhets kopierings data och börja skydda.

![instrument panel för Recovery Services-valv](./media/backup-try-azure-backup-in-10-mins/backup-process.png)


### <a name="additional-scenarios"></a>Fler scenarier
-   **Säkerhetskopiera vissa filer och mappar i Azure VM** – den primära metoden för att säkerhetskopiera virtuella datorer i Azure (VM) är genom att använda ett Azure Backup tillägg på den virtuella datorn. Detta säkerhetskopierar hela den virtuella datorn. Om du vill säkerhetskopiera vissa filer och mappar i en virtuell dator kan du installera MARS-agenten i virtuella Azure-datorer. [Läs mer](https://docs.microsoft.com/azure/backup/backup-architecture#architecture-built-in-azure-vm-backup).

-   **Offline-dirigering** -första fullständiga säkerhets kopiering av data till Azure, vanligt vis överföring av stora mängder data och kräver mer nätverks bandbredd jämfört med efterföljande säkerhets kopieringar som bara överför delta/ökning. Azure Backup komprimerar de första säkerhets kopiorna. Genom processen för dirigering av dirigering kan Azure Backup använda diskar för att ladda upp komprimerade första säkerhets kopierings data offline till Azure. [Läs mer](https://docs.microsoft.com/azure/backup/backup-azure-backup-server-import-export-).


## <a name="next-steps"></a>Nästa steg
[Matrisen MARS agent support](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent)

[Vanliga frågor och svars/MARS-agenten](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq)
