---
title: "Komma igång med säkerhetskopiering och återställning av Azure SQL-databaser för dataskydd och återställning | Microsoft Docs"
description: "Den här självstudiekursen förklarar hur du återställer data från automatiska säkerhetskopieringar till en tidigare tidpunkt, hur du lagrar automatiska säkerhetskopieringar i Azure Recovery Services-valvet och hur du återställer från Azure Recovery Services-valvet"
keywords: sql database tutorial
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: aeb8c4c3-6ae2-45f7-b2c3-fa13e3752eed
ms.service: sql-database
ms.custom: business continuity
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 12/08/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: a9adc82faab977ad5da1c9dc281d45db3101d8c8
ms.openlocfilehash: 3fb97f80d5012e993f92eb592d877faabc94c43e


---
# <a name="get-started-with-backup-and-restore-for-data-protection-and-recovery"></a>Komma igång med säkerhetskopiering och återställning för dataskydd och återställning
I den här introduktionskursen lär du dig hur du använder Azure Portal för att:

- Visa befintliga säkerhetskopieringar av en databas
- Återställa en databas till en tidigare tidpunkt
- Konfigurera långsiktig kvarhållning av en säkerhetskopia av en databas i Azure Recovery Services-valvet
- Återställa en databas från Azure Recovery Services-valvet

**Uppskattad tidsåtgång**: Den här kursen tar cirka 30 minuter att slutföra (förutsatt att du redan har uppfyllt kraven).

> [!TIP]
> Du kan utföra samma uppgifter i en introduktionskurs genom att använda [PowerShell](sql-database-get-started-backup-recovery-powershell.md).
>

## <a name="prerequisites"></a>Krav

* Du behöver ett Azure-konto. Du kan [öppna ett kostnadsfritt Azure-konto](/pricing/free-trial/?WT.mc_id=A261C142F) eller [aktivera Visual Studio-prenumerantförmåner](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). 

* Du måste kunna ansluta till Azure Portal med ett konto som är medlem i rollen som prenumerationsägare eller deltagare. Mer information om rollbaserad åtkomstkontroll (RBAC) finns i [Getting started with access management in the Azure portal](../active-directory/role-based-access-control-what-is.md) (Komma igång med åtkomsthantering på Azure Portal).

* Du har slutfört [Komma igång med Azure SQL Database-servrar, databaser och brandväggsregler med hjälp av Azure Portal och SQL Server Management Studio](sql-database-get-started.md) eller motsvarande [PowerShell-version](sql-database-get-started-powershell.md) av den här självstudiekursen. Om du inte har gjort det går du kursen eller kör PowerShell-skriptet i slutet av [PowerShell-versionen](sql-database-get-started-powershell.md) av den här kursen innan du fortsätter.


> [!NOTE]
> I den här självstudiekursen lär du dig mer om följande: [SQL Database-säkerhetskopieringar](sql-database-automated-backups.md), [Långsiktig kvarhållning av säkerhetskopior](sql-database-long-term-retention.md) och [Återställa en Azure SQL-databas med hjälp av automatiska databassäkerhetskopieringar](sql-database-recovery-using-backups.md).
>  

## <a name="sign-in-to-the-azure-portal-using-your-azure-account"></a>Logga in på Azure Portal med ditt Azure-konto
Använd din [befintliga prenumeration](https://account.windowsazure.com/Home/Index) och följ dessa steg för att ansluta till Azure Portal.

1. Öppna din webbläsare och anslut till [Azure-portalen](https://portal.azure.com/).
2. Logga in på [Azure-portalen](https://portal.azure.com/).
3. Ange autentiseringsuppgifterna för din prenumeration på sidan **Logga in**.
   
   ![Logga in](./media/sql-database-get-started/login.png)

<a name="create-logical-server-bk"></a>

## <a name="view-the-oldest-restore-point-from-the-service-generated-backups-of-a-database"></a>Visa den äldsta återställningspunkten från de tjänstgenererade säkerhetskopiorna av en databas

I det här avsnittet av självstudiekursen visar du information om den senaste återställningspunkten från de [tjänstgenererade automatiska säkerhetskopiorna](sql-database-automated-backups.md) av din databas. 

1. Öppna bladet **SQL-databas** för din databas, **sqldbtutorialdb**.

   ![Blad för ny exempeldatabas](./media/sql-database-get-started/new-sample-db-blade.png)

2. Klicka på **Återställ** i verktygsfältet.

   ![verktygsfältet Återställ](./media/sql-database-get-started-backup-recovery/restore-toolbar.png)

3. Granska den senaste återställningspunkten på bladet Återställ.

   ![äldsta återställningspunkt](./media/sql-database-get-started-backup-recovery/oldest-restore-point.png)

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Återställa en databas till en tidigare tidpunkt

I det här avsnittet av självstudiekursen ska du återställa databasen till en ny databas från en viss tidigare tidpunkt.

1. På bladet **Återställ** för databasen granskar du standardnamnet för den nya databasen som du vill använda för att återställa databasen till en tidigare tidpunkt (namnet är det befintliga databasnamnet med en tidsstämpel). Det här namnet ändras för att återspegla den tidpunkt som du anger i nästa steg.

   ![namn på återställd databas](./media/sql-database-get-started-backup-recovery/restored-database-name.png)

2. Klicka på **kalenderikonen** i inmatningsrutan **Återställningspunkt (UTC)**.

   ![återställningspunkt](./media/sql-database-get-started-backup-recovery/restore-point.png)

2. Välj ett datum inom kvarhållningsperioden i kalendern

   ![datum för återställningspunkt](./media/sql-database-get-started-backup-recovery/restore-point-date.png)

3. I inmatningsrutan **Återställningspunkt (UTC)** anger du den tid på det valda datumet som du vill återställa data i databasen från de automatiska databassäkerhetskopiorna till.

   ![tid för återställningspunkt](./media/sql-database-get-started-backup-recovery/restore-point-time.png)

   >[!NOTE]
   >Observera att databasens namn har ändrats för att återspegla det datum och den tid du valt. Observera också att du inte kan ändra den server till vilken du återställer till en specifik tidpunkt. Om du vill återställa till en annan server använder du [geo-återställning](sql-database-disaster-recovery.md#recover-using-geo-restore). Observera även att du kan återställa till en [elastisk pool](sql-database-elastic-jobs-overview.md) eller till en annan prisnivå. 
   >

4. Klicka på **OK** för att återställa databasen till en tidigare tidpunkt till den nya databasen.

5. Klicka på meddelandeikonen i verktygsfältet för att visa återställningsjobbets status.

   ![förlopp för återställningsjobb](./media/sql-database-get-started-backup-recovery/restore-job-progress.png)

6. När återställningsjobbet har slutförts kan du öppna bladet **SQL-databaser** för att visa den nyligen återställda databasen.

   ![återställd databasen](./media/sql-database-get-started-backup-recovery/restored-database.png)

> [!NOTE]
> Här kan du ansluta till den återställda databasen med hjälp av SQL Server Management Studio för att utföra nödvändiga åtgärder, till exempel [för att extrahera en del data från den återställda databasen och kopiera dem till den befintliga databasen eller för att ta bort den befintliga databasen och byta namn på den återställda databasen till det befintliga databasnamnet](sql-database-recovery-using-backups.md#point-in-time-restore).
>

## <a name="configure-long-term-retention-of-automated-backups-in-an-azure-recovery-services-vault"></a>Konfigurera långsiktig kvarhållning av automatiska säkerhetskopieringar i ett Azure Recovery Services-valv 

I det här avsnittet av självstudiekursen ska du [konfigurera ett Azure Recovery Services-valv så att automatiska säkerhetskopieringar bevaras](sql-database-long-term-retention.md) under längre tid än kvarhållningsperioden för din tjänstnivå. 


> [!TIP]
> Om du vill ta bort säkerhetskopior finns [Ta bort säkerhetskopior för långsiktig kvarhållning](sql-database-long-term-retention-delete.md).
>

1. Öppna bladet **SQL Server** för din server, **sqldbtutorialserver**.

   ![Bladet SQL-server](./media/sql-database-get-started/sql-server-blade.png)

2. Klicka på **Long-term backup retention** (Långsiktig kvarhållning av säkerhetskopior).

   ![länk till långsiktig kvarhållning av säkerhetskopior](./media/sql-database-get-started-backup-recovery/long-term-backup-retention-link.png)

3. På bladet **sqldbtutorial - Long-term backup retention** (sqldbtutorial – Långsiktig kvarhållning av säkerhetskopior) granskar du och accepterar villkoren för förhandsversionen (om du inte redan gjort det, eller om den här funktionen inte längre finns som en förhandsversion).

   ![acceptera villkoren för förhandsversionen](./media/sql-database-get-started-backup-recovery/accept-the-preview-terms.png)

4. Konfigurera långsiktig kvarhållning för säkerhetskopior för sqldbtutorialdb genom att välja databasen i rutnätet och klicka på **Konfigurera** i verktygsfältet.

   ![välj databas för långsiktig kvarhållning av säkerhetskopior](./media/sql-database-get-started-backup-recovery/select-database-for-long-term-backup-retention.png)

5. På bladet **Konfigurera** klickar du på **Konfigurera inställningarna** under **Recovery Services-valv**.

   ![länk för att konfigurera valv](./media/sql-database-get-started-backup-recovery/configure-vault-link.png)

6. På bladet **Recovery Services-valv** väljer du ett befintligt valv, om det finns ett. Om inget Recovery Services-valv hittades för din prenumeration klickar du för att avbryta flödet och skapar ett Recovery Services-valv.

   ![länk för att skapa nytt valv](./media/sql-database-get-started-backup-recovery/create-new-vault-link.png)

7. På bladet **Recovery Services-valv** klickar du på **Lägg till**.

   ![länk för att lägga till nytt valv](./media/sql-database-get-started-backup-recovery/add-new-vault-link.png)
   
8. På bladet **Recovery Services-valv** anger du ett giltigt namn för det nya Recovery Services-valvet.

   ![namn på nytt valv](./media/sql-database-get-started-backup-recovery/new-vault-name.png)

9. Välj din prenumeration och resursgrupp och välj sedan platsen för valvet. När du är klar klickar du på **Skapa**.

   ![skapa nytt valv](./media/sql-database-get-started-backup-recovery/create-new-vault.png)

   > [!IMPORTANT]
   > Valvet måste finnas i samma region som den logiska Azure SQL-servern och måste använda samma resursgrupp som den logiska servern.
   >

10. När det nya valvet har skapats följer du stegen för att gå tillbaka till bladet **Recovery Services-valv**.

11. På bladet **Recovery Services-valv** klickar du på valvet och sedan på **Välj**.

   ![välj befintligt valv](./media/sql-database-get-started-backup-recovery/select-existing-vault.png)

12. På bladet **Konfigurera** anger du ett giltigt namn för den nya kvarhållningsprincipen, ändrar standardkvarhållningsprincipen om det behövs och klickar sedan på **OK**.

   ![definiera kvarhållningsprincip](./media/sql-database-get-started-backup-recovery/define-retention-policy.png)

13. På bladet **sqldbtutorial - Long-term backup retention** (sqldbtutorial – Långsiktig kvarhållning av säkerhetskopior) klickar du på **Spara** och sedan på **OK** för att tillämpa principen för långsiktig kvarhållning av säkerhetskopior på alla valda databaser.

   ![definiera kvarhållningsprincip](./media/sql-database-get-started-backup-recovery/save-retention-policy.png)

14. Klicka på **Spara** för att aktivera långsiktig kvarhållning av säkerhetskopior med den här nya principen för Azure Recovery Services-valvet som du har konfigurerat.

   ![definiera kvarhållningsprincip](./media/sql-database-get-started-backup-recovery/enable-long-term-retention.png)

15. När långsiktig kvarhållning av säkerhetskopior har aktiverats öppnar du bladet **sqldbtutorialvault** (gå till **Alla resurser** och välj det i listan över resurser för din prenumeration).

   ![visa Recovery Services-valv](./media/sql-database-get-started-backup-recovery/view-recovery-services-vault.png)

> [!IMPORTANT]
> När de har konfigurerats visas säkerhetskopiorna i valvet inom sju dagar. Fortsätt inte den här självstudiekursen förrän säkerhetskopiorna visas i valvet.
>

## <a name="view-backups-in-long-term-retention"></a>Visa säkerhetskopior med långsiktig kvarhållning

I det här avsnittet av självstudiekursen ska du visa information om databassäkerhetskopiorna i [långsiktig kvarhållning av säkerhetskopior](sql-database-long-term-retention.md). 

1. Öppna bladet **sqldbtutorialvault** (gå till **Alla resurser** och välj det i listan över resurser för din prenumeration) för att visa mängden lagringsutrymme som används av din databassäkerhetskopior i valvet.

   ![visa Recovery Services-valvet med säkerhetskopior](./media/sql-database-get-started-backup-recovery/view-recovery-services-vault-with-data.png)

2. Öppna bladet **SQL-databas** för din databas, **sqldbtutorialdb**.

   ![Blad för ny exempeldatabas](./media/sql-database-get-started/new-sample-db-blade.png)

3. Klicka på **Återställ** i verktygsfältet.

   ![verktygsfältet Återställ](./media/sql-database-get-started-backup-recovery/restore-toolbar.png)

4. Klicka på **På lång sikt** på bladet Återställ.

5. Under Azure vault backups (Säkerhetskopior i Azure-valv) klickar du på **Välj en säkerhetskopia** för att visa de tillgängliga databassäkerhetskopiorna i långsiktig kvarhållning av säkerhetskopior.

   ![säkerhetskopior i valv](./media/sql-database-get-started-backup-recovery/view-backups-in-vault.png)

## <a name="restore-a-database-from-a-backup-in-long-term-backup-retention"></a>Återställa en databas från en säkerhetskopia i långsiktig kvarhållning av säkerhetskopior

I det här avsnittet av självstudiekursen ska du återställa databasen till en ny databas från en säkerhetskopia i Azure Recovery Services-valvet.

1. På bladet **Säkerhetskopior i Azure-valv** klickar du på säkerhetskopian som ska återställas och klickar sedan på **Välj**.

   ![välj säkerhetskopia i valv](./media/sql-database-get-started-backup-recovery/select-backup-in-vault.png)

2. I textrutan **Databasnamn** anger du namnet på den återställda databasen.

   ![nytt databasnamn](./media/sql-database-get-started-backup-recovery/new-database-name.png)

3. Klicka på **OK** för att återställa databasen från säkerhetskopian i valvet till den nya databasen.

4. Klicka på meddelandeikonen i verktygsfältet för att visa återställningsjobbets status.

   ![förlopp över återställningsjobb från valvet](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

5. När återställningsjobbet har slutförts kan du öppna bladet **SQL-databaser** för att visa den nyligen återställda databasen.

   ![återställd databas från valvet](./media/sql-database-get-started-backup-recovery/restored-database-from-vault.png)

> [!NOTE]
> Här kan du ansluta till den återställda databasen med hjälp av SQL Server Management Studio för att utföra nödvändiga åtgärder, till exempel [för att extrahera en del data från den återställda databasen och kopiera dem till den befintliga databasen eller för att ta bort den befintliga databasen och byta namn på den återställda databasen till det befintliga databasnamnet](sql-database-recovery-using-backups.md#point-in-time-restore).
>

## <a name="next-steps"></a>Nästa steg

- Mer information om tjänstgenererade automatiska säkerhetskopior finns i avsnittet om [automatiska säkerhetskopior](sql-database-automated-backups.md)
- Mer information om långsiktig kvarhållning av säkerhetskopior finns i avsnittet om [långsiktig kvarhållning av säkerhetskopior](sql-database-long-term-retention.md)
- Mer information om hur du återställer från säkerhetskopior finns i avsnittet om hur du [återställer från säkerhetskopior](sql-database-recovery-using-backups.md)



<!--HONumber=Jan17_HO2-->


