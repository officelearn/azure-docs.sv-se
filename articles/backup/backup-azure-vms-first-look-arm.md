---
title: Säkerhetskopiera en virtuell Azure-dator från VM-inställningarna
description: I den här artikeln lär du dig hur du säkerhetskopierar en valfri virtuell Azure-dator eller flera virtuella Azure-datorer med tjänsten Azure Backup.
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: 72d6e5657add3e815bb0d77fadbdbc716712bee5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "76705453"
---
# <a name="back-up-an-azure-vm-from-the-vm-settings"></a>Säkerhetskopiera en virtuell Azure-dator från VM-inställningarna

Den här artikeln beskriver hur du säkerhetskopierar virtuella Azure-datorer med tjänsten [Azure Backup](backup-overview.md) . Du kan säkerhetskopiera virtuella Azure-datorer på ett par olika sätt:

- Enskild virtuell Azure-dator: anvisningarna i den här artikeln beskriver hur du säkerhetskopierar en virtuell Azure-dator direkt från VM-inställningarna.
- Flera virtuella Azure-datorer: du kan konfigurera ett Recovery Services valv och konfigurera säkerhets kopiering för flera virtuella Azure-datorer. Följ anvisningarna i [den här artikeln](backup-azure-arm-vms-prepare.md) för det här scenariot.

## <a name="before-you-start"></a>Innan du börjar

1. [Lär dig](backup-architecture.md#how-does-azure-backup-work) hur säkerhets kopiering fungerar och [kontrol lera](backup-support-matrix.md#azure-vm-backup-support) support kraven.
2. [Få en översikt](backup-azure-vms-introduction.md) över säkerhets kopiering av virtuella Azure-datorer.

### <a name="azure-vm-agent-installation"></a>Installation av Azure VM-agent

För att kunna säkerhetskopiera virtuella Azure-datorer installerar Azure Backup ett tillägg på den virtuella dator agenten som körs på datorn. Om den virtuella datorn skapades från en Azure Marketplace-avbildning körs agenten. I vissa fall, till exempel om du skapar en anpassad virtuell dator eller om du migrerar en dator från en lokal plats. Du kan behöva installera agenten manuellt.

- Om du behöver installera VM-agenten manuellt följer du anvisningarna för virtuella [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) -eller [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) -datorer.
- När agenten har installerats installerar Azure Backup säkerhets kopierings tillägget till agenten när du aktiverar säkerhets kopiering. Den uppdaterar och uppdaterar tillägget utan åtgärder från användaren.

## <a name="back-up-from-azure-vm-settings"></a>Säkerhetskopiera från inställningar för virtuella Azure-datorer

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Klicka på **alla tjänster** och skriv **virtuella datorer**i filtret och klicka sedan på **virtuella datorer**.
3. I listan över virtuella datorer väljer du den virtuella dator som du vill säkerhetskopiera.
4. På menyn VM klickar du på **säkerhets kopiering**.
5. I **Recovery Services valv**gör du följande:
   - Om du redan har ett valv klickar du på **Välj befintlig**och väljer ett valv.
   - Om du inte har något valv klickar du på **Skapa nytt**. Ange ett namn för valvet. Den skapas i samma region och resurs grupp som den virtuella datorn. Du kan inte ändra de här inställningarna när du aktiverar säkerhets kopiering direkt från VM-inställningarna.

   ![Guiden Aktivera säkerhetskopiering](./media/backup-azure-vms-first-look-arm/vm-menu-enable-backup-small.png)

6. I **Välj säkerhets kopierings policy**gör du följande:

   - Lämna standard principen. Detta säkerhetskopierar den virtuella datorn en gång om dagen vid den angivna tidpunkten och behåller säkerhets kopiorna i valvet i 30 dagar.
   - Välj en befintlig säkerhets kopierings policy om du har en.
   - Skapa en ny princip och definiera princip inställningarna.  

   ![Välja säkerhetskopieringspolicy](./media/backup-azure-vms-first-look-arm/set-backup-policy.png)

7. Klicka på **Aktivera säkerhets kopiering**. Detta associerar säkerhets kopierings principen med den virtuella datorn.

    ![Knappen Aktivera säkerhetskopiering](./media/backup-azure-vms-first-look-arm/vm-management-menu-enable-backup-button.png)

8. Du kan följa konfigurations förloppet i Portal meddelanden.
9. När jobbet har slutförts går du till menyn VM och klickar på **säkerhetskopiera**. Sidan visar säkerhets kopierings status för den virtuella datorn, information om återställnings punkter, jobb som körs och aviseringar som utfärdats.

   ![Status för säkerhetskopiering](./media/backup-azure-vms-first-look-arm/backup-item-view-update.png)

10. När du har aktiverat säkerhets kopiering körs en första säkerhets kopiering. Du kan starta den första säkerhets kopieringen direkt eller vänta tills den startar enligt schemat för säkerhets kopiering.
    - Tills den första säkerhets kopieringen är klar visas den **senaste säkerhets kopierings statusen** som **Varning (första säkerhets kopiering väntar)**.
    - Klicka på namnet på säkerhets kopierings principen för att se när nästa schemalagda säkerhets kopiering kommer att köras.

## <a name="run-a-backup-immediately"></a>Kör en säkerhets kopiering direkt

1. Om du vill köra en säkerhets kopiering direkt går du till menyn VM och klickar på **säkerhetskopiera**  >  **nu**.

    ![Kör säkerhets kopiering](./media/backup-azure-vms-first-look-arm/backup-now-update.png)

2. I **Backup** använder du nu kalender kontrollen för att välja tills återställnings punkten kommer att behållas > och **OK**.

    ![Bevarande dag för säkerhets kopior](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

3. Med Portal meddelanden kan du se att säkerhets kopierings jobbet har utlösts. Klicka på **Visa alla jobb**för att övervaka säkerhets kopierings förloppet.

## <a name="back-up-from-the-recovery-services-vault"></a>Säkerhetskopiera från Recovery Services-valvet

Följ anvisningarna i den här artikeln för att aktivera säkerhets kopiering för virtuella Azure-datorer genom att konfigurera ett Azure Backup Recovery Services-valv och aktivera säkerhets kopiering i valvet.

>[!NOTE]
> **Azure Backup har nu stöd för säkerhets kopiering och återställning av selektiva diskar med den virtuella Azure-datorn säkerhets kopierings lösning.**
>
>Idag har Azure Backup stöd för säkerhets kopiering av alla diskar (operativ system och data) i en virtuell dator tillsammans med säkerhets kopierings lösningen för virtuella datorer. Med funktionen exkludera disk får du ett alternativ för att säkerhetskopiera ett eller flera av de många data diskarna i en virtuell dator. Detta ger en effektiv och kostnads effektiv lösning för dina säkerhets kopierings-och återställnings behov. Varje återställnings punkt innehåller data för de diskar som ingår i säkerhets kopieringen, vilket gör att du kan få en del av diskarna återställd från den aktuella återställnings punkten under återställnings åtgärden. Detta gäller för återställning av båda från ögonblicks bilden och valvet.
>
>**Registrera dig för för hands versionen genom att skriva till oss påAskAzureBackupTeam@microsoft.com**

## <a name="next-steps"></a>Nästa steg

- Om du har problem med någon av procedurerna i den här artikeln läser du [fel söknings guiden](backup-azure-vms-troubleshoot.md).
- [Lär dig mer om](backup-azure-manage-vms.md) att hantera säkerhets kopior.
