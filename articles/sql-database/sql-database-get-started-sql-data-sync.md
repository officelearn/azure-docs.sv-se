---
title: Konfigurera Data Sync
description: Den här självstudien visar hur du konfigurerar Azure SQL Data Sync
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 01/14/2019
ms.openlocfilehash: 019ddbac1900856666b958d90b4395f25eb5ee84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268969"
---
# <a name="tutorial-set-up-sql-data-sync-between-azure-sql-database-and-sql-server-on-premises"></a>Självstudiekurs: Konfigurera SQL Data Sync mellan Azure SQL Database och SQL Server lokalt

I den här självstudien får du lära dig hur du konfigurerar Azure SQL Data Sync genom att skapa en synkroniseringsgrupp som innehåller både Azure SQL Database- och SQL Server-instanser. Synkroniseringsgruppen är anpassad konfigurerad och synkroniseras enligt det schema du anger.

Självstudien förutsätter att du har åtminstone några tidigare erfarenheter med SQL Database och SQL Server.

En översikt över SQL Data Sync finns [i Synkronisera data över molndatabaser och lokala databaser med Azure SQL Data Sync](sql-database-sync-data.md).

Exempel på PowerShell om hur du konfigurerar SQL Data Sync finns i [Synkronisera mellan Azure SQL-databaser](scripts/sql-database-sync-data-between-sql-databases.md) eller [en Azure SQL-databas och en lokal SQL Server-databas](scripts/sql-database-sync-data-between-azure-onprem.md)

> [!IMPORTANT]
> Azure SQL Data Sync har **inte** stöd för Azure SQL Database Managed Instance just nu.

## <a name="create-sync-group"></a>Skapa synkroniseringsgrupp

1. Gå till [Azure-portalen](https://portal.azure.com) för att hitta din SQL-databas. Sök efter och välj **SQL-databaser**.

    ![Sök efter SQL-databaser, Microsoft Azure-portal](media/sql-database-get-started-sql-data-sync/search-for-sql-databases.png)

1. Markera den databas som du vill använda som navdatabas för Datasynkronisering.

    ![Välj från SQL-databaslista, Microsoft Azure-portal](media/sql-database-get-started-sql-data-sync/select-sql-database.png)

    > [!NOTE]
    > Hubbdatabasen är en synkroniseringstopologis centrala slutpunkt, där en synkroniseringsgrupp har flera databasslutpunkter. Alla andra medlemsdatabaser med slutpunkter i synkroniseringsgruppen synkroniseras med navdatabasen.

1. På **SQL-databasmenyn** för den valda databasen väljer du **Synkronisera till andra databaser**.

    ![Synkronisera med andra databaser, SQL-databas, Microsoft Azure-portal](media/sql-database-get-started-sql-data-sync/sync-to-other-databases.png)

1. På sidan **Synkronisera till andra databaser** väljer du Ny **synkroniseringsgrupp**. Sidan **Ny synkroniseringsgrupp** öppnas med **skapa synkroniseringsgrupp (steg 1)** markerad.

   ![Steg 1-inställningar](media/sql-database-get-started-sql-data-sync/stepone.png)

   Ändra följande inställningar på sidan **Skapa datasynkroniseringsgrupp:**

   | Inställning                        | Beskrivning |
   | ------------------------------ | ------------------------------------------------- |
   | **Namn på synkronisera grupp** | Ange ett namn för den nya synkroniseringsgruppen. Det här namnet skiljer sig från namnet på själva databasen. |
   | **Synkronisera metadatadatabas** | Välj att skapa en databas (rekommenderas) eller att använda en befintlig databas.<br/><br/>Om du väljer **Ny databas**väljer du Skapa **ny databas.** På **sidan SQL Database** namnger och konfigurerar du sedan den nya databasen och väljer **OK**.<br/><br/>Om du väljer **Använd befintlig databas**väljer du databasen i listan. |
   | **Automatisk synkronisering** | Välj **På** eller **Av**.<br/><br/>Om du väljer **På**anger du ett tal och väljer **Sekunder,** **Minuter,** **Timmar**eller **Dagar** i avsnittet **Synkroniseringsfrekvens.** |
   | **Konfliktlösning** | Välj **Hub win** eller Medlem **vinna**.<br/><br/>**Hub win** innebär att när konflikter uppstår, överskrivningar data i navdatabasen motstridiga data i medlemsdatabasen.<br/><br/>**Medlemsvinst** innebär att när konflikter uppstår skriver data i medlemsdatabasen över motstridiga data i navdatabasen. |

   > [!NOTE]
   > Microsoft rekommenderar att du skapar en ny, tom databas som kan användas som databas för **synkroniseringsmetadata**. Data Sync skapar tabeller i den här databasen och kör en frekvent arbetsbelastning. Den här databasen delas som **synkroniseringsmetadatadatabas** för alla synkroniseringsgrupper i en markerad region och du kan inte ändra databasen eller dess namn utan att ta bort alla synkroniseringsgrupper och synkroniseringsagenter i regionen.

   Välj **OK** och vänta tills synkroniseringsgruppen har skapats och distribuerats.

## <a name="add-sync-members"></a>Lägga till synkroniseringsmedlemmar

När den nya synkroniseringsgruppen har skapats och distribuerats markeras **Lägg till synkroniseringsmedlemmar (steg 2)** på sidan **Ny synkroniseringsgrupp.**

I avsnittet **Navdatabas** anger du befintliga autentiseringsuppgifter för SQL Database-servern där navdatabasen finns. Ange inte *nya* autentiseringsuppgifter i det här avsnittet.

![Steg 2-inställningar](media/sql-database-get-started-sql-data-sync/steptwo.png)

### <a name="to-add-an-azure-sql-database"></a>Så här lägger du till en Azure SQL-databas

I avsnittet **Medlemsdatabas** lägger du eventuellt till en Azure SQL-databas i synkroniseringsgruppen genom att välja **Lägg till en Azure SQL-databas**. Sidan **Konfigurera Azure SQL Database** öppnas.

  ![Steg 2 - konfigurera databas](media/sql-database-get-started-sql-data-sync/steptwo-configure.png)

  Ändra följande inställningar på sidan **Konfigurera Azure SQL Database:**

  | Inställning                       | Beskrivning |
  | ----------------------------- | ------------------------------------------------- |
  | **Namn på synkronisera medlem** | Ange ett namn för den nya synkroniseringsmedlemmen. Det här namnet skiljer sig från själva databasnamnet. |
  | **Prenumeration** | Välj den associerade Azure-prenumerationen för faktureringsändamål. |
  | **Azure SQL Server** | Välj den befintliga SQL Database-servern. |
  | **Azure SQL Database** | Välj den befintliga SQL-databasen. |
  | **Synkronisera vägbeskrivningar** | Välj **Dubbelriktad synkronisering**, **Till hubben**eller **från hubben**. |
  | **Användarnamn** och **lösenord** | Ange befintliga autentiseringsuppgifter för SQL Database-servern där medlemsdatabasen finns. Ange inte *nya* autentiseringsuppgifter i det här avsnittet. |

  Välj **OK** och vänta på att den nya synkroniseringsmedlemmen har skapats och distribuerats.

<a name="add-on-prem"></a>
### <a name="to-add-an-on-premises-sql-server-database"></a>Så här lägger du till en lokal SQL Server-databas

I avsnittet **Medlemsdatabas** lägger du eventuellt till en lokal SQL Server i synkroniseringsgruppen genom att välja **Lägg till en lokal databas**. Sidan **Konfigurera lokalt** öppnas där du kan göra följande:

1. Välj **Välj Sync Agent Gateway**. Sidan **Välj synkroniseringsagent** öppnas.

   ![Skapa en synkroniseringsagent](media/sql-database-get-started-sql-data-sync/steptwo-agent.png)

1. På sidan **Välj synkroniseringsagent** väljer du om du vill använda en befintlig agent eller skapa en agent.

   Om du väljer **Befintliga agenter**väljer du den befintliga agenten i listan.

   Om du väljer **Skapa en ny agent**gör du följande:

   1. Hämta datasynkroniseringsagenten från länken och installera den på datorn där SQL Server finns. Du kan också hämta agenten direkt från [SQL Azure Data Sync Agent](https://www.microsoft.com/download/details.aspx?id=27693).

      > [!IMPORTANT]
      > Du måste öppna utgående TCP-port 1433 i brandväggen för att låta klientagenten kommunicera med servern.

   1. Ange ett namn på agenten.

   1. Välj **Skapa och generera nyckel** och kopiera agentnyckeln till Urklipp.

   1. Välj **OK** om du vill stänga sidan **Välj synkroniseringsagent.**

1. Leta reda på och kör appen Client Sync Agent på SQL Server-datorn.

   ![Klientagentappen för datasynkronisering](media/sql-database-get-started-sql-data-sync/datasync-preview-clientagent.png)

    1. I synkroniseringsagentappen väljer du **Skicka agentnyckel**. Dialogrutan **Konfiguration av synkroniseringsmetadatadatabasen** öppnas.

    1. Klistra in agentnyckeln som kopierats från Azure-portalen i dialogrutan Konfiguration av **synkroniseringsmetadatadatabas.** Ange även befintliga autentiseringsuppgifter för Azure SQL Database-servern där metadatadatabasen finns. (Om du har skapat en metadatadatabas finns databasen på samma server som navdatabasen.) Välj **OK** och vänta tills konfigurationen är klar.

        ![Ange agentnyckel och serverautentiseringsuppgifter](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-enterkey.png)

        > [!NOTE]
        > Om du får ett brandväggsfel skapar du en brandväggsregel på Azure för att tillåta inkommande trafik från SQL Server-datorn. Du kan skapa regeln manuellt i portalen eller i SQL Server Management Studio (SSMS). I SSMS ansluter du till navdatabasen på Azure genom att ange dess namn som <hub_database_name>.database.windows.net.

    1. Välj **Registrera om du** vill registrera en SQL Server-databas med agenten. Dialogrutan **SQL Server-konfiguration** öppnas.

        ![Lägga till och konfigurera en SQL Server-databas](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-adddb.png)

    1. Välj att ansluta med SQL Server-autentisering eller Windows-autentisering i dialogrutan **SQL Server-konfiguration.** Om du väljer SQL Server-autentisering anger du befintliga autentiseringsuppgifter. Ange SQL Server-namnet och namnet på den databas som du vill synkronisera och välj **Testa anslutning** för att testa inställningarna. Välj sedan **Spara** och den registrerade databasen visas i listan.

        ![SQL Server-databasen är nu registrerad](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-dbadded.png)

    1. Stäng appen Client Sync Agent.

1. Välj **databasen**på sidan **Konfigurera lokalt** i portalen .

1. Ange ett namn på den nya synkroniseringsmedlemmen i fältet **Synkronisera medlemsnamn** på sidan **Välj databas.** Det här namnet skiljer sig från namnet på själva databasen. Välj databasen i listan. I fältet **Synkronisera vägbeskrivningar** väljer du **Dubbelriktad synkronisering**, **Till hubben**eller **Från hubben**.

    ![Markera den lokala databasen](media/sql-database-get-started-sql-data-sync/datasync-preview-selectdb.png)

1. Välj **OK** för att stänga sidan **Välj databas.** Välj sedan **OK** för att stänga sidan **Konfigurera lokalt** och vänta på att den nya synkroniseringsmedlemmen ska skapas och distribueras. Slutligen väljer du **OK** för att stänga sidan **Välj synkroniseringsmedlemmar.**

> [!NOTE]
> Om du vill ansluta till SQL Data Sync och den lokala agenten lägger du till ditt användarnamn i rollen *DataSync_Executor*. Data sync skapar den här rollen på SQL Server-instansen.

## <a name="configure-sync-group"></a>Konfigurera synkroniseringsgrupp

När de nya synkroniseringsgruppmedlemmarna har skapats och distribuerats markeras **synkroniseringsgruppen Konfigurera (steg 3)** på sidan **Ny synkroniseringsgrupp.**

![Steg 3-inställningar](media/sql-database-get-started-sql-data-sync/stepthree.png)

1. På sidan **Tabeller** väljer du en databas i listan över medlemmar i synkroniseringsgruppen och väljer **Uppdatera schema**.

1. Välj de tabeller som du vill synkronisera i listan. Som standard är alla kolumner markerade, så inaktivera kryssrutan för de kolumner som du inte vill synkronisera. Se till att låta primärnyckelkolumnen vara markerad.

1. Välj **Spara**.

1. Som standard synkroniseras inte databaser förrän de har schemalagts eller körts manuellt. Om du vill köra en manuell synkronisering navigerar du till din SQL-databas i Azure-portalen, väljer Synkronisera till andra databaser och väljer **synkroniseringsgruppen.** Sidan **Datasynkronisering** öppnas. Välj **Synkronisera**.

    ![Manuell synkronisering](media/sql-database-get-started-sql-data-sync/datasync-sync.png)

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

**Hur ofta kan Data Sync synkronisera mina data?**

Den minimala varaktigheten mellan synkroniseringarna är fem minuter.

**Skapar SQL Data Sync tabeller helt?**

Om tabeller i synkroniseringsschemat saknas i destinationsdatabasen skapar SQL Data Sync dem med de kolumner du har valt. Detta resulterar dock inte i ett schema med fullständig återgivning av följande skäl:

- Endast de kolumner som du väljer skapas i måltabellen. Kolumner som inte valts ignoreras.
- Endast markerade kolumnindex skapas i måltabellen. Index för kolumner som inte valts ignoreras.
- Index på XML-typkolumner skapas inte.
- CHECK-begränsningar skapas inte.
- Utlösare i källtabellerna skapas inte.
- Vyer och lagrade procedurer skapas inte.

På grund av dessa begränsningar rekommenderar vi följande:

- Skapa schemat för full återgivning för produktionsmiljöer själv.
- När du experimenterar med tjänsten använder du funktionen för automatisk etablering.

**Varför ser jag tabeller som jag inte har skapat?**

Data Sync skapar ytterligare tabeller i databasen för ändringsspårning. Ta inte bort dessa eller datasynkronisering slutar fungera.

**Är mina data konvergent efter en synkronisering?**

Inte nödvändigtvis. Ta en synkroniseringsgrupp med ett nav och tre ekrar (A, B och C) där synkroniseringar är Hub till A, Hub till B och Hub till C. Om en ändring görs i databas A *efter* synkroniseringen mellan navet till en skrivs inte den ändringen till databas B eller databas C förrän nästa synkroniseringsuppgift.

**Hur får jag schemaändringar till en synkroniseringsgrupp?**

Skapa och sprida alla schemaändringar manuellt.

1. Replikera schemaändringarna manuellt till navet och till alla synkroniseringsmedlemmar.
1. Uppdatera synkroniseringsschemat.

Så här lägger du till nya tabeller och kolumner:

Nya tabeller och kolumner påverkar inte den aktuella synkroniseringen och Data Sync ignorerar dem förrän de läggs till i synkroniseringsschemat. När du lägger till nya databasobjekt följer du sekvensen:

1. Lägg till nya tabeller eller kolumner i navet och till alla synkroniseringsmedlemmar.
1. Lägg till nya tabeller eller kolumner i synkroniseringsschemat.
1. Börja infoga värden i de nya tabellerna och kolumnerna.

Så här ändrar du datatypen för en kolumn:

När du ändrar datatypen för en befintlig kolumn fortsätter Data Sync att fungera så länge de nya värdena passar den ursprungliga datatypen som definierats i synkroniseringsschemat. Om du till exempel ändrar typen i källdatabasen från **int** till **bigint**fortsätter DataSynkronisering att fungera tills du infogar ett värde som är för stort för int-datatypen. **int** Om du vill slutföra ändringen replikerar du schemaändringen manuellt till navet och till alla synkroniseringsmedlemmar och uppdaterar sedan synkroniseringsschemat.

**Hur exporterar och importerar jag en databas med Data Sync?**

När du har exporterat en databas som en *BACPAC-fil* och importerat filen för att skapa en databas gör du följande för att använda Datasynkronisering i den nya databasen:

1. Rensa datasynkroniseringsobjekten och ytterligare tabeller i den nya databasen med hjälp av [det här skriptet](https://github.com/vitomaz-msft/DataSyncMetadataCleanup/blob/master/Data%20Sync%20complete%20cleanup.sql). Skriptet tar bort alla nödvändiga datasynkroniseringsobjekt från databasen.
1. Återskapa synkroniseringsgruppen med den nya databasen. Om du inte längre behöver den gamla synkroniseringsgruppen tar du bort den.

**Var hittar jag information om klientagenten?**

Vanliga frågor och svar om klientagenten finns i [Vanliga frågor och svar om ombud](sql-database-data-sync-agent.md#agent-faq).

## <a name="next-steps"></a>Nästa steg

Grattis! Du har skapat en synkroniseringsgrupp som innehåller både en SQL Database-instans och en SQL Server-databas.

Mer information om SQL Data Sync finns i:

- [Datasynkroniseringsagent för Azure SQL Data Sync](sql-database-data-sync-agent.md)
- [Metodtips](sql-database-best-practices-data-sync.md) och [felsökning av problem med Azure SQL Data Sync](sql-database-troubleshoot-data-sync.md)
- [Övervaka SQL Data Sync med Azure Monitor-loggar](sql-database-sync-monitor-oms.md)
- [Uppdatera synkroniseringsschemat med Transact-SQL](sql-database-update-sync-schema.md) eller [PowerShell](scripts/sql-database-sync-update-schema.md)

Mer information om SQL Database finns i:

- [Översikt över SQL Database](sql-database-technical-overview.md)
- [Livscykelhantering för databas](https://msdn.microsoft.com/library/jj907294.aspx)
