---
title: Säkerhetskopiera SQL Server-databas till Azure | Microsoft Docs
description: Den här självstudien beskrivs säkerhetskopiering av SQL Server till Azure. Den här artikeln beskriver också återställning av SQL Server.
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
ms.date: 7/6/2018
ms.author: markgal;anuragm
ms.custom: ''
ms.openlocfilehash: 32f45b66c4b1d22da3ffc4310a8a47c17319301f
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38302831"
---
# <a name="back-up-sql-server-database-in-azure"></a>Säkerhetskopiera SQL Server-databas i Azure

SQL Server-databaser är verksamhetskritiska arbetsbelastningar som kräver låg mål för återställningspunkt (RPO) och långsiktig kvarhållning. Azure Backup är en lösning för säkerhetskopiering SQL Server som kräver ingen infrastruktur, vilket innebär att ingen komplexa säkerhetskopieringsserver, ingen hanteringsagent eller lagring av säkerhetskopior att hantera. Azure Backup tillhandahåller centraliserad hantering av dina säkerhetskopior på alla SQL-servrar, eller med olika arbetsbelastningar.

 I den här artikeln lär du dig:

> [!div class="checklist"]
> * Förutsättningar för att säkerhetskopiera SQL Server till Azure
> * Skapa och använda ett Recovery Services-valv
> * Konfigurera säkerhetskopieringar för SQL Server-databas
> * Skapa en princip för säkerhetskopiering (eller kvarhållning) för återställningspunkterna
> * Så här återställer du databasen

Innan du startar procedurerna i den här artikeln bör du ha en SQL-Server som körs i Azure. [Du kan använda virtuella datorer med SQL marketplace för att snabbt skapa en SQL Server](../sql-database/sql-database-get-started-portal.md).

## <a name="public-preview-limitations"></a>Offentliga begränsningar i förhandsversionen

Följande är kända begränsningar för den offentliga förhandsversionen.

- SQL-dator kräver Internetanslutning för att komma åt Azure offentliga IP-adresser. Mer information finns i avsnittet [nätverksanslutningen](backup-azure-sql-database.md#establish-network-connectivity).
- Du kan skydda upp till 2 000 SQL-databaser i ett Recovery Services-valv. Ytterligare SQL-databaser ska lagras i separata Recovery Services-valvet.
- [Distribuerade tillgänglighet grupper säkerhetskopiering har begränsningar](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/distributed-availability-groups?view=sql-server-2017).
- SQL Failover Cluster Instances (FCI) stöds inte.
- Använd Azure-portalen för att konfigurera Azure Backup för att skydda SQL Server-databaser. Stöd för Azure PowerShell, CLI och REST API: er är inte tillgänglig.

## <a name="supported-azure-geos"></a>Azure-regioner som stöds

- Australien, sydöstra (ASE) 
- Brasilien, södra (BRS)
- Kanada, centrala (CNC)
- Kanada, östra (CE)
- USA, centrala (CUS)
- Asien, östra (EA)
- Australien, östra (AE) 
- USA, östra (EUS)
- USA, östra 2 (EUS2)
- Japan, östra (JPE)
- Japan, västra (JPW)
- Indien, centrala (INC) 
- Indien, södra (INS)
- Korea, centrala (KRC)
- Korea, södra (KRS)
- USA, norra centrala (NCUS) 
- Europa, norra (NE) 
- USA, södra centrala (SCUS) 
- Asien, sydöstra (SEA)
- Storbritannien, södra (UKS) 
- Storbritannien, västra (UKW) 
- Europa, västra (WE) 
- USA, västra (WUS)
- USA, västra centrala (WCUS)
- USA, västra 2 (WUS 2) 

## <a name="supported-operating-systems-and-versions-of-sql-server"></a>Operativsystem som stöds och versioner av SQLServer

Följande operativsystem stöds. SQL-marketplace Azure-datorer och inte finns på marketplace-datorer (där SQL Server är manuellt installerad), stöds.

### <a name="supported-operating-systems"></a>Operativsystem som stöds

- Windows Server 2012
- Windows Server 2012 R2
- Windows Server 2016

Linux stöds för närvarande inte.

### <a name="supported-versionseditions-of-sql-server"></a>Stöds versioner av SQL Server

- SQL 2012 Enterprise, Standard, webb, Developer, Express
- SQL 2014 Enterprise, Standard, webb, Developer, Express
- SQL 2016 Enterprise, Standard, webb, Developer, Express
- SQL 2017 Enterprise, Standard, webb, Developer, Express


## <a name="prerequisites-for-using-azure-backup-to-protect-sql-server"></a>Krav för att använda Azure Backup för att skydda SQL Server 

Kontrollera att följande villkor innan du kan säkerhetskopiera SQL Server-databasen. :

- Identifiera eller [skapar ett Recovery Services-valv](backup-azure-sql-database.md#create-a-recovery-services-vault) i samma region eller språk, som den virtuella datorn som är värd för SQL Server.
- [Kontrollera behörigheterna för den virtuella datorn](backup-azure-sql-database.md#set-permissions-for-non-marketplace-sql-vms) behövs för att säkerhetskopiera SQL-databaser.
- [SQL-dator är ansluten till nätverket](backup-azure-sql-database.md#establish-network-connectivity).

> [!NOTE]
> Du kan ha endast en lösning för säkerhetskopiering åt gången för att säkerhetskopiera SQL Server-databaser. Inaktivera andra SQL-säkerhetskopiering innan du använder den här funktionen, annan säkerhetskopieringar ska påverka och misslyckas. Du kan aktivera Azure Backup för IaaS VM tillsammans med SQL-säkerhetskopiering utan eventuella konflikter 
>

Om dessa villkor finns i din miljö, fortsätter du till avsnittet [konfigurera valvet för att skydda en SQL-databas](backup-azure-sql-database.md#configure-your-vault-to-protect-a-sql-database). Om några av krav inte finns, kan du fortsätta läsa det här avsnittet.


## <a name="establish-network-connectivity"></a>Upprätta nätverksanslutning

SQL-dator ha anslutning till Azure offentliga IP-adresser för alla åtgärder. SQL VM-åtgärder (till exempel databasidentifiering, konfigurera säkerhetskopieringen, schemalagda säkerhetskopieringar, återställa återställningspunkter och så vidare) misslyckas utan anslutning till offentliga IP-adresser. Använd något av följande alternativ för att tillhandahålla ett tydligt sätt för säkerhetskopieringstrafik:

- Lista över tillåtna Azure datacenter IP-intervall – godkänna Azure datacenter IP-intervall, Använd den [center hämtningssidan för information om IP-intervall och instruktioner](https://www.microsoft.com/download/details.aspx?id=41653). 
- Distribuera en HTTP-proxyserver dirigeras trafiken – när du säkerhetskopierar en SQL-databas på en virtuell dator, tillägget på den virtuella datorn använder HTTPS-API: er för att skicka kommandon för hantering av Azure Backup och data till Azure Storage. Säkerhetskopieringstillägget använder också Azure Active Directory (AAD) för autentisering. Dirigera säkerhetskopieringstillägget-trafik för dessa tre tjänster via HTTP-proxy, eftersom det är den enda komponenten som konfigurerats för åtkomst till det offentliga internet.

Rätt balans mellan alternativen är: hanterbarhet, detaljerad kontroll och kostnad.

> [!NOTE]
>Tjänsttaggar för Azure Backup ska vara tillgänglig vid allmän tillgänglighet.
>

| Alternativ | Fördelar | Nackdelar |
| ------ | ---------- | ------------- |
| Whitelist IP-intervall | Ingen extra kostnad. <br/> Använd för att öppna åtkomst i en NSG **Set-AzureNetworkSecurityRule** cmdlet. | Komplext för att hantera som den berörda IP-intervall ändras med tiden. <br/>Ger åtkomst till hela Azure, inte bara lagring.|
| Använda en HTTP-proxy   | Detaljerad kontroll i proxyn över lagringen URL: er tillåts. <br/>Enskild punkt för internet-åtkomst till virtuella datorer. <br/> Inte kan komma att ändras för Azure-IP-adress. | Ytterligare kostnader för att köra en virtuell dator med proxy-programvara. |

## <a name="set-permissions-for-non-marketplace-sql-vms"></a>Ange behörigheter för virtuella datorer inte finns i marketplace med SQL

Om du vill säkerhetskopiera en virtuell dator, Azure Backup kräver den **AzureBackupWindowsWorkload** att installera tillägget. Om du använder Azure marketplace-datorer kan gå vidare till [identifiera SQL server-databaser](backup-azure-sql-database.md#discover-sql-server-databases). Om den virtuella datorn som är värd för dina SQL-databaser inte har skapats från Azure marketplace, slutför du följande avsnitt för att installera tillägget och ange lämpliga behörigheter. Förutom den **AzureBackupWindowsWorkload** tillägg, Azure Backup kräver SQL sysadmin-behörighet för att skydda SQL-databaser. Vid identifiering av databaser på den virtuella datorn, skapar ett konto, NT Service\AzureWLBackupPluginSvc i Azure Backup. För Azure Backup för att identifiera SQL-databaser, NT Service\AzureWLBackupPluginSvc-kontot måste ha SQL och SQL sysadmin-behörighet. Följande procedur beskriver hur du anger dessa behörigheter.

Konfigurera behörigheter:

1. I den [Azure-portalen](https://portal.azure.com), öppna Recovery Services-valvet som du använder för att skydda SQL-databaser.
2. I valvets instrumentpanel klickar du på **+ säkerhetskopiera** att öppna den **säkerhetskopieringsmål** menyn.

   ![Klicka på + säkerhetskopiering för att öppna menyn säkerhetskopiering mål](./media/backup-azure-sql-database/open-backup-menu.png)

3. På den **säkerhetskopieringsmål** menyn i den **var körs din arbetsbelastning?** menyn lämna **Azure** som standard.

4. På den **vad vill du säkerhetskopiera** menyn Expandera den nedrullningsbara menyn och välj **SQL Server i Azure VM**.

    ![Klicka på + säkerhetskopiering för att öppna menyn säkerhetskopiering mål](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    Den **säkerhetskopieringsmål** menyn visas två nya steg: **identifiera databaser i virtuella datorer** och **Konfigurera säkerhetskopiering**. **Identifiera databaser i virtuella datorer** startar en sökning efter Azure-datorer.

    ![Visar de nya säkerhetskopiering mål stegen](./media/backup-azure-sql-database/backup-goal-menu-step-one.png)

5. Klicka på **Starta identifiering** att söka efter oskyddade virtuella datorer i prenumerationen. Beroende på antalet oskyddade virtuella datorer i prenumerationen kan ta det en stund att gå igenom alla virtuella datorer.

    ![Säkerhetskopiering väntar](./media/backup-azure-sql-database/discovering-sql-databases.png)
 
    När en virtuell dator i oskyddade identifieras så visas det i listan. Oskyddade virtuella datorer visas efter deras VM-namn och resursgrupp grupp. Det är möjligt för flera virtuella datorer har samma namn. Men virtuella datorer med samma namn som hör till olika resursgrupper. Om en virtuell dator i förväntade inte visas i listan, kan du se om den virtuella datorn redan skyddas till ett valv.

6. Från listan över virtuella datorer, väljer du den virtuella datorn som innehåller SQL-databasen som du vill säkerhetskopiera klicka sedan på **identifiera databaser**. 

    Identifieringsprocessen installerar den **AzureBackupWindowsWorkload** tillägget på den virtuella datorn. Tillägget kan kommunicera med den virtuella datorn så att du kan säkerhetskopiera SQL-databaser med Azure Backup-tjänsten. När tillägget-installerar Azure Backup skapar Windows virtuellt tjänstkonto, **NT Service\AzureWLBackupPluginSvc**, på den virtuella datorn. Det virtuella tjänstkontot kräver SQL sysadmin-behörighet. När virtuella konto installeras, om du ser felet, **UserErrorSQLNoSysadminMembership**, finns i avsnittet [åtgärda SQL sysadmin-behörighet](backup-azure-sql-database.md#fixing-sql-sysadmin-permissions).

    Meddelandefältet visar förloppet för identifieringen för databasen. Beroende på hur många databaser finns på den virtuella datorn, kan det ta en stund innan jobbet är klart. När valda databaser har identifierats, visas ett meddelande.

    ![meddelande för lyckad distribution](./media/backup-azure-sql-database/notifications-db-discovered.png)

När du kopplar databasen med Recovery Services-valvet, nästa steg är att [konfigurera säkerhetskopieringen](backup-azure-sql-database.md#configure-your-vault-to-protect-a-sql-database).

### <a name="fixing-sql-sysadmin-permissions"></a>Åtgärda SQL sysadmin-behörighet

Under installationen, om du ser felet, **UserErrorSQLNoSysadminMembership**, använda ett konto med SQL sysadmin-behörighet för att logga in till SQL Server Management Studio (SSMS). Om du inte behöver särskilda behörigheter, ska Windows-autentisering fungera.

1. På SQL-Server öppnar du den **Security/inloggningar** mapp.

    ![Öppna mapparna SQL Server- och säkerhets- och logga in om du vill se konton](./media/backup-azure-sql-database/security-login-list.png)

2. Högerklicka på mappen inloggningar och välj **ny inloggning**, och i inloggning - ny dialogrutan klickar du på **sökning**

    ![Öppna dialogrutan för sökning i inloggning – ny](./media/backup-azure-sql-database/new-login-search.png)

3. Eftersom Windows virtuellt tjänstkonto, **NT Service\AzureWLBackupPluginSvc** redan har skapats under registrering av virtuell dator och SQL-identifieringsfas, anger du namn som det visas i den  **Ange ett objektnamn att välja** dialogrutan. Klicka på **Kontrollera namn** att matcha namnet. 

    ![Klicka på Kontrollera namn för att lösa okänd tjänstens namn](./media/backup-azure-sql-database/check-name.png)

4. Klicka på **OK** att stänga dialogrutan Välj användare eller grupp.

5. I den **serverroller** dialogrutan, se till att den **sysadmin** roll har valts. Klicka sedan på **OK** att Stäng **inloggning – ny**.

    ![Kontrollera att serverrollen sysadmin har valts](./media/backup-azure-sql-database/sysadmin-server-role.png)

    Behörigheterna som krävs ska nu finnas.

6. Även om du har åtgärdat felet behörigheter behöver du fortfarande koppla databasen till Recovery Services-valvet. I Azure-portalen **skyddade servrar** listan, högerklicka på servern i fel och välj **identifiera databaser**.

    ![Kontrollera att servern har de behörigheter som krävs](./media/backup-azure-sql-database/check-erroneous-server.png)

    Meddelandefältet visar förloppet för identifieringen för databasen. Beroende på hur många databaser finns på den virtuella datorn, kan det ta en stund innan jobbet är klart. När valda databaser har hittats, visas ett meddelande i meddelandefältet.

    ![meddelande för lyckad distribution](./media/backup-azure-sql-database/notifications-db-discovered.png)

När du kopplar databasen med Recovery Services-valvet, nästa steg är att [konfigurera säkerhetskopieringen](backup-azure-sql-database.md#configure-your-vault-to-protect-a-sql-database).

[!INCLUDE [Section explaining how to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Identifiera SQL Server-databaser

Azure Backup kan identifiera alla databaser på en SQL Server-instansen så att du kan skydda dem per dina behov för säkerhetskopiering. Använd följande procedur för att identifiera den virtuella datorn som är värd för SQL-databaser. När du har identifierat den virtuella datorn installerar Azure Backup ett enkelt tillägg för att identifiera SQL server-databaser.

1. Logga in på din prenumeration i den [Azure-portalen](https://portal.azure.com/).
2. I den vänstra menyn och väljer **alla tjänster**.

    ![Välj alternativet för alla tjänster i huvudmenyn](./media/backup-azure-sql-database/click-all-services.png) <br/>

3. I dialogrutan för alla tjänster, Skriv *återställningstjänster*. När du börjar skriva, filtrerar listan över resurser i dina indata. När du ser det väljer **Recovery Services-valv**.

    ![Skriv återställningstjänster i dialogrutan för alla tjänster](./media/backup-azure-sql-database/all-services.png) <br/>

    Listan över Recovery Services-valv i prenumerationen visas. 

4. Från listan över Recovery Services-valv, väljer du valvet som du vill använda för att skydda din SQL-databaser.

5. I valvets instrumentpanel klickar du på **+ säkerhetskopiera** att öppna den **säkerhetskopieringsmål** menyn.

   ![Klicka på + säkerhetskopiering för att öppna menyn säkerhetskopiering mål](./media/backup-azure-sql-database/open-backup-menu.png)

6. På den **säkerhetskopieringsmål** menyn i den **var körs din arbetsbelastning?** menyn lämna **Azure** som standard.

7. På den **vad vill du säkerhetskopiera** menyn Expandera den nedrullningsbara menyn och välj **SQL Server i Azure VM**.

    ![Klicka på + säkerhetskopiering för att öppna menyn säkerhetskopiering mål](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    När den **säkerhetskopieringsmål** menyn visas två steg: identifiera databaser i virtuella datorer och konfigurera säkerhetskopiering. 

    ![Visar de nya säkerhetskopiering mål stegen](./media/backup-azure-sql-database/backup-goal-menu-step-one.png)

8. Klicka på **Starta identifiering** att söka efter oskyddade virtuella datorer i prenumerationen. Beroende på antalet oskyddade virtuella datorer i prenumerationen kan ta det en stund att gå igenom alla virtuella datorer.

    ![Säkerhetskopiering väntar](./media/backup-azure-sql-database/discovering-sql-databases.png)
 
    När en virtuell dator i oskyddade identifieras så visas det i listan. Flera virtuella datorer kan ha samma namn. Dock flera virtuella datorer med samma namn som hör till olika resursgrupper. De oskyddade virtuella datorerna visas efter deras VM-namn och resursgrupp grupp. Om en virtuell dator i förväntade inte visas kan du se om den virtuella datorn redan skyddas i ett valv.

9. Från listan över virtuella datorer, markerar du kryssrutan för den virtuella datorn som innehåller SQL-databaser som du vill skydda och klickar på **identifiera databaser**.

    Azure Backup identifierar alla SQL-databaser på den virtuella datorn. Information om vad som händer under fasen för identifiering av databasen finns i avsnittet nedan [Backend-åtgärder när identifiering av SQL-databaser](backup-azure-sql-database.md#backend-operations-when-discovering-sql-databases). Efter identifiering av SQL-databaser, är det dags att [konfigurera säkerhetskopieringsjobbet](backup-azure-sql-database.md#configure-your-vault-to-protect-a-sql-database).

### <a name="backend-operations-when-discovering-sql-databases"></a>Backend-åtgärder när identifiering av SQL-databaser

När du använder den **identifiera databaser** verktyg, Azure Backup utförs följande åtgärder i bakgrunden:

- registrerar den virtuella datorn med Recovery Services-valv för säkerhetskopiering av arbetsbelastning. Alla databaser på den registrerade virtuella datorn kan endast säkerhetskopieras till Recovery Services-valvet. 

- installerar de **AzureBackupWindowsWorkload** tillägget på den virtuella datorn. Säkerhetskopiering av en SQL-databas är en lösning för utan Agent, det vill säga med tillägget är installerat på den virtuella datorn även ingen agent är installerad på SQL-databasen.

- skapar tjänstkonto, **NT Service\AzureWLBackupPluginSvc**, på den virtuella datorn. Alla åtgärder för säkerhetskopiering och återställning använder kontot. **NT Service\AzureWLBackupPluginSvc** måste SQL sysadmin-behörighet. Alla virtuella datorer för SQL Marketplace medföljer SqlIaaSExtension installerad och AzureBackupWindowsWorkload använder SQLIaaSExtension för att automatiskt få behörigheter som krävs. Om den virtuella datorn har inte installerats SqlIaaSExtension, identifiera DB åtgärden misslyckas och du får felmeddelandet **UserErrorSQLNoSysAdminMembership**. Om du vill lägga till sysadmin-behörighet för säkerhetskopiering, följer du anvisningarna i [ställa in Azure Backup behörigheter för virtuella datorer inte finns i marketplace med SQL](backup-azure-sql-database.md#set-permissions-for-non--marketplace-sql-vms).

    ![Välj den virtuella datorn och databasen](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup-for-sql-server-database"></a>Konfigurera säkerhetskopiering för SQL Server-databas

Azure Backup tillhandahåller hanteringstjänster för att skydda SQL Server-databaser och hantera säkerhetskopieringsjobb. Hanterings- och övervakningsfunktioner beror på Recovery Services-valvet. 

> [!NOTE]
> Du kan ha endast en lösning för säkerhetskopiering åt gången för att säkerhetskopiera SQL Server-databaser. Inaktivera andra SQL-säkerhetskopiering innan du använder den här funktionen, annan säkerhetskopieringar ska påverka och misslyckas. Du kan aktivera Azure Backup för IaaS VM tillsammans med SQL-säkerhetskopiering utan eventuella konflikter 
>

Konfigurera skydd för SQL-databasen:

1. Öppna Recovery Services-valvet som är registrerad med SQL-dator.

2. I valvets instrumentpanel klickar du på **+ säkerhetskopiera** att öppna den **säkerhetskopieringsmål** menyn.

    ![Klicka på + säkerhetskopiering för att öppna menyn säkerhetskopiering mål](./media/backup-azure-sql-database/open-backup-menu.png)

3. På den **säkerhetskopieringsmål** menyn i den **var körs din arbetsbelastning?** menyn lämna **Azure** som standard.

4. På den **vad vill du säkerhetskopiera** menyn Expandera den nedrullningsbara menyn och välj **SQL Server i Azure VM**.

    ![Klicka på + säkerhetskopiering för att öppna menyn säkerhetskopiering mål](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    När den **säkerhetskopieringsmål** menyn visas två steg: identifiera databaser i virtuella datorer och konfigurera säkerhetskopiering. Om du har gått igenom den här artikeln i ordning, har du redan upptäckt oskyddade virtuella datorer och det här valvet har registrerats med en virtuell dator. Nu är du redo att konfigurera skydd för SQL-databaser.

5. Klicka på menyn säkerhetskopieringsmål **Konfigurera säkerhetskopiering**.

    ![Visar de nya säkerhetskopiering mål stegen](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

    Azure Backup-tjänsten visar alla SQL-instanser med fristående databaser samt SQL AlwaysOn-Tillgänglighetsgrupper. Klicka på ikonen bredvid namnet på instansen för att visa databaserna för att visa de fristående databaserna i SQL-instansen. Följande bilder visar exempel på en fristående instans och en Always On-tillgänglighetsgrupp.

    > [!NOTE]
    > När det gäller SQL Always On Availability Group respekterar vi inställning för SQL-säkerhetskopiering. Men på grund av en SQL-plattformsbegränsning fullständiga och differentiella säkerhetskopieringar måste göras från den primära noden. Säkerhetskopiering av loggen kan göras baserat på din inställning för säkerhetskopiering. På grund av den här begränsningen måste den primära noden alltid registreras för Tillgänglighetsgrupper.
    >

    ![Lista över databaser i SQL-instans](./media/backup-azure-sql-database/discovered-databases.png)

    Klicka på ikonen bredvid AlwaysOn-Tillgänglighetsgrupper för att visa listan över databaser.

    ![Lista över databaser i AlwaysOn-tillgänglighetsgrupp](./media/backup-azure-sql-database/discovered-database-availability-group.png)

6. I listan över databaser, väljer du alla som du vill skydda och klickar på **OK**.

    ![Välj flera databaser att skydda dem](./media/backup-azure-sql-database/select-multiple-database-protection.png)

    Du kan välja upp till 50 databaser i taget. Om du vill skydda fler än 50 databaser kan se flera pass. När du skyddar de första 50 databaserna kan du upprepa det här steget om du vill skydda en uppsättning databaser.
    > [!Note] 
    > För att optimera säkerhetskopierade belastning, delar Azure Backup upp stora säkerhetskopieringsjobb i flera batchar. Det maximala antalet databaser i en säkerhetskopiering är 50.
    >
    >

7. Att skapa eller välj en princip för säkerhetskopiering, i den **säkerhetskopiering** menyn och välj **säkerhetskopieringspolicy**, för att öppna menyn.

    ![Välj alternativet för princip för säkerhetskopiering](./media/backup-azure-sql-database/select-backup-policy.png)

8. Från den **Välj säkerhetskopieringspolicy** nedrullningsbara menyn, Välj en princip för säkerhetskopiering och klicka på **OK**. Information om hur du skapar en egen princip för säkerhetskopiering finns i avsnittet [definierar en säkerhetskopieringspolicy](backup-azure-sql-database.md#define-a-backup-policy).

    ![Välj en princip för säkerhetskopiering från den nedrullningsbara menyn](./media/backup-azure-sql-database/select-backup-policy-steptwo.png)

    I menyn säkerhetskopieringspolicy, från den **Välj säkerhetskopieringspolicy** nedrullningsbara menyn som du kan välja: 
    - standardprincipen för HourlyLogBackup 
    - en befintlig säkerhetskopieringspolicy som har skapats för SQL,
    - att [definiera en ny princip](backup-azure-sql-database.md#define-a-backup-policy) utifrån dina mål för återställningspunkt (RPO) och Kvarhållningsintervall. 

    > [!Note]
    > Azure Backup stöder långsiktig kvarhållning baserat på farfar-far-son säkerhetskopiering schemat att optimera serverdelen lagringsanvändningen samtidigt som du uppfyller efterlevnadskrav.
    >

9. När du har valt en princip för säkerhetskopiering, i den **säkerhetskopieringsmenyn** klickar du på **Aktivera säkerhetskopiering**.

    ![Aktivera den valda säkerhetskopieringsprincipen](./media/backup-azure-sql-database/enable-backup-button.png)

    Du kan spåra konfigurationsförloppet i meddelandefältet på portalen.

    ![Visa meddelandefältet](./media/backup-azure-sql-database/notifications-area.png)


### <a name="define-a-backup-policy"></a>definierar en säkerhetskopieringspolicy

En princip för säkerhetskopiering definierar en matris över när säkerhetskopiorna tas och hur länge säkerhetskopiorna ska bevaras. Du kan använda Azure Backup för att schemalägga tre typer av säkerhetskopiering för SQL-databaser:

* Fullständig säkerhetskopiering – en fullständig säkerhetskopia säkerhetskopierar hela databasen. En fullständig säkerhetskopia innehåller alla data i en specifik databas eller uppsättning filgrupper eller filer och tillräckligt med loggfiler att återställa dessa data. Du kan endast utlösa en fullständig säkerhetskopiering per dag. Du kan välja att ta en fullständig säkerhetskopiering med dagliga och veckovisa intervall. 
* Differentiell säkerhetskopiering – en differentiell säkerhetskopiering baseras på den senaste, föregående fullständig säkerhetskopieringen. En differentiell säkerhetskopiering fångar endast de data som har ändrats sedan den fullständiga säkerhetskopian. Du kan endast utlösa en differentiell säkerhetskopiering per dag. Du kan inte konfigurera en fullständig säkerhetskopia och en differentiell säkerhetskopiering på samma dag.
* Säkerhetskopiering av transaktionsloggen – en säkerhetskopiering av loggen kan point-in-time-återställning upp till en specifik sekund. Du kan högst, konfigurera transaktionell loggsäkerhetskopior var 15: e minut.

Principen har skapats på Recovery Services-valvet nivå. Om du har flera valv valv kan använda samma säkerhetskopieringsprincip, men du måste använda principen för säkerhetskopiering för varje valv. När du skapar en princip för säkerhetskopiering, varje dag, är fullständig säkerhetskopiering standardinställningen. Du kan lägga till en differentiell säkerhetskopiering, men endast om du växlar fullständiga säkerhetskopieringar ska göras varje vecka. Följande procedur beskriver hur du skapar en princip för säkerhetskopiering för en SQLServer i virtuella Azure-datorer.

Skapa en princip för säkerhetskopiering

1. På menyn säkerhetskopieringspolicy, från den **Välj säkerhetskopieringspolicy** nedrullningsbara menyn och välj **Skapa ny**.

   ![Skapa ny säkerhetskopieringsprincip](./media/backup-azure-sql-database/create-new-backup-policy.png)

    Menyn säkerhetskopieringspolicy växlar ange fälten som krävs för en ny säkerhetskopieringsprincip för SQL server.

   ![den nya principen för säkerhetskopiering fält](./media/backup-azure-sql-database/blank-new-policy.png)

2. I **principnamn**, ange ett namn. 

3. En fullständig säkerhetskopia är obligatoriskt. Du kan acceptera standardvärdena för fullständig säkerhetskopiering eller klicka på **fullständig säkerhetskopiering** att redigera principen.

    ![den nya principen för säkerhetskopiering fält](./media/backup-azure-sql-database/full-backup-policy.png)

    Välj dagliga eller veckovisa för frekvensen i principen fullständig säkerhetskopiering. Om du väljer varje dag, väljer du timme och tidszon när jobbet börjar. Om du väljer dagliga fullständiga säkerhetskopior kan skapa du inte differentiella säkerhetskopieringar.

   ![inställningen för varje dag](./media/backup-azure-sql-database/daily-interval.png)

    Om du väljer varje vecka, väljer du dagen i veckan, timme och tidszonen när jobbet börjar.

   ![inställningen för varje vecka](./media/backup-azure-sql-database/weekly-interval.png)

4. Som standard markeras alla alternativ för Kvarhållningsintervallet (dagliga, veckovisa, månatliga och årliga). Avmarkera alla kvarhållningsintervallet som du inte vill och ange intervall för att använda. I menyn fullständig säkerhetskopiering principen klickar du på **OK** att acceptera inställningarna.

   ![kvarhållningsinställning intervallet intervall](./media/backup-azure-sql-database/retention-range-interval.png)

    Återställningspunkter är taggade för kvarhållning, baserat på deras Kvarhållningsintervall. Exempel: Om du väljer en daglig utlöses fullständig säkerhetskopiering endast en fullständig säkerhetskopiering varje dag. Beroende på din kvarhållning av veckovis, specifik dag säkerhetskopiering taggade och bevaras baserat på veckovisa kvarhållningsintervallet. Månatliga och årliga Kvarhållningsintervall fungerar på samma sätt.

5. Om du vill lägga till en princip för differentiell säkerhetskopiering, klickar du på **differentiell säkerhetskopiering** menyn öppnas. 

   ![Öppna differentiell princip](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

    I menyn differentiell säkerhetskopiering princip väljer **aktivera** att öppna kontrollerna frekvens och kvarhållning. Du kan endast utlösa en differentiell säkerhetskopiering per dag.
    > [!Important] 
    > Mest kan differentiella säkerhetskopieringar bevaras i 180 dagar. Om du behöver längre kvarhållning, måste du använda fullständiga säkerhetskopieringar kan du använda inte differentiella säkerhetskopieringar.
    >

   ![Redigera differentiell princip](./media/backup-azure-sql-database/enable-differential-backup-policy.png)

    Klicka på **OK** att spara principen och återgå till huvudmenyn i Backup-principen.

6. Om du vill lägga till en transaktionell princip för säkerhetskopiering av loggen, klickar du på **Loggsäkerhetskopiering** menyn öppnas. I menyn Loggsäkerhetskopiering väljer **aktivera**, och Ställ in frekvensen och kvarhållning kontroller. Loggsäkerhetskopior kan ske så ofta som var 15: e minut och kan behållas i upp till 35 dagar. Klicka på **OK** att spara principen och återgå till huvudmenyn i Backup-principen.

   ![Redigera princip för säkerhetskopiering av loggen](./media/backup-azure-sql-database/log-backup-policy-editor.png)

7. Välj om du vill aktivera komprimering av SQL-säkerhetskopiering. Komprimering är inaktiverad som standard.

    På serversidan använder Azure Backup SQL native säkerhetskopieringskomprimering.

8. När du har gjort alla ändringar till princip för säkerhetskopiering, klickar du på **OK**. 

   ![Acceptera ny princip](./media/backup-azure-sql-database/backup-policy-click-ok.png)

## <a name="restore-a-sql-database"></a>Återställa en SQL-databas

Azure Backup innehåller funktioner för att återställa enskilda databaser till ett visst datum eller tid, upp till en specifik andra med säkerhetskopieringar av transaktionsloggen. Baserat på återställningstiderna som du anger kan Azure Backup bestämmer automatiskt de aktuella fullständig, differentiell och kedja av loggsäkerhetskopior som krävs för att återställa dina data.

Du kan även välja en fullständig eller Differentiell säkerhetskopia att återställa till en specifik återställningspunkt i stället för en viss tid.
 > [!Note]
 > Innan du aktiverar återställning av ”master”-databasen starta SQL Server i enanvändarläge med startalternativ ”-m AzureWorkloadBackup”. Argumentet för -m är namnet på klienten, endast den här klienten ska tillåtas att öppna anslutningen. Stoppa tjänsten SQL Agent innan du aktiverar återställning för alla systemdatabaser (modell, master, msdb). Stäng alla program som kan försöka stjäla en anslutning till någon av dessa databaser.
>

Du återställer en databas

1. Öppna Recovery Services-valvet som är registrerad med SQL-dator.

2. I instrumentpanelen för valvet väljer **användning** Säkerhetskopieringsobjekt för att öppna menyn Säkerhetskopieringsobjekt.

    ![Klicka på + säkerhetskopiering för att öppna menyn säkerhetskopiering mål](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

3. I den **Säkerhetskopieringsobjekt** menyn, Välj typ av säkerhetskopieringshantering **SQL i Azure VM**. 

    ![Klicka på + säkerhetskopiering för att öppna menyn säkerhetskopiering mål](./media/backup-azure-sql-database/sql-restore-backup-items.png)

    I listan med Säkerhetskopieringsobjekt justeras för att visa listan över SQL-databaser. 

4. Välj den databas som du vill återställa från listan över SQL-databaser.

    ![Välj SQL i Azure VM från listan](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

    När du väljer databasen, öppnar menyn. Den här menyn visar säkerhetskopiering information för databasen, inklusive:

    * den äldsta och de senaste återställningspunkter
    * Logga Säkerhetskopieringsstatus för de senaste 24 timmarna (för databaser i Full- och samtidigt loggas återställningsmodellen, om konfigurerad för transaktionell loggsäkerhetskopior)

5. I den valda databas-menyn klickar du på **återställa DB** att öppna menyn återställning.

    ![Välj Återställ databas](./media/backup-azure-sql-database/restore-db-button.png)

    Den **återställa** öppnas och så gör den **återställa konfigurationen** menyn. Den **återställa konfigurationen** menyn är det första steget i att konfigurera återställningen. I den här menyn väljer du var du vill återställa data. Alternativen är:
    * Alternativ plats – Använd det här alternativet om du vill återställa databasen till en alternativ plats utan att försämra analysera.
    * Skriv över DB – återställer data till samma SQL Server-instans som den ursprungliga källan. Effekten av detta är du skriva över den ursprungliga databasen.

    > [!Important]
    > Om den valda databasen tillhör en Always On-tillgänglighetsgrupp, tillåter inte SQL databasen kan skrivas över. I det här fallet bara den **alternativ plats** är aktiverat.
    >

    ![Klicka på Konfigurera för att öppna menyn för konfiguration av återställning](./media/backup-azure-sql-database/restore-restore-configuration-menu.png)

### <a name="restore-to-an-alternate-location"></a>Återställ till en alternativ plats

Den här proceduren beskriver återställer data till en annan plats. Om du vill skriva över databasen när du återställer går du vidare till avsnittet [återställa och skriva över databasen](backup-azure-sql-database.md#restore-and-overwrite-the-database). Den här proceduren förutsätter att du har ditt Recovery Services-valv är öppet och finns på menyn återställa konfigurationen. Om du inte börja med avsnittet [återställa en SQL database](backup-azure-sql-database.md#restore-a-sql-database).

> [!NOTE]
> Du kan återställa databasen till en SQL-Server i samma Azure-region och målservern måste vara registrerad till Recovery Services-valvet. 
>

Den **Server** nedrullningsbara menyn visar bara de SQL-servrar som registrerats med Recovery Services-valvet. Om den server som inte är i den **Server** listan, finns i avsnittet [identifiera SQL server-databaser](backup-azure-sql-database.md#discover-sql-server-databases) att hitta servern. Under identifieringsprocessen för databasen är nya servrar registrerade på Recovery Services-valvet.

1. I den **återställa konfigurationen** menyn:

    * Välj **alternativ plats**,
    * för **Server**, väljer du den SQLServer där du vill återställa databasen.
    * i den **instans** nedrullningsbara menyn, Välj en SQL-instans
    * i den **återställt databasnamn** dialogrutan, ange namnet på måldatabasen.
    * Om så är tillämpligt, Välj **Åsidosätt om databasen med samma namn finns redan på den valda SQL-instansen**.
    * Klicka på **OK** slutförts konfigurera mål och flytta till att välja en återställningspunkt.

    ![Välj alternativ plats, instans och namn på menyn för återställning-konfiguration](./media/backup-azure-sql-database/restore-configuration-menu.png)

2. I den **: Välj återställningspunkt** menyn kan du välja antingen en loggar (tidpunkt) eller fullständig och differentiell återställningspunkt. Om du vill återställa till en viss point-in-time-loggning, fortsätter du med det här steget. Om du vill återställa en fullständig eller differentiell återställningspunkt kan du gå vidare till steg 3.

    ![Välj Återställ point-menyn](./media/backup-azure-sql-database/recovery-point-menu.png)

    Punkten tidpunkt för återställning är bara tillgängligt för säkerhetskopieringar för databaser med fullständig & Bulk logged återställningsmodellen. Att återställa till en viss punkt i tid:

    1. Välj **loggar (tidpunkt)** som alternativ för återställning.

        ![Välj typ av återställning](./media/backup-azure-sql-database/recovery-point-logs.png)

    2. Under **återställa datum/tid**, klickar du på kalenderikonen för att öppna kalendern. Datum i fetstil innehålla återställningspunkter det aktuella datumet är markerat. Välj ett datum i kalendern med återställningspunkter. Du kan inte välja datum med några återställningspunkter.

        ![Öppna kalender](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

        När du har valt ett datum visar i tidslinjen diagrammet tillgängliga återställningspunkter intill varandra.

    3. Med tidslinjediagram eller dialogruta i, ange en viss tidpunkt för återställningspunkten och klicka på **OK** att utföra steget återställningspunkt.
    
       ![Öppna kalender](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

        Den **: Välj återställningspunkt** menyn har stängts och **Advanced Configuration** menyn öppnas.

       ![menyn för avancerad konfiguration](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    4. Från den **Advanced Configuration** menyn:

        * Att ha databasen inte kan användas efter återställning, på den **Återställ med NORECOVERY** menyn och välj **aktiverad**.
        * Om du vill ändra återställningsplatsen på målservern, ange en ny sökväg i den **Target** kolumn.
        * Klicka på **OK** att godkänna inställningarna och Stäng **Advanced Configuration**.

    5. På den **återställa** -menyn klickar du på **återställa** att starta återställningsjobbet. Du kan följa förloppet i området för meddelanden. Du kan också spåra förloppet i återställningsjobb för databasen.

       ![menyn för avancerad konfiguration](./media/backup-azure-sql-database/restore-job-notification.png)

3. I den **: Välj återställningspunkt** menyn, Välj en återställningspunkt. Du kan välja antingen en loggar (tidpunkt) eller fullständig och differentiell. Om du vill återställa en point-in-time-logg, går du tillbaka till steg 2. Det här steget återställs en specifik fullständig eller differentiell återställningspunkt. Med det här alternativet kan du se alla fullständig och differentiell återställningspunkter under de senaste 30 dagarna. Om du vill se återställningspunkter som är äldre än 30 dagar, klickar du på **Filter** att öppna den **Filter återställningspunkter** menyn. Om du väljer en differentiell återställningspunkt måste Azure Backup först återställer lämplig fullständig återställningspunkt och tillämpar sedan den valda differentiella återställningspunkten.

    ![Välj Återställ point-menyn](./media/backup-azure-sql-database/recovery-point-menu.png)

    1. I den **: Välj återställningspunkt** menyn, Välj **fullständig och differentiell**.

       ![Välj Återställ point-menyn](./media/backup-azure-sql-database/recovery-point-logs-fd.png)

        Lista över tillgängliga återställningspunkter visas.

    2. Välj en återställningspunkt i listan med återställningspunkter och klicka på **OK** Slutför proceduren återställningspunkt. 

        ![Välj fullständig återställningspunkt](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

        Den **återställningspunkt** menyn har stängts och **Advanced Configuration** menyn öppnas.

        ![menyn för avancerad konfiguration](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    3. Från den **Advanced Configuration** menyn:

        * Att ha databasen inte kan användas efter återställning, på den **Återställ med NORECOVERY** menyn och välj **aktiverad**. **Återställ med NORECOVERY** är inaktiverad som standard.
        * Om du vill ändra återställningsplatsen på målservern, ange en ny sökväg i den **Target** kolumn.
        * Klicka på **OK** att godkänna inställningarna och Stäng **Advanced Configuration**.

    4. På den **återställa** -menyn klickar du på **återställa** att starta återställningsjobbet. Du kan följa förloppet i området för meddelanden. Du kan också spåra förloppet i återställningsjobb för databasen.

       ![menyn för avancerad konfiguration](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-and-overwrite-the-database"></a>Återställa och skriva över databasen

Den här proceduren beskriver återställning av data och skriva över databasen. Om du vill återställa till en annan plats går du vidare till avsnittet [återställa till en alternativ plats](backup-azure-sql-database.md#restore-to-an-alternate-location). Den här proceduren förutsätter att du har ditt Recovery Services-valv är öppet och finns på den **återställa konfigurationen** menyn (se följande bild). Om du inte börja med avsnittet [återställa en SQL database](backup-azure-sql-database.md#restore-a-sql-database).

![Klicka på Konfigurera för att öppna menyn för konfiguration av återställning](./media/backup-azure-sql-database/restore-overwrite-db.png)

Den **Server** nedrullningsbara menyn visar bara de SQL-servrar som registrerats med Recovery Services-valvet. Om den server som inte är i den **Server** listan, finns i avsnittet [identifiera SQL server-databaser](backup-azure-sql-database.md#discover-sql-server-databases) att hitta servern. Under identifieringsprocessen för databasen är nya servrar registrerade på Recovery Services-valvet.

1. I den **återställa konfigurationen** menyn och välj **skriva över DB** och klicka på **OK** att slutföra konfigurera mål. 

   ![Klicka på Skriv över DB](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

    Den **Server**, **instans**, och **återställt databasnamn** dialogrutor krävs inte.

2. I den **: Välj återställningspunkt** menyn kan du välja antingen en loggar (tidpunkt) eller fullständig och differentiell återställningspunkt. Om du vill återställa en point-in-time-logg kan fortsätta med det här steget. Om du vill återställa en fullständig och differentiell återställningspunkt kan du gå vidare till steg 3.

    ![Välj Återställ point-menyn](./media/backup-azure-sql-database/recovery-point-menu.png)

    Punkten tidpunkt för återställning är bara tillgängligt för säkerhetskopieringar för databaser med fullständig & Bulk logged återställningsmodellen. Att välja en tidpunkt för återställning till en viss andra:

    1. Välj **loggar (tidpunkt)** som alternativ för återställning.

        ![Välj typ av återställning](./media/backup-azure-sql-database/recovery-point-logs.png)

    2. Under **återställa datum/tid**, klickar du på kalenderikonen för att öppna kalendern. Datum i fetstil innehålla återställningspunkter det aktuella datumet är markerat. Välj ett datum i kalendern med återställningspunkter. Du kan inte välja datum med några återställningspunkter.

        ![Öppna kalender](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

        När du har valt ett datum visas i tidslinjen diagrammet tillgängliga återställningspunkter.

    3. Med tidslinjediagram eller dialogruta i, ange en viss tidpunkt för återställningspunkten och klicka på **OK** att utföra steget återställningspunkt.
    
       ![Öppna kalender](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

        Den **: Välj återställningspunkt** menyn har stängts och **Advanced Configuration** menyn öppnas.

       ![menyn för avancerad konfiguration](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    4. Från den **Advanced Configuration** menyn:

        * Att ha databasen inte kan användas efter återställning, på den **Återställ med NORECOVERY** menyn och välj **aktiverad**.
        * Om du vill ändra återställningsplatsen på målservern, ange en ny sökväg i den **Target** kolumn.
        * Klicka på **OK** att godkänna inställningarna och Stäng **Advanced Configuration**.

    5. På den **återställa** -menyn klickar du på **återställa** att starta återställningsjobbet. Du kan följa förloppet i området för meddelanden. Du kan också spåra förloppet i återställningsjobb för databasen.

       ![menyn för avancerad konfiguration](./media/backup-azure-sql-database/restore-job-notification.png)

3. I den **: Välj återställningspunkt** menyn, Välj en återställningspunkt. Du kan välja antingen en loggar (tidpunkt) eller fullständig och differentiell. Om du vill återställa en point-in-time-logg, går du tillbaka till steg 2. Det här steget återställs en specifik fullständig eller differentiell återställningspunkt. Med det här alternativet kan du se alla fullständig och differentiell återställningspunkter under de senaste 30 dagarna. Om du vill se återställningspunkter som är äldre än 30 dagar, klickar du på **Filter** att öppna den **Filter återställningspunkter** menyn. Om du väljer en differentiell återställningspunkt måste Azure Backup först återställer lämplig fullständig återställningspunkt och tillämpar sedan den valda differentiella återställningspunkten.

    ![Välj Återställ point-menyn](./media/backup-azure-sql-database/recovery-point-menu.png)

    1. I den **: Välj återställningspunkt** menyn, Välj **fullständig och differentiell**.

       ![Välj Återställ point-menyn](./media/backup-azure-sql-database/recovery-point-logs-fd.png)

        Lista över tillgängliga återställningspunkter visas.

    2. Välj en återställningspunkt i listan med återställningspunkter och klicka på **OK** Slutför proceduren återställningspunkt. 

        ![Välj fullständig återställningspunkt](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

        Den **återställningspunkt** menyn har stängts och **Advanced Configuration** menyn öppnas.

        ![menyn för avancerad konfiguration](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    3. Från den **Advanced Configuration** menyn:

        * Att ha databasen inte kan användas efter återställning, på den **Återställ med NORECOVERY** menyn och välj **aktiverad**. **Återställ med NORECOVERY** är inaktiverad som standard.
        * Om du vill ändra återställningsplatsen på målservern, ange en ny sökväg i den **Target** kolumn.
        * Klicka på **OK** att godkänna inställningarna och Stäng **Advanced Configuration**.

    4. På den **återställa** -menyn klickar du på **återställa** att starta återställningsjobbet. Du kan följa förloppet i området för meddelanden. Du kan också spåra förloppet i återställningsjobb för databasen.

       ![menyn för avancerad konfiguration](./media/backup-azure-sql-database/restore-job-notification.png)


## <a name="manage-azure-backup-operations-for-sql-on-azure-vms"></a>Hantera Azure Backup-åtgärder för SQL på Azure Virtual Machines

Det här avsnittet innehåller information om de olika Azure Backup hanteringsåtgärderna tillgänglig för SQL på Azure virtual machines. Följande avancerade åtgärder finns:

* Övervaka jobb
* Säkerhetskopieringsaviseringar
* Stoppa skydd på en SQL-databas
* Återuppta skyddet av en SQL-databas
* Utlösa en adhoc-säkerhetskopieringsjobb
* Avregistrera en SQL-server

### <a name="monitor-jobs"></a>Övervaka jobb
Azure Backup är en lösning för klassen innehåller avancerade säkerhetskopiering aviseringar och meddelanden efter fel (Se säkerhetskopiering aviseringar nedan). Om du vill övervaka specifika jobb kan du använda någon av följande alternativ efter behov:

#### <a name="use-azure-portal-for-all-adhoc-operations"></a>Använd Azure-portalen för alla ad hoc-åtgärder
Azure Backup visar utlöses alla manuellt eller ad hoc,-jobb i portalen för Backup-jobb. Jobb som är tillgängliga i portalen Inkluderingen: alla konfigurera säkerhetskopieringsåtgärder manuellt utlöses säkerhetskopieringsåtgärder, återställningsåtgärder, registrering och identifiera databasåtgärder och stoppa säkerhetskopiering. 
![menyn för avancerad konfiguration](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> Alla schemalagda säkerhetskopieringsjobb inklusive fullständiga, differentiella och Loggbaserade säkerhetskopiering kommer inte att visas i portalen och kan övervakas med SQL Server Management Studio enligt beskrivningen nedan.
>

#### <a name="use-sql-server-management-studio-for-backup-jobs"></a>Använd SQL Server Management Studio för säkerhetskopieringsjobb
Azure Backup använder SQL interna API: er för alla säkerhetskopieringsåtgärder. Med inbyggda API: er, kan du hämta alla jobbinformation från den [SQL-tabell för säkerhetskopian](https://docs.microsoft.com/sql/relational-databases/system-tables/backupset-transact-sql?view=sql-server-2017) i msdb-databasen.

I följande exempel är en fråga för att hämta alla säkerhetskopieringsjobb för en databas med namnet, **DB1**. Anpassa frågan för mer avancerad övervakning.
```
select CAST (
Case type
                when 'D' 
                                 then 'Full'
                when  'I'
                               then 'Differential' 
                ELSE 'Log'
                END         
                AS varchar ) AS 'BackupType',
database_name, 
server_name,
machine_name,
backup_start_date,
backup_finish_date,
DATEDIFF(SECOND, backup_start_date, backup_finish_date) AS TimeTakenByBackupInSeconds,
backup_size AS BackupSizeInBytes
  from msdb.dbo.backupset where user_name = 'NT SERVICE\AzureWLBackupPluginSvc' AND database_name =  <DB1>  
 
```

### <a name="backup-alerts"></a>Säkerhetskopieringsaviseringar

Med säkerhetskopieringar sker var 15: e minut, kan det vara tidsödande att ibland övervakning säkerhetskopieringsjobben. Azure Backup planerat för den här potentiellt tedious situationen genom att tillhandahålla e-postaviseringar som utlöses av alla Säkerhetskopieringsfel. Aviseringar konsolideras på databasnivå med hjälp av felkoder. Om en databas har flera Säkerhetskopieringsfel, i stället för att ta emot en avisering för varje fel får till exempel e-post till första felet. Du kan logga in på Azure portal att övervaka efterföljande försök för den här databasen. 

Övervaka aviseringar om säkerhetskopiering:

1. Logga in på Azure-prenumerationen i den [Azure-portalen](https://portal.azure.com).

2. Öppna Recovery Services-valvet som är registrerad med SQL-dator.

3. I menyn Recovery Services-valv väljer **aviseringar och händelser**. 

   ![menyn för avancerad konfiguration](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

4. I den **aviseringar och händelser** menyn och välj **säkerhetskopiering aviseringar** att visa listan över aviseringar.

   ![menyn för avancerad konfiguration](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

### <a name="stop-protection-on-a-sql-server-database"></a>Stoppa skydd på en SQL Server-databas

Om du slutar skydda en SQL Server-databas, frågar Azure Backup om du vill behålla återställningspunkterna. Det finns två sätt att sluta skydda SQL-databas:

* Stoppa alla framtida säkerhetskopieringsjobb och ta bort alla återställningspunkter
* Stoppa alla framtida säkerhetskopieringsjobb, men lämna kvar återställningspunkterna 

Lämna kvar återställningspunkterna innebär en kostnad som återställningspunkter för SQL har den skyddade SQL-instansen priser kostnad, plus förbrukad lagring. Läs mer om Azure Backup priser för SQL, den [sidan med prissättning för Azure Backup](https://azure.microsoft.com/pricing/details/backup/). Stoppa skyddet av databasen:

1. Öppna Recovery Services-valvet som är registrerad med SQL-dator.

2. I instrumentpanelen för valvet väljer **användning** Säkerhetskopieringsobjekt för att öppna menyn Säkerhetskopieringsobjekt.

    ![Klicka på + säkerhetskopiering för att öppna menyn säkerhetskopiering mål](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

3. I den **Säkerhetskopieringsobjekt** menyn, Välj typ av säkerhetskopieringshantering **SQL i Azure VM**. 

    ![Klicka på + säkerhetskopiering för att öppna menyn säkerhetskopiering mål](./media/backup-azure-sql-database/sql-restore-backup-items.png)

    I listan med Säkerhetskopieringsobjekt justeras för att visa listan över SQL-databaser. 

4. Välj den databas du vill sluta skydda från listan över SQL-databaser.

    ![Välj SQL i Azure VM från listan](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

    När du väljer databasen, öppnar menyn. 

5. I den valda databas-menyn klickar du på **stoppa säkerhetskopiering** att sluta skydda databasen.

    ![Välj Återställ databas](./media/backup-azure-sql-database/stop-db-button.png)

    Den **stoppa säkerhetskopiering** menyn öppnas.

6. I den **stoppa säkerhetskopiering** menyn välja att Behåll säkerhetskopieringsdata eller ta bort säkerhetskopieringsdata. Alternativt kan du ange ett skäl för att stoppa skydd och en kommentar.

    ![Välj Återställ databas](./media/backup-azure-sql-database/stop-backup-button.png)

7. Klicka på **stoppa säkerhetskopiering** upphöra med skyddet på databasen. 

### <a name="resume-protection-for-a-sql-database"></a>Återuppta skyddet av en SQL-databas

Om den **Behåll säkerhetskopieringsdata** alternativet valdes när stoppa skydd för SQL-databas, är det möjligt att återuppta skyddet. Om den säkerhetskopierade data inte har kvar, kan inte skyddet återuppta. 

1. Om du vill återuppta skyddet av SQL-databasen, öppna säkerhetskopieringsobjektet och klicka på **återuppta säkerhetskopiering**.

    ![återuppta databasskyddet](./media/backup-azure-sql-database/resume-backup-button.png)

   Menyn säkerhetskopieringspolicy öppnas.

2. Från den **Säkerhetskopieringspolicyn** menyn, Välj en princip och klicka på **spara**.

### <a name="trigger-an-adhoc-backup"></a>Utlös en ad hoc-säkerhetskopiering

Du kan utlösa en ad hoc-säkerhetskopiering när som helst. Det finns fyra typer av ad hoc-säkerhetskopiering. Mer information om varje typ finns i artikeln [typer av SQL-säkerhetskopior](https://docs.microsoft.com/sql/relational-databases/backup-restore/backup-overview-sql-server?view=sql-server-2017#types-of-backups).

* Fullständig säkerhetskopiering
* Kopiera bara fullständig säkerhetskopia
* Differentiell säkerhetskopia
* Loggsäkerhetskopia

### <a name="unregister-a-sql-server"></a>Avregistrera en SQL-Server

Avregistrera en SQLServer när du tar bort skydd, men innan du tar bort valvet

1. Öppna Recovery Services-valvet som är registrerad med SQL-dator.

2. I den **hantera** avsnittet vault-menyn klickar du på **infrastruktur för säkerhetskopiering**.  

   ![återuppta databasskyddet](./media/backup-azure-sql-database/backup-infrastructure-button.png)

3. I den **hanteringsservrar** klickar du på **skyddade servrar**.

   ![återuppta databasskyddet](./media/backup-azure-sql-database/protected-servers.png)

    Skyddade servrar-menyn öppnas. 

4. I den **skyddade servrar** menyn väljer du den server som du vill avregistrera. Om du vill ta bort valvet måste du avregistrera alla servrar.

5. Högerklicka på den skyddade servern på skyddade servrar-menyn och välj **ta bort**. 

   ![återuppta databasskyddet](./media/backup-azure-sql-database/delete-protected-server.png)

## <a name="sql-database-backup-faq"></a>SQL database säkerhetskopiering vanliga frågor och svar

Följande avsnitt innehåller ytterligare information om säkerhetskopiering för SQL-databas.

### <a name="can-i-throttle-the-speed-of-the-sql-backup-policy-so-it-minimizes-impact-on-the-sql-server"></a>Jag kan begränsa hastigheten på SQL-principen för säkerhetskopiering så att den minimerar påverkan på SQLServer

Ja, kan du begränsa den hastighet med vilken principen för säkerhetskopiering körs. Ändra inställningen:

1. På SQL-Server i den `C:\Program Files\Azure Workload Backup\bin` mappen öppnar **TaskThrottlerSettings.json**.

2. I den **TaskThrottlerSettings.json** filen, ändra **DefaultBackupTasksThreshold** till ett lägre värde, till exempel 5.

3. Spara ändringarna och stäng filen.

4. Öppna Aktivitetshanteraren på SQL-Server och starta om den **Azure Backup arbetsbelastning Coordinator-tjänsten**.

### <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>Jag kan köra en fullständig säkerhetskopiering från en sekundär replik

Nej, den här funktionen stöds inte.

### <a name="do-successful-backup-jobs-create-alerts"></a>Skapar lyckad säkerhetskopieringsjobb aviseringar

Nej. Lyckade jobb genererar inte aviseringar. Aviseringar skickas bara för säkerhetskopieringsjobb som misslyckas.

### <a name="are-scheduled-backup-job-details-shown-in-the-jobs-menu"></a>Information om schemalagda säkerhetskopieringsjobb visas i menyn jobb

Nej. Menyn jobb visar information om ad hoc, men visar inte schemalagda säkerhetskopieringsjobb. Om alla schemalagda säkerhetskopieringsjobb misslyckas, hittar du all information i aviseringar för misslyckade jobbet. Om du vill övervaka alla schemalagda och adhoc-säkerhetskopieringsjobb, [använder SQL Server Management Studio](backup-azure-sql-database.md#use-sql-server-management-studio-for-backup-jobs).

### <a name="if-i-select-a-sql-server-will-future-databases-automatically-be-added"></a>Om jag väljer en SQLServer framtida databaser automatiskt till

Nej. När du konfigurerar skydd för en SQL-server om du markerar kryssrutan på servernivå, läggs alla databaser. Men om du lägger till databaser till SQLServer när du har konfigurerat skyddet, du måste manuellt lägga till nya databaser för att skydda dem. Databaserna finns inte inkluderas i det konfigurera skyddet.

### <a name="if-i-change-the-recovery-model-how-do-i-restart-protection"></a>Hur startar jag skydd om jag ändrar återställningsmodellen

Om du ändrar återställningsmodellen kan aktivera en fullständig säkerhetskopiering och säkerhetskopieringar börjar som förväntat.

## <a name="next-steps"></a>Nästa steg

Om du vill läsa mer om Azure Backup kan du ta en titt på PowerShell-exemplet för att säkerhetskopiera krypterade virtuella datorer.

> [!div class="nextstepaction"]
> [Säkerhetskopiera krypterade virtuella datorer](./scripts/backup-powershell-sample-backup-encrypted-vm.md)
