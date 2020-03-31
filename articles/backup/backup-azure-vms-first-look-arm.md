---
title: Säkerhetskopiera en virtuell Azure-dator från vm-inställningarna
description: I den här artikeln kan du läsa om hur du säkerhetskopierar antingen en unik Azure-virtuell dator eller flera virtuella Azure-datorer med Azure Backup-tjänsten.
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: 72d6e5657add3e815bb0d77fadbdbc716712bee5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76705453"
---
# <a name="back-up-an-azure-vm-from-the-vm-settings"></a>Säkerhetskopiera en virtuell Azure-dator från vm-inställningarna

I den här artikeln beskrivs hur du säkerhetskopierar virtuella Azure-datorer med [Azure Backup-tjänsten.](backup-overview.md) Du kan säkerhetskopiera virtuella Azure-datorer med hjälp av ett par metoder:

- Enkel Virtuell Azure: Instruktionerna i den här artikeln beskriver hur du säkerhetskopierar en Azure-virtuell dator direkt från vm-inställningarna.
- Flera virtuella Azure-datorer: Du kan konfigurera ett Recovery Services-valv och konfigurera säkerhetskopiering för flera virtuella Azure-datorer. Följ instruktionerna i den [här artikeln](backup-azure-arm-vms-prepare.md) för det här scenariot.

## <a name="before-you-start"></a>Innan du börjar

1. [Lär dig](backup-architecture.md#how-does-azure-backup-work) hur säkerhetskopiering fungerar och [verifiera](backup-support-matrix.md#azure-vm-backup-support) supportkraven.
2. [Få en översikt över](backup-azure-vms-introduction.md) Azure VM-säkerhetskopiering.

### <a name="azure-vm-agent-installation"></a>Installation av Azure VM-agent

För att säkerhetskopiera virtuella Azure-datorer installerar Azure Backup ett tillägg på vm-agenten som körs på datorn. Om din virtuella dator skapades från en Azure marketplace-avbildning körs agenten. I vissa fall, till exempel om du skapar en anpassad virtuell dator, eller om du migrerar en dator från lokalt. Du kan behöva installera agenten manuellt.

- Om du behöver installera VM-agenten manuellt följer du instruktionerna för [virtuella datorer](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) med Windows eller [Linux.](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux)
- När agenten har installerats installerar Azure Backup tillägget för säkerhetskopiering till agenten när du aktiverar säkerhetskopiering. Den uppdaterar och korrigerar tillägget utan att användaren behöver göra något.

## <a name="back-up-from-azure-vm-settings"></a>Säkerhetskopiera från Azure VM-inställningar

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Klicka på **Alla tjänster** och skriv **virtuella datorer**i filtret och klicka sedan på Virtuella **datorer**.
3. Välj den virtuella dator som du vill säkerhetskopiera i listan över virtuella datorer.
4. Klicka på **Säkerhetskopiering**på VM-menyn.
5. Gör följande i **Valvet**för Återställningstjänster:
   - Om du redan har ett valv klickar du på **Markera befintligt**och väljer ett valv.
   - Om du inte har ett valv klickar du på **Skapa nytt**. Ange ett namn för valvet. Den skapas i samma region och resursgrupp som den virtuella datorn. Du kan inte ändra dessa inställningar när du aktiverar säkerhetskopiering direkt från vm-inställningarna.

   ![Guiden Aktivera säkerhetskopiering](./media/backup-azure-vms-first-look-arm/vm-menu-enable-backup-small.png)

6. Gör följande i **Välj principer för säkerhetskopiering:**

   - Lämna standardprincipen. Detta säkerhetskopierar den virtuella datorn en gång om dagen vid den angivna tidpunkten och behåller säkerhetskopior i valvet i 30 dagar.
   - Välj en befintlig princip för säkerhetskopiering om du har en.
   - Skapa en ny princip och definiera principinställningarna.  

   ![Välja säkerhetskopieringspolicy](./media/backup-azure-vms-first-look-arm/set-backup-policy.png)

7. Klicka på **Aktivera säkerhetskopiering**. Detta associerar säkerhetskopieringsprincipen med den virtuella datorn.

    ![Knappen Aktivera säkerhetskopiering](./media/backup-azure-vms-first-look-arm/vm-management-menu-enable-backup-button.png)

8. Du kan spåra konfigurationsstatus i portalmeddelandena.
9. När jobbet är klart klickar du på **Säkerhetskopiering**på VM-menyn . Sidan visar säkerhetskopieringsstatus för den virtuella datorn, information om återställningspunkter, jobb som körs och utfärdade aviseringar.

   ![Status för säkerhetskopiering](./media/backup-azure-vms-first-look-arm/backup-item-view-update.png)

10. När du har aktiverat säkerhetskopiering körs en första säkerhetskopia. Du kan starta den första säkerhetskopian omedelbart eller vänta tills den startar i enlighet med säkerhetskopieringsschemat.
    - Tills den första säkerhetskopian är klar visas **statusen Senaste säkerhetskopiering** som **Varning (Inledande väntande säkerhetskopiering).**
    - Klicka på principnamnet för säkerhetskopian om du vill se när nästa schemalagda säkerhetskopiering körs.

## <a name="run-a-backup-immediately"></a>Kör en säkerhetskopia omedelbart

1. Om du vill köra en säkerhetskopia direkt klickar du på **Säkerhetskopiering av säkerhetskopian** > på VM**nu**.

    ![Kör säkerhetskopiering](./media/backup-azure-vms-first-look-arm/backup-now-update.png)

2. I **Säkerhetskopiering Använd** nu kalenderkontrollen för att välja tills återställningspunkten ska behållas > och **OK**.

    ![Lagringsdag för säkerhetskopiering](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

3. Portalmeddelanden meddelar dig att säkerhetskopieringsjobbet har utlösts. Om du vill övervaka förloppet för säkerhetskopiering klickar du på **Visa alla jobb**.

## <a name="back-up-from-the-recovery-services-vault"></a>Säkerhetskopiera från valvet för Återställningstjänster

Följ instruktionerna i den här artikeln för att aktivera säkerhetskopiering för virtuella Azure-datorer genom att konfigurera ett Azure Backup Recovery Services-valv och aktivera säkerhetskopiering i valvet.

>[!NOTE]
> **Azure Backup stöder nu selektiv säkerhetskopiering och återställning av disk med hjälp av azure virtual machine-säkerhetskopieringslösningen.**
>
>Idag stöder Azure Backup säkerhetskopiering av alla diskar (operativsystem och data) i en virtuell dator tillsammans med hjälp av lösningen för säkerhetskopiering av virtuella datorer. Med exkluderingsdiskfunktioner får du möjlighet att säkerhetskopiera en eller några från de många datadiskarna i en virtuell dator. Detta ger en effektiv och kostnadseffektiv lösning för dina säkerhetskopierings- och återställningsbehov. Varje återställningspunkt innehåller data för diskarna som ingår i säkerhetskopieringen, vilket gör att du ytterligare kan få en delmängd diskar återställda från den angivna återställningspunkten under återställningen. Detta gäller för att återställa både från ögonblicksbilden och valvet.
>
>**För att registrera dig för förhandsgranskningen, skriv till oss påAskAzureBackupTeam@microsoft.com**

## <a name="next-steps"></a>Nästa steg

- Om du har problem med någon av procedurerna i den här artikeln läser du [felsökningsguiden](backup-azure-vms-troubleshoot.md).
- [Läs mer om](backup-azure-manage-vms.md) hur du hanterar dina säkerhetskopior.
