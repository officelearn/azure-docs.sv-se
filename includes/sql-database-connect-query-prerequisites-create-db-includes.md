---
author: MightyPen
ms.service: sql-database
ms.topic: include
ms.date: 01/28/2019
ms.author: genemi
ms.openlocfilehash: edeaa932abe4d1882f957d0ed26aaddd97dabe3f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55198069"
---
<!-- sql-database-connect-query-prerequisites-create-db-includes.md -->

- En Azure SQL Database på en [logisk server](https://docs.microsoft.com/azure/sql-database/sql-database-single-index) eller [hanterad instans](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index). Du kan använda någon av följande metoder för att skapa en databas:

| Logisk server | Managed Instance |
| --- | --- |
| [Portal](../articles/sql-database/sql-database-get-started-portal.md) | [Portal](../articles/sql-database/sql-database-managed-instance-get-started.md) |
| [CLI](../articles/sql-database/sql-database-get-started-cli.md) | |
| [PowerShell](../articles/sql-database/sql-database-get-started-powershell.md) | |

- **Endast logisk Server** – en konfigurerad brandväggsregel på servernivå som gör att du kan ansluta till din logiska server. Mer information finns i [Skapa brandväggsregel på servernivå](../articles/sql-database/sql-database-get-started-portal-firewall.md).
- **Endast hanterad instans** – konfigurerad anslutning från datorn som har åtkomst till den hanterade instansen. Du kan använda följande alternativ:
  - [Virtuell Azure-dator](../articles/sql-database/sql-database-managed-instance-configure-vm.md) i samma virtuella Azure-nätverk som den hanterade instansen som har åtkomst till instansen.
  - [Punkt-till-plats-anslutning](../articles/sql-database/sql-database-managed-instance-configure-p2s.md) på datorn som gör att du kan ansluta datorn till det virtuella nätverket där den hanterade instansen finns och använda den hanterade instansen på samma sätt som andra SQL-servrar i nätverket.
