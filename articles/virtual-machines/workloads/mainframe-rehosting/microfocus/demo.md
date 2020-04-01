---
title: Konfigurera Micro Focus CICS BankDemo för Micro Focus Enterprise Developer 4.0 på virtuella Azure-datorer
description: Kör Micro Focus BankDemo-programmet på virtuella Azure-datorer (VMs) för att lära dig att använda Micro Focus Enterprise Server och Enterprise Developer.
author: sread
ms.author: sread
ms.date: 03/30/2020
ms.topic: article
ms.service: multiple
ms.openlocfilehash: db9d6bab2f383102434512aa63d7566cff1f579b
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411079"
---
# <a name="set-up-micro-focus-cics-bankdemo-for-micro-focus-enterprise-developer-40-on-azure"></a>Konfigurera Micro Focus CICS BankDemo för Micro Focus Enterprise Developer 4.0 på Azure

När du konfigurerar Micro Focus Enterprise Server 4.0 och Enterprise Developer 4.0 på Azure kan du testa distributioner av IBM z/OS-arbetsbelastningar. Den här artikeln visar hur du konfigurerar CICS BankDemo, ett exempelprogram som medföljer Enterprise Developer.

CICs står för Customer Information Control System, den transaktionsplattform som används av många av de online stordator applikationer. BankDemo-programmet är bra för att lära sig hur Enterprise Server och Enterprise Developer fungerar och hur du hanterar och distribuerar ett verkligt program komplett med grön skärm terminaler.

> [!NOTE]
> Kommer snart: Instruktioner för hur du konfigurerar [Micro Focus Enterprise Server 5.0](https://techcommunity.microsoft.com/t5/azurecat/micro-focus-enterprise-server-5-0-quick-start-template-on-azure/ba-p/1160110) på virtuella Azure-datorer.

## <a name="prerequisites"></a>Krav

- En virtuell dator med [Företagsutvecklare](set-up-micro-focus-azure.md). Tänk på att Enterprise Developer har en fullständig instans av Enterprise Server på den för utveckling och teständamål. Den här instansen är den instans av Enterprise Server som används för demon.

- [SQL Server 2017 Express-utgåva](https://www.microsoft.com/sql-server/sql-server-editions-express). Hämta och installera det på den virtuella datorn för företagsutvecklare. Enterprise Server kräver en databas för hantering av CICS-regioner och BankDemo-programmet använder också en SQL Server-databas som heter BANKDEMO. Den här demon förutsätter att du använder SQL Server Express för båda databaserna. När du installerar väljer du den grundläggande installationen.

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) (SSMS). SSMS används för att hantera databaser och köra ett T-SQL-skript. Hämta och installera det på den virtuella datorn för företagsutvecklare.

- [Visual Studio 2019](https://azure.microsoft.com/downloads/) med den senaste Service Pack- eller [Visual Studio Community,](https://visualstudio.microsoft.com/vs/community/)som du kan ladda ner gratis.

- Rumba Desktop eller annan 3270 emulator.

## <a name="configure-the-windows-environment"></a>Konfigurera Windows-miljön

När du har installerat Enterprise Developer 4.0 på den virtuella datorn måste du konfigurera instansen av Enterprise Server som medföljer den. För att göra det måste du installera några ytterligare Windows-funktioner enligt följande.

1. Använd RDP för att logga in på den virtuella företagsserver 4.0 som du skapade.

2. Klicka på **sökikonen** bredvid **Start-knappen** och skriv **Windows-funktioner**. Guiden Lägg till roller och funktioner i Serverhanteraren öppnas.

3. Välj **Webbserverroll (IIS)** och kontrollera sedan följande alternativ:

    - Verktyg för webbhantering
    - IIS 6 Management Kompatibilitet (välj alla tillgängliga funktioner)
    - IIS-hanteringskonsol
    - Skript och verktyg för IIS-hantering
    - IIS-hanteringstjänst

4. Välj **World Wide Web Services**och kontrollera följande alternativ:

     Funktioner för programutveckling:
    - .NET-utökningsbarhet
    - ASP.NET
    - Vanliga HTTP-funktioner: Lägg till alla tillgängliga funktioner
    - Hälsa och diagnostik: Lägg till alla tillgängliga funktioner
    - Säkerhet:
        - Grundläggande autentisering
        - Windows-autentisering

5. Välj **Windows Process Activation Service** och alla dess underordnade.

6. För **funktioner**kontrollerar du **Microsoft .NET framework 3.5.1**och kontrollerar följande alternativ:

    - HTTP-aktivering av Windows Communication Foundation
    - Windows Communication Foundation icke-HTTP-aktivering

7. För **funktioner**kontrollerar du **Microsoft .NET framework 4.6**och kontrollerar följande alternativ:

   - Namngiven pipe-aktivering
   - TCP-aktivering
   - Delning av TCP-port

     ![Guiden Lägg till roller och funktioner: Rolltjänster](media/01-demo-roles.png)

8. När du har markerat alla alternativ klickar du på **Nästa** för att installera.

9. När du har funktionerna i Windows går du till **Administrationsverktyg för \> Kontrollpanelen och Administrationsverktyg för säkerhet \> **och väljer **Tjänster**. Bläddra nedåt och se till att följande tjänster körs och ställ in **på Automatisk:**

    - **NetTcpPortSharing**
    - **Net.Pipe lyssnare Adapter**
    - **Net.tcp-lyssnare-adapter**

10. Om du vill konfigurera IIS- och WAS-stöd letar du upp **kommandotolken för Micro Focus Enterprise Developer (64 bitar)** på menyn och körs som **administratör**.

11. Skriv **wassetup –i** och tryck på **Retur**.

12. När skriptet har körts kan du stänga fönstret.

## <a name="configure-the-local-system-account-for-sql-server"></a>Konfigurera det lokala systemkontot för SQL Server

Vissa Enterprise Server-processer måste kunna logga in sql server och skapa databaser och andra objekt. Dessa processer använder det lokala systemkontot, så du måste ge självadministratören behörighet till det kontot.

1. Starta **SSMS** och klicka på **Anslut** för att ansluta till den lokala SQLEXPRESS Server med Windows-autentisering. Den ska vara tillgänglig i listan **Servernamn.**

2. Till vänster expanderar du mappen **Säkerhet** och väljer **Inloggningar**.

3. Välj **NT\\AUTHORITY SYSTEM** och välj **Egenskaper**.

4. Välj **Serverroller** och kontrollera **sysadmin**.

     ![Fönstret SSMS-objektutforskaren: Inloggningsegenskaper](media/02-demo-explorer.png)

## <a name="create-the-bankdemo-database-and-all-its-objects"></a>Skapa BankDemo-databasen och alla dess objekt

1. Öppna **Utforskaren** och navigera till **C:\\Användare\\Offentliga\\dokument\\\\Micro\\Focus\\Enterprise Developer\\Samples\\Storframe\\CICS DotNet BankDemo\\SQL**.

2. Kopiera innehållet i **filen BankDemoCreateAll.SQL** till urklipp.

3. Öppna **SSMS**. Till höger klickar du på **Server** och väljer **Ny fråga**.

4. Klistra in innehållet i Urklipp i rutan **Ny fråga.**

5. Kör SQL genom att klicka på **Kör** från fliken **Kommando** ovanför frågan.

Frågan ska köras utan fel. När den är klar har du exempeldatabasen för BankDemo-programmet.

![SQLQuery1.sql-utgång](media/03-demo-query.png)

## <a name="verify-that-the-database-tables-and-objects-have-been-created"></a>Kontrollera att databastabellerna och databasobjekten har skapats

1. Högerklicka på **BANKDEMO-databasen** och välj **Uppdatera**.

2. Expandera **databasen** och välj **Tabeller**. Du skulle se något i stil med följande.

     ![BANKDEMO-tabellen expanderad i Objektutforskaren](media/04-demo-explorer.png)

## <a name="build-the-application-in-enterprise-developer"></a>Skapa programmet i Enterprise Developer

1. Öppna Visual Studio och logga in.

2. Under **File** alternativet Arkiv-meny väljer du **Öppna projekt/lösning**, navigerar till **C:\\\\Användare Offentliga\\dokument\\Micro Focus\\Enterprise Developer\\Samples\\Storframe\\CICS\\DotNet\\BankDemo**och väljer **sln-filen.**

3. Ta dig tid att undersöka objekten. COBOL-program visas i Solution Explorer med CBL-tillägget tillsammans med CopyBooks (CPY) och JCL.

4. Högerklicka på **BankDemo2-projektet** och välj **Ange som startprojekt**.

    > [!NOTE]
    > BankDemo Project använder HCOSS (Host Compatibility Option för SQL Server), som inte används för den här demon.

5. Högerklicka på **BankDemo2-projektet** i **Solution Explorer**och välj **Bygg**.

    > [!NOTE]
    > Att bygga på lösningsnivå resulterar i fel, eftersom HCOSS inte har konfigurerats.

6. När projektet har skapats undersöker du **utdatafönstret.** Det bör se ut som på bilden nedan.

     ![Utdatafönster som visar lyckad version](media/05-demo-output.png)

## <a name="deploy-the-bankdemo-application-into-the-region-database"></a>Distribuera BankDemo-programmet i regiondatabasen

1. Öppna en kommandotolk för företagsutvecklare (64 bitar) som administratör.

2. Navigera till **exemplen\\\\%PUBLIC%\\Documents\\Micro\\Focus\\\\Enterprise\\Developer tar exempel på Stordator CICS DotNet BankDemo**.

3. Kör **bankdemodbdeploay** i kommandotolken och inkludera parametern för databasen som ska distribueras till, till exempel:

    ```
    bankdemodbdeploy (local)/sqlexpress
    ```

> [!NOTE]
> Se till att använda ett snedstreck (/) inte ett bakåtskurna(\\). Det här skriptet körs ett tag.

![Administration: Kommandotolken för företagsutvecklare](media/06-demo-cmd.png)

## <a name="create-the-bankdemo-region-in-enterprise-administrator-for-net"></a>Skapa BankDemo-regionen i företagsadministratör för .NET

1. Öppna **företagsservern för .NET Administration.Open** the Enterprise Server for .NET Administration UI.

2. Om du vill starta SNAP-MODULEN FÖR MMC väljer du **Micro Focus Enterprise Developer \> Configuration \> Enterprise Server för .NET Admin**på **Start-menyn** i Windows. (För Windows Server väljer du **Micro Focus Enterprise \> Developer Enterprise Server för .NET Admin**).

3. Expandera behållaren **Regioner** i den vänstra rutan och högerklicka sedan på **CICS**.

4. Välj **Definiera region** om du vill skapa en ny CICS-region som kallas **BANKDEMO**, som finns i den (lokala) databasen.

5. Ange databasserverinstansen, klicka på **Nästa**och ange sedan regionnamnet **BANKDEMO**.

     ![Dialogrutan Definiera region](media/07-demo-cics.png)

6. Om du vill välja regiondefinitionsfilen för databasen över flera regioner letar du reda på **\_regionbankdemo\_db.config** i **C:\\\\Användare offentliga\\dokument\\Micro Focus\\Enterprise Developer\\Samples\\Storframe\\CICS\\DotNet\\BankDemo**.

     ![Definiera region - Regionnamn: BANKDEMO](media/08-demo-cics.png)

7. Klicka på **Slutför**.

## <a name="create-xa-resource-definitions"></a>Skapa XA-resursdefinitioner

1. Expandera **System**och sedan **XA-resursdefinitioner**i den vänstra rutan **i företagsservern för .NET** Administration-användargränssnittet . Den här inställningen definierar hur regionintertar med Enterprise Server och programdatabaserna.

2. Högerklicka på **XA-resursdefinitioner** och välj **Lägg till serverinstans**.

3. Välj **Databastjänstinstans**i listrutan . Det blir den lokala datorn SQLEXPRESS.

4. Välj instansen från behållaren **XA Resource\\Definitions (machinename sqlexpress)** och klicka på **Lägg till**.

5. Välj **Databas XA-resursdefinition** och skriv sedan **BANKDEMO** för **namn** och **region**.

     ![Skärmen Ny databas XA-resursdefinition](media/09-demo-xa.png)

6. Klicka på ellipserna (**...**) för att visa guiden Anslutningssträng. För **Servernamn**, typ **\\(lokal) SQLEXPRESS**. För **inloggning**väljer du **Windows-autentisering**. För databasnamn skriver du **BANKDEMO**

     ![Redigera skärmen Anslutningssträng](media/10-demo-string.png)

7. Testa anslutningen.

## <a name="start-the-bankdemo-region"></a>Starta BANKDEMO-regionen

> [!NOTE]
> Det första steget är viktigt: Du måste ange att regionen ska använda den XA-resursdefinition som du just skapade.

1. Navigera till **REGIONEN BANDEMO CICS** under **regionbehållaren**och välj sedan **Startfil för redigera region** i **åtgärdsfönstret.** Bläddra ned till SQL-egenskaperna och ange **bankdemo** för **XA-resursnamnet**eller använd ellipsen för att markera det.

2. Klicka på ikonen **Spara** för att spara ändringarna.

3. Högerklicka på **BANKDEMO-CICS-regionen** i **konsolfönstret** och välj **Start-/stoppregion**.

4. Välj **Start**i rutan Start/Stopp-region längst ned i rutan **Start/Stopp-region** som visas i den mitta rutan . Efter några sekunder startar regionen.

     ![Rutan SQL Start/Stopp](media/11-demo-sql.png)

     ![CICS Region BANKDEMO - Startskärm](media/12-demo-cics.png)

## <a name="create-a-listener"></a>Skapa en lyssnare

Skapa en lyssnare för de TN3270-sessioner som använder BankDemo-programmet.

1. Expandera **Konfigurationsredigerare** i den vänstra rutan och välj **Lyssnare**.

2. Klicka på ikonen **Öppna fil** och markera filen **seelistener.exe.config.** Den här filen redigeras och läses in varje gång Enterprise Server startar.

3. Lägg märke till de två regioner som tidigare definierats (ESDEMO och JCLDEMO).

4. Om du vill skapa en ny region för BANKDEMO högerklickar du på **Regioner**och väljer **Lägg till region**.

5. Välj **BANKDEMO-region**.

6. Lägg till en TN3270-kanal genom att högerklicka på **BANKDEMO-regionen** och välja **Lägg till kanal**.

7. För **Namn**anger du **TN3270**. För **Port**anger du **9024**. ESDEMO-programmet använder port 9230 så du måste använda en annan port.

8. Om du vill spara filen klickar du på **ikonen Spara** eller väljer **Spara** **fil** \> .

9. Om du vill starta lyssnaren klickar du på ikonen **Starta lyssnare** eller väljer **Alternativ** \> **Starta lyssnare**.

     ![Fönster i lyssnare konfigurationsredigeraren](media/13-demo-listener.png)


## <a name="configure-rumba-to-access-the-bankdemo-application"></a>Konfigurera Rumba för att komma åt BankDemo-programmet

Det sista du behöver göra är att konfigurera en 3270 session med Rumba, en 3270 emulator. Med det här steget kan du komma åt BankDemo-programmet via lyssnaren som du skapade.

1. Starta Rumba Desktop på **Start-menyn** i Windows.

2. Välj **TN3270**under menyalternativet **Anslutningar** .

3. Klicka på **Infoga** och skriv **127.0.0.1** för IP-adressen och **9024** för den användardefinierade porten.

4. Klicka på **Anslut**längst ned i dialogrutan . En svart CICS-skärm visas.

5. Skriv **bank** för att visa den första 3270-skärmen för BankDemo-programmet.

6. Skriv **B0001** och för lösenordet för användar-ID: er. Den första skärmen BANK20 öppnas.

![Stordator Display](media/14-demo.png)
![Välkomstskärm Stordator Display - Rumba - Subsystem Demonstration skärm](media/15-demo.png)

Grattis! Du kör nu ett CICS-program i Azure med Micro Focus Enterprise Server.

## <a name="next-steps"></a>Nästa steg

- [Köra Enterprise Server i Docker-behållare på Azure](run-enterprise-server-container.md)
- [Stordatormigrering - Portal](https://blogs.msdn.microsoft.com/azurecat/2018/11/16/mainframe-migration-to-azure-portal/)
- [Virtuella datorer](https://docs.microsoft.com/azure/virtual-machines/linux/overview)
- [Felsökning](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
- [Avmystifiera stordator till Azure-migrering](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/en-us/)
