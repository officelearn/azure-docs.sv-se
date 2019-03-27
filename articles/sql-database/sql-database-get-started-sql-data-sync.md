---
title: Konfigurera Azure SQL Data Sync | Microsoft Docs
description: Den här självstudiekursen visar hur du ställer in Azure SQL Data Sync
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
manager: craigg
ms.date: 01/14/2019
ms.openlocfilehash: 82b85ffd685df52e702db15e5a5b57a53a3b4f64
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58486010"
---
# <a name="tutorial-set-up-sql-data-sync-between-azure-sql-database-and-sql-server-on-premises"></a>Självstudier: Ställ in SQL Data Sync mellan Azure SQL Database och SQL Server lokalt

I den här självstudien får du lära dig hur du ställer in Azure SQL Data Sync genom att skapa en synkroniseringsgrupp som innehåller både Azure SQL Database och SQL Server-instanser. Synkroniseringsgruppen är konfigurerade och synkroniserar enligt det schema som du anger.

Självstudien förutsätter att du har minst tidigare erfarenhet med SQL Database och SQL Server.

En översikt över SQL Data Sync finns [synkronisera data i molnet och lokala databaser med Azure SQL Data Sync](sql-database-sync-data.md).

PowerShell-exempel på hur du konfigurerar SQL Data Sync finns [synkronisera mellan Azure SQL-databaser](scripts/sql-database-sync-data-between-sql-databases.md) eller [en Azure SQL Database och en lokal SQL Server-databas](scripts/sql-database-sync-data-between-azure-onprem.md)

> [!IMPORTANT]
> Azure SQL Data Sync har **inte** stöd för Azure SQL Database Managed Instance just nu.

## <a name="create-sync-group"></a>Skapa synkroniseringsgrupp

1. Gå till Azure portal i webbläsaren. Leta upp din SQL-databas från själva instrumentpanelen eller, Välj den **SQL-databaser** ikonen i verktygsfältet och på den **SQL-databaser** väljer du den databas som du vill använda som hubbdatabasen för datasynkronisering.

    > [!NOTE]
    > Hubben databas en synkronisering topologi centrala slutpunkt, där en synkroniseringsgrupp har flera slutpunkter för databasen. Alla andra medlem databaser med slutpunkter i synkroniseringsgruppen, synkronisera med hubbdatabasen.

1. På den **SQL-databas** för den valda databasen väljer **synkronisera till andra databaser**.

    ![Synkronisera till andra databaser-alternativ](media/sql-database-get-started-sql-data-sync/datasync-overview.png)

1. På den **synkronisera till andra databaser** väljer **ny Synkroniseringsgrupp**. Den **ny synkroniseringsgrupp** öppnas med **skapa synkroniseringsgruppen (steg 1)** markerade.

   ![Steg 1-inställningar](media/sql-database-get-started-sql-data-sync/stepone.png)

   På den **skapa Datasynkroniseringsgrupp** ändrar du följande inställningar:

   | Inställning                        | Beskrivning |
   | ------------------------------ | ------------------------------------------------- |
   | **Synkroniseringsgruppsnamn** | Ange ett namn för den nya sync-gruppen. Det här namnet skiljer sig från namnet på själva databasen. |
   | **Metadatasynkroniserings-databasen** | Välja att skapa en databas (rekommenderas) eller Använd en befintlig databas.<br/><br/>Om du väljer **ny databas**väljer **Skapa ny databas.** På den **SQL Database** , namn och konfigurera den nya databasen och välj **OK**.<br/><br/>Om du väljer **Använd befintlig databas**, Välj databasen i listan. |
   | **Automatisk synkronisering** | Välj **på** eller **av**.<br/><br/>Om du väljer **på**, ange ett tal och välj **sekunder**, **minuter**, **timmar**, eller **dagar** i den **Synkroniseringsfrekvensen** avsnittet. |
   | **Konfliktlösning** | Välj **hubbvinst** eller **medlemsvinst**.<br/><br/>**Hubbvinst** innebär att när konflikter uppstår data i hubbdatabasen skriver över data som orsakar konflikter i medlemsdatabasen.<br/><br/>**Medlemsvinst** innebär att när konflikter uppstår data i medlemsdatabasen skriver över data som orsakar konflikter i hubbdatabasen. |

   > [!NOTE]
   > Microsoft rekommenderar att du skapar en ny, tom databas som den **Metadatasynkroniserings-databasen**. Datasynkronisering skapar tabeller i den här databasen och kör en återkommande arbetsbelastning. Den här databasen delas som den **Metadatasynkroniserings-databasen** för alla synkroniseringsgrupper i en vald region och du inte kan ändra databasen eller dess namn utan att ta bort alla synkroniseringsgrupper och Synkroniseringsagenter i regionen.

   Välj **OK** och vänta tills synkroniseringsgruppen skapas och distribueras.

## <a name="add-sync-members"></a>Lägg till synkroniseringsmedlemmar

När nya synkroniseringsgruppen skapas och distribueras, **Lägg till synkroniseringsmedlemmar (steg 2)** är markerad på den **ny synkroniseringsgrupp** sidan.

I den **Hubbdatabasen** ange befintliga autentiseringsuppgifter för SQL Database-servern där hubb-databasen finns. Ange inte *nya* autentiseringsuppgifter i det här avsnittet.

![Steg 2-inställningar](media/sql-database-get-started-sql-data-sync/steptwo.png)

### <a name="to-add-an-azure-sql-database"></a>Att lägga till en Azure SQL Database

I den **medlemsdatabas** avsnittet om du vill lägga till en Azure SQL Database i synkroniseringsgruppen genom att välja **lägga till en Azure SQL Database**. Den **konfigurera Azure SQL Database** öppnas.

  ![Steg 2 – konfigurera databasen](media/sql-database-get-started-sql-data-sync/steptwo-configure.png)

  På den **konfigurera Azure SQL Database** ändrar du följande inställningar:

  | Inställning                       | Beskrivning |
  | ----------------------------- | ------------------------------------------------- |
  | **Namn på synkroniseringsmedlem** | Ange ett namn för den nya synkroniseringsmedlemmen. Det här namnet skiljer sig från namnet på databasen själva. |
  | **Prenumeration** | Välj den associera Azure-prenumerationen för fakturering. |
  | **Azure SQL Server** | Välj den befintliga SQL Database-servern. |
  | **Azure SQL Database** | Välj den befintliga SQL-databasen. |
  | **Synkroniseringsriktningar** | Välj **dubbelriktad synkronisering**, **till hubben**, eller **från hubben**. |
  | **Användarnamn** och **lösenord** | Ange de befintliga autentiseringsuppgifterna för SQL Database-servern där medlemsdatabasen finns. Ange inte *nya* autentiseringsuppgifter i det här avsnittet. |

  Välj **OK** och vänta på ny synkroniseringsmedlemmen skapas och distribueras.

<a name="add-on-prem"></a>
### <a name="to-add-an-on-premises-sql-server-database"></a>Att lägga till en lokal SQL Server-databas

I den **medlemsdatabas** avsnittet om du vill lägga till en lokal SQL Server i synkroniseringsgruppen genom att välja **Lägg till en lokal databas**. Den **konfigurera lokala** öppnas där du kan göra följande:

1. Välj **Välj Synkroniseringsagentsgatewayen**. Den **Välj Synkroniseringsagent** öppnas.

   ![Skapa en sync-agenten](media/sql-database-get-started-sql-data-sync/steptwo-agent.png)

1. På den **väljer Synkroniseringsagenten** väljer du om du vill använda en befintlig agent eller skapa en agent.

   Om du väljer **befintliga agenter**, Välj den befintliga agenten i listan.

   Om du väljer **skapa en ny agent**, gör du följande:

   1. Ladda ned data sync-agenten från länken och installera den på den dator där SQL Server. Du kan också hämta agenten direkt från [SQL Azure Data Sync-agenten](https://www.microsoft.com/download/details.aspx?id=27693).

      > [!IMPORTANT]
      > Du måste öppna utgående TCP-port 1433 i brandväggen så att klientagenten kommunicera med servern.

   1. Ange ett namn för agenten.

   1. Välj **skapa och generera nyckel** och kopiera agentnyckeln till Urklipp.

   1. Välj **OK** att Stäng den **Välj Synkroniseringsagent** sidan.

1. Hitta och kör appen Klientsynkroniseringsagenten på SQL Server-datorn.

   ![Data synkroniseras agent-klientappen](media/sql-database-get-started-sql-data-sync/datasync-preview-clientagent.png)

    1. Välj i appen sync-agenten **skicka Agentnyckeln**. Den **synkronisera Metadata databaskonfiguration** öppnas dialogrutan.

    1. I den **synkronisera Metadata databaskonfiguration** dialogrutan Klistra in agentnyckeln som kopieras från Azure-portalen. Ange även de befintliga autentiseringsuppgifterna för Azure SQL Database-servern där metadata-databasen finns. (Om du har skapat en metadata-databasen är den här databasen på samma server som hubbdatabasen.) Välj **OK** och vänta tills konfigurationen har slutförts.

        ![Ange autentiseringsuppgifter för agenten nyckel och server](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-enterkey.png)

        > [!NOTE]
        > Om du får ett Brandväggsfel kan du skapa en brandväggsregel på Azure för att tillåta inkommande trafik från SQL Server-datorn. Du kan skapa regeln manuellt i portalen eller i SQL Server Management Studio (SSMS). I SSMS, ansluta till hubb-databasen på Azure genom att ange dess namn som < hub_database_name >. database.windows.net.

    1. Välj **registrera** att registrera en SQL Server-databas med agenten. Den **SQL Server-konfigurationsfilen** öppnas dialogrutan.

        ![Lägga till och konfigurera en SQL Server-databas](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-adddb.png)

    1. I den **SQL Server-konfigurationsfilen** dialogrutan väljer du att ansluta med hjälp av SQL Server-autentisering eller Windows-autentisering. Om du väljer SQL Server-autentisering anger du de befintliga autentiseringsuppgifterna. Ange namnet på SQL Server och namnet på databasen som du vill synkronisera och välj **Testanslutning** att testa dina inställningar. Välj sedan **spara** och den registrerade databasen visas i listan.

        ![SQL Server-databas är nu registrerad](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-dbadded.png)

    1. Stäng klienten synkronisera agenten app.

1. I portalen på den **konfigurera lokala** väljer **väljer du databasen**.

1. På den **Välj databas** sidan den **Synkroniseringsmedlemsnamnet** fältet, ange ett namn för den nya synkroniseringsmedlemmen. Det här namnet skiljer sig från namnet på själva databasen. Välj databasen i listan. I den **Synkroniseringsriktningar** väljer **dubbelriktad synkronisering**, **till hubben**, eller **från hubben**.

    ![Välj databasen på plats](media/sql-database-get-started-sql-data-sync/datasync-preview-selectdb.png)

1. Välj **OK** att Stäng den **Välj databas** sidan. Välj sedan **OK** att Stäng den **konfigurera lokala** sidan och vänta på ny synkroniseringsmedlemmen skapas och distribueras. Välj slutligen **OK** att Stäng den **Välj synkroniseringsmedlemmar** sidan.

> [!NOTE]
> Anslut till SQL Data Sync och lokal agent genom att lägga till ditt användarnamn till rollen *DataSync_Executor*. Datasynkronisering skapar den här rollen på SQL Server-instansen.

## <a name="configure-sync-group"></a>Konfigurera synkroniseringsgrupp

När nya synkronisera gruppmedlemmarna skapas och distribueras, **konfigurera synkroniseringsgrupp (steg 3)** är markerat i den **ny synkroniseringsgrupp** sidan.

![Steg 3-inställningar](media/sql-database-get-started-sql-data-sync/stepthree.png)

1. På den **tabeller** , Välj en databas från listan över gruppmedlemmar för synkronisering och välj **uppdatera schemat**.

1. Markera de tabeller som du vill synkronisera i listan. Alla kolumner har valts som standard, så inaktivera kryssrutan för de kolumner som du inte vill synkronisera. Glöm inte att lämna primärnyckelkolumnen valt.

1. Välj **Spara**.

1. Som standard synkroniseras inte databaser tills schemalagd eller manuellt köra. Om du vill köra en manuell synkronisering, navigera till din SQL-databas i Azure portal, Välj **synkronisera till andra databaser**, och välj synkroniseringsgruppen. Den **datasynkronisering** öppnas. Välj **Synkronisera**.

    ![Manuell synkronisering](media/sql-database-get-started-sql-data-sync/datasync-sync.png)

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

**Hur ofta kan datasynkronisering synkronisera mina data?**

Minimal varaktigheten mellan synkroniseringar är fem minuter.

**Skapar SQL Data Sync fullständigt tabeller?**

Om synkronisering schemat tabeller saknas i måldatabasen skapar dem i SQL Data Sync med de markerade kolumnerna. Dock resultera inte detta i ett schema för fullständig exakthet av följande skäl:

- Endast kolumner som du väljer skapas i tabellen. Kolumner som inte valts ignoreras.
- Endast markerade kolumnindex skapas i tabellen. För kolumner som inte valts ignoreras dessa index.
- Index för kolumner av typen XML skapas inte.
- Kontrollbegränsningar skapas inte.
- Utlösare källtabellerna skapas inte.
- Vyer och lagrade procedurer skapas inte.

På grund av dessa begränsningar rekommenderar vi följande:

- För produktionsmiljöer, skapa schemat för fullständig exakthet själv.
- När du experimenterar med tjänsten, kan du använda funktionen för automatisk etablering.

**Varför ser jag tabeller som jag inte har skapat?**

Datasynkronisering skapar ytterligare tabeller i databasen för ändringsspårning. Ta inte bort dessa eller datasynkronisering slutar fungera.

**Är Mina data avsmalnande efter en synkronisering?**

Inte nödvändigtvis. Ta en synkroniseringsgrupp med en hubb och tre ekrar (A, B och C) där synkroniseringar har Hub till A, b-hubb och Hub till C. Om en ändring görs till databasen A *när* hubben till en synkronisering som ändras inte skrivas till B-databas eller databas C tills den nästa synkronisering.

**Hur får jag schemaändringar i en synkroniseringsgrupp**

Se och sprida alla schemaändringar manuellt.

1. Replikera ändringar av entitetsschemat manuellt till hubben och för alla synkroniseringsmedlemmar.
1. Uppdatera synkroniseringsschemat.

För att lägga till nya tabeller och kolumner:

Nya tabeller och kolumner som inte påverkar den aktuella synkroniseringen och datasynkronisering ignorerar dem förrän de läggs till i synkroniseringsschemat. När du lägger till nya databasobjekt, följa de:

1. Lägga till nya tabeller eller kolumner till hubben och alla synkroniseringsmedlemmar.
1. Lägga till nya tabeller eller kolumner till i synkroniseringsschemat.
1. Börja lägga till värden i de nya tabeller och kolumner.

För att ändra datatypen för en kolumn:

När du ändrar datatypen för en befintlig kolumn, fortsätter Data Sync att fungera så länge de nya värdena passar den ursprungliga datatyp som definierats i synkroniseringsschemat. Exempel: Om du ändrar typen i källdatabasen från **int** till **bigint**, datasynkronisering fortsätter att fungera tills du infogar ett värde som är för stor för den **int** datatyp. Replikera en schemaändring manuellt till hubben och för alla synkroniseringsmedlemmar för att slutföra ändringen och sedan uppdatera synkroniseringsschemat.

**Hur kan jag exportera och importera en databas med Data Sync?**

När du exporterar en databas som en *.bacpac* filen och importera filen för att skapa en databas, gör följande om du vill använda datasynkronisering i den nya databasen:

1. Rensa Data Sync-objekt och ytterligare tabeller på den nya databasen med hjälp av [det här skriptet](https://github.com/vitomaz-msft/DataSyncMetadataCleanup/blob/master/Data%20Sync%20complete%20cleanup.sql). Skriptet tar bort alla nödvändiga Data Sync-objekt från databasen.
1. Återskapa synkroniseringsgrupp med den nya databasen. Om du behöver inte längre gamla synkroniseringsgruppen kan du ta bort den.

**Var hittar jag information om klientens agent?**

Läs vanliga frågor om klientagenten [agenten vanliga frågor och svar](sql-database-data-sync-agent.md#agent-faq).

## <a name="next-steps"></a>Nästa steg

Grattis! Du har skapat en synkroniseringsgrupp som innehåller både en SQL-databasinstans och en SQL Server-databas.

Mer information om SQL Data Sync finns i:

- [Data Sync-agenten för Azure SQL Data Sync](sql-database-data-sync-agent.md)
- [Bästa praxis](sql-database-best-practices-data-sync.md) och [så här felsöker du problem med Azure SQL Data Sync](sql-database-troubleshoot-data-sync.md)
- [Övervaka SQL Data Sync med Azure Monitor-loggar](sql-database-sync-monitor-oms.md)
- [Uppdatera synkroniseringsschemat med Transact-SQL](sql-database-update-sync-schema.md) eller [PowerShell](scripts/sql-database-sync-update-schema.md)

Mer information om SQL Database finns i:

- [Översikt över SQL Database](sql-database-technical-overview.md)
- [Livscykelhantering för databas](https://msdn.microsoft.com/library/jj907294.aspx)
