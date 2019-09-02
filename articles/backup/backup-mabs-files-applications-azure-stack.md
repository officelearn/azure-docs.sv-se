---
title: Säkerhetskopiera filer i Azure Stack virtuella datorer
description: Använd Azure Backup för att säkerhetskopiera och återställa Azure Stack filer och program till din Azure Stack-miljö.
ms.reviewer: adigan
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 06/05/2018
ms.author: dacurwin
ms.openlocfilehash: d9e7aaca99e551e17e8b4be5ef6146a19e44355f
ms.sourcegitcommit: d470d4e295bf29a4acf7836ece2f10dabe8e6db2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/02/2019
ms.locfileid: "70210208"
---
# <a name="back-up-files-and-applications-on-azure-stack"></a>Säkerhetskopiera filer och program på Azure Stack

Du kan använda Azure Backup för att skydda (eller säkerhetskopiera) filer och program på Azure Stack. Om du vill säkerhetskopiera filer och program installerar du Microsoft Azure Backup server som en virtuell dator som körs på Azure Stack. Du kan skydda filerna på alla Azure Stack-servrar i samma virtuella nätverk. När du har installerat Azure Backup Server lägger du till Azure-diskar för att öka den lokala lagrings platsen för säkerhets kopierings data på kort sikt. Azure Backup Server använder Azure Storage för långsiktig kvarhållning.

> [!NOTE]
> Även om Azure Backup Server och System Center Data Protection Manager (DPM) liknar, stöds inte DPM för användning med Azure Stack.
>

Den här artikeln beskriver inte hur du installerar Azure Backup Server i Azure Stacks miljön. Information om hur du installerar Azure Backup Server på Azure Stack finns i artikeln [installera Azure Backup Server](backup-mabs-install-azure-stack.md).

## <a name="back-up-files-and-folders-in-azure-stack-vms-to-azure"></a>Säkerhetskopiera filer och mappar i Azure Stack virtuella datorer till Azure

Om du vill konfigurera Azure Backup Server för att skydda filer i Azure Stack virtuella datorer öppnar du Azure Backup Server-konsolen. Du använder-konsolen för att konfigurera skydds grupper och för att skydda data på dina virtuella datorer.

1. I Azure Backup Server-konsolen klickar du på **skydd** och i verktygsfältet klickar du på **nytt** för att öppna guiden **Skapa ny skydds grupp** .

   ![Konfigurera skydd i Azure Backup Server-konsolen](./media/backup-mabs-files-applications-azure-stack/1-mabs-menu-create-protection-group.png)

    Det kan ta några sekunder innan guiden öppnas. När guiden öppnas klickar du på **Nästa** för att gå till sidan **Välj typ av skydds grupp** .

   ![Guiden ny skydds grupp öppnas](./media/backup-mabs-files-applications-azure-stack/2-create-new-protection-group-wiz.png)

2. På skärmen **Välj typ av skydds grupp** väljer du **servrar** och klickar på **Nästa**.

    ![Guiden ny skydds grupp öppnas](./media/backup-mabs-files-applications-azure-stack/3-select-protection-group-type.png)

    Sidan **Välj grupp medlemmar** öppnas.

    ![Guiden ny skydds grupp öppnas](./media/backup-mabs-files-applications-azure-stack/4-opening-screen-choose-servers.png)

3. På skärmen **Välj grupp medlemmar** klickar du på **+** för att expandera listan över under objekt. Markera kryss rutan för alla objekt som du vill skydda. När alla objekt har marker ATS klickar du på **Nästa**.

    ![Guiden ny skydds grupp öppnas](./media/backup-mabs-files-applications-azure-stack/5-select-group-members.png)

    Microsoft rekommenderar att du placerar alla data som ska dela en skydds princip i en skydds grupp. Fullständig information om hur du planerar och distribuerar skydds grupper finns i artikeln System Center DPM, [distribuera skydds grupper](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-1801).

4. På skärmen **Välj data skydds metod** anger du ett namn på skydds gruppen. Markera kryss rutan för **Jag vill använda kortsiktigt skydd med:** och **Jag vill ha onlineskydd**. Klicka på **Nästa**.

    ![Guiden ny skydds grupp öppnas](./media/backup-mabs-files-applications-azure-stack/6-select-data-protection-method.png)

    Om du vill välja att **Jag vill ha onlineskydd**måste du först välja **Jag vill ha kortsiktigt skydd med:** Diskdefragmenter. Azure Backup Server skyddar inte på band, så disk är det enda alternativet för kortsiktigt skydd.

5. På skärmen **Ange kortvariga mål** väljer du hur länge du vill behålla återställnings punkterna som sparas på disk och när du ska spara stegvisa säkerhets kopior. Klicka på **Nästa**.

    > [!IMPORTANT]
    > Du bör **inte** behålla återställnings data (säkerhets kopiering) på Azure Backup Server anslutna diskar i mer än fem dagar.
    >

    ![Guiden ny skydds grupp öppnas](./media/backup-mabs-files-applications-azure-stack/7-select-short-term-goals.png)

    I stället för att välja ett intervall för stegvisa säkerhets kopieringar för att köra en fullständig snabb säkerhets kopiering precis innan varje schemalagd återställnings punkt klickar du på **precis innan en återställnings punkt**. Om du skyddar program arbets belastningar skapar Azure Backup Server återställnings punkter enligt schemat för synkroniseringsfrekvens (förutsatt att programmet stöder stegvis säkerhets kopiering). Om programmet inte har stöd för stegvisa säkerhets kopieringar kör Azure Backup Server en fullständig snabb säkerhets kopiering.

    Ange när återställnings punkter ska skapas i **fil återställnings punkter**. Klicka på **ändra** för att ange tider och dagar i veckan när återställnings punkter skapas.

6. På skärmen **Granska diskallokering** granskar du disk utrymmet för lagringspoolen som allokerats för skydds gruppen.

    **Total data storlek** är storleken på de data som du vill säkerhetskopiera och **disk utrymmet som** ska tillhandahållas på Azure Backup Server är det rekommenderade utrymmet för skydds gruppen. Azure Backup Server väljer den ideala säkerhets kopierings volymen baserat på inställningarna. Du kan dock redigera säkerhets kopierings volym alternativen i disk tilldelnings informationen. För arbets belastningarna väljer du önskad lagring i list menyn. Dina ändringar ändrar värdena för total lagring och ledigt lagrings utrymme i fönstret tillgängliga Disklagring. Underetablerat utrymme är mängden lagrings Azure Backup Server föreslår att du lägger till volymen, för att fortsätta med säkerhets kopieringar smidigt i framtiden.

7. I **Välj metod för skapande av replik**väljer du hur du vill hantera den första fullständiga datareplikeringen. Om du väljer att replikera över nätverket rekommenderar Azure att du väljer en tid med låg belastning. Överväg att replikera data med hjälp av flyttbara media för stora mängder data eller mindre än optimala nätverks förhållanden.

8. I **Välj alternativ för konsekvens kontroll**väljer du hur du vill automatisera konsekvens kontroller. Aktivera konsekvens kontroller för att endast köra när datareplikeringen blir inkonsekvent eller enligt ett schema. Om du inte vill konfigurera automatisk konsekvens kontroll kör du en manuell kontroll när som helst genom att:
    * I **skydds** delen i Azure Backup Server-konsolen högerklickar du på skydds gruppen och väljer **utför konsekvens kontroll**.

9. Om du väljer att säkerhetskopiera till Azure, på sidan **ange data** för onlineskydd, se till att de arbets belastningar som du vill säkerhetskopiera till Azure är markerade.

10. I **Ange schema för onlinesäkerhetskopiering**anger du när stegvisa säkerhets kopieringar till Azure ska ske.

    Du kan schemalägga säkerhets kopieringar så att de körs varje dag/vecka/månad/år och tiden/datumet då de ska köras. Säkerhets kopieringar kan göras upp till två gånger per dag. Varje gång ett säkerhets kopierings jobb körs skapas en data återställnings punkt i Azure från kopian av säkerhetskopierade data som lagras på den Azure Backup Server disken.

11. Ange hur återställnings punkterna som skapas med säkerhets kopiorna per dag/vecka/månad/år ska behållas i Azure i **Ange bevarande princip för online**.

12. I **Välj online-replikering**anger du hur den första fullständiga replikeringen av data ska ske.

13. Granska inställningarna i **Sammanfattning**. När du klickar på **Skapa grupp**sker den första datareplikeringen. När datareplikeringen är klar visas skydds gruppens status som **OK**på sidan **status** . Det första säkerhets kopierings jobbet sker i enlighet med inställningarna för skydds gruppen.

## <a name="recover-file-data"></a>Återställa fildata

Använd Azure Backup Server-konsolen för att återställa data till den virtuella datorn.

1. Klicka på **återställning** i navigerings fältet i Azure Backup Server-konsolen och bläddra efter de data som du vill återställa. I resultat fönstret väljer du data.

2. I kalendern i avsnittet återställnings punkter är datum i fetstil att återställnings punkter är tillgängliga. Välj det datum som ska återställas.

3. I fönstret **återställnings Bart objekt** väljer du det objekt som du vill återställa.

4. I rutan **åtgärder** klickar du på **Återställ** för att öppna återställnings guiden.

5. Du kan återställa data på följande sätt:

    * **Återställ till den ursprungliga platsen** – om klient datorn är ansluten via VPN fungerar inte det här alternativet. Använd i stället en alternativ plats och kopiera sedan data från den platsen.
    * **Återställa till en alternativ plats**

6. Ange återställnings alternativ:

    * För **återställning av befintliga versioner**väljer du **Skapa kopia**, **hoppa över**eller **Skriv över**. Överskrivning är bara tillgängligt när du återställer till den ursprungliga platsen.
    * För **återställning av säkerhet**väljer du **tillämpa inställningar på mål datorn** eller **tillämpar säkerhets inställningarna för återställnings punkt versionen**.
    * För **begränsning av nätverks bandbredd**klickar du på **ändra** för att aktivera begränsning av nätverks bandbredd.
    * **Meddelande** Klicka på **Skicka ett e-postmeddelande när återställningen är slutförd**och ange de mottagare som ska ta emot meddelandet. Avgränsa e-postadresserna med kommatecken.
    * När du har gjort dina val klickar du på **Nästa**

7. Granska återställnings inställningarna och klicka på **Återställ**.

    >[!Note]
    >Medan återställnings jobbet pågår avbryts alla synkroniseringsjobb för de valda återställnings objekten.

Om du använder Modern Backup Storage (MB) stöds inte återställning av fil Server slut användar återställning (EUR). Fil Server EUR har ett beroende på tjänsten Volume Shadow Copy (VSS) som Modern Backup Storage inte använder. Om EUR är aktiverat använder du följande steg för att återställa data:

1. Navigera till de skyddade filerna och högerklicka på fil namnet och välj **Egenskaper**.

2. På menyn **Egenskaper** klickar du på **tidigare versioner** och väljer den version som du vill återställa.

## <a name="view-azure-backup-server-with-a-vault"></a>Visa Azure Backup Server med ett valv

Du kan visa Azure Backup Server entiteter i Azure Portal genom att följa stegen nedan:

1. Öppna Recovery Services valv.
2. Klicka på infrastruktur för säkerhets kopiering.
3. Visa säkerhets kopierings hanterings servrar.

## <a name="see-also"></a>Se också

Information om hur du använder Azure Backup Server för att skydda andra arbets belastningar finns i någon av följande artiklar:

* [Säkerhetskopiera SharePoint-servergrupp](https://docs.microsoft.com/azure/backup/backup-mabs-sharepoint-azure-stack)
* [Säkerhetskopiera SQL Server](https://docs.microsoft.com/azure/backup/backup-mabs-sql-azure-stack)
