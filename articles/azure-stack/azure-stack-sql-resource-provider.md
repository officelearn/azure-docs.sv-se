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
ms.date: 05/01/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: b1cc1fad6b0831bcf0bab5ba4f37b753c3cf33ca
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/04/2018
ms.locfileid: "33207073"
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>Använda SQL-databaser på Microsoft Azure-stacken
Använda SQL Server resource provider kort för att exponera SQL-databaser som en tjänst av [Azure Stack](azure-stack-poc.md). När du installerar resursprovidern och ansluta till en eller flera instanser av SQL Server kan du och användarna skapa:
- Databaser för moln-ursprungliga appar.
- Webbplatser som är baserade på SQL.
- Arbetsbelastningar som är baserade på SQL.
Du behöver inte etablera en virtuell dator (VM) som är värd för SQL Server varje gång.

Resursprovidern stöder inte alla databasen hanteringsfunktioner i [Azure SQL Database](https://azure.microsoft.com/services/sql-database/). Till exempel elastiska databaspooler och möjligheten att reglera databasprestanda uppåt och nedåt automatiskt inte tillgänglig. Dock resource provider har stöd liknande skapa, läsa, uppdatera och ta bort CRUD-åtgärder. API: et är inte kompatibel med SQL-databas.

## <a name="sql-resource-provider-adapter-architecture"></a>Adapter-arkitektur för SQL-provider
Resursprovidern består av tre komponenter:

- **SQL resource provider kortet VM**, vilket är en Windows-dator som kör provider-tjänster.
- **Resursprovidern själva**, som bearbetar begäranden om etablering och visar databasen resurser.
- **Servrar som värdar för SQL Server**, som ger kapacitet för databaser kallas värdservrar.

Du måste skapa en (eller flera) instanser av SQL Server och/eller ge åtkomst till externa SQL Server-instanser.

> [!NOTE]
> Värd för servrar som är installerade på Azure-stacken måste integrerade system skapas från en klientprenumeration. De kan inte skapas från standard providern prenumerationen. De måste skapas från klientportalen eller från en PowerShell-session med en lämplig inloggning. Alla värdservrar avgiftsbelagda virtuella datorer och måste ha rätt licenser för. Tjänstadministratören kan vara klient Prenumerationens ägare.


## <a name="next-steps"></a>Nästa steg

[Distribuera SQL Server-resursprovidern](azure-stack-sql-resource-provider-deploy.md)
