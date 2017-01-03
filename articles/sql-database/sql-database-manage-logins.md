---
title: "SQL Database, autentisering och auktorisering: bevilja åtkomst| Microsoft Docs"
description: "Lär dig mer om SQL Database säkerhetshantering, särskilt hur du hanterar säkerheten för databasåtkomst och inloggning via huvudnamnkonto på servernivå."
keywords: "sql database-säkerhet, hantering av databassäkerhet, inloggningssäkerhet, databassäkerhet, databasåtkomst"
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: 0a65a93f-d5dc-424b-a774-7ed62d996f8c
ms.service: sql-database
ms.custom: authentication and authorization
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 09/14/2016
ms.author: rickbyh
translationtype: Human Translation
ms.sourcegitcommit: 498ee06fedf986a1b7fb4e362c4b5cb688f6a685
ms.openlocfilehash: 37cce678e0a9dfb081719cee4be7a04a838c1012


---
# <a name="sql-database-authentication-and-authorization-granting-access"></a>SQL Database, autentisering och auktorisering: bevilja åtkomst
> [!div class="op_single_selector"]
> * [Självstudier för att komma igång](sql-database-get-started-security.md)
> * [Bevilja åtkomst](sql-database-manage-logins.md)
> 
> 

Börja här för en översikt över SQL Database åtkomstkoncept för administratörer, icke-administratörer och roller.

## <a name="unrestricted-administrative-accounts"></a>Obegränsade administrativa konton
Det finns två möjliga administrativa konton med obegränsad behörighet för åtkomst till den virtuella huvuddatabasen alla användardatabaser. Dessa konton kallas huvudnamnkonton på servernivå.

### <a name="azure-sql-database-subscriber-account"></a>Azure SQL Database-prenumerationskonto
Ett enskilt inloggningskonto skapas när en logisk SQL-instans skapas, med namnet SQL Database-prenumerationskonto. Det här kontot ansluter med hjälp av SQL Server-autentisering (användarnamn och lösenord). Kontot är administratör på den logiska server-instansen och på alla användardatabaser kopplade till denna instans. Behörigheterna för prenumerationskontot kan inte begränsas. Endast ett av dessa konton kan finnas.

### <a name="azure-active-directory-administrator"></a>Azure Active Directory-administratör
Ett Azure Active Directory-konto kan också konfigureras som en administratör. Detta konto kan vara en enskild Azure AD-användare, eller kan vara en Azure AD-grupp som innehåller flera Azure AD-användare. Det är valfritt att konfigurera en Azure AD-administratör, men en Azure AD-administratör måste konfigureras om du vill använda Windows-autentisering för Azure AD-konton för att ansluta till SQL Database. Mer information om hur du konfigurerar åtkomst till Azure Active Directory finns i [Ansluta till SQL Database eller SQL Data Warehouse med Azure Active Directory-autentisering](sql-database-aad-authentication.md) och [SSMS-stöd för Azure AD MFA med SQL Database och SQL Data Warehouse](sql-database-ssms-mfa-authentication.md).

### <a name="configuring-the-firewall"></a>Konfigurering av brandväggen
När brandväggen på servernivå är konfigurerad kan Azure SQL Database-prenumerationskontot och Azure Active Directory-kontot ansluta till huvuddatabasen och alla användardatabaser. Brandväggen på servernivå kan konfigureras via portalen. När en anslutning upprättats kan även ytterligare brandväggsregler på servernivå konfigureras med hjälp av Transact-SQL-instruktionen [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx). Mer information om konfigureringen av brandväggen finns i [How to: Configure an Azure SQL Database firewall using the Azure Portal](sql-database-configure-firewall-settings.md) (Så här gör du: Konfigurera en Azure SQL Database-brandvägg med hjälp av Azure Portal).

### <a name="administrator-access-path"></a>Åtkomstväg för administratör
När brandväggen på servernivå är korrekt konfigurerad kan SQL Database-prenumerationskontot och i Azure Active Directory SQL Server-administratörer ansluta med hjälp av klientverktyg som SQL Server Management Studio eller SQL Server Data Tools. Endast de senaste verktygen innehåller alla funktioner och möjligheter. I följande diagram visas en typisk konfiguration för två administratörskonton.
    ![Åtkomstväg för administratör](./media/sql-database-manage-logins/1sql-db-administrator-access.png)

Vid användning av en öppen port i brandväggen på servernivå kan administratörer ansluta till en SQL Database.

### <a name="connecting-to-a-database-by-using-sql-server-management-studio"></a>Ansluta till en databas med hjälp av SQL Server Management Studio
En genomgång av anslutning med hjälp av SQL Server Management Studio finns i [Anslut till SQL Database med SQL Server Management Studio och kör en exempelfråga i T-SQL](sql-database-connect-query-ssms.md).

> [!IMPORTANT]
> Det rekommenderas att du alltid använder den senaste versionen av Management Studio för att förbli synkroniserad med uppdateringar av Microsoft Azure och SQL Database. [Uppdatera SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

## <a name="additional-special-accounts"></a>Ytterligare specialkonton
SQL Database har två begränsade administrativa roller i den virtuella huvuddatabasen där användarkonton kan läggas till.

### <a name="database-creators"></a>Databasskapare
De administrativa kontona kan skapa nya databaser. För att skapa ett ytterligare konto som kan skapa databaser måste du skapa en användare i huvuddatabasen och lägga till användaren i särskilda **dbmanager**-databasroller. Användaren kan vara en oberoende databasanvändare, eller en användare baserad på en SQL Server-inloggning i den virtuella huvuddatabasen.

1. Anslut till den virtuella huvuddatabasen med ett administratörskonto.
2. Alternativt steg: Skapa en SQL Server-autentiseringsinloggning med hjälp av instruktionen [SKAPA INLOGGNING](https://msdn.microsoft.com/library/ms189751.aspx). Exempel på instruktion:
   
   ```
   CREATE LOGIN Mary WITH PASSWORD = '<strong_password>';
   ```
   
   > [!NOTE]
   > Använd ett starkt lösenord när du skapar en inloggning eller en oberoende databasanvändare. Mer information finns i [Starka lösenord](https://msdn.microsoft.com/library/ms161962.aspx).
   > 
   > 
   
   För att förbättra prestandan cachelagras inloggningar (huvudnamn på servernivå) tillfälligt på databasnivån. Information om hur du uppdaterar autentiseringscache finns i [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx).
3. Skapa en användare i den virtuella huvuddatabasen med hjälp av instruktionen [SKAPA ANVÄNDARE](https://msdn.microsoft.com/library/ms173463.aspx). Användaren kan vara en Azure Active Directory-autentiserad oberoende databasanvändare (om du har konfigurerat din miljö för Azure AD-autentisering), eller en SQL Server-autentiserad oberoende databasanvändare, eller en SQL Server-autentiserad användare baserad på en SQL Server-autentiserad inloggning (skapad i föregående steg.) Exempel på instruktioner:
   
   ```
   CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
   CREATE USER Tran WITH PASSWORD = '<strong_password>';
   CREATE USER Mary FROM LOGIN Mary; 
   ```
4. Lägg till den nya användaren till **dbmanager**-databasrollen med hjälp av instruktionen [ALTER ROLE](https://msdn.microsoft.com/library/ms189775.aspx). Exempel på instruktioner:
   
   ```
   ALTER ROLE dbmanager ADD MEMBER Mary; 
   ALTER ROLE dbmanager ADD MEMBER [mike@contoso.com];
   ```
   
   > [!NOTE]
   > Dbmanager är en databasroll i en virtuell huvuddatabas, så du kan bara lägga till en användare till dbmanager-rollen. Du kan inte lägga till en inloggning på servernivå till databasnivårollen.
   > 
   > 
5. Vid behov konfigurerar du brandväggen på servernivå så att den nya användaren kan ansluta.

Nu kan användaren ansluta till den virtuella huvuddatabasen och kan skapa nya databaser. Det konto som skapar databasen blir ägare till databasen.

### <a name="login-managers"></a>Inloggningshanterare
Om du vill kan du slutföra samma steg (skapa en inloggning och användare och lägga till en användare till rollen **loginmanager**) så att användare kan skapa nya inloggningar i den virtuella huvuddatabasen. Detta är vanligtvis inte nödvändigt, eftersom Microsoft rekommenderar att du använder oberoende databasanvändare, som autentiseras på databasnivå istället för att använda användare baserat på inloggningar. Mer information finns i [Oberoende databasanvändare – göra databasen portabel](https://msdn.microsoft.com/library/ff929188.aspx).

## <a name="non-administrator-users"></a>Användare som är icke-administratörer
Icke-administratörskonton behöver i allmänhet inte åtkomst till den virtuella huvuddatabasen. Skapa oberoende databasanvändare på databasnivå med hjälp av instruktionen [SKAPA ANVÄNDARE (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx). Användaren kan vara en Azure Active Directory-autentiserad oberoende databasanvändare (om du har konfigurerat din miljö för Azure AD-autentisering), eller en SQL Server-autentiserad oberoende databasanvändare, eller en SQL Server-autentiserad användare baserad på en SQL Server-autentiserad inloggning (skapad i föregående steg.) Mer information finns i [Oberoende databasanvändare – göra databasen portabel](https://msdn.microsoft.com/library/ff929188.aspx). 

För att skapa användare, anslut till databasen och köra instruktioner som liknar följande exempel:

```
CREATE USER Mary FROM LOGIN Mary; 
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

Initialt kan endast en av administratörerna eller ägaren av databasen skapa användare. För att auktorisera ytterligare användare som ska kunna skapa nya användare: Ge den valda användaren `ALTER ANY USER`-behörighet genom att använda en instruktion som:

```
GRANT ALTER ANY USER TO Mary;
```

Om du vill ge ytterligare användare fullständig behörighet till databasen, gör dem till medlemmar i den fasta databasrollen **db_owner** med hjälp av `ALTER ROLE`-instruktionen.

> [!NOTE]
> Den främsta anledningen till att skapa databasanvändare baserat på inloggningar är när du har SQL Server-autentiserade användare som behöver åtkomst till flera databaser. Användare baserade på inloggningar är knutna till inloggningen och endast ett lösenord för att logga in. Oberoende databasanvändare i individuella databaser är var och en individuella enheter och var och en underhåller ett eget lösenord. Detta kan förvirra oberoende databasanvändare om de inte upprätthåller sina lösenord som identiska.
> 
> 

### <a name="configuring-the-database-level-firewall"></a>Konfigurera brandvägg på databasnivå
Bästa praxis är att icke-administratörer bara har åtkomst genom brandväggen till de databaser som de använder. Istället för att auktorisera deras IP-adresser genom brandväggen på servernivå och ge dem åtkomst till alla databaser, använd instruktionen [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) för att konfigurera brandväggen på databasnivå. Brandväggen på databasnivå kan inte konfigureras via portalen.

### <a name="non-administrator-access-path"></a>Åtkomstväg för icke-administratör
När brandväggen på databasnivå är korrekt konfigurerad kan databasanvändare ansluta med klientverktyg som SQL Server Management Studio eller SQL Server Data Tools. Endast de senaste verktygen innehåller alla funktioner och möjligheter. I följande diagram visas en typisk åtkomstväg för en icke-administratör.
![Åtkomstväg för icke-administratör](./media/sql-database-manage-logins/2sql-db-nonadmin-access.png)

## <a name="groups-and-roles"></a>Grupper och roller
Effektiv åtkomsthantering använder behörigheter tilldelade grupper och roller i stället för enskilda användare. Till exempel vid användning av autentisering med Azure Active Directory:

* Placera Azure Active Directory-användare i en Azure Active Directory-grupp. Skapa en oberoende databasanvändare för gruppen. Placera en eller flera databasanvändare i en databasroll. Och tilldela därefter behörigheter till databasrollen.

Vid användning av SQL Server-autentisering:

* Skapa oberoende databasanvändare i databasen. Placera en eller flera databasanvändare i en databasroll. Och tilldela därefter behörigheter till databasrollen.

Databasrollerna kan vara de inbyggda rollerna, som **db_owner**, **db_ddladmin**, **db_datawriter**, **db_datareader**, **db_denydatawriter** och **db_denydatareader**. **db_owner** används ofta för att endast ge fullständig behörighet till några användare. De andra fasta databasrollerna är användbara för att snabbt få en enkel databas i utveckling, men de rekommenderas inte för de flesta produktionsdatabaserna. Till exempel ger den fasta databasrollen **db_datareader** läsbehörighet till alla tabeller i databasen, vilket vanligtvis är mer än är absolut nödvändigt. Det är mycket bättre att använda instruktionen [SKAPA ROLL](https://msdn.microsoft.com/library/ms187936.aspx) för att skapa dina egna anpassade databasroller och noggrant bevilja varje roll minsta möjliga behörighet som krävs för företagets behov. När en användare är medlem i flera roller sammanställs behörigheterna för alla.

## <a name="permissions"></a>Behörigheter
Det finns över 100 behörigheter som individuellt kan beviljas eller nekas i SQL Database. Många av de här behörigheterna är kapslade. Till exempel inkluderar `UPDATE`-behörighet på ett schema `UPDATE`-behörighet för alla tabeller i schemat. Som i de flesta andra behörighetssystem åsidosätter ett nekande av en behörighet en beviljad. På grund av den kapslade karaktären och antalet behörigheter kan det krävas noggranna studier för att designa ett behörighetssystem som korrekt skyddar databasen. Börja med listan över behörigheter på [Behörigheter (Databasmotor)](https://msdn.microsoft.com/library/ms191291.aspx) och granska den [stora bilden](http://go.microsoft.com/fwlink/?LinkId=229142) med behörigheter.

## <a name="next-steps"></a>Nästa steg
[Säkra din SQL Database](sql-database-security.md)

[Skapa en tabell \(Självstudie\)](https://msdn.microsoft.com/library/ms365315.aspx)

[Lägga till och uppdatera data i en tabell \(Självstudie\)](https://msdn.microsoft.com/library/ms365309.aspx)

[Läsa data i en tabell \(Självstudie\)](https://msdn.microsoft.com/library/ms365310.aspx)

[Skapa vyer och lagrade procedurer](https://msdn.microsoft.com/library/ms365311.aspx)

[Bevilja åtkomst till ett databasobjekt](https://msdn.microsoft.com/library/ms365327.aspx)

## <a name="additional-resources"></a>Ytterligare resurser
[Säkra din SQL Database](sql-database-security.md)

[Security Center för SQL Server Database Engine och Azure SQL Database](https://msdn.microsoft.com/library/bb510589.aspx) 




<!--HONumber=Dec16_HO3-->


