---
title: Aktivera säkerhetskopiering när du skapar en virtuell Azure-dator
description: Beskriver hur du aktiverar säkerhetskopiering när du skapar en Virtuell Azure-dator med Azure Backup.
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: 7739109eb8bad88c9b723e67e13adc78c127499a
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80672812"
---
# <a name="enable-backup-when-you-create-an-azure-vm"></a>Aktivera säkerhetskopiering när du skapar en virtuell Azure-dator

Använd Azure Backup-tjänsten för att säkerhetskopiera virtuella Azure-datorer (VIRTUELLA datorer). Virtuella datorer säkerhetskopieras enligt ett schema som anges i en säkerhetskopieringsprincip och återställningspunkter skapas från säkerhetskopior. Återställningspunkter lagras i Recovery Services-valv.

I den här artikeln beskrivs hur du aktiverar säkerhetskopiering när du skapar en virtuell dator (VM) i Azure-portalen.  

## <a name="before-you-start"></a>Innan du börjar

- [Kontrollera](backup-support-matrix-iaas.md#supported-backup-actions) vilka operativsystem som stöds om du aktiverar säkerhetskopiering när du skapar en virtuell dator.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Om du inte redan är inloggad på ditt konto loggar du in på [Azure-portalen](https://portal.azure.com).

## <a name="create-a-vm-with-backup-configured"></a>Skapa en virtuell dator med konfigurerad säkerhetskopiering

1. Klicka på **Skapa en resurs**i Azure-portalen .

2. Klicka på **Beräkna**på Azure Marketplace och välj sedan en vm-avbildning.

3. Konfigurera den virtuella datorn i enlighet med Instruktionerna för [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal) eller [Linux.](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal)

4. Klicka på **På**i **Aktivera säkerhetskopiering**på fliken **Hantering** .
5. Säkerhetskopior av Azure Backup till ett Recovery Services-valv. Klicka på **Skapa ny** om du inte har ett befintligt valv.
6. Acceptera det föreslagna valvets namn eller ange ditt eget.
7. Ange eller skapa en resursgrupp där valvet ska placeras. Resursgruppsvalvet kan skilja sig från resursgruppen för virtuell dator.

    ![Aktivera säkerhetskopiering för en virtuell dator](./media/backup-during-vm-creation/enable-backup.png)

8. Acceptera standardprincipen för säkerhetskopiering eller ändra inställningarna.
    - En princip för säkerhetskopiering anger hur ofta ögonblicksbilder av den virtuella datorn ska ta säkerhetskopior och hur lång tid det tar att behålla dessa säkerhetskopior.
    - Standardprincipen säkerhetskopierar den virtuella datorn en gång om dagen.
    - Du kan anpassa din egen säkerhetskopieringsprincip för en Azure VM för att ta säkerhetskopior dagligen eller veckovis.
    - [Läs mer](backup-azure-vms-introduction.md#backup-and-restore-considerations) om säkerhetskopieringsöverväganden för virtuella Azure-datorer.
    - [Läs mer](backup-instant-restore-capability.md) om funktionen för omedelbar återställning.

      ![Standardprincip för säkerhetskopiering](./media/backup-during-vm-creation/daily-policy.png)

## <a name="azure-backup-resource-group-for-virtual-machines"></a>Resursgrupp för Azure Backup för virtuella datorer

Säkerhetskopieringstjänsten skapar en separat resursgrupp (RG), som skiljer sig från resursgruppen för den virtuella datorn för att lagra återställningspunktsamlingen (RPC). RPC rymmer de omedelbara återställningspunkterna för hanterade virtuella datorer. Standardnamnformatet för resursgruppen som skapats av `AzureBackupRG_<Geo>_<number>`säkerhetskopieringstjänsten är: . Till exempel: *AzureBackupRG_northeurope_1*. Nu kan du anpassa resursgruppnamnet som skapats av Azure Backup.

Punkter att notera:

1. Du kan antingen använda standardnamnet på RG eller redigera den enligt företagets krav.
2. Du anger RG-namnmönstret som indata när vm-säkerhetskopieringsprincipen skapas. RG-namnet ska vara av `<alpha-numeric string>* n <alpha-numeric string>`följande format: . 'n' ersätts med ett heltal (från 1) och används för att skala ut om den första RG är full. En RG kan ha max 600 varvtal idag.
              ![Välj namn när du skapar princip](./media/backup-during-vm-creation/create-policy.png)
3. Mönstret bör följa RG namngivningsreglerna nedan och den totala längden bör inte överstiga den högsta tillåtna RG-namnlängden.
    1. Resursgruppsnamn tillåter bara alfanumeriska tecken, punkter, understreck, bindestreck och parentes. De kan inte sluta i en period.
    2. Resursgruppsnamn kan innehålla upp till 74 tecken, inklusive namnet på RG och suffixet.
4. Den `<alpha-numeric-string>` första är obligatorisk medan den andra efter 'n' är valfri. Detta gäller endast om du ger ett anpassat namn. Om du inte anger något i någon av textrutorna används standardnamnet.
5. Du kan redigera namnet på RG genom att ändra principen om och när det behövs. Om namnmönstret ändras skapas nya RPs i den nya RG.If the name pattern is changed, new RPs will be created in the new RG. De gamla RPs-skivorna finns dock fortfarande kvar i den gamla RG:et och flyttas inte, eftersom RP-samlingen inte stöder resursflyttning. Så småningom RPs kommer att få skräp samlas in när poängen löper ut.
![Ändra namn vid ändring av princip](./media/backup-during-vm-creation/modify-policy.png)
6. Det rekommenderas att inte låsa resursgruppen som skapats för användning av säkerhetskopieringstjänsten.

Information om hur du konfigurerar azure backup-resursgruppen för virtuella datorer med PowerShell läser du [Skapa Azure Backup-resursgrupp under bevarande av ögonblicksbilder](backup-azure-vms-automation.md#creating-azure-backup-resource-group-during-snapshot-retention).

## <a name="start-a-backup-after-creating-the-vm"></a>Starta en säkerhetskopia när du har skapat den virtuella datorn

Säkerhetskopieringen av den virtuella datorn körs i enlighet med din säkerhetskopieringsprincip. Vi rekommenderar dock att du kör en första säkerhetskopia.

När den virtuella datorn har skapats gör du följande:

1. Klicka på **Säkerhetskopiering**i egenskaperna för den virtuella datorn. VM-statusen är Inledande väntande säkerhetskopiering tills den första säkerhetskopieringen körs
2. Klicka på **Säkerhetskopiera nu** om du vill köra en säkerhetskopiering på begäran.

    ![Kör en säkerhetskopiering på begäran](./media/backup-during-vm-creation/run-backup.png)

## <a name="use-a-resource-manager-template-to-deploy-a-protected-vm"></a>Använda en Resource Manager-mall för att distribuera en skyddad virtuell dator

De föregående stegen förklarar hur du använder Azure-portalen för att skapa en virtuell dator och skydda den i ett Recovery Services-valv. Om du snabbt vill distribuera en eller flera virtuella datorer och skydda dem i ett Recovery Services-valv läser du mallen [Distribuera en Virtuell Windows-dator och aktiverar säkerhetskopiering](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/).

## <a name="next-steps"></a>Nästa steg

Nu när du har skyddat den virtuella datorn kan du läsa om hur du hanterar och återställer dem.

- [Hantera och övervaka virtuella datorer](backup-azure-manage-vms.md)
- [Återställa virtuell dator](backup-azure-arm-restore-vms.md)

Om du stöter på några problem [läser du](backup-azure-vms-troubleshoot.md) felsökningsguiden.
