---
title: "Aktivera automatisk justering för Azure SQL Database | Microsoft Docs"
description: "Du kan aktivera automatisk justering på Azure SQL Database enkelt."
services: sql-database
documentationcenter: 
author: veljko-msft
manager: drasumic
editor: vvasic
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Inactive
ms.date: 09/19/2016
ms.author: veljko-msft
ms.openlocfilehash: 82db5996c1ba1f224593e4eaa5b3b0067755db49
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="enable-automatic-tuning"></a>Aktivera automatisk inställning

Azure SQL Database är en automatiskt hanterade datatjänst som ständigt övervakar dina frågor och anger vilka åtgärder som du kan utföra för att förbättra prestandan för din arbetsbelastning. Du kan granska rekommendationer och manuellt koppla dem, eller låta Azure SQL Database automatiskt tillämpa korrigerande åtgärder – den här funktionen kallas **automatiskt prestandajustering läge**. Automatisk justering kan aktiveras på servern eller databasnivå.

## <a name="enable-automatic-tuning-on-server"></a>Aktivera automatisk justering på servern

Om du vill aktivera automatisk justering på Azure SQL Database-server, gå till servern i Azure-portalen och välj **automatisk justering** på menyn. Välj Automatisk justering alternativ som du vill aktivera och välja **Verkställ**:

![Server](./media/sql-database-automatic-tuning-enable/server.png)

Alternativ för automatisk justering på servern som tillämpas på alla databaser på servern. Som standard alla databaser ärver konfigurationen från sina överordnade servern, men detta kan åsidosättas och anges separat för varje databas.

## <a name="configure-automatic-tuning-on-database"></a>Konfigurera automatisk inställning för databasen

Azure-portalen kan du ange automatisk justering konfiguration individuellt på varje databas.

> [!NOTE]
> Allmänt rekommenderar vi att hantera automatisk justering konfigurationen på servernivå så samma inställningar kan tillämpas på varje databas automatiskt. Konfigurera automatisk inställning på en individuell databas om databasen skiljer sig som andra på samma server.
>

Om du vill aktivera automatisk inställning på en enskild databas, gå till databasen i Azure-portalen och sedan väljer **automatisk justering**. Du kan konfigurera en enskild databas om du vill ärva inställningar från servern genom att välja alternativet eller ange konfigurationen för en databas individuellt.

![Databas](./media/sql-database-automatic-tuning-enable/database.png)

När du har valt rätt konfiguration, klickar du på **tillämpa**.

## <a name="next-steps"></a>Nästa steg
* Läs den [automatisk justering artikel](sql-database-automatic-tuning.md) lära dig mer om automatisk justering och hur det kan hjälpa dig att förbättra prestandan.
* Se [rekommendationer](sql-database-advisor.md) en översikt över Azure SQL Database prestanda rekommendationer.
* Se [insikter i frågeprestanda](sql-database-query-performance.md) mer information om hur du visar påverkan på prestandan för din de vanligaste frågorna.
