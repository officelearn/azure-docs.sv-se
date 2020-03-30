---
title: Säkerhetskopiera filer i virtuella Azure Stack-datorer
description: Använd Azure Backup för att säkerhetskopiera och återställa Azure Stack-filer och program till din Azure Stack-miljö.
ms.topic: conceptual
ms.date: 06/05/2018
ms.openlocfilehash: 2bcdf7f720708db1487d7d5cdaee41dc93c05728
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74172320"
---
# <a name="back-up-files-and-applications-on-azure-stack"></a>Säkerhetskopiera filer och program på Azure Stack

Du kan använda Azure Backup för att skydda (eller säkerhetskopiera) filer och program på Azure Stack. Om du vill säkerhetskopiera filer och program installerar du Microsoft Azure Backup Server som en virtuell dator som körs på Azure Stack. Du kan skydda filerna på alla Azure Stack-servrar i samma virtuella nätverk. När du har installerat Azure Backup Server lägger du till Azure-diskar för att öka det lokala lagringsutrymmet som är tillgängligt för kortsiktiga säkerhetskopieringsdata. Azure Backup Server använder Azure-lagring för långsiktig kvarhållning.

> [!NOTE]
> Även om DPM (Azure Backup Server och System Center Data Protection Manager) är liknande, stöds inte DPM för användning med Azure Stack.
>

Den här artikeln omfattar inte installation av Azure Backup Server i Azure Stack-miljön. Information om hur du installerar Azure Backup Server på Azure Stack finns i artikeln [Installera Azure Backup Server](backup-mabs-install-azure-stack.md).

## <a name="back-up-files-and-folders-in-azure-stack-vms-to-azure"></a>Säkerhetskopiera filer och mappar i virtuella Azure Stack-datorer till Azure

Om du vill konfigurera Azure Backup Server för att skydda filer i virtuella Azure Stack-datorer öppnar du Azure Backup Server-konsolen. Du använder konsolen för att konfigurera skyddsgrupper och skydda data på dina virtuella datorer.

1. Klicka på **Skydd** i Azure Backup Server-konsolen och klicka på **Nytt** i verktygsfältet för att öppna guiden **Skapa nytt skydd.**

   ![Konfigurera skydd i Azure Backup Server-konsolen](./media/backup-mabs-files-applications-azure-stack/1-mabs-menu-create-protection-group.png)

    Det kan ta några sekunder innan guiden öppnas. När guiden har öppnats klickar du på **Nästa** för att gå vidare till skärmen **Välj skyddsgrupptyp.**

   ![Guiden Ny skyddsgrupp öppnas](./media/backup-mabs-files-applications-azure-stack/2-create-new-protection-group-wiz.png)

2. På skärmen **Välj skydd grupptyp** väljer du **Servrar** och klickar på **Nästa**.

    ![Guiden Ny skyddsgrupp öppnas](./media/backup-mabs-files-applications-azure-stack/3-select-protection-group-type.png)

    Skärmen **Välj gruppmedlemmar** öppnas.

    ![Guiden Ny skyddsgrupp öppnas](./media/backup-mabs-files-applications-azure-stack/4-opening-screen-choose-servers.png)

3. På skärmen **Välj gruppmedlemmar** klickar du för **+** att expandera listan med underobjekt. Markera kryssrutan för alla objekt som du vill skydda. När alla objekt har markerats klickar du på **Nästa**.

    ![Guiden Ny skyddsgrupp öppnas](./media/backup-mabs-files-applications-azure-stack/5-select-group-members.png)

    Microsoft rekommenderar att alla data som ska delas en skyddsprincip läggs till i en skyddsgrupp. Fullständig information om planering och distribution av skyddsgrupper finns i artikeln System Center DPM, [Deploy Protection Groups](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-1801).

4. Skriv ett namn för skyddsgruppen på skärmen **Välj dataskyddsmetod.** Markera kryssrutan för **jag vill ha kortsiktigt skydd med hjälp av:** och jag vill ha **online-skydd**. Klicka på **Nästa**.

    ![Guiden Ny skyddsgrupp öppnas](./media/backup-mabs-files-applications-azure-stack/6-select-data-protection-method.png)

    För att välja **Jag vill ha online-skydd,** måste du först välja **Jag vill ha kortsiktigt skydd med:** Disk. Azure Backup Server skyddar inte band, så disk är det enda valet för kortsiktigt skydd.

5. På skärmen **Ange kortsiktiga mål** väljer du hur länge återställningspunkterna ska sparas på disken och när inkrementella säkerhetskopior ska sparas. Klicka på **Nästa**.

    > [!IMPORTANT]
    > Du bör **inte** behålla data för återställning av drift (säkerhetskopiering) på Azure Backup Server-anslutna diskar i mer än fem dagar.
    >

    ![Guiden Ny skyddsgrupp öppnas](./media/backup-mabs-files-applications-azure-stack/7-select-short-term-goals.png)

    I stället för att välja ett intervall för inkrementella säkerhetskopior klickar du på **Precis före en återställningspunkt**om du vill köra en snabb fullständig säkerhetskopiering strax före varje schemalagd återställningspunkt. Om du skyddar programarbetsbelastningar skapar Azure Backup Server återställningspunkter per schemat för synkroniseringsfrekvens (förutsatt att programmet stöder inkrementella säkerhetskopieringar). Om programmet inte stöder inkrementella säkerhetskopior kör Azure Backup Server en snabb fullständig säkerhetskopiering.

    För **Återställningspunkter för filer**anger du när återställningspunkter ska skapas. Klicka på **Ändra** om du vill ange tider och dagar i veckan när återställningspunkter skapas.

6. På skärmen **Granska diskallokering** granskar du det lagringsutrymme för lagringspool som tilldelats för skyddsgruppen.

    **Total datastorlek** är storleken på de data som du vill säkerhetskopiera och **diskutrymme som ska etableras** på Azure Backup Server är det rekommenderade utrymmet för skyddsgruppen. Azure Backup Server väljer den perfekta säkerhetskopieringsvolymen, baserat på inställningarna. Du kan dock redigera valen av säkerhetskopieringsvolym under Disk allocation details (Diskallokeringsdetaljer). Välj önskad lagringsplats för arbetsbelastningarna i den nedrullningsbara menyn. Redigeringarna ändrar värdena för Totalt lagringsutrymme och Ledigt lagringsutrymme i fönstret Tillgängligt disklagringsutrymme. Underetablerat utrymme är mängden lagringsutrymme Azure Backup Server föreslår att du lägger till volymen, för att fortsätta med säkerhetskopior smidigt i framtiden.

7. Välj hur du vill hantera den första fullständiga datareplikeringen i Välj metod för att **skapa repliker.** Om du bestämmer dig för att replikera över nätverket rekommenderar Azure att du väljer en lågtrafiktid. För stora mängder data eller mindre än optimala nätverksvillkor bör du överväga att replikera data med flyttbara media.

8. I **Välj alternativ för konsekvenskontroll** väljer du hur du vill automatisera konsekvenskontroller. Aktivera konsekvenskontroller så att de bara körs när datareplikering blir inkonsekvent eller enligt ett schema. Om du inte vill konfigurera automatisk konsekvenskontroll kör du en manuell kontroll när som helst genom att:
    * Högerklicka på skyddsgruppen i **skyddsområdet** för Azure Backup Server-konsolen och välj **Utför konsekvenskontroll**.

9. Om du väljer att säkerhetskopiera till Azure, på sidan **Ange dataskyddsdata,** se till att de arbetsbelastningar som du vill säkerhetskopiera till Azure är valda.

10. I **Ange online-säkerhetskopieringsschema**anger du när inkrementella säkerhetskopior till Azure ska ske.

    Du kan schemalägga säkerhetskopieringar så att de körs varje dag/vecka/månad/år och ange vilken tid/datum de ska köras. Säkerhetskopieringar kan göras upp till två gånger per dag. Varje gång ett säkerhetskopieringsjobb körs skapas en dataåterställningspunkt i Azure från kopian av säkerhetskopierade data som lagras på Azure Backup Server-disken.

11. I **Ange onlinelagringsprincipen**anger du hur återställningspunkterna som skapas från de dagliga/veckovisa/månatliga/årliga säkerhetskopiorna behålls i Azure.

12. Ange hur den första fullständiga replikeringen av data ska ske i **Välj onlinereplikering.**

13. Granska inställningarna i **Sammanfattning.** När du klickar på **Skapa grupp**sker den första datareplikeringen. När datareplikeringen är klar visas skyddsgruppstatusen **som OK**på sidan **Status** . Det första säkerhetskopieringsjobbet sker i linje med skyddsgruppsinställningarna.

## <a name="recover-file-data"></a>Återställa fildata

Använd Azure Backup Server-konsolen för att återställa data till din virtuella dator.

1. Klicka på **Återställning** i navigeringsfältet i Azure Backup Server-konsolen och bläddra efter de data som du vill återställa. Välj dessa data i resultatfönstret.

2. I kalendern i avsnittet återställningspunkter anger datum i fetstil återställningspunkter som är tillgängliga. Välj det datum som ska återställas.

3. Markera det objekt som du vill återställa i **fönstret Återvinningsbara objekt.**

4. Klicka på **Återställ** i fönstret **Åtgärder** för att öppna återställningsguiden.

5. Så här kan du återställa data:

    * **Återställ till den ursprungliga platsen** - Om klientdatorn är ansluten via VPN fungerar inte det här alternativet. Använd i stället en alternativ plats och kopiera sedan data från den platsen.
    * **Återställa till en alternativ plats**

6. Ange återställningsalternativ:

    * För **befintligt versionsåterställningsbeteende**väljer du **Skapa kopia,** **Hoppa över**eller **Skriva över**. Överskrivning är endast tillgängligt när du återställer till den ursprungliga platsen.
    * Om **du vill återställa säkerhet**väljer du Använd inställningar för **måldatorn** eller **Använd säkerhetsinställningarna för återställningspunktsversionen**.
    * Klicka **Network bandwidth usage throttling**på **Ändra** för att aktivera begränsning av nätverksbandbredd.
    * **Anmälan** Klicka på **Skicka ett e-postmeddelande när återställningen är klar**och ange vilka mottagare som ska få meddelandet. Avgränsa e-postadresserna med kommatecken.
    * När du har gjort valen klickar du på **Nästa**

7. Granska återställningsinställningarna och klicka på **Återställ**.

    >[!Note]
    >Medan återställningsjobbet pågår avbryts alla synkroniseringsjobb för de valda återställningsobjekten.

Om du använder MBS (Modern Backup Storage), stöds inte återställning av filserverslutanvändare (EUR). File Server EUR har ett beroende av VSS (Volume Shadow Copy Service), som modern säkerhetskopieringslagring inte använder. Om EUR är aktiverat använder du följande steg för att återställa data:

1. Navigera till de skyddade filerna och högerklicka på filnamnet och välj **Egenskaper**.

2. Klicka på Tidigare **versioner** på **Egenskapsmenyn** och välj den version som du vill återställa.

## <a name="view-azure-backup-server-with-a-vault"></a>Visa Azure Backup Server med ett valv

Så här visar du Azure Backup Server-entiteter i Azure-portalen:

1. Öppna valvet för återställningstjänster.
2. Klicka på Infrastruktur för säkerhetskopiering.
3. Visa servrar för säkerhetskopieringshantering.

## <a name="next-steps"></a>Nästa steg

Information om hur du använder Azure Backup Server för att skydda andra arbetsbelastningar finns i någon av följande artiklar:

* [Säkerhetskopiera SharePoint-servergrupp](https://docs.microsoft.com/azure/backup/backup-mabs-sharepoint-azure-stack)
* [Säkerhetskopiera SQL-servrar](https://docs.microsoft.com/azure/backup/backup-mabs-sql-azure-stack)
