---
title: "Azure Portal: Azure SQL Database-brandväggsregler på servernivå | Microsoft Docs"
description: "Lär dig hur du konfigurerar brandväggsregler på servernivå för IP-adresser som har åtkomst till Azure SQL-server med hjälp av Azure-portalen."
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
ms.assetid: c3b206b5-af6e-41af-8306-db12ecfc1b5d
ms.service: sql-database
ms.custom: authentication and authorization
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 11/28/2016
ms.author: rickbyh;carlrab
translationtype: Human Translation
ms.sourcegitcommit: 86bc7d89bb5725add8ba05b6f0978467147fd3ca
ms.openlocfilehash: a866b757c9c6946cdff4fe636e81bedbdfa29322


---
# <a name="create-and-manage-azure-sql-database-server-level-firewall-rules-using-the-azure-portal"></a>Skapa och hantera Azure SQL Database-brandväggsregler på servernivå på Azure Portal
> [!div class="op_single_selector"]
> * [Översikt](sql-database-firewall-configure.md)
> * [Azure Portal](sql-database-configure-firewall-settings.md)
> * [TSQL](sql-database-configure-firewall-settings-tsql.md)
> * [PowerShell](sql-database-configure-firewall-settings-powershell.md)
> * [REST-API](sql-database-configure-firewall-settings-rest.md)
> 

Brandväggsregler på servernivå gör att administratörer kan få åtkomst till en SQL Database-server från en angiven IP-adress eller ett angivet IP-adressintervall. Du kan också använda brandväggsregler på servernivå för användare om du har många databaser med samma åtkomstkrav och du inte vill lägga tid på att konfigurera varje databas individuellt. Microsoft rekommenderar att du använder brandväggsregler på databasnivå när det är möjligt för att förbättra säkerheten och göra databasen mer portabel. En översikt över SQL Database-brandväggar finns i [Översikt över SQL Database-brandväggsregler](sql-database-firewall-configure.md).

> [!Note]
> Information om portabla databaser i kontexten för företagskontinuitet finns i [Autentiseringskrav för haveriberedskap](sql-database-geo-replication-security-config.md).
>

[!INCLUDE [Create SQL Database firewall rule](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Hantera befintliga brandväggsregler på servernivå via Azure Portal
Upprepa stegen för att hantera brandväggsreglerna på servernivå.

* Klicka på Lägg till klient-IP för att lägga till den aktuella datorn.
* Om du vill lägga till ytterligare IP-adresser skriver du regelnamnet, start-IP-adressen och slut-IP-adressen.
* Om du vill redigera en befintlig regel klickar du på något av fälten i regeln och redigerar det.
* Om du vill ta bort en befintlig regel hovrar du över regeln tills X visas i slutet av raden. Klicka på X för att ta bort regeln.

Klicka på **Spara** för att spara ändringarna.

## <a name="next-steps"></a>Nästa steg

- Om du vill gå en introduktionskurs rekommenderar vi [Självstudiekurs om SQL Database: Skapa en server, en brandväggsregel på servernivå, en exempeldatabas, en brandväggsregel på databasnivå och ansluta till SQL Server](sql-database-get-started.md).
- Om du vill gå en introduktionskurs om säkerhet rekommenderar vi [Komma igång med säkerhet](sql-database-get-started-security.md)
- Hjälp med att ansluta till en Azure SQL-databas från öppen källkod eller program från tredje part finns i [Client quick-start code samples to SQL Database](https://msdn.microsoft.com/library/azure/ee336282.aspx) (Snabbstart med kodexempel för att ansluta klienter till SQL Database).
- Information om hur du skapar fler användare som kan ansluta till databaser finns i [SQL Database Authentication and Authorization: Granting Access](https://msdn.microsoft.com/library/azure/ee336235.aspx) (SQL Database-autentisering och SQL Database-auktorisering: bevilja åtkomst).

## <a name="additional-resources"></a>Ytterligare resurser
* [Skydda en databas](sql-database-security-overview.md)   
* [Security Center för SQL Server Database Engine och Azure SQL Database](https://msdn.microsoft.com/library/bb510589)   






<!--HONumber=Jan17_HO1-->


