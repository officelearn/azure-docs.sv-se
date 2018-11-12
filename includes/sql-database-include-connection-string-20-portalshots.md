---
title: Hämta anslutningssträngen från Azure-portalen
description: Hämta anslutningssträngen från Azure-portalen
keywords: SQL-anslutning, anslutningssträng
services: sql-database
author: dalechen
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: include
ms.date: 07/13/2018
ms.author: ninarn
ms.openlocfilehash: 6ead2e0ea326b5c3f2e76e7aa9cc4ab3c50d4154
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51262802"
---
### <a name="obtain-the-connection-string-from-the-azure-portal"></a>Hämta anslutningssträngen från Azure-portalen
Använd den [Azure-portalen](https://portal.azure.com/) att hämta anslutningssträngen som krävs för ditt klientprogram att interagera med Azure SQL Database.

1. Välj **alla tjänster** > **SQL-databaser**.

2. Ange namnet på din databas i rutan för textfiltrering nära det övre vänstra hörnet av den **SQL-databaser** bladet.

3. Välj raden för databasen.

4. När bladet visas för din databas för visual bekvämlighet väljer den **minimera** knappar för att dölja blad som du använde för att bläddra och filtrera i databasen.

5. På bladet för din databas, väljer **visa databasanslutningssträngar**.

6. Kopiera rätt anslutningssträng. dvs. Om du tänker använda anslutningsbibliotek ADO.NET kopierar du lämpliga strängen från den **ADO.NET** fliken.

    ![Kopiera ADO anslutningssträngen för databasen][20-CopyAdoConnectionString]

7. Redigera anslutningssträngen efter behov. dvs. Infoga ditt lösenord i anslutningssträngen eller ta bort ”@&lt;servername&gt;” från användarnamnet om användarnamn eller servernamn är för långt.

8. Klistra in informationen i anslutningssträngen i klientkoden för programmet i ett format eller någon annan.

Mer information finns i [anslutningssträngar och konfigurationsfiler](https://msdn.microsoft.com/library/ms254494.aspx).

<!-- Image references. -->



[20-CopyAdoConnectionString]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-b.png


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->
