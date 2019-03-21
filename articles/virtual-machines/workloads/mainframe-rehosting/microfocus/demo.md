---
title: Konfigurera Micro fokus CICS BankDemo för Micro fokus Enterprise Developer 4.0 i Azure | Microsoft Docs
description: Kör Micro fokus BankDemo programmet på Azure för att lära dig att använda Micro fokus Enterprise Server och Enterprise.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
keywords: ''
ms.openlocfilehash: 733649c2b7f27ec5e80cb77adba708a158fb2c22
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58099475"
---
# <a name="set-up-micro-focus-cics-bankdemo-for-micro-focus-enterprise-developer-40-in-azure"></a>Konfigurera Micro fokus CICS BankDemo för Micro fokus Enterprise Developer 4.0 i Azure

När du ställer in Micro fokus Enterprise Server 4.0 och Enterprise Developer 4.0 på Azure kan du testa distributioner av IBM z/OS-arbetsbelastningar. Den här artikeln visar hur du ställer in CICS BankDemo, ett exempelprogram som medföljer företagsutvecklare.

CICs står för kontrollsystem kund, transaktion-plattform som används av många av de online stordatorprogram. Programmet BankDemo är perfekt för att lära sig hur Enterprise Server och företagsutvecklare fungerar och hur du hanterar och distribuera en typ av App med grön skärm terminaler.

## <a name="prerequisites"></a>Förutsättningar

- En virtuell dator med [företagsutvecklare](set-up-micro-focus-on-azure.md). Tänk på att företagsutvecklare har en fullständig instans av Enterprise-Server på den för utveckling och testning. Det här är instansen av Enterprise-Server som används för demon.

- [SQL Server 2017 Express edition](https://www.microsoft.com/sql-server/sql-server-editions-express). Ladda ned och installera den på den virtuella datorn Developer Enterprise. Enterprise-servern kräver en databas för hanteringen av CICS regioner och BankDemo programmet använder också en SQL Server-databas som heter BANKDEMO. Den här demon förutsätter att du använder SQL Server Express för båda databaserna. När du installerar, väljer du grundläggande installationen.

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) (SSMS). SSMS används för att hantera databaser och köra ett T-SQL-skript. Ladda ned och installera den på den virtuella datorn Developer Enterprise.

- [Visual Studio 2017](https://azure.microsoft.com/downloads/) med senaste servicepack eller [Visual Studio Community](https://visualstudio.microsoft.com/vs/community/), som du kan ladda ned kostnadsfritt.

- Rumba Desktop eller någon annan 3270 emulatorn.

## <a name="configure-the-windows-environment"></a>Konfigurera Windows-miljö

När du har installerat Enterprise Developer 4.0 på den virtuella datorn måste du konfigurera instansen av Enterprise-Server som levereras med den. Gör att måste du installera några ytterligare Windows-funktioner på följande sätt.

1. Du kan använda RDP för att logga in på den Enterprise Server 4.0 virtuella dator du skapade.

2. Klicka på den **Search** ikonen bredvid den **starta** knappen och skriv **Windows-funktioner**. Serverhanteraren Lägg till roller och funktioner som guiden öppnas.

3. Välj **webbserverrollen (IIS)**, och kontrollera följande:

    - Innehåll i webbhanteringsverktyg
    - IIS 6-Kompatibilitetshantering (Markera alla tillgängliga funktioner)
    - IIS-hanteringskonsol
    - IIS-hanteringsskript och verktyg
    - IIS Management Service

4. Välj **World Wide Web Services**, och kontrollera följande:

     Funktioner för programutveckling:
    - .NET-utökningsbarhet
    - ASP.NET
    - Vanliga HTTP-funktioner: Lägg till alla tillgängliga funktioner
    - Hälsa och diagnostik: Lägg till alla tillgängliga funktioner
    - Säkerhet:
        - Grundläggande autentisering
        - Windows-autentisering

5. Välj **Windows Process Activation Service** och alla dess underordnade.

6. För **funktioner**, kontrollera **Microsoft .NET framework 3.5.1**, och kontrollera följande:

    - Windows Communication Foundation HTTP-aktivering
    - Windows Communication Foundation HTTP-aktivering

7. För **funktioner**, kontrollera **Microsoft .NET framework 4.6**, och kontrollera följande:

   - Namngiven Pipe-aktivering
   - TCP-aktivering
   - Delning av TCP-Port

     ![Guiden Lägg till roller och funktioner: Rolltjänster](media/01-demo-roles.png)

8. När du har valt alla alternativ klickar du på **nästa** att installera.

9. När du har Windows-funktioner, gå till **Kontrollpanelen \> System och säkerhet \> Administrationsverktyg**, och välj **Services**. Rulla nedåt och kontrollera att följande tjänster körs och inställd på **automatisk**:

    - **NetTcpPortSharing**
    - **Net.Pipe lyssnare nätverkskort**
    - **NET.TCP Listener nätverkskort**

10. Om du vill konfigurera IIS och WAS stöd från menyn hitta **Micro fokus Enterprise Developer kommandotolk (64-bitars)** och kan köras som **administratör**.

11. Typ **wassetup – i** och tryck på **RETUR**.

12. När skriptet har körts, kan du stänga fönstret.

## <a name="configure-the-local-system-account-for-sql-server"></a>Konfigurera det lokala systemkontot för SQL Server

Vissa Enterprise Server-processer måste kunna logga in på SQL Server och skapa databaser och andra objekt. Dessa processer använder det lokala systemkontot så måste du ge sysadmin-behörighet till det kontot.

1. Starta den **SSMS** och klicka på **Connect** att ansluta till den lokala SQLEXPRESS-servern med hjälp av Windows-autentisering. Det ska vara tillgängliga på den **servernamn** lista.

2. Till vänster expanderar den **Security** mapp och välj **inloggningar**.

3. Välj **NT AUTHORITY\\SYSTEM** och välj **egenskaper**.

4. Välj **serverroller** och kontrollera **sysadmin**.

     ![SSMS Object Explorer-fönstret: Inloggningsegenskaper](media/02-demo-explorer.png)

## <a name="create-the-bankdemo-database-and-all-its-objects"></a>Skapa BankDemo databasen och alla objekt

1. Öppna **Windows Explorer** och gå till **C:\\användare\\offentliga\\dokument\\Micro fokus\\företagsutvecklare\\ Exempel\\stordatorprogram\\CICS\\DotNet\\BankDemo\\SQL**.

2. Kopiera innehållet i **BankDemoCreateAll.SQL** filen till Urklipp.

3. Öppna **SSMS**. Till höger klickar du på **Server** och välj **ny fråga**.

4. Klistra in innehållet i Urklipp i den **ny fråga** box.

5. Kör SQL genom att klicka på **kör** från den **kommandot** fliken ovan frågan.

Frågan ska köras utan fel. När den är klar har du exempeldatabasen för BankDemo-programmet.

![SQLQuery1.sql output](media/03-demo-query.png)

## <a name="verify-that-the-database-tables-and-objects-have-been-created"></a>Kontrollera att database-tabeller och objekt har skapats

1. Högerklicka på den **BANKDEMO** databasen och välj **uppdatera**.

2. Expandera den **databasen** och välj **tabeller**. Du bör se något som liknar följande.

     ![BANKDEMO tabellen expanderas i Object Explorer](media/04-demo-explorer.png)

## <a name="build-the-application-in-enterprise-developer"></a>Skapa program i Enterprise Developer

1. Öppna Visual Studio och logga in.

2. Under den **filen** menyalternativet Välj **öppna projekt/lösning**, gå till **C:\\användare\\offentliga\\dokument\\Micro Fokus\\företagsutvecklare\\exempel\\stordatorprogram\\CICS\\DotNet\\BankDemo**, och välj den **sln**fil.

3. Ta en stund att granska objekt. COBOL program visas i Solution Explorer med tillägget CBL tillsammans med CopyBooks (CPY) och JCL.

4. Högerklicka på den **BankDemo2** projektet och välj **Set as Startup Project**.

    > [!NOTE]
    > Projektet BankDemo gör användning av HCOSS (värd kompatibilitet alternativet för SQL Server), som inte används för den här demonstrationen.

5. I **Solution Explorer**, högerklicka på den **BankDemo2** projektet och välj **skapa**.

    > [!NOTE]
    > Skapa på nivån lösning resulterar i fel, HCOSS har inte konfigurerats.

6. När projektet har skapats, granska de **utdata** fönster. Det bör se ut som på bilden nedan.

     ![Utdatafönstret visar version](media/05-demo-output.png)

## <a name="deploy-the-bankdemo-application-into-the-region-database"></a>Distribuera programmet BankDemo till Region-databasen

1. Öppna en kommandotolk (64-bitars) för företagsutvecklare som administratör.

2. Navigera till den **offentliga %\\dokument\\Micro fokus\\företagsutvecklare\\exempel\\stordatorprogram\\CICS\\DotNet\\ BankDemo**.

3. I Kommandotolken kör **bankdemodbdeploy** och inkluderar du parametern för databasen för att distribuera till, till exempel:

    ```
    bankdemodbdeploy (local)/sqlexpress
    ```

> [!NOTE]
> Se till att använda ett snedstreck (/) inte ett omvänt snedstreck (\\). Det här skriptet körs under en tid.

![Administration: Enterprise Developer Kommandotolkens fönster](media/06-demo-cmd.png)

## <a name="create-the-bankdemo-region-in-enterprise-administrator-for-net"></a>Skapa regionen BankDemo i företagsadministratör för .NET

1. Öppna den **Enterprise Server för .NET-Administration** Användargränssnittet.

2. Att starta snapin-modulen MMC från Windows **starta** menyn, Välj **Micro fokus företagsutvecklare \> Configuration \> Enterprise Server för .NET-administratören**. (För Windows Server, väljer **Micro fokus företagsutvecklare \> Enterprise Server för .NET-administratören**).

3. Expandera den **regioner** behållaren i den vänstra rutan och högerklicka sedan **CICS**.

4. Välj **definiera Region** att skapa ett nytt CICS område som kallas **BANKDEMO**, värd (lokal) databasen.

5. Ange databasserverinstansen, klickar du på **nästa**, och ange sedan områdesnamnet **BANKDEMO**.

     ![Definiera Region dialogrutan](media/07-demo-cics.png)

6. Välj den region definitionsfilen för interregionala databasen genom att leta upp **region\_bankdemo\_db.config** i **C:\\användare\\offentliga\\ Dokument\\Micro fokus\\företagsutvecklare\\exempel\\stordatorprogram\\CICS\\DotNet\\BankDemo**.

     ![Definiera Region – Regionsnamn: BANKDEMO](media/08-demo-cics.png)

7. Klicka på **Slutför**.

## <a name="create-xa-resource-definitions"></a>Skapa definitioner för XA-resursen

1. I den vänstra rutan i den **Enterprise Server för .NET-Administration** användargränssnitt, expandera **System**, och sedan **XA Resursdefinitionerna**. Den här inställningen anger hur regionen samverkar med Enterprise Server och programdatabaser.

2. Högerklicka på **XA Resursdefinitionerna** och välj **lägga till serverinstansen**.

3. I listrutan, väljer **databasen tjänstinstans**. Är den lokala datorn SQLEXPRESS.

4. Välj instansen under de **XA Resursdefinitionerna (machinename\\sqlexpress)** behållare och klickar på **Lägg till**.

5. Välj **databasen XA Resursdefinitionen** och skriv sedan **BANKDEMO** för den **namn** och **Region**.

     ![Ny databas XA Resursdefinitionen skärm](media/09-demo-xa.png)

6. Klicka på ellipserna (**...** ) att ta fram guiden anslutningssträngen. För **servernamn**, typ **(lokal)\\SQLEXPRESS**. För **inloggning**väljer **Windows-autentisering**. Databasens namn, skriver du in **BANKDEMO**

     ![Redigera anslutningssträng skärmen](media/10-demo-string.png)

7. Testa anslutningen.

## <a name="start-the-bankdemo-region"></a>Starta regionen BANKDEMO

> [!NOTE]
> Det första steget är viktigt: Du måste ange regionen du använder XA-Resursdefinitionen som du nyss skapade.

1. Navigera till den **BANDEMO CICS Region** under den **regioner behållare**, och välj sedan **redigera Region startfil** från den **åtgärder** fönstret. Rulla ned till SQL-egenskaper och ange **bankdemo** för den **XA-resursnamnet** , eller använda de tre punkterna för att välja den.

2. Klicka på den **spara** ikon för att spara dina ändringar.

3. Högerklicka på **BANKDEMO CICS Region** i den **konsolen** och väljer **Starta/Stoppa Region**.

4. Längst ned i den **Starta/Stoppa Region** som visas i den mellersta rutan väljer **starta**. Efter några sekunder börjar regionen.

     ![SQL Starta/Stoppa box](/media/11-demo-sql.png)

     ![CICS Region BANKDEMO - igång skärmen](media/12-demo-cics.png)

## <a name="create-a-listener"></a>Skapa en lyssnare

Du måste skapa en lyssnare för TN3270-sessioner som har åtkomst till programmet BankDemo.

1. I den vänstra rutan expanderar **Configuration redigerare** och välj **lyssnare**.

2. Klicka på den **Öppna fil** och välj den **seelistener.exe.config** fil. Den här filen kommer att redigera och läses in varje gång Enterprise Server startas.

3. Lägg märke till de två regionerna som tidigare definierats (ESDEMO och JCLDEMO).

4. Skapa en ny Region för BANKDEMO genom att högerklicka på **regioner**, och välj **Lägg till Region**.

5. Välj **BANKDEMO Region**.

6. Lägga till en kanal för TN3270 genom att högerklicka på **BANKDEMO Region** och välja **Lägg till Channel**.

7. För **namn**, ange **TN3270**. För **Port**, ange **9024**. (Observera att ESDEMO-program använder port 9230 så måste du använda en annan port.)

8. Om du vill spara filen, klickar du på den **spara** ikonen eller välj **filen** \> **spara**.

9. Starta lyssnaren, klicka på den **starta lyssnaren** ikonen eller välj **alternativ** \> **starta lyssnaren**.

     ![Lyssnaren Konfigurationsredigeraren windows](media/13-demo-listener.png)


## <a name="configure-rumba-to-access-the-bankdemo-application"></a>Konfigurera surfa för att komma åt programmet BankDemo

Det sista som du behöver göra är att konfigurera en 3270-session med surfa, en 3270-emulator. Det här steget kan du komma åt programmet BankDemo via lyssnare som du nyss skapade.

1. Från Windows **starta** menyn Starta surfa Desktop.

2. Under den **anslutningar** menyalternativet Välj **TN3270**.

3. Klicka på **infoga** och skriv **127.0.0.1** för IP-adress och **9024** för användardefinierad-port.

4. Längst ned i dialogrutan klickar du på **Connect**. En svart CICS skärm visas.

5. Typ **bank** att visa inledande 3270 skärmen för BankDemo-programmet.

6. Användar-ID, skriver du in **B0001** och lösenordet kan ange något. Den första skärmen BANK20 öppnas.

![Stordatorprogram visas välkomstskärm](media/14-demo.png)
![stordatorprogram visa - surfa - undersystemet Demonstration skärmen](media/15-demo.png)

Grattis! Nu körs ett CICS program i Azure med hjälp av Micro fokus Enterprise Server.

## <a name="learn-more"></a>Läs mer

- [Stordatormigrering - Portal](https://blogs.msdn.microsoft.com/azurecat/2018/11/16/mainframe-migration-to-azure-portal/)
- [Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/linux/overview)
- [Felsökning](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
- [Avmystifiera stordatorprogram till Azure-migrering](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/en-us/)
