---
title: "Säkerhetskopiera klassisk distribuerade virtuella Azure-datorer till ett säkerhetskopieringsvalv | Microsoft Docs"
description: "Identifiera, registrera och säkerhetskopiera virtuella datorer med de här procedurerna för virtuell dator i Azure-säkerhetskopiering."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "säkerhetskopiering av virtuella datorer Säkerhetskopiera virtuella; säkerhetskopiering och katastrofåterställning återställning. säkerhetskopiering"
ms.assetid: c0ab5469-65fd-4af5-ae9b-f5d183f82ce8
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: markgal;trinadhk;
ms.openlocfilehash: ca3fb650a133efb67c0ad2cd96847c6a0e21c876
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/11/2017
---
# <a name="back-up-azure-virtual-machines-classic-portal"></a>Säkerhetskopiera virtuella Azure-datorer (klassiska portal)
> [!div class="op_single_selector"]
> * [Säkerhetskopiera virtuella datorer till Recovery Services-valvet](backup-azure-arm-vms.md)
> * [Säkerhetskopiera virtuella datorer till Backup-valvet](backup-azure-vms.md)
>
>

Den här artikeln innehåller procedurerna för att säkerhetskopiera en klassisk distribuerade Azure virtuell dator (VM) till en Backup-valvet. Det finns några uppgifter som du behöver ta hand om innan du kan säkerhetskopiera en virtuell Azure-dator. Om du inte redan har gjort Slutför den [krav](backup-azure-vms-prepare.md) att förbereda miljön för att säkerhetskopiera dina virtuella datorer.

Mer information finns i artiklar på [planerar din infrastruktur för VM-säkerhetskopiering i Azure](backup-azure-vms-introduction.md) och [virtuella Azure-datorer](https://azure.microsoft.com/documentation/services/virtual-machines/).

> [!NOTE]
> Azure har två distributionsmodeller som används för att skapa och arbeta med resurser: [Resource Manager och den klassiska distributionsmodellen](../azure-resource-manager/resource-manager-deployment-model.md). Ett säkerhetskopieringsvalv kan bara skydda klassisk distribuerade virtuella datorer. Du kan inte skydda Hanteraren för filserverresurser distribuerade virtuella datorer med en Backup-valvet. Se [säkerhetskopiera virtuella datorer till Recovery Services-valvet](backup-azure-arm-vms.md) mer information om hur du arbetar med Recovery Services-valv.
>
>

Säkerhetskopiering av virtuella Azure-datorer omfattar tre viktiga steg:

![Tre steg för att säkerhetskopiera en Azure IaaS-VM](./media/backup-azure-vms/3-steps-for-backup.png)

> [!NOTE]
> Säkerhetskopieringen av virtuella datorer är en lokal process. Du kan säkerhetskopiera virtuella datorer i en region för säkerhetskopieringsvalvet i en annan region. Så du måste skapa ett säkerhetskopieringsvalv i varje Azure-region där det finns virtuella datorer som ska säkerhetskopieras.
>
> [!IMPORTANT]
> Från och med mars 2017 kan du inte längre använda den klassiska portalen för att skapa säkerhetskopieringsvalv.
> Nu kan du uppgradera dina säkerhetskopieringsvalv till Recovery Services-valv. Mer information finns i artikeln [Upgrade a Backup vault to a Recovery Services vault](backup-azure-upgrade-backup-to-recovery-services.md) (Uppgradera ett säkerhetskopieringsvalv till ett Recovery Services-valv). Microsoft rekommenderar att du uppgraderar dina säkerhetskopieringsvalv till Recovery Services-valv.<br/> Efter den 30 November 2017 kommer du inte att kunna använda PowerShell för att skapa säkerhetskopieringsvalv. **Med 30 November 2017**:
>- Alla återstående säkerhetskopieringsvalv uppgraderas automatiskt till Recovery Services-valv.
>- Du kan inte längre komma åt dina säkerhetskopierade data i den klassiska portalen. Använd i stället Azure Portal till att få åtkomst till dina säkerhetskopierade data i Recovery Services-valv.
>

## <a name="step-1---discover-azure-virtual-machines"></a>Steg 1 – identifiera virtuella Azure-datorer
Om du vill se till att alla nya virtuella datorer (VM) lägga till prenumerationen identifieras innan du registrerar kör identifieringen. Under processen uppmanas Azure att returnera listan med virtuella datorer i prenumerationen, tillsammans med ytterligare information som molntjänstens namn och regionen.

1. Logga in på den [klassisk portal](http://manage.windowsazure.com/)
2. I listan över Azure-tjänster, klickar du på **återställningstjänster** att öppna listan över säkerhetskopiering och Site Recovery-valv.
    ![Öppna valvet lista](./media/backup-azure-vms/choose-vault-list.png)
3. Välj valvet för att säkerhetskopiera en virtuell dator i listan över säkerhetskopieringsvalv.

    Om det här är ett nytt valv portalen öppnas på den **Snabbstart** sidan.

    ![Öppna registrerade objekt-menyn](./media/backup-azure-vms/vault-quick-start.png)

    Om valvet tidigare har konfigurerats, öppnar portalen senast använda-menyn.
4. Klicka på menyn valvet (överst på sidan) **registrerade objekt**.

    ![Öppna registrerade objekt-menyn](./media/backup-azure-vms/vault-menu.png)
5. Välj  **Virtuell Azure-dator** på **Typ**-menyn.

    ![Välja arbetsbelastning](./media/backup-azure-vms/discovery-select-workload.png)
6. Klicka på **Identifiera** längst ned på sidan.
    ![Knappen Identifiera](./media/backup-azure-vms/discover-button-only.png)

    Identifieringen kan ta några minuter medan de virtuella datorerna visas i tabellformat. Ett meddelande visas längst ned på skärmen som anger att processen körs.

    ![Identifiera virtuella datorer](./media/backup-azure-vms/discovering-vms.png)

    Meddelandet ändras när processen är klar. Om identifieringen inte gick att hitta de virtuella datorerna, först se till att de virtuella datorerna finns. Om de virtuella datorerna finns, se till att de virtuella datorerna finns i samma region som säkerhetskopieringsvalvet. Om de virtuella datorerna finns och är i samma region, se till att de virtuella datorerna inte redan har registrerats ett säkerhetskopieringsvalv. Om en virtuell dator har tilldelats ett säkerhetskopieringsvalv som den inte är tillgänglig som ska tilldelas andra säkerhetskopieringsvalv.

    ![Identifieringen är klar](./media/backup-azure-vms/discovery-complete.png)

    När du har identifierat nya objekt, gå till steg 2 och registrera dina virtuella datorer.

## <a name="step-2---register-azure-virtual-machines"></a>Steg 2 – registrera virtuella Azure-datorer
Registrerar du en virtuell Azure-dator för att associera den med Azure Backup-tjänsten. Detta inträffar vanligtvis en gång.

1. Navigera till säkerhetskopieringsvalvet under **återställningstjänster** i Azure-portalen och klicka sedan på **registrerade objekt**.
2. Välj **Virtuell Azure-dator** i listrutan.

    ![Välja arbetsbelastning](./media/backup-azure-vms/discovery-select-workload.png)
3. Klicka på **Registrera** längst ned på sidan.
    ![Knappen Registrera](./media/backup-azure-vms/register-button-only.png)
4. På snabbmenyn **Registrera objekt** väljer du de virtuella datorer som du vill registrera. Om det finns två eller flera virtuella datorer med samma namn, kan du använda Molntjänsten för att skilja dem åt.

   > [!TIP]
   > Flera virtuella datorer kan registreras samtidigt.
   >
   >

    Ett jobb skapas för varje virtuell dator som du har valt.
5. Klicka på **Visa jobb** i meddelandet för att gå till sidan **Jobb**.

    ![Registrera jobb](./media/backup-azure-vms/register-create-job.png)

    Den virtuella datorn visas också i listan över registrerade objekt, tillsammans med statusen för registreringsåtgärden.

    ![Registreringsstatus 1](./media/backup-azure-vms/register-status01.png)

    När åtgärden har slutförts ändras statusen för att återspegla tillståndet för *registrerade objekt*.

    ![Registreringsstatus 2](./media/backup-azure-vms/register-status02.png)

## <a name="step-3---protect-azure-virtual-machines"></a>Steg 3 – skydda virtuella Azure-datorer
Du kan nu konfigurera en princip för säkerhetskopiering och kvarhållning för den virtuella datorn. Flera virtuella datorer kan skyddas med hjälp av en enda skydda åtgärd.

Azure-säkerhetskopieringsvalv skapas efter maj 2015 medföljer en standardprincip inbyggda i valvet. Den här standardprincipen innehåller en Standardkvarhållning av 30 dagar och ett schema för säkerhetskopiering av en gång dagligen.

1. Navigera till säkerhetskopieringsvalvet under **återställningstjänster** i Azure-portalen och klicka sedan på **registrerade objekt**.
2. Välj **Virtuell Azure-dator** i listrutan.

    ![Välja arbetsbelastning på portalen](./media/backup-azure-vms/select-workload.png)
3. Klicka på **Skydda** längst ned på sidan.

    Den **skydda objekt guiden** visas. Guiden visar endast virtuella datorer som registrerats och inte skyddas. Välj de virtuella datorer som du vill skydda.

    Om det finns två eller flera virtuella datorer med samma namn, kan du använda Molntjänsten för att skilja mellan de virtuella datorerna.

   > [!TIP]
   > Du kan skydda flera virtuella datorer samtidigt.
   >
   >

    ![Konfigurera skydd i stor skala](./media/backup-azure-vms/protect-at-scale.png)

4. Välj en **Säkerhetskopieringsschemat** att säkerhetskopiera virtuella datorer som du har valt. Du kan välja från en befintlig uppsättning principer eller definiera en ny.

    Flera virtuella datorer kan vara associerade med varje säkerhetskopieringspolicy. Den virtuella datorn kan dock bara vara kopplad till en princip vid en viss tidpunkt.

    ![Skydda med ny princip](./media/backup-azure-vms/policy-schedule.png)

   > [!NOTE]
   > En säkerhetskopieringspolicy innehåller ett kvarhållningsschema för de schemalagda säkerhetskopieringarna. Om du väljer en säkerhetskopieringsprincip kan ändra du inte alternativ för kvarhållning i nästa steg.
   >
   >

5. Välj en **Kvarhållningsintervall** ska associeras med säkerhetskopiorna.

    ![Skydda med flexibel kvarhållning](./media/backup-azure-vms/policy-retention.png)

    Bevarandeprincipen anger hur länge en säkerhetskopia ska lagras. Du kan ange andra bevarandeprinciper baserat på när säkerhetskopieringen görs. Exempelvis kan en säkerhetskopieringspunkt tas dagligen (som fungerar som en operativa återställningspunkt) bevaras i 90 dagar. Jämförelse behöva en säkerhetskopieringspunkt vidtas i slutet av varje kvartal (som audit) bevaras för många månader eller år.

    ![Virtuella datorer säkerhetskopieras med återställningspunkter](./media/backup-azure-vms/long-term-retention.png)

    I det här exemplet avbildningen:

   * **Dagliga bevarandeprincip**: säkerhetskopieringar tas dagligen sparas i 30 dagar.
   * **Varje vecka bevarandeprincip**: säkerhetskopior som gjorts varje vecka på söndag bevaras i 104 veckor.
   * **Månatliga bevarandeprincip**: säkerhetskopieringar som utförts på den sista söndagen i varje månad bevaras i 120 månader.
   * **Årlig bevarandeprincip**: säkerhetskopieringar som utförts på den första söndagen i varje januari bevaras för 99 år.

     Ett jobb skapas för att konfigurera protection-principen och koppla de virtuella datorerna till principen för varje virtuell dator som du har valt.
6. Visa lista med **Konfigurera skydd** jobb valv-menyn klickar du på **jobb** och välj **Konfigurera skydd** från den **åtgärden** filter.

    ![Jobbet Konfigurera skydd](./media/backup-azure-vms/protect-configureprotection.png)

## <a name="initial-backup"></a>Den första säkerhetskopieringen
När den virtuella datorn är skyddad med en princip som det visas under den **skyddade objekt** flik med status för *skyddade - (väntar på inledande säkerhetskopia)*. Som standard är den första säkerhetskopieringen den *inledande säkerhetskopieringen*.

Utlöser den första säkerhetskopian omedelbart när du har konfigurerat skyddet:

1. Längst ned i den **skyddade objekt** klickar du på **säkerhetskopiering nu**.

    Tjänsten Azure Backup skapar ett säkerhetskopieringsjobb för den första säkerhetskopieringen.
2. Klicka på fliken **Jobb** för att visa listan över jobb.

    ![Säkerhetskopiering pågår](./media/backup-azure-vms/protect-inprogress.png)

> [!NOTE]
> Under säkerhetskopieringen utfärdar tjänsten Azure Backup ett kommando till reservanknytning i varje virtuell dator att tömma alla jobb för skrivning och dra en programkonsekvent ögonblicksbild.
>
>

När den första säkerhetskopian är klar, status för den virtuella datorn i den **skyddade objekt** är *skyddade*.

![Virtuella datorer säkerhetskopieras med återställningspunkter](./media/backup-azure-vms/protect-backedupvm.png)

## <a name="viewing-backup-status-and-details"></a>Visa information och status för säkerhetskopiering
När skyddade, ökar antalet virtuella datorer även i den **instrumentpanelen** sidan Sammanfattning. Den **instrumentpanelen** också visar antalet jobb från de senaste 24 timmarna som var *lyckade*, har *misslyckades*, och är *pågår*. På den **jobb** använder den **Status**, **åtgärden**, eller **från** och **till** menyer att filtrera jobb.

![Status för säkerhetskopiering i instrumentpanelens sida](./media/backup-azure-vms/dashboard-protectedvms.png)

Värdena i instrumentpanelen uppdateras var 24: e timme.

## <a name="troubleshooting-errors"></a>Felsökning av fel
Om du stöter på problem under säkerhetskopieringen av virtuell dator granskar du den [VM felsökningsartikel](backup-azure-vms-troubleshoot.md) för hjälp.

## <a name="next-steps"></a>Nästa steg
* [Hantera och övervaka dina virtuella datorer](backup-azure-manage-vms.md)
* [Återställa virtuella datorer](backup-azure-restore-vms.md)
