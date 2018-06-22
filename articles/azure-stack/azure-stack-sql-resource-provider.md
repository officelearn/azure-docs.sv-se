---
title: Med hjälp av SQL-databaser på Azure-stacken | Microsoft Docs
description: Lär dig hur du kan distribuera SQL-databaser som en tjänst på Azure-stacken och snabba steg för att distribuera SQL Server resource provider nätverkskort.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 55d0e51606e8768a01c0b5a7766dbafe24d97a0d
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2018
ms.locfileid: "36307833"
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>Använda SQL-databaser på Microsoft Azure-stacken

Använda SQL Server resource provider kort API för att exponera SQL-databaser som en tjänst av [Azure Stack](azure-stack-poc.md). När du installerar resursprovidern och ansluta till en eller flera instanser av SQL Server kan du och användarna skapa:

- Databaser för moln-ursprungliga appar.
- Webbplatser som använder SQL.
- Arbetsbelastningar som använder SQL.

Resursprovidern ger inte de funktioner för hantering av [Azure SQL Database](https://azure.microsoft.com/services/sql-database/). Till exempel stöds elastiska pooler som automatiskt allokerar resurser inte. Dock resource provider stöder liknande skapa, läsa, uppdatera och ta bort CRUD-åtgärder på en SQL Server-databas. Mer information om resursprovidern API finns [Windows Azure Pack SQL Server Resource Provider REST API-referens](https://msdn.microsoft.com/library/dn528529.aspx).

>[!NOTE]
SQL Server-resursprovidern API är inte kompatibelt med Azure SQL Database.

## <a name="sql-resource-provider-adapter-architecture"></a>Adapter-arkitektur för SQL-provider

Resursprovidern består av följande komponenter:

- **SQL resource provider kortet virtuell dator (VM)**, vilket är en Windows virtuell dator som kör provider-tjänster.
- **Resursprovidern**, som bearbetar begäranden och ansluter till databasen resurser.
- **Servrar som värdar för SQL Server**, som ger kapacitet för databaser kallas värdservrar.

Du måste skapa minst en instans av SQL Server eller ge åtkomst till externa SQL Server-instanser.

> [!NOTE]
> Värd för servrar som är installerade på Azure-stacken måste integrerade system skapas från en klientprenumeration. De kan inte skapas från standard providern prenumerationen. De måste skapas från klientportalen eller med hjälp av PowerShell med lämpliga logga in. Alla värdservrar är fakturerbar virtuella datorer och måste ha licenser. Tjänstadministratören kan vara klient Prenumerationens ägare.

## <a name="next-steps"></a>Nästa steg

[Distribuera SQL Server-resursprovidern](azure-stack-sql-resource-provider-deploy.md)
