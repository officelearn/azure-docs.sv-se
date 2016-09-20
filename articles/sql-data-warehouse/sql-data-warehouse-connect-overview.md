<properties
   pageTitle="Anslut till Azure SQL Data Warehouse | Microsoft Azure"
   description="Anslutningsöversikt för anslutning till Azure SQL Data Warehouse"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/27/2016"
   ms.author="sonyama;barbkess"/>

# Anslut till Azure SQL Data Warehouse

> [AZURE.SELECTOR]
- [Översikt](sql-data-warehouse-connect-overview.md)
- [Autentisering](sql-data-warehouse-authentication.md)
- [Drivrutiner](sql-data-warehouse-connection-strings.md)

Översikt för anslutning till Azure SQL Data Warehouse. 

## Identifiera anslutningssträng från portalen

Din SQL Data Warehouse är kopplad till en Azure SQL Server. För att ansluta, behöver du det fullständigt kvalificerade namnet på servern.  Till exempel **myserver**.database.windows.net.

För att hitta det fullständigt kvalificerade servernamnet:

1. Gå till [Azure-portalen][].
2. Klicka på **SQL-databaser** och klicka på den databas du vill ansluta till. Det här exemplet använder exempeldatabasen AdventureWorksDW.
3. Leta upp det fullständiga servernamnet.

    ![Fullständigt servernamn][1]

## Inställningar för anslutning

SQL Data Warehouse standardiserar några inställningar under anslutning och objektskapande. Dessa kan inte åsidosättas.

| Databasinställning       | Värde                        |
| :--------------------- | :--------------------------- |
| [ANSI_NULLS][]         | ON                           |
| [QUOTED_IDENTIFIERS][] | ON                           |
| [DATEFORMAT][]         | mdy                          |
| [DATEFIRST][]          | 7                            |

## Övervaka anslutningar och frågor

När en anslutning har upprättats och en session har etablerats, är du redo att skriva och skicka frågor till SQL Data Warehouse.  Information om hur du övervakar sessioner och frågor finns i [Övervaka din arbetsbelastning med DMV:er][].

## Nästa steg

Om du vill börja ställa frågor till datalagret med Visual Studio och andra program hittar du mer information i [Fråga med Visual Studio][]. 

<!--Articles-->
[Fråga med Visual Studio]: ./sql-data-warehouse-query-visual-studio.md
[Övervaka din arbetsbelastning med DMV:er]: ./sql-data-warehouse-manage-monitor.md

<!--MSDN references-->
[ANSI_NULLS]: https://msdn.microsoft.com/library/ms188048.aspx
[QUOTED_IDENTIFIERS]: https://msdn.microsoft.com/library/ms174393.aspx
[DATEFORMAT]: https://msdn.microsoft.com/library/ms189491.aspx
[DATEFIRST]: https://msdn.microsoft.com/library/ms181598.aspx

<!--Other-->
[Azure-portalen]: https://portal.azure.com

<!--Image references-->
[1]: media/sql-data-warehouse-connect-overview/get-server-name.png





<!--HONumber=sep16_HO1-->


