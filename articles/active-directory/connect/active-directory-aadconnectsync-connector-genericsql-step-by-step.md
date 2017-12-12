---
title: "Allmän SQL Connector steg-för-steg | Microsoft Docs"
description: "Den här artikeln är guida dig genom en enkel HR-system steg med hjälp av allmänna SQL-anslutningen."
services: active-directory
documentationcenter: 
author: AndKjell
manager: mtillman
editor: 
ms.assetid: 28c1cc60-24fd-4d0d-a36d-b4aba6de86e7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 311ef784497310b5605e25daf13102e3ff4bb08c
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="generic-sql-connector-step-by-step"></a>Stegvisa anvisningar för allmän SQL-anslutningsapp
Det här avsnittet finns stegvisa instruktioner. Den skapar en enkel HR-exempeldatabas och använda den för att importera vissa användare och deras gruppmedlemskap.

## <a name="prepare-the-sample-database"></a>Förbereda exempeldatabasen
På en server som kör SQL Server som kör SQL-skript finns i [bilaga A](#appendix-a). Det här skriptet skapar en exempeldatabas med namnet GSQLDEMO. Det ser ut som den här bilden objektmodell för databasen som skapats:  
![Objektmodell](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/objectmodel.png)

Skapa även en användare som du vill använda för att ansluta till databasen. I den här genomgången användaren kallas FABRIKAM\SQLUser och finns i domänen.

## <a name="create-the-odbc-connection-file"></a>Skapa filen ODBC-anslutning
Den allmänna SQL-anslutningen använder ODBC för att ansluta till fjärrservern. Vi måste först skapa en fil med information för ODBC-anslutning.

1. Starta verktyget för ODBC-hantering på servern:  
   ![ODBC](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc.png)
2. Välj fliken **fil-DSN**. Klicka på **Lägg till...** .  
   ![ODBC1](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc1.png)
3. Out-of-box-drivrutinen fungerar finjustering så markerar du den och klicka på **Nästa >**.  
   ![ODBC2](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc2.png)
4. Ge filen ett namn, t. ex. **GenericSQL**.  
   ![ODBC3](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc3.png)
5. Klicka på **Slutför**.  
   ![ODBC4](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc4.png)
6. Tid för att konfigurera anslutningen. Ger en bra beskrivning för datakällan och ange namnet på den server som kör SQL Server.  
   ![ODBC5](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc5.png)
7. Välj hur du autentiserar med SQL. I detta fall kan använda vi Windows-autentisering.  
   ![ODBC6](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc6.png)
8. Ange namnet på exempeldatabasen, **GSQLDEMO**.  
   ![ODBC7](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc7.png)
9. Behåll allt standardvärdet på den här skärmen. Klicka på **Slutför**.  
   ![ODBC8](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc8.png)
10. Kontrollera att allt fungerar som förväntat, klicka på **testa datakällan**.  
    ![ODBC9](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc9.png)
11. Kontrollera att testet lyckas.  
    ![ODBC10](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc10.png)
12. ODBC-konfigurationsfilen ska nu visas i fil-DSN.  
    ![ODBC11](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc11.png)

Nu har vi filen vi behöver och kan börja skapa kopplingen.

## <a name="create-the-generic-sql-connector"></a>Skapa allmänna SQL-koppling
1. Välj i Synchronization Service Manager-UI **kopplingar** och **skapa**. Välj **generiskt SQL (Microsoft)** och ge det ett beskrivande namn.  
   ![Connector1](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector1.png)
2. Hitta DSN-fil som du skapade i föregående avsnitt och överför den till servern. Ange autentiseringsuppgifter för att ansluta till databasen.  
   ![Connector2](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector2.png)
3. I den här genomgången vi gör det enkelt för oss och säger att det finns två objekttyper **användare** och **gruppen**.
   ![Connector3](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector3.png)
4. För att hitta attribut vill vi att anslutningen ska identifiera attributen genom att titta på själva tabellen. Eftersom **användare** är ett reserverat ord SQL, vi behöver ge inom hakparenteser [].  
   ![Connector4](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector4.png)
5. Tid för att definiera fästpunkt-attribut och DN-attribut. För **användare**, vi använder en kombination av två attribut användarnamn och EmployeeID. För **grupp**, använder vi GroupName (inte realistiska i praktiken, men i den här genomgången fungerar).
   ![Connector5](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector5.png)
6. Inte alla attributtyper kan identifieras i en SQL-databas. Attributtypen referens kan inte särskilt. Vi behöver ändra OwnerID och MemberID refererar för objekttypen grupp.  
   ![Connector6](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector6.png)
7. Det valda som referensattribut i föregående steg kräver objekttypen attributen dessa värden är en referens till. I vårt fall objekttyp för användaren.  
   ![Connector7](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector7.png)
8. Välj på sidan globala parametrar **vattenstämpel** som delta-strategi. Också skriva i formatet datum/tid **åååå-MM-dd: mm: ss**.
   ![Connector8](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector8.png)
9. På den **konfigurera partitioner och hierarkier** markerar du båda objekttyper.
   ![Connector9](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector9.png)
10. På den **Välj objekttyper** och **Välj attribut**, väljer både objekttyper och alla attribut. På den **konfigurera ankare** klickar du på **Slutför**.

## <a name="create-run-profiles"></a>Skapa körningsprofiler
1. Välj i Synchronization Service Manager-UI **kopplingar**, och **Konfigurera körningsprofiler**. Klicka på **ny profil**. Vi börjar med **fullständig Import**.  
   ![Runprofile1](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/runprofile1.png)
2. Välj typ **fullständig Import (endast steget)**.  
   ![Runprofile2](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/runprofile2.png)
3. Markerar du partitionen **objekt = användare**.  
   ![Runprofile3](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/runprofile3.png)
4. Välj **tabell** och skriv **[användare]**. Rulla ned till avsnittet med flera värden objektet typ och ange data som i följande bild. Välj **Slutför** spara steget.  
   ![Runprofile4a](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/runprofile4a.png)  
   ![Runprofile4b](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/runprofile4b.png)  
5. Välj **nytt steg**. Den här gången väljer **objekt = grupp**. Använd konfigurationen enligt följande bild på den sista sidan. Klicka på **Slutför**.  
   ![Runprofile5a](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/runprofile5a.png)  
   ![Runprofile5b](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/runprofile5b.png)  
6. Valfritt: Om du vill kan du kan konfigurera ytterligare körning av profiler. Den här genomgången används endast den fullständiga importen.
7. Klicka på **OK** att slutföra ändringarna för körning av profiler.

## <a name="add-some-test-data-and-test-the-import"></a>Lägga till vissa testdata och testa importen
Fylla lite testdata i din exempeldatabas. När du är klar väljer du **kör** och **fullständig import**.

Här är en användare med två telefonnummer och en grupp med medlemmar.  
![cs1](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/cs1.png)  
![CS2](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/cs2.png)  

## <a name="appendix-a"></a>Bilaga A
**SQL-skript för att skapa exempeldatabasen**

```SQL
---Creating the Database---------
Create Database GSQLDEMO
Go
-------Using the Database-----------
Use [GSQLDEMO]
Go
-------------------------------------
USE [GSQLDEMO]
GO
/****** Object:  Table [dbo].[GroupMembers]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[GroupMembers](
    [MemberID] [int] NOT NULL,
    [Group_ID] [int] NOT NULL
) ON [PRIMARY]

GO
/****** Object:  Table [dbo].[GROUPS]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[GROUPS](
    [GroupID] [int] NOT NULL,
    [GROUPNAME] [nvarchar](200) NOT NULL,
    [DESCRIPTION] [nvarchar](200) NULL,
    [WATERMARK] [datetime] NULL,
    [OwnerID] [int] NULL,
PRIMARY KEY CLUSTERED
(
    [GroupID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]

GO
/****** Object:  Table [dbo].[USERPHONE]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
SET ANSI_PADDING ON
GO
CREATE TABLE [dbo].[USERPHONE](
    [USER_ID] [int] NULL,
    [Phone] [varchar](20) NULL
) ON [PRIMARY]

GO
SET ANSI_PADDING OFF
GO
/****** Object:  Table [dbo].[USERS]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[USERS](
    [USERID] [int] NOT NULL,
    [USERNAME] [nvarchar](200) NOT NULL,
    [FirstName] [nvarchar](100) NULL,
    [LastName] [nvarchar](100) NULL,
    [DisplayName] [nvarchar](100) NULL,
    [ACCOUNTDISABLED] [bit] NULL,
    [EMPLOYEEID] [int] NOT NULL,
    [WATERMARK] [datetime] NULL,
PRIMARY KEY CLUSTERED
(
    [USERID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]

GO
ALTER TABLE [dbo].[GroupMembers]  WITH CHECK ADD  CONSTRAINT [FK_GroupMembers_GROUPS] FOREIGN KEY([Group_ID])
REFERENCES [dbo].[GROUPS] ([GroupID])
GO
ALTER TABLE [dbo].[GroupMembers] CHECK CONSTRAINT [FK_GroupMembers_GROUPS]
GO
ALTER TABLE [dbo].[GroupMembers]  WITH CHECK ADD  CONSTRAINT [FK_GroupMembers_USERS] FOREIGN KEY([MemberID])
REFERENCES [dbo].[USERS] ([USERID])
GO
ALTER TABLE [dbo].[GroupMembers] CHECK CONSTRAINT [FK_GroupMembers_USERS]
GO
ALTER TABLE [dbo].[GROUPS]  WITH CHECK ADD  CONSTRAINT [FK_GROUPS_USERS] FOREIGN KEY([OwnerID])
REFERENCES [dbo].[USERS] ([USERID])
GO
ALTER TABLE [dbo].[GROUPS] CHECK CONSTRAINT [FK_GROUPS_USERS]
GO
ALTER TABLE [dbo].[USERPHONE]  WITH CHECK ADD  CONSTRAINT [FK_USERPHONE_USER] FOREIGN KEY([USER_ID])
REFERENCES [dbo].[USERS] ([USERID])
GO
ALTER TABLE [dbo].[USERPHONE] CHECK CONSTRAINT [FK_USERPHONE_USER]
GO
```
