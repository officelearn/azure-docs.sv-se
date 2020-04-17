---
title: Hantera och övervaka SQL Server-DB:er på en virtuell Azure-dator
description: I den här artikeln beskrivs hur du hanterar och övervakar SQL Server-databaser som körs på en Virtuell Azure.This article describes how to manage and monitor SQL Server databases that are running on an Azure VM.
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 14e3a4797fe60a3d1857f1e6d947fa0c669bdcfe
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537312"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>Hantera och övervaka säkerhetskopierade SQL Server-databaser

I den här artikeln beskrivs vanliga uppgifter för att hantera och övervaka SQL Server-databaser som körs på en virtuell Azure-dator (VM) och som säkerhetskopieras till ett Azure Backup Recovery Services-valv av [Azure Backup-tjänsten.](backup-overview.md) Du får lära dig hur du övervakar jobb och aviseringar, stoppar och återupptar databasskydd, kör säkerhetskopieringsjobb och avregistrerar en virtuell dator från säkerhetskopior.

Om du ännu inte har konfigurerat säkerhetskopior för SQL Server-databaser läser du [Säkerhetskopiera SQL Server-databaser på virtuella Azure-datorer](backup-azure-sql-database.md)

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>Övervaka manuella säkerhetskopieringsjobb i portalen

Azure Backup visar alla manuellt utlösta jobb i portalen **för säkerhetskopieringjobb.** Jobben som visas i den här portalen inkluderar databasidentifiering och registrering samt säkerhetskopierings- och återställningsåtgärder.

![Portalen För säkerhetskopieringsjobb](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> Portalen **Säkerhetskopiering jobb** visar inte schemalagda säkerhetskopieringsjobb. Använd SQL Server Management Studio för att övervaka schemalagda säkerhetskopieringsjobb, enligt beskrivningen i nästa avsnitt.
>

Mer information om övervakningsscenarier finns [i Övervakning i Azure-portalen](backup-azure-monitoring-built-in-monitor.md) och [övervakning med Azure Monitor](backup-azure-monitoring-use-azuremonitor.md).  

## <a name="view-backup-alerts"></a>Visa säkerhetskopieringsaviseringar

Eftersom säkerhetskopieringar av loggar inträffar var 15:e minut kan det vara tråkigt att övervaka säkerhetskopieringsjobb. Azure Backup underlättar övervakning genom att skicka e-postmeddelanden. E-postaviseringar är:

- Utlöses för alla säkerhetskopieringsfel.
- Konsoliderad på databasnivå efter felkod.
- Skickas endast för en databass första säkerhetskopieringsfel.

Så här övervakar du aviseringar om säkerhetskopiering av databaser:

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. På instrumentpanelen i valvet väljer du **Aviseringar och händelser**.

   ![Välja aviseringar och händelser](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

3. I **Aviseringar och händelser**väljer du **Säkerhetsaviseringar**.

   ![Välja säkerhetskopieringsaviseringar](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

## <a name="stop-protection-for-a-sql-server-database"></a>Stoppa skydd för en SQL-serverdatabas

Du kan sluta säkerhetskopiera en SQL Server-databas på ett par olika sätt:

- Stoppa alla framtida säkerhetskopieringsjobb och ta bort alla återställningspunkter.
- Stoppa alla framtida säkerhetskopieringsjobb och lämna återställningspunkterna intakta.

Om du väljer att lämna återställningspunkter bör du tänka på följande:

- Alla återställningspunkter förblir intakta för alltid, all beskärning ska stanna vid stoppskyddet med lagringsdata.
- Du debiteras för den skyddade instansen och förbrukad lagring. Mer information finns i [Azure Backup-priser](https://azure.microsoft.com/pricing/details/backup/).
- Om du tar bort en datakälla utan att stoppa säkerhetskopior misslyckas nya säkerhetskopior. Gamla återställningspunkter upphör att gälla enligt principen, men en sista återställningspunkt kommer alltid att behållas tills du stoppar säkerhetskopiorna och tar bort data.

Så här stoppar du skydd för en databas:

1. På instrumentpanelen för valvet väljer du **Säkerhetskopieringsobjekt**.

2. Under **Hantering av säkerhetskopiering**väljer du **SQL i Azure VM**.

    ![Välja SQL på Azure VM](./media/backup-azure-sql-database/sql-restore-backup-items.png)

3. Välj den databas som du vill stoppa skyddet för.

    ![Välja den databas som skydd ska stoppas för](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

4. På databasmenyn väljer du **Stoppa säkerhetskopiering**.

    ![Välja Avbryt säkerhetskopiering](./media/backup-azure-sql-database/stop-db-button.png)

5. På menyn **Stoppa säkerhetskopiering** väljer du om data ska behållas eller tas bort. Om du vill kan du ange en orsak och kommentar.

    ![Behålla eller ta bort data på menyn Stoppa säkerhetskopiering](./media/backup-azure-sql-database/stop-backup-button.png)

6. Välj **Stoppa säkerhetskopiering**.

> [!NOTE]
>
>Mer information om alternativet ta bort data finns i vanliga frågor och svar nedan:
>
>- [Vad händer med säkerhetskopiorna om jag tar bort en databas från en instans med automatiskt skydd?](faq-backup-sql-server.md#if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups)
>- [Om jag stoppar säkerhetskopiering av en databas med autoskyddat, vad kommer det att fungera?](faq-backup-sql-server.md#if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior)
>
>

## <a name="resume-protection-for-a-sql-database"></a>Återuppta skyddet för en SQL-databas

När du stoppar skyddet för SQL-databasen kan du senare återuppta skyddet om du väljer alternativet **Behåll säkerhetskopierade data.** Om du inte behåller säkerhetskopieringsdata kan du inte återuppta skyddet.

Så här återupptar du skyddet för en SQL-databas:

1. Öppna säkerhetskopian och välj **Återuppta säkerhetskopiering**.

    ![Välj Resume backup (Återuppta säkerhetskopiering) för att återuppta databasskyddet](./media/backup-azure-sql-database/resume-backup-button.png)

2. På menyn **Säkerhetskopieringspolicy** väljer du en policy och sedan **Spara**.

## <a name="run-an-on-demand-backup"></a>Kör en säkerhetskopiering på begäran

Du kan köra olika typer av säkerhetskopieringar på begäran:

- Fullständig säkerhetskopia
- Fullständig säkerhetskopia med endast kopiering
- Differentiell säkerhetskopia
- Loggsäkerhetskopia

Du måste ange kvarhållningstiden för fullständig säkerhetskopiering endast för kopia, men kvarhållningsintervallet för fullständig säkerhetskopiering på begäran ställs automatiskt in på 45 dagar från den aktuella tiden.

Mer information finns i [SQL Server-typer av säkerhetskopiering](backup-architecture.md#sql-server-backup-types).

## <a name="unregister-a-sql-server-instance"></a>Avregistrera en SQL-serverinstans

Avregistrera en SQL Server-instans när du har inaktiverat skyddet men innan du tar bort valvet:

1. Välj **Säkerhetskopieringsinfrastruktur**under **Hantera**på instrumentpanelen för valvet .  

   ![Välja infrastruktur för säkerhetskopiering](./media/backup-azure-sql-database/backup-infrastructure-button.png)

2. Under **Hanteringsservrar** väljer du **Skyddade servrar**.

   ![Välja skyddade servrar](./media/backup-azure-sql-database/protected-servers.png)

3. I **Skyddade servrar**väljer du den server som ska avregistreras. Om du vill ta bort valvet måste du avregistrera alla servrar.

4. Högerklicka på den skyddade servern och välj **Avregistrera**.

   ![Välja Ta bort](./media/backup-azure-sql-database/delete-protected-server.jpg)

## <a name="modify-policy"></a>Ändra princip

Ändra principen för att ändra säkerhetskopieringsfrekvens eller kvarhållningsintervall.

> [!NOTE]
> Alla ändringar i lagringsperioden kommer att tillämpas retroaktivt på alla äldre återställningspunkter förutom de nya.

Gå till **Hantera** > **principer för säkerhetskopiering och** välj den princip som du vill redigera i instrumentpanelen i valvet.

  ![Hantera principer för säkerhetskopiering](./media/backup-azure-sql-database/modify-backup-policy.png)

  ![Ändra princip för säkerhetskopiering](./media/backup-azure-sql-database/modify-backup-policy-impact.png)

Principändring påverkar alla associerade säkerhetskopieringsobjekt och utlöser motsvarande **konfigurera skyddsjobb.**

### <a name="inconsistent-policy"></a>Inkonsekvent policy

Ibland kan en ändringsprincipåtgärd leda till en **inkonsekvent** principversion för vissa säkerhetskopieringsobjekt. Detta händer när motsvarande **konfigurationsskyddsjobb** misslyckas för säkerhetskopieringsobjektet när en ändringsprincipåtgärd har utlösts. Det visas på följande sätt i vyn för säkerhetskopieringsobjekt:

  ![Inkonsekvent policy](./media/backup-azure-sql-database/inconsistent-policy.png)

Du kan åtgärda principversionen för alla påverkade objekt med ett klick:

  ![Åtgärda inkonsekvent princip](./media/backup-azure-sql-database/fix-inconsistent-policy.png)

## <a name="re-register-extension-on-the-sql-server-vm"></a>Registrera tillägg för att registrera om på den virtuella datorn för SQL Server

Ibland kan arbetsbelastningstillägget på den virtuella datorn påverkas av en eller annan anledning. I sådana fall kommer alla åtgärder som utlöses på den virtuella datorn att börja misslyckas. Du kan då behöva registrera tillägget på den virtuella datorn igen. **Omregistrera** åtgärden installerar om tillägget för säkerhetskopiering av arbetsbelastningen på den virtuella datorn för att åtgärderna ska kunna fortsätta.

Använd det här alternativet med försiktighet. När utlöses på en virtuell dator med ett redan felfritt tillägg, kommer den här åtgärden att orsaka att tillägget startas om. Detta kan leda till att alla pågående jobb misslyckas. Kontrollera vänligen ett eller flera av [symptomen](backup-sql-server-azure-troubleshoot.md#re-registration-failures) innan du utlöser omregistreringen.

## <a name="next-steps"></a>Nästa steg

Mer information finns i [Felsöka säkerhetskopior i en SQL Server-databas](backup-sql-server-azure-troubleshoot.md).
