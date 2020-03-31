---
title: Hämta anslutningssträngen från Azure-portalen
description: Hämta anslutningssträngen från Azure-portalen
keywords: sql-anslutning,anslutningssträng
services: sql-database
author: dalechen
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: include
ms.date: 07/13/2018
ms.author: ninarn
ms.openlocfilehash: 6ead2e0ea326b5c3f2e76e7aa9cc4ab3c50d4154
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67187423"
---
### <a name="obtain-the-connection-string-from-the-azure-portal"></a>Hämta anslutningssträngen från Azure-portalen
Använd [Azure-portalen](https://portal.azure.com/) för att hämta anslutningssträngen som krävs för att klientprogrammet ska kunna interagera med Azure SQL Database.

1. Välj **Alla tjänster** > **SQL-databaser**.

2. Ange namnet på databasen i filtertextrutan längst upp till vänster i **SQL-databasbladet.**

3. Markera raden för databasen.

4. När bladet visas för databasen väljer du knapparna **Minimera** för att dölja bladen som du använde för att bläddra och filtrera databasen.

5. På bladet för databasen väljer du **Visa databasanslutningssträngar**.

6. Kopiera lämplig anslutningssträng. dvs. Om du tänker använda ADO.NET anslutningsbiblioteket kopierar du lämplig sträng från **fliken ADO.NET.**

    ![Kopiera ADO-anslutningssträngen för databasen][20-CopyAdoConnectionString]

7. Redigera anslutningssträngen efter behov. dvs Infoga ditt lösenord i anslutningssträngen,&lt;eller&gt;ta bort "@ servername" från användarnamnet eller servernamnet är för lång.

8. I ett eller annat format klistrar du in information om anslutningssträngen i klientprogramkoden.

Mer information finns i [Anslutningssträngar och konfigurationsfiler](https://msdn.microsoft.com/library/ms254494.aspx).

<!-- Image references. -->



[20-CopyAdoConnectionString]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-b.png


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->
