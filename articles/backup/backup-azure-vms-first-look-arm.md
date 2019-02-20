---
title: Säkerhetskopiera en virtuell Azure-dator från inställningarna för virtuella datorer med Azure Backup-tjänsten
description: Lär dig hur du säkerhetskopierar en virtuell Azure-dator med Azure Backup-tjänsten
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2019
ms.author: raynew
ms.openlocfilehash: 40557d4e71dfea5996396cde634f7a1c80913556
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430547"
---
# <a name="back-up-an-azure-vm-from-the-vm-settings"></a>Säkerhetskopiera en virtuell Azure-dator från inställningarna för virtuella datorer

Den här artikeln förklarar hur du säkerhetskopierar virtuella Azure-datorer med den [Azure Backup](backup-overview.md) service. Du kan säkerhetskopiera virtuella Azure-datorer med flera olika metoder:

- Enskild virtuell Azure-dator: Anvisningarna i den här artikeln beskrivs hur du säkerhetskopierar en virtuell Azure-dator direkt från inställningarna för virtuella datorer.
- Flera virtuella Azure-datorer: Du kan ställa in ett Recovery Services-valv och konfigurera säkerhetskopiering för flera virtuella Azure-datorer. Följ instruktionerna i [i den här artikeln](backup-azure-arm-vms-prepare.md) för det här scenariot.

 

## <a name="before-you-start"></a>Innan du börjar

1. [Lär dig](backup-architecture.md#how-does-azure-backup-work) hur säkerhetskopiering fungerar, och [Kontrollera](backup-support-matrix.md#azure-vm-backup-support) supportkrav. 
2. [Få en översikt](backup-azure-vms-introduction.md) av virtuell Azure-säkerhetskopiering.

### <a name="azure-vm-agent-installation"></a>Installation av Azure VM-agent

För att säkerhetskopiera virtuella Azure-datorer, installerar Azure Backup ett tillägg på VM-agenten som körs på datorn. Om den virtuella datorn har skapats från en Azure marketplace-avbildning, körs agenten. I vissa fall, till exempel om du skapar en anpassad virtuell dator, eller om du migrerar en virtuell dator från en lokal plats. Du kan behöva installera agenten manuellt. 

- Om du behöver installera VM-agenten manuellt följer du anvisningarna för [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) eller [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) virtuella datorer. 
- När agenten har installerats kan du aktivera säkerhetskopiering, installerar Azure Backup tillägget för säkerhetskopiering av agenten. Den uppdateringar och korrigeringsfiler tillägget utan inblandning av användaren.

## <a name="back-up-from-azure-vm-settings"></a>Säkerhetskopiera från Virtuella Azure-inställningar


1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Klicka på **alla tjänster** i filtret, skriver **virtuella datorer**, och klicka sedan på **virtuella datorer**. 
3. Välj den virtuella datorn som du vill säkerhetskopiera i listan över virtuella datorer.
4. På VM-menyn klickar du på **Backup**. 
5. I **Recovery Services-valv**, gör du följande:
  - Om du redan har ett valv, klickar du på **Välj befintlig**, och välj ett valv.
  - Om du inte har ett valv, klickar du på **Skapa ny**. Ange ett namn för valvet. Den skapas i samma region- och grupp som den virtuella datorn. Du kan inte ändra dessa inställningar när du aktiverar säkerhetskopiering direkt från inställningarna för virtuella datorer.

  ![Guiden Aktivera säkerhetskopiering](./media/backup-azure-vms-first-look-arm/vm-menu-enable-backup-small.png)

6. I **Välj säkerhetskopieringspolicy**, gör du följande:

  - Lämna standardprincipen. Detta säkerhetskopierar den virtuella datorn en gång om dagen vid den tid som anges och behåller säkerhetskopiorna i valvet under 30 dagar.
  - Välj en befintlig säkerhetskopieringspolicy om du har en.
  - Skapa en ny princip och definiera principinställningarna.  

  ![Välja säkerhetskopieringspolicy](./media/backup-azure-vms-first-look-arm/set-backup-policy.png)

7. Klicka på **Aktivera säkerhetskopiering**. Det här associerar principen för säkerhetskopiering med den virtuella datorn. 

    ![Knappen Aktivera säkerhetskopiering](./media/backup-azure-vms-first-look-arm/vm-management-menu-enable-backup-button.png)

8. Du kan spåra konfigurationsförloppet i portalen meddelanden.
9. När jobbet har slutförts, i VM-menyn klickar du på **Backup**. På sidan visas status för säkerhetskopiering för virtuella datorer, information om återställningspunkter, jobb som körs och aviseringar som har utfärdats.

  ![Säkerhetskopieringsstatus](./media/backup-azure-vms-first-look-arm/backup-item-view-update.png)

10. När du har aktiverat säkerhetskopiering, en [första säkerhetskopieringen](#run-the-initial-backup) körs. Du kan starta den första säkerhetskopieringen direkt eller vänta tills den startas i enlighet med schemat för säkerhetskopiering.
    - Tills den första säkerhetskopieringen har slutförts, den **status för senaste säkerhetskopiering** som **varning (första säkerhetskopiering väntar)**.
    - Om du vill se när nästa schemalagda säkerhetskopiering körs, klickar du på namnet på säkerhetskopieringsprincipen.
    
   

> [!NOTE]
> Azure Backup skapas en separat resursgrupp (andra än resursgrupp för virtuell dator) för att lagra återställningspunkter med namnformatet **AzureBackupRG_geography_number** (exempel: AzureBackupRG_northeurope_1). Du bör inte låsa den här resursgruppen.



## <a name="run-a-backup-immediately"></a>Köra en säkerhetskopia omedelbart 

1. Om du vill köra en säkerhetskopia omedelbart, i VM-menyn klickar du på **säkerhetskopiering** > **Säkerhetskopiera nu**.

    ![Kör säkerhetskopiering](./media/backup-azure-vms-first-look-arm/backup-now-update.png)

2. I **Säkerhetskopiera nu** använder kalenderkontrollen för att välja tills när återställningspunkten ska behållas > och **OK**.
  
    ![Kvarhållning av säkerhetskopior dag](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

3. Portalaviseringarna att du vet att säkerhetskopieringsjobbet har utlösts. Om du vill övervaka säkerhetskopiering klickar du på **visa alla jobb**.




## <a name="back-up-from-the-recovery-services-vault"></a>Säkerhetskopiera från Recovery Services-valvet

Följ instruktionerna i den här artikeln för att aktivera säkerhetskopiering för virtuella Azure-datorer genom att skapa ett Azure Backup Recovery Services-valv och aktiverar säkerhetskopiering i valvet.

## <a name="next-steps"></a>Nästa steg

- Om du har problem med någon av procedurerna i den här artikeln läser den [felsökningsguide för](backup-azure-vms-troubleshoot.md).
- [Lär dig mer om](backup-azure-manage-vms.md) hantera säkerhetskopior.

