---
title: Med hjälp av SQL-databaser på Azure Stack | Microsoft Docs
description: Lär dig hur du kan distribuera SQL-databaser som en tjänst på Azure Stack och snabba steg för att distribuera SQL Server-resursprovideradaptern.
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
ms.date: 10/25/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.openlocfilehash: 3d608843ef31a1ed665fcb1fd90b822f34f77fdd
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50086360"
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>Använda SQL-databaser på Microsoft Azure Stack

Använda SQL Server-resursprovideradaptern för att erbjuda SQL-databaser som en tjänst av [Azure Stack](azure-stack-poc.md). När du har installerat resursprovidern och ansluter den till en eller flera instanser av SQL Server kan du och dina användare skapa:

- Databaser för molnbaserade appar.
- Webbplatser som använder SQL.
- Arbetsbelastningar som använder SQL.

Resursprovidern inte tillhandahåller de funktioner för hantering av [Azure SQL Database](https://azure.microsoft.com/services/sql-database/). Till exempel stöds elastiska pooler som automatiskt tilldelar resurser inte. Dock resurs providern stöder liknande skapa, läsa, uppdatera och ta bort (CRUD)-åtgärder på en SQL Server-databas. 

## <a name="sql-resource-provider-adapter-architecture"></a>Adapter-arkitektur för SQL-provider

Resursprovidern består av följande komponenter:

- **SQL resource provider nätverkskort virtuell dator (VM)**, vilket är en Windows Server VM som körs i tjänsterna.
- **Resursprovidern**, som bearbetar begäranden och kommer åt databasresurser.
- **Servrar som är värdar för SQL Server**, vilket ger kapacitet för databaser kallas värdservrar.

Du måste skapa minst en instans av SQL Server eller ge åtkomst till externa SQL Server-instanser.

> [!NOTE]
> Som är värd för servrar som är installerade på Azure Stack måste integrerade system skapas från en klientprenumeration. De kan inte skapas från providern Standardprenumeration. De måste skapas från klientportalen eller med hjälp av PowerShell med lämpliga inloggningen. Alla värdservrar är fakturerbara virtuella datorer och måste ha licenser. Tjänstadministratören kan vara ägare till den klient-prenumerationen.

## <a name="next-steps"></a>Nästa steg

[Distribuera SQL Server-resursprovider](azure-stack-sql-resource-provider-deploy.md)
