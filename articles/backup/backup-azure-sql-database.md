---
title: Säkerhetskopiera SQL Server-databas till Azure | Microsoft Docs
description: Den här självstudiekursen beskriver SQL Server-säkerhetskopiering till Azure. Artikeln beskriver även återställning av SQL Server.
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: ''
keywords: ''
ms.assetid: ''
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 6/1/2018
ms.author: markgal;anuragm
ms.custom: ''
ms.openlocfilehash: 160055e4fdc6a5dfa401eaa3dc44427d13af22a8
ms.sourcegitcommit: 0a7c9629dea6f5eb52cc45a1e31112def210dfc2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34730908"
---
# <a name="back-up-sql-server-database-in-azure"></a>Säkerhetskopiera SQL Server-databas i Azure

SQL Server-databaser är viktiga arbetsbelastningar som kräver låg Recovery punkt mål (RPO) och långsiktig lagring. Azure Backup är en lösning för SQL Serverbackup som kräver noll infrastruktur, vilket innebär att inga komplexa reservserver, ingen hanteringsagent eller lagring för säkerhetskopiering att hantera. Azure-säkerhetskopiering tillhandahåller centraliserad hantering för dina säkerhetskopieringar över alla SQL-servrar, eller med olika arbetsbelastningar.

 I den här artikeln lär du dig:

> [!div class="checklist"]
> * Förutsättningar för att säkerhetskopiera SQL Server till Azure
> * Skapa och använda ett Recovery Services-valv
> * Konfigurera säkerhetskopieringar för SQL Server-databas
> * Ange en princip för säkerhetskopiering (eller kvarhållning) för återställningspunkterna
> * Så här återställer du databasen

Innan du startar procedurerna i den här artikeln bör du ha en SQL-Server som körs i Azure. [Du kan använda SQL marketplace virtuella datorer för att snabbt skapa en SQL Server](../sql-database/sql-database-get-started-portal.md).

## <a name="public-preview-limitations"></a>Allmänna begränsningar i förhandsversionen

Följande är kända begränsningar för förhandsversion.

- SQL-dator kräver en Internetanslutning för att komma åt Azure offentliga IP-adresser. Mer information finns i avsnittet [nätverksanslutningen](backup-azure-sql-database.md#establish-network-connectivity).
- Du kan skydda upp till 2000 SQL-databaser i en Recovery Services-valvet. Ytterligare SQL-databaser ska lagras i en separat Recovery Services-valvet.
- [Distribuerade tillgänglighet grupper säkerhetskopiering har begränsningar](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/distributed-availability-groups?view=sql-server-2017).
- SQL Failover Cluster instanser (FCI) stöds inte.
- Använd Azure-portalen för att konfigurera Azure Backup för att skydda SQL Server-databaser. Stöd för Azure PowerShell, CLI och REST API: er är inte tillgänglig.

## <a name="supported-operating-systems-and-versions-of-sql-server"></a>Operativsystem som stöds och versioner av SQLServer

Följande operativsystem som stöds och versioner av SQL Server som gäller för SQL-marketplace virtuella Azure-datorer och icke-marketplace virtuella datorer (där SQL Server manuellt installeras).

### <a name="supported-operating-systems"></a>Operativsystem som stöds

- Windows Server 2012
- Windows Server 2012 R2
- Windows Server 2016

Linux stöds inte för närvarande.

### <a name="supported-versionseditions-of-sql-server"></a>Stöds versioner av SQL Server

- SQL 2012 Enterprise, Standard, webb, Developer, Express
- SQL 2014 Enterprise, Standard, webb, Developer, Express
- SQL 2016 Enterprise, Standard, webb, Developer, Express
- SQL 2017 Enterprise, Standard, webb, Developer, Express


## <a name="prerequisites-for-using-azure-backup-to-protect-sql-server"></a>Krav för att använda Azure Backup för att skydda SQL Server 

Kontrollera följande innan du kan säkerhetskopiera SQL Server-databasen. :

- Identifiera eller [skapa ett Recovery Services-valv](backup-azure-sql-database.md#create-a-recovery-services-vault) i samma region eller språk, som den virtuella datorn där SQL Server.
- [Kontrollera behörigheterna för den virtuella datorn](backup-azure-sql-database.md#set-permissions-for-non-marketplace-sql-vms) behövs för att säkerhetskopiera SQL-databaser.
- [SQL-datorn är ansluten till nätverket](backup-azure-sql-database.md#establish-network-connectivity).

Om dessa villkor finns i din miljö, fortsätter du till avsnittet [konfigurera ditt valv för att skydda en SQL-databas](backup-azure-sql-database.md#configure-your-vault-to-protect-a-sql-database). Om någon av nödvändiga inte finns kommer fortsätta att läsa det här avsnittet.


## <a name="establish-network-connectivity"></a>Upprätta nätverksanslutning

För alla åtgärder måste virtuella SQL anslutningen till Azure offentliga IP-adresser. SQL virtuella åtgärder (till exempel databasen identifiering, konfigurera säkerhetskopiering, schemalagda säkerhetskopieringar, återställa återställningspunkter och så vidare) misslyckas utan anslutning till de offentliga IP-adresserna. Använd något av följande alternativ för att göra det lätt för säkerhetskopiering trafik:

- Godkända Azure datacenter-IP-intervall - att godkända Azure datacenter-IP-intervall, Använd den [center hämtningssidan för information om IP-adressintervall och instruktioner](https://www.microsoft.com/download/details.aspx?id=41653). 
- Distribuera en HTTP-proxyserver för dirigera trafiken - när du säkerhetskopierar en SQL-databas på en virtuell dator, säkerhetskopiering tillägget på den virtuella datorn använder HTTPS-API: er för att skicka kommandon för hantering av Azure Backup och data till Azure Storage. Sekundär anknytning använder också Azure Active Directory (AAD) för autentisering. Vidarebefordra trafiken reservanknytning för dessa tre tjänster via HTTP-proxy, eftersom det är den enda komponenten som konfigurerats för åtkomst till internet.

Kompromisser mellan alternativen är: hanterbarhet, granulär kontroll och kostnader.

> [!NOTE]
>Tjänsten taggar för Azure Backup ska vara tillgänglig efter allmän tillgänglighet.
>

| Alternativ | Fördelar | Nackdelar |
| ------ | ---------- | ------------- |
| Whitelist IP-adressintervall | Inga ytterligare kostnader. <br/> För att öppna åtkomst i en NSG, Använd **Set AzureNetworkSecurityRule** cmdlet. | Komplext för att hantera som den berörda IP-adressintervall ändras med tiden. <br/>Tillhandahåller åtkomst till Azure, inte bara lagring som helhet.|
| Använda en HTTP-proxy   | Granulär kontroll i proxyn över lagringen webbadresser tillåts. <br/>Enskild plats internet-åtkomst till virtuella datorer. <br/> Inte ändras Azure IP-adress. | Ytterligare kostnader för att köra en virtuell dator med proxy-programvara. |

## <a name="set-permissions-for-non-marketplace-sql-vms"></a>Ange behörigheter för icke-marketplace SQL virtuella datorer

Om du vill säkerhetskopiera en virtuell dator Azure Backup kräver den **AzureBackupWindowsWorkload** tillägget installeras. Om du använder Azure marketplace virtuella datorer kan du gå vidare till [identifiera SQL server-databaser](backup-azure-sql-database.md#discover-sql-server-databases). Om den virtuella datorn som är värd för SQL-databaser inte har skapats från Azure marketplace, slutför du följande avsnitt för att installera tillägget och ange lämpliga behörigheter. Förutom den **AzureBackupWindowsWorkload** tillägg, Azure Backup kräver SQL sysadmin-behörigheter för att skydda SQL-databaser. Vid identifiering av databaser på den virtuella datorn, skapar ett konto, NT Service\AzureWLBackupPluginSvc i Azure Backup. För Azure Backup för att identifiera SQL-databaser, måste Service\AzureWLBackupPluginSvc NT-kontot ha SQL logga in och SQL-sysadmin-behörighet. Nedan förklaras hur du anger dessa behörigheter.

Att konfigurera behörigheter:

1. I den [Azure-portalen](https://portal.azure.com), öppna Recovery Services-valvet som du använder för att skydda SQL-databaser.
2. I menyn valvet instrumentpanelen klickar du på **+ säkerhetskopiering** att öppna den **säkerhetskopiering målet** menyn.

   ![Klicka på + säkerhetskopia som du vill öppna menyn mål för säkerhetskopian](./media/backup-azure-sql-database/open-backup-menu.png)

3. På den **säkerhetskopiering målet** menyn i den **var körs din arbetsbelastning?** menyn lämna **Azure** som standard.

4. På den **vad vill du säkerhetskopiera** menyn Visa den nedrullningsbara menyn och välj **SQL Server i Azure VM**.

    ![Klicka på + säkerhetskopia som du vill öppna menyn mål för säkerhetskopian](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    Den **säkerhetskopiering målet** menyn visas två nya steg: **identifiera DBs i virtuella datorer** och **Konfigurera säkerhetskopiering**. **Identifiera databaser i virtuella datorer** startar en sökning efter virtuella Azure-datorer.

    ![Visar nya målet stegen för säkerhetskopiering](./media/backup-azure-sql-database/backup-goal-menu-step-one.png)

5. Klicka på **Starta identifiering** att söka efter oskyddade virtuella datorer i prenumerationen. Det kan ta en stund att gå igenom alla virtuella datorer beroende på antalet oskyddade virtuella datorer i prenumerationen.

    ![Säkerhetskopiering väntar](./media/backup-azure-sql-database/discovering-sql-databases.png)
 
    När en virtuell dator i oskyddade identifieras visas det i listan. Oskyddade virtuella datorer listas av deras virtuella namn och resurs-grupp. Det är möjligt för flera virtuella datorer har samma namn. Virtuella datorer med samma namn tillhör dock olika resursgrupper. Om en virtuell dator i förväntade inte visas i listan kan du se om den virtuella datorn redan skyddad till ett valv.

6. I listan över virtuella datorer väljer du den virtuella datorn som innehåller SQL-databasen som du vill säkerhetskopiera och klicka på **identifiera DBs**. 

    Identifieringsprocessen installerar den **AzureBackupWindowsWorkload** tillägget på den virtuella datorn. Tillägget kan kommunicera med den virtuella datorn så att den kan säkerhetskopiera SQL-databaser med Azure Backup-tjänsten. När tillägget installeras, Azure-säkerhetskopiering skapar Windows-kontot för tjänsten virtual **NT Service\AzureWLBackupPluginSvc**, på den virtuella datorn. Virtuella tjänstkontot kräver SQL-sysadmin-behörighet. Installationsprocessen virtuella service-kontot, om du ser felet, **UserErrorSQLNoSysadminMembership**, finns i avsnittet [åtgärda SQL-sysadmin-behörighet](backup-azure-sql-database.md#fixing-sql-sysadmin-permissions).

    Meddelandefältet visar förloppet för databas-identifiering. Beroende på hur många databaser finns på den virtuella datorn, kan det ta en stund innan jobbet ska slutföras. När valda databaser har hittats, visas ett meddelande.

    ![meddelande för distributionen](./media/backup-azure-sql-database/notifications-db-discovered.png)

När du koppla databasen till Recovery Services-valvet nästa steg är att [konfigurera säkerhetskopieringen](backup-azure-sql-database.md#configure-your-vault-to-protect-a-sql-database).

### <a name="fixing-sql-sysadmin-permissions"></a>Åtgärda SQL-sysadmin-behörighet

Under installationen om du ser felet, **UserErrorSQLNoSysadminMembership**, logga in i SQL Server Management Studio (SSMS) med ett konto som har behörighet för SQL-sysadmin. Om du inte behöver särskilda behörigheter, bör du kunna använda Windows-autentisering för att identifiera kontot.

1. Öppna på SQL-Server i **Security-inloggningar** mapp.

    ![Öppna SQL Server- och säkerhets- och inloggningen mappar för att se konton](./media/backup-azure-sql-database/security-login-list.png)

2. Högerklicka på mappen inloggningar och välj **ny inloggning**, och i inloggning - ny dialogruta på **sökning**

    ![Öppna dialogrutan för sökning i inloggning - ny](./media/backup-azure-sql-database/new-login-search.png)

3. Eftersom Windows-kontot för tjänsten virtual **NT Service\AzureWLBackupPluginSvc** redan har skapats under registrering av virtuell dator och SQL identifiering fasen Ange kontonamnet som det visas i den  **Ange objektnamn att välja** dialogrutan. Klicka på **Kontrollera namn** att matcha namnet. 

    ![Klicka på Kontrollera namn för att lösa tjänstnamnet okänt](./media/backup-azure-sql-database/check-name.png)

4. Klicka på **OK** att stänga dialogrutan Välj användare eller grupp.

5. I den **serverroller** dialogrutan, kontrollera att den **sysadmin** roll har valts. Klicka på **OK** att stänga **inloggning - ny**.

    ![Kontrollera att serverrollen sysadmin har valts](./media/backup-azure-sql-database/sysadmin-server-role.png)

    Behörigheterna som krävs bör nu finns.

6. Om du fasta behörigheter felet fortfarande måste du koppla databasen till Recovery Services-valvet. I Azure portal **skyddade servrar** lista, högerklicka på servern i fel och välj **identifiera DBs**.

    ![Kontrollera att servern har de behörigheter som krävs](./media/backup-azure-sql-database/check-erroneous-server.png)

    Meddelandefältet visar förloppet för databas-identifiering. Beroende på hur många databaser finns på den virtuella datorn, kan det ta en stund innan jobbet ska slutföras. När valda databaser har hittats, visas ett meddelande i meddelandefältet.

    ![meddelande för distributionen](./media/backup-azure-sql-database/notifications-db-discovered.png)

När du koppla databasen till Recovery Services-valvet nästa steg är att [konfigurera säkerhetskopieringen](backup-azure-sql-database.md#configure-your-vault-to-protect-a-sql-database).

[!INCLUDE [Section explaining how to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Identifiera SQL Server-databaser

Azure Backup kan identifiera alla databaser på en SQL Server-instansen så att du kan skydda dem per dina behov för säkerhetskopiering. Använd följande procedur för att identifiera den virtuella datorn som är värd för SQL-databaser. När du har identifierat den virtuella datorn installerar Azure Backup ett enkelt tillägg för att identifiera de SQL server-databaserna.

1. Logga in på prenumerationen i den [Azure-portalen](https://portal.azure.com/).
2. Välj i den vänstra menyn **alla tjänster**.

    ![Välj alternativet för alla tjänster på huvudmenyn](./media/backup-azure-sql-database/click-all-services.png) <br/>

3. Ange i dialogrutan alla tjänster *återställningstjänster*. När du börjar skriva filtrerar listan över resurser i dina indata. När du ser det välja **Recovery Services-valv**.

    ![Skriv återställningstjänster i dialogrutan för alla tjänster](./media/backup-azure-sql-database/all-services.png) <br/>

    Lista över Recovery Services-valv i prenumerationen visas. 

4. Välj valvet som du vill använda för att skydda din SQL-databaser från listan över Recovery Services-valvet.

5. I menyn valvet instrumentpanelen klickar du på **+ säkerhetskopiering** att öppna den **säkerhetskopiering målet** menyn.

   ![Klicka på + säkerhetskopia som du vill öppna menyn mål för säkerhetskopian](./media/backup-azure-sql-database/open-backup-menu.png)

6. På den **säkerhetskopiering målet** menyn i den **var körs din arbetsbelastning?** menyn lämna **Azure** som standard.

7. På den **vad vill du säkerhetskopiera** menyn Visa den nedrullningsbara menyn och välj **SQL Server i Azure VM**.

    ![Klicka på + säkerhetskopia som du vill öppna menyn mål för säkerhetskopian](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    När markerat den **säkerhetskopiering målet** menyn visas två steg: identifiera DBs i virtuella datorer och konfigurera säkerhetskopiering. 

    ![Visar nya målet stegen för säkerhetskopiering](./media/backup-azure-sql-database/backup-goal-menu-step-one.png)

8. Klicka på **Starta identifiering** att söka efter oskyddade virtuella datorer i prenumerationen. Det kan ta en stund att gå igenom alla virtuella datorer beroende på antalet oskyddade virtuella datorer i prenumerationen.

    ![Säkerhetskopiering väntar](./media/backup-azure-sql-database/discovering-sql-databases.png)
 
    När en virtuell dator i oskyddade identifieras visas det i listan. Flera virtuella datorer kan ha samma namn. Dock tillhör flera virtuella datorer med samma namn olika resursgrupper. Oskyddade virtuella datorer listas av deras virtuella namn och resurs-grupp. Om inte en förväntad virtuell dator visas kan du se om den virtuella datorn redan skyddad till ett valv.

9. Från listan över virtuella datorer, markerar du kryssrutan för den virtuella datorn som innehåller SQL-databaser som du vill skydda och klickar på **identifiera DBs**.

    Azure-säkerhetskopiering identifierar alla SQL-databaser på den virtuella datorn. Information om vad som händer under fasen databasen identifiering finns i följande avsnitt [Backend åtgärder när identifiering av SQL-databaser](backup-azure-sql-database.md#backend-operations-when-discovering-sql-databases). Efter identifiering av SQL-databaser, är du redo att [konfigurera säkerhetskopieringsjobbet](backup-azure-sql-database.md#configure-your-vault-to-protect-a-sql-database).

### <a name="backend-operations-when-discovering-sql-databases"></a>Backend-åtgärder när identifiering av SQL-databaser

När du använder den **identifiera DBs** i bakgrunden verktyget Azure Backup utförs följande åtgärder:

- registrerar den virtuella datorn med Recovery Services-valvet för säkerhetskopiering av arbetsbelastningen. Alla databaser på den registrerade virtuella kan endast säkerhetskopieras till Recovery Services-valvet. 

- installerar den **AzureBackupWindowsWorkload** tillägget på den virtuella datorn. Säkerhetskopiera en SQL-databas är en lösning för utan Agent, det vill säga med filnamnstillägget är installerad på den virtuella datorn även ingen agent installerad på SQL-databasen.

- skapar tjänstkonto, **NT Service\AzureWLBackupPluginSvc**, på den virtuella datorn. Alla åtgärder för säkerhetskopiering och återställning använder service-kontot. **NT Server\AzureWLBackupPluginSvc** måste SQL-sysadmin-behörighet. Alla SQL-Marketplace virtuella datorer som medföljer SqlIaaSExtension installerad och AzureBackupWindowsWorkload använder SQLIaaSExtension att automatiskt få behörigheter som krävs. Om den virtuella datorn inte har installerat SqlIaaSExtension, identifiera DB åtgärden misslyckas och du får felmeddelandet **UserErrorSQLNoSysAdminMembership**. Om du vill lägga till en sysadmin-behörighet för säkerhetskopiering, följ instruktionerna i [ställa in Azure Backup behörigheter för icke-marketplace SQL virtuella datorer](backup-azure-sql-database.md#set-permissions-for-non--marketplace-sql-vms).

    ![Välj den virtuella datorn och databasen](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup-for-sql-server-database"></a>Konfigurera säkerhetskopiering av SQL Server-databas

Azure-säkerhetskopiering tillhandahåller tjänster för att skydda din SQL Server-databaser och hantera säkerhetskopieringsjobb. Hantering och övervakningsfunktioner beror på Recovery Services-valvet. Konfigurera skydd för SQL-databasen:

1. Öppna Recovery Services-valvet som är registrerad med SQL-dator.

2. I menyn valvet instrumentpanelen klickar du på **+ säkerhetskopiering** att öppna den **säkerhetskopiering målet** menyn.

    ![Klicka på + säkerhetskopia som du vill öppna menyn mål för säkerhetskopian](./media/backup-azure-sql-database/open-backup-menu.png)

3. På den **säkerhetskopiering målet** menyn i den **var körs din arbetsbelastning?** menyn lämna **Azure** som standard.

4. På den **vad vill du säkerhetskopiera** menyn Visa den nedrullningsbara menyn och välj **SQL Server i Azure VM**.

    ![Klicka på + säkerhetskopia som du vill öppna menyn mål för säkerhetskopian](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    När markerat den **säkerhetskopiering målet** menyn visas två steg: identifiera DBs i virtuella datorer och konfigurera säkerhetskopiering. Om du har gått igenom den här artikeln i ordning du redan har upptäckt oskyddade virtuella datorer och valvet har registrerats med en virtuell dator. Nu är du redo att konfigurera skydd för SQL-databaser.

5. Mål för säkerhetskopiering-menyn klickar du på **Konfigurera säkerhetskopiering**.

    ![Visar nya målet stegen för säkerhetskopiering](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

    Azure Backup-tjänsten visar alla SQL-instanser med fristående databaser, samt SQL AlwaysOn-Tillgänglighetsgrupper. Klicka på ikonen bredvid namnet på instansen för att visa databaserna om du vill visa fristående databaser i SQL-instansen. Följande bilder visar exempel på en fristående instans och en Always On-tillgänglighetsgrupp.

    > [!NOTE]
    > Fullständig och differentiell säkerhetskopiering inträffa från den primära noden som SQL-plattformen har denna begränsning. Säkerhetskopieringen kan ske baserat på dina inställningar för säkerhetskopiering. Den primära noden måste registreras på grund av den här begränsningen.
    >

    ![Lista över databaser i SQL-instans](./media/backup-azure-sql-database/discovered-databases.png)

    Klicka på ikonen bredvid AlwaysOn-Tillgänglighetsgrupper för att visa listan över databaser.

    ![Lista över databaser i AlwaysOn-tillgänglighetsgrupp](./media/backup-azure-sql-database/discovered-database-availability-group.png)

6. I listan över databaser, väljer du alla som du vill skydda och klickar på **OK**.

    ![Välj flera databaser att skydda dem](./media/backup-azure-sql-database/select-multiple-database-protection.png)

    Du kan välja upp till 50 databaser i taget. Om du vill skydda fler än 50 databaser Se flera gånger. När du har skyddat de första 50 databaserna Upprepa det här steget om du vill skydda en uppsättning databaser.
    > [!Note] 
    > Om du vill optimera säkerhetskopiering belastningar bryter Azure Backup stora säkerhetskopieringsjobb till flera journaler. Det maximala antalet databaser i en säkerhetskopiering är 50.
    >
    >

7. Att skapa eller välj en princip för säkerhetskopiering, i den **säkerhetskopiering** väljer du **säkerhetskopiera princip**, för att öppna menyn.

    ![Välj alternativet för princip för säkerhetskopiering](./media/backup-azure-sql-database/select-backup-policy.png)

8. Från den **Välj säkerhetskopieringsprincip** nedrullningsbara menyn, Välj en princip för säkerhetskopiering och på **OK**. Information om hur du skapar en egen princip för säkerhetskopiering finns i avsnittet [definiera en princip för säkerhetskopiering](backup-azure-sql-database.md#define-a-backup-policy).

    ![Välj en princip för säkerhetskopiering på menyn](./media/backup-azure-sql-database/select-backup-policy-steptwo.png)

    I menyn säkerhetskopiering princip från den **Välj säkerhetskopieringsprincip** listrutan, kan du: 
    - standardprincipen för HourlyLogBackup 
    - en säkerhetskopieringsprincip tidigare skapade för SQL,
    - att [definiera en ny princip](backup-azure-sql-database.md#define-a-backup-policy) baserat på återställningspunktmål (RPO) och Kvarhållningsintervall. 

    > [!Note]
    > Azure-säkerhetskopiering har stöd för långsiktig kvarhållning baserat på farfar-pappa-son Säkerhetskopieringsschemat att optimera backend lagringsanvändningen och uppfyller efterlevnad behov.
    >

9. När du har valt en princip för säkerhetskopiering, i den **säkerhetskopiering menyn** klickar du på **Aktivera säkerhetskopiering**.

    ![Aktivera principen för valda säkerhetskopiering](./media/backup-azure-sql-database/enable-backup-button.png)

    Du kan följa förloppet konfiguration i meddelandefältet på portalen.

    ![Visa meddelandefältet](./media/backup-azure-sql-database/notifications-area.png)


### <a name="define-a-backup-policy"></a>definierar en säkerhetskopieringspolicy

En princip för säkerhetskopiering definierar en matris över när säkerhetskopieringar tas och hur länge säkerhetskopior bevaras. Du kan använda Azure Backup för att schemalägga tre typer av säkerhetskopiering för SQL-databaser:

* Fullständig säkerhetskopiering – en fullständig säkerhetskopiering säkerhetskopierar hela databasen. En fullständig säkerhetskopia innehåller alla data i en viss databas eller en uppsättning filer eller filgrupper och tillräckligt med loggen att återställa dessa data. Du kan som mest utlösa en fullständig säkerhetskopiering per dag. Du kan välja att göra en fullständig säkerhetskopiering med dagliga och veckovisa intervall. 
* Differentiell säkerhetskopiering – en differentiell säkerhetskopiering är baserad på senaste, föregående fullständiga säkerhetskopieringen. En differentiell säkerhetskopiering fångar endast de data som har ändrats sedan en fullständig säkerhetskopiering. Du kan som mest utlösa en differentiell säkerhetskopiering per dag. Du kan konfigurera en fullständig säkerhetskopiering och en differentiell säkerhetskopiering på samma dag.
* Säkerhetskopia av transaktionsloggen - en säkerhetskopiering av loggen kan point-in-time-återställning upp till en specifik andra. Du kan som mest konfigurera transaktionella säkerhetskopior var 15: e minut.

Principen skapas på Recovery Services-valvet nivå. Om du har flera valv i valv kan använda samma säkerhetskopieringsprincip, men du måste tillämpa principen för säkerhetskopiering till varje valvet. När du skapar en princip för säkerhetskopiering, varje dag, är fullständig säkerhetskopiering standardinställningen. Du kan lägga till en differentiell säkerhetskopiering, men endast om du växlar fullständig säkerhetskopiering ska utföras varje vecka. Följande procedur beskriver hur du skapar en princip för säkerhetskopiering för en SQLServer i en virtuell Azure-dator.

Så här skapar du en princip för säkerhetskopiering

1. På menyn säkerhetskopiering princip från den **Välj säkerhetskopieringsprincip** nedrullningsbara menyn och väljer **Skapa nytt**.

   ![Skapa ny säkerhetskopieringsprincip](./media/backup-azure-sql-database/create-new-backup-policy.png)

    Menyn säkerhetskopiering princip växlar ange fälten som krävs för en ny säkerhetskopieringsprincip för SQL server.

   ![Ny princip för säkerhetskopiering-fält](./media/backup-azure-sql-database/blank-new-policy.png)

2. I **principnamn**, ange ett namn. 

3. En fullständig säkerhetskopia är obligatoriskt. Du kan acceptera standardvärdena för fullständig säkerhetskopiering eller klicka på **fullständig säkerhetskopiering** att redigera principen.

    ![Ny princip för säkerhetskopiering-fält](./media/backup-azure-sql-database/full-backup-policy.png)

    Välj dagliga eller veckovisa för frekvensen i principen för fullständig säkerhetskopiering. Om du väljer varje dag, Välj timme och tidszonen, när jobbet startar. Om du väljer dagliga fullständiga säkerhetskopior kan skapa du inte differentiella säkerhetskopieringar.

   ![inställningen för varje dag](./media/backup-azure-sql-database/daily-interval.png)

    Om du väljer varje vecka, välja dag i veckan, timme och tidszonen när jobbet startar.

   ![inställningen för varje vecka](./media/backup-azure-sql-database/weekly-interval.png)

4. Som standard markeras alla alternativ för Kvarhållningsintervallet (dagliga, varje vecka, månad och år). Avmarkera alla kvarhållningsintervallet som du inte vill och ange intervall för att använda. Klicka på menyn fullständig säkerhetskopiering princip **OK** att acceptera inställningarna.

   ![inställningen för kvarhållning intervall](./media/backup-azure-sql-database/retention-range-interval.png)

    Återställningspunkter märks för kvarhållning, baserat på deras Kvarhållningsintervall. Till exempel om du väljer en daglig utlöses fullständig säkerhetskopiering endast en fullständig säkerhetskopiering varje dag. Beroende på din veckovisa kvarhållning, specifik dag säkerhetskopiering märkta och behålls baserat på veckovisa kvarhållningsintervallet. Månatliga och årliga Kvarhållningsintervall fungerar på samma sätt.

5. Om du vill lägga till en princip för differentiell säkerhetskopiering, klickar du på **differentiell säkerhetskopiering** menyn öppnas. 

   ![Öppna differentiell princip](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

    I menyn differentiell säkerhetskopiering princip väljer **aktivera** att öppna frekvens och kvarhållning kontroller. Du kan som mest utlösa en differentiell säkerhetskopiering per dag.
    > [!Important] 
    > Mest kan differentiella säkerhetskopieringar endast bevaras i 180 dagar. Om du behöver längre kvarhållning, måste du använda fullständig säkerhetskopiering kan du använda inte differentiella säkerhetskopieringar.
    >

   ![Redigera differentiell princip](./media/backup-azure-sql-database/enable-differential-backup-policy.png)

    Klicka på **OK** att spara principen och återgå till huvudmenyn princip för säkerhetskopiering.

6. Om du vill lägga till en transaktionell Loggsäkerhetskopiering princip, klickar du på **Loggsäkerhetskopiering** menyn öppnas. Välj menyn Loggsäkerhetskopiering **aktivera**, och ange frekvens och kvarhållning kontroller. Loggsäkerhetskopior kan ske så ofta som var 15: e minut och kan endast bevaras i upp till 35 dagar. Klicka på **OK** att spara principen och återgå till huvudmenyn princip för säkerhetskopiering.

   ![Redigera princip för säkerhetskopiering av loggen](./media/backup-azure-sql-database/log-backup-policy-editor.png)

7. Välj om du vill aktivera komprimering för SQL-säkerhetskopiering. Komprimering är inaktiverat som standard.

    På serverdelen använder Azure Backup SQL native säkerhetskopieringskomprimering.

8. När du har gjort alla ändringar i princip för säkerhetskopiering, klickar du på **OK**. 

   ![differentiella Kvarhållningsintervall](./media/backup-azure-sql-database/differential-backup-policy.png)

## <a name="restore-a-sql-database"></a>Återställa en SQL-databas

Azure-säkerhetskopiering ger funktioner för att återställa enskilda databaser till ett visst datum eller tid, upp till en specifik andra med säkerhetskopieringar av transaktionsloggen. Baserat på återställningstider som du anger måste Azure Backup bestämmer automatiskt lämpliga Full, differentiella och kedja av loggsäkerhetskopior som krävs för att återställa dina data.

Alternativt kan du välja en fullständig eller differentiella säkerhetskopia att återställa till en specifik återställningspunkt i stället för en viss tid.

Att återställa en databas

1. Öppna Recovery Services-valvet som är registrerad med SQL-dator.

2. I valvet-instrumentpanelen väljer **användning** säkerhetskopiering objekt att öppna menyn objekt för säkerhetskopiering.

    ![Klicka på + säkerhetskopia som du vill öppna menyn mål för säkerhetskopian](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

3. I den **Säkerhetskopieringsobjekt** -menyn, Välj vilken typ av säkerhetskopiering management, **SQL i Azure VM**. 

    ![Klicka på + säkerhetskopia som du vill öppna menyn mål för säkerhetskopian](./media/backup-azure-sql-database/sql-restore-backup-items.png)

    Listan säkerhetskopiering objekt justeras om du vill visa listan över SQL-databaser. 

4. Välj den databas som du vill återställa från listan över SQL-databaser.

    ![Välj SQL i Azure VM listan](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

    När du väljer databasen öppnas menyn. Den här menyn innehåller säkerhetskopiering information för inkludering av databasen:

    * äldsta och senaste återställningspunkter
    * Logga säkerhetskopiering status för de senaste 24 timmarna (för databaser i fullständig och Bulk loggade återställningsmodellen, om konfigurerad för transaktionell säkerhetskopior)

5. I den valda databas-menyn klickar du på **återställa DB** att öppna menyn återställning.

    ![Välj databas för återställning](./media/backup-azure-sql-database/restore-db-button.png)

    Den **återställa** menyn öppnas och så har den **återställa konfigurationen** menyn. Den **återställa konfigurationen** menyn är det första steget i att konfigurera återställningen. I den här menyn väljer du om du vill återställa data. Alternativen är:
    * Alternativ plats, Använd det här alternativet om du vill återställa databasen till en alternativ plats samtidigt som den ursprungliga källdatabasen.
    * Skriv över DB - återställer data till samma SQL Server-instans som den ursprungliga källan. Effekten av det här är du skriva över originaldatabasen.

    > [!Important]
    > Om den valda databasen tillhör en Always On-tillgänglighetsgrupp, tillåter inte SQL databasen kan skrivas över. I det här fallet endast den **alternativ plats** är aktiverat.
    >

    ![Klicka på Konfigurera om du vill öppna återställning configuration-menyn](./media/backup-azure-sql-database/restore-restore-configuration-menu.png)

### <a name="restore-to-an-alternate-location"></a>Återställ till en alternativ plats

Den här proceduren beskriver hur återställer data till en alternativ plats. Om du vill skriva över databasen när du återställer hoppa till avsnittet [återställa och skriva över databasen](backup-azure-sql-database.md#restore-and-overwrite-the-database). Den här proceduren förutsätter att du har öppnat din Recovery Services-valvet och på menyn återställa konfigurationen. Om du inte börja med avsnittet [återställa en SQL-databas](backup-azure-sql-database.md#restore-a-sql-database).

Den **Server** nedrullningsbara menyn visar bara SQL-servrar som har registrerats med Recovery Services-valvet. Om den server du vill inte är i den **Server** lista, se avsnittet [identifiera SQL server-databaser](backup-azure-sql-database.md#discover-sql-server-databases) att hitta servern. Under identifieringsprocessen databasen är nya servrar registrerade på Recovery Services-valvet.

1. I den **återställa konfigurationen** menyn:

    * Välj **alternativ plats**,
    * för **Server**, väljer du den SQLServer där du vill återställa databasen.
    * i den **instans** nedrullningsbara menyn, Välj en SQL-instans
    * i den **återställts databasnamn** dialogrutan, ange namnet på måldatabasen.
    * Markera **skriver du över DB med samma namn finns redan på den valda SQL-instansen**.
    * Klicka på **OK** slutförts konfigurerar mål och flytta till att välja en återställningspunkt.

    ![Välj alternativ plats, instans och namnet återställning configuration-menyn](./media/backup-azure-sql-database/restore-configuration-menu.png)

2. I den **Välj återställningspunkt** -menyn väljer du antingen en loggar (tidpunkt) eller fullständiga & differentiella återställningspunkt. Om du vill återställa till en viss tidpunkt i logg fortsätta med det här steget. Om du vill återställa en återställningspunkt med fullständig eller differentiell gå vidare till steg 3.

    ![Välj Återställ punkt-menyn](./media/backup-azure-sql-database/recovery-point-menu.png)

    Punkten tidpunkt för återställning är endast tillgängligt för säkerhetskopior av databaser med fullständig & Bulk logged återställningsmodellen. Att återställa till en viss punkt i tid:

    1. Välj **loggar (tidpunkt)** som alternativ för återställning.

        ![Välj typ av återställning startpunkt](./media/backup-azure-sql-database/recovery-point-logs.png)

    2. Under **återställa tidsvärdet**, klicka på kalenderikonen för att öppna kalendern. Datum i fetstil innehåller återställningspunkter, och det aktuella datumet är markerat. Välj ett datum i kalendern med återställningspunkter. Du kan välja datum med några återställningspunkter.

        ![Öppna kalender](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

        När du har valt ett datum visar tidslinje-diagrammet tillgängliga återställningspunkter i ett intervall.

    3. Med tidslinje-diagrammet eller tid dialogrutan, ange en viss tid för återställningspunkten och klicka på **OK** att slutföra steget återställningspunkt.
    
       ![Öppna kalender](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

        Den **Välj återställningspunkt** menyn har stängts och **Advanced Configuration** menyn öppnas.

       ![Avancerad konfiguration-menyn](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    4. Från den **Advanced Configuration** menyn:

        * Att ha databasen inte kan användas efter återställning, på den **Restore med with NORECOVERY** väljer du **aktiverad**.
        * Om du vill ändra återställningsplatsen på målservern, ange en ny sökväg i den **mål** kolumn.
        * Klicka på **OK** att godkänna inställningarna och Stäng **Advanced Configuration**.

    5. På den **återställa** -menyn klickar du på **återställa** att starta återställningsjobbet. Du kan följa förloppet i området meddelanden. Du kan också spåra förloppet på återställningsjobb för databasen.

       ![Avancerad konfiguration-menyn](./media/backup-azure-sql-database/restore-job-notification.png)

3. I den **Välj återställningspunkt** -menyn, Välj en återställningspunkt. Du kan välja antingen en loggar (tidpunkt) eller fullständiga & Differential. Om du vill återställa en tidpunkt i logg går du tillbaka till steg 2. Det här steget återställs en specifik fullständig eller differentiell återställningspunkt. Med det här alternativet visas alla fullständig och differentiell återställningspunkter för de senaste 30 dagarna. Om du vill se återställningspunkter som är äldre än 30 dagar, klickar du på **Filter** att öppna den **Filter återställningspunkter** menyn. Om du väljer en differentiell återställningspunkt Azure Backup först återställer lämpliga fullständig återställningspunkten och tillämpar sedan den valda återställningspunkten Differential.

    ![Välj Återställ punkt-menyn](./media/backup-azure-sql-database/recovery-point-menu.png)

    1. I den **Välj återställningspunkt** -menyn, Välj **fullständiga & differentiella**.

       ![Välj Återställ punkt-menyn](./media/backup-azure-sql-database/recovery-point-logs-fd.png)

        Lista över tillgängliga återställningspunkter visas.

    2. Välj en återställningspunkt från listan över återställningspunkter, och klicka på **OK** Slutför proceduren återställningspunkt. 

        ![Välj fullständig återställningspunkt](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

        Den **återställningspunkt** menyn har stängts och **Advanced Configuration** menyn öppnas.

        ![Avancerad konfiguration-menyn](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    3. Från den **Advanced Configuration** menyn:

        * Att ha databasen inte kan användas efter återställning, på den **Restore med with NORECOVERY** väljer du **aktiverad**. **Återställa med NORECOVERY** är inaktiverat som standard.
        * Om du vill ändra återställningsplatsen på målservern, ange en ny sökväg i den **mål** kolumn.
        * Klicka på **OK** att godkänna inställningarna och Stäng **Advanced Configuration**.

    4. På den **återställa** -menyn klickar du på **återställa** att starta återställningsjobbet. Du kan följa förloppet i området meddelanden. Du kan också spåra förloppet på återställningsjobb för databasen.

       ![Avancerad konfiguration-menyn](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-and-overwrite-the-database"></a>Återställa och skriva över databasen

Den här proceduren går igenom återställa data och skriva över databasen. Om du vill återställa till en annan plats, hoppa till avsnittet [återställa till en alternativ plats](backup-azure-sql-database.md#restore-to-an-alternate-location). Den här proceduren förutsätter att du har öppnat din Recovery Services-valvet och på den **återställa konfigurationen** menyn (se följande bild). Om du inte börja med avsnittet [återställa en SQL-databas](backup-azure-sql-database.md#restore-a-sql-database).

![Klicka på Konfigurera om du vill öppna återställning configuration-menyn](./media/backup-azure-sql-database/restore-overwrite-db.png)

Den **Server** nedrullningsbara menyn visar bara SQL-servrar som har registrerats med Recovery Services-valvet. Om den server du vill inte är i den **Server** lista, se avsnittet [identifiera SQL server-databaser](backup-azure-sql-database.md#discover-sql-server-databases) att hitta servern. Under identifieringsprocessen databasen är nya servrar registrerade på Recovery Services-valvet.

1. I den **återställa konfigurationen** väljer du **skriva över DB** och på **OK** att slutföra konfigurera målet. 

   ![Klicka på över DB](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

    Den **Server**, **instans**, och **återställts databasnamn** dialogrutor behövs inte.

2. I den **Välj återställningspunkt** -menyn väljer du antingen en loggar (tidpunkt) eller fullständiga & differentiella återställningspunkt. Om du vill återställa en tidpunkt i logg fortsätta med det här steget. Om du vill återställa en återställningspunkt med fullständiga & differentiella gå vidare till steg 3.

    ![Välj Återställ punkt-menyn](./media/backup-azure-sql-database/recovery-point-menu.png)

    Punkten tidpunkt för återställning är endast tillgängligt för säkerhetskopior av databaser med fullständig & Bulk logged återställningsmodellen. Att välja en tidpunkt för återställning till en specifik andra:

    1. Välj **loggar (tidpunkt)** som alternativ för återställning.

        ![Välj typ av återställning startpunkt](./media/backup-azure-sql-database/recovery-point-logs.png)

    2. Under **återställa tidsvärdet**, klicka på kalenderikonen för att öppna kalendern. Datum i fetstil innehåller återställningspunkter, och det aktuella datumet är markerat. Välj ett datum i kalendern med återställningspunkter. Du kan välja datum med några återställningspunkter.

        ![Öppna kalender](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

        När du har valt ett datum visas i tidslinje-diagrammet tillgängliga återställningspunkter.

    3. Med tidslinje-diagrammet eller tid dialogrutan, ange en viss tid för återställningspunkten och klicka på **OK** att slutföra steget återställningspunkt.
    
       ![Öppna kalender](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

        Den **Välj återställningspunkt** menyn har stängts och **Advanced Configuration** menyn öppnas.

       ![Avancerad konfiguration-menyn](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    4. Från den **Advanced Configuration** menyn:

        * Att ha databasen inte kan användas efter återställning, på den **Restore med with NORECOVERY** väljer du **aktiverad**.
        * Om du vill ändra återställningsplatsen på målservern, ange en ny sökväg i den **mål** kolumn.
        * Klicka på **OK** att godkänna inställningarna och Stäng **Advanced Configuration**.

    5. På den **återställa** -menyn klickar du på **återställa** att starta återställningsjobbet. Du kan följa förloppet i området meddelanden. Du kan också spåra förloppet på återställningsjobb för databasen.

       ![Avancerad konfiguration-menyn](./media/backup-azure-sql-database/restore-job-notification.png)

3. I den **Välj återställningspunkt** -menyn, Välj en återställningspunkt. Du kan välja antingen en loggar (tidpunkt) eller fullständiga & Differential. Om du vill återställa en tidpunkt i logg går du tillbaka till steg 2. Det här steget återställs en specifik fullständig eller differentiell återställningspunkt. Med det här alternativet visas alla fullständig och differentiell återställningspunkter för de senaste 30 dagarna. Om du vill se återställningspunkter som är äldre än 30 dagar, klickar du på **Filter** att öppna den **Filter återställningspunkter** menyn. Om du väljer en differentiell återställningspunkt Azure Backup först återställer lämpliga fullständig återställningspunkten och tillämpar sedan den valda återställningspunkten Differential.

    ![Välj Återställ punkt-menyn](./media/backup-azure-sql-database/recovery-point-menu.png)

    1. I den **Välj återställningspunkt** -menyn, Välj **fullständiga & differentiella**.

       ![Välj Återställ punkt-menyn](./media/backup-azure-sql-database/recovery-point-logs-fd.png)

        Lista över tillgängliga återställningspunkter visas.

    2. Välj en återställningspunkt från listan över återställningspunkter, och klicka på **OK** Slutför proceduren återställningspunkt. 

        ![Välj fullständig återställningspunkt](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

        Den **återställningspunkt** menyn har stängts och **Advanced Configuration** menyn öppnas.

        ![Avancerad konfiguration-menyn](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    3. Från den **Advanced Configuration** menyn:

        * Att ha databasen inte kan användas efter återställning, på den **Restore med with NORECOVERY** väljer du **aktiverad**. **Återställa med NORECOVERY** är inaktiverat som standard.
        * Om du vill ändra återställningsplatsen på målservern, ange en ny sökväg i den **mål** kolumn.
        * Klicka på **OK** att godkänna inställningarna och Stäng **Advanced Configuration**.

    4. På den **återställa** -menyn klickar du på **återställa** att starta återställningsjobbet. Du kan följa förloppet i området meddelanden. Du kan också spåra förloppet på återställningsjobb för databasen.

       ![Avancerad konfiguration-menyn](./media/backup-azure-sql-database/restore-job-notification.png)


## <a name="manage-azure-backup-operations-for-sql-on-azure-vms"></a>Hantera Azure Backup-åtgärder för SQL på virtuella Azure-datorer

Det här avsnittet innehåller information om de olika Azure Backup hanteringsåtgärderna tillgängliga för SQL på virtuella Azure-datorer. Det finns följande avancerade åtgärder:

* Övervaka jobb
* Aviseringar om säkerhetskopiering
* Stoppa skydd på en SQL-databas
* Återuppta skydd för en SQL-databas
* Utlös en ad hoc-säkerhetskopieringsjobb
* Avregistrera en SQLServer

### <a name="monitor-jobs"></a>Övervaka jobb

Azure Backup använder SQL native API: er för alla säkerhetskopieringsåtgärder. Med den ursprungliga API: er kan du hämta alla jobbinformation från den [SQL säkerhetskopieuppsättningen tabell](https://docs.microsoft.com/sql/relational-databases/system-tables/backupset-transact-sql?view=sql-server-2017) i msdb-databasen. Dessutom Azure Backup visar alla manuellt utlösta eller ad hoc, jobb i jobb Backup-portalen. Projekten i portalen är: alla konfigurera säkerhetskopieringar för återställningsåtgärderna, registrering och identifiera databasåtgärder och stoppa säkerhetskopieringsåtgärder. Alla schemalagda jobb kan också övervakas med OMS logganalys. Med hjälp av logganalys tar bort jobb oreda och innehåller detaljerade flexibilitet för att övervaka eller filtrering specifika jobb.

![Avancerad konfiguration-menyn](./media/backup-azure-sql-database/jobs-list.png)

### <a name="backup-alerts"></a>Aviseringar om säkerhetskopiering

Med säkerhetskopieringar sker var 15: e minut, kan det vara tråkigt att ibland övervakning säkerhetskopieringsjobb. Azure-säkerhetskopiering planerade för detta potentiellt tråkigt genom att tillhandahålla e-postaviseringar som utlösts av alla säkerhetskopieringen har misslyckats. Aviseringar konsolideras på databasnivå av felkod. Om en databas har flera Säkerhetskopieringsfel i stället för att ta emot en avisering för varje fel får till exempel e-post för det första misslyckandet. Du kan logga in på Azure-portalen att övervaka efterföljande försök för den här databasen. 

Övervaka aviseringar om säkerhetskopiering:

1. Logga in på Azure-prenumerationen i den [Azure-portalen](https://portal.azure.com).

2. Öppna Recovery Services-valvet som är registrerad med SQL-dator.

3. I menyn Recovery Services-valvet väljer **aviseringar och händelser**. 

   ![Avancerad konfiguration-menyn](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

4. I den **aviseringar och händelser** väljer du **säkerhetskopiering aviseringar** att visa listan över aviseringar.

   ![Avancerad konfiguration-menyn](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

### <a name="stop-protection-on-a-sql-server-database"></a>Stoppa skydd på en SQL Server-databas

Om du slutar skydda en SQL Server-databas, frågar Azure Backup om du vill behålla återställningspunkterna. Det finns två sätt att sluta skydda SQL-databas:

* Stoppa alla framtida säkerhetskopieringsjobb och ta bort alla återställningspunkter
* Stoppa alla framtida säkerhetskopieringsjobb men lämna kvar återställningspunkter 

Lämna återställningspunkterna innebär en kostnad som återställningspunkterna för SQL utföra SQL-skyddade instansen priser kostnad plus lagringsutrymme som förbrukas. Mer information om Azure Backup priser för SQL finns i [Azure Backup sida med priser](https://azure.microsoft.com/pricing/details/backup/). Ta bort skyddet för databasen:

1. Öppna Recovery Services-valvet som är registrerad med SQL-dator.

2. I valvet-instrumentpanelen väljer **användning** säkerhetskopiering objekt att öppna menyn objekt för säkerhetskopiering.

    ![Klicka på + säkerhetskopia som du vill öppna menyn mål för säkerhetskopian](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

3. I den **Säkerhetskopieringsobjekt** -menyn, Välj vilken typ av säkerhetskopiering management, **SQL i Azure VM**. 

    ![Klicka på + säkerhetskopia som du vill öppna menyn mål för säkerhetskopian](./media/backup-azure-sql-database/sql-restore-backup-items.png)

    Listan säkerhetskopiering objekt justeras om du vill visa listan över SQL-databaser. 

4. Markera den databas du vill sluta skydda från listan över SQL-databaser.

    ![Välj SQL i Azure VM listan](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

    När du väljer databasen öppnas menyn. 

5. I den valda databas-menyn klickar du på **stoppa säkerhetskopiering** att sluta skydda databasen.

    ![Välj databas för återställning](./media/backup-azure-sql-database/stop-db-button.png)

    Den **stoppa säkerhetskopiering** menyn öppnas.

6. I den **stoppa säkerhetskopiering** menyn och välja att behålla säkerhetskopierade Data eller ta bort säkerhetskopierade Data. Du kan också kan du ange en orsak för att stoppa skydd och en kommentar.

    ![Välj databas för återställning](./media/backup-azure-sql-database/stop-backup-button.png)

7. Klicka på **stoppa säkerhetskopiering** upphöra med skyddet för databasen. 

### <a name="resume-protection-for-a-sql-database"></a>Återuppta skydd för en SQL-databas

Om den **behålla säkerhetskopierade Data** alternativet valdes när stoppa skydd för SQL-databas, är det möjligt att skyddet ska återupptas. Om den säkerhetskopiera informationen inte har kvar, kan inte skyddet återuppta. 

1. Om du vill återuppta skyddet för SQL-databasen, öppna objektet säkerhetskopiering och klicka på **återuppta säkerhetskopiering**.

    ![återuppta databasskyddet](./media/backup-azure-sql-database/resume-backup-button.png)

   Princip för säkerhetskopiering-menyn öppnas.

2. Från den **princip för säkerhetskopiering** menyn, Välj en princip och klicka på **spara**.

### <a name="trigger-an-adhoc-backup"></a>Utlös en ad hoc-säkerhetskopiering

Du kan utlösa en ad hoc-säkerhetskopiering när som helst. Det finns fyra typer av ad hoc-säkerhetskopiering. Mer information om varje typ, finns i artikeln [typer av SQL-säkerhetskopiering](https://docs.microsoft.com/sql/relational-databases/backup-restore/backup-overview-sql-server?view=sql-server-2017#types-of-backups).

* Fullständig säkerhetskopiering
* Kopiera endast fullständig säkerhetskopia
* Differentiell säkerhetskopiering
* Loggsäkerhetskopiering

### <a name="unregister-a-sql-server"></a>Avregistrera en SQLServer

Avregistrera en SQLServer efter att ta bort skydd, men innan du tar bort valvet

1. Öppna Recovery Services-valvet som är registrerad med SQL-dator.

2. I den **hantera** avsnittet valvet-menyn klickar du på **säkerhetskopiering infrastruktur**.  

   ![återuppta databasskyddet](./media/backup-azure-sql-database/backup-infrastructure-button.png)

3. I den **hanteringsservrar** klickar du på **skyddade servrar**.

   ![återuppta databasskyddet](./media/backup-azure-sql-database/protected-servers.png)

    Skyddade servrar menyn öppnas. 

4. I den **skyddade servrar** -menyn väljer du den server som du vill avregistrera. Om du vill ta bort valvet måste du avregistrera alla servrar.

5. Högerklicka på den skyddade servern i menyn skyddade servrar och välj **ta bort**. 

   ![återuppta databasskyddet](./media/backup-azure-sql-database/delete-protected-server.png)

## <a name="next-steps"></a>Nästa steg

Om du vill läsa mer om Azure Backup kan du ta en titt på PowerShell-exemplet för att säkerhetskopiera krypterade virtuella datorer.

> [!div class="nextstepaction"]
> [Säkerhetskopiera krypterade virtuella datorer](./scripts/backup-powershell-sample-backup-encrypted-vm.md)
