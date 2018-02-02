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
ms.openlocfilehash: b599273874a4b5a3bbcb78284d69b4c8d02b5f2c
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="enable-automatic-tuning"></a>Aktivera automatisk inställning

Azure SQL Database är en automatiskt hanterade datatjänst som ständigt övervakar dina frågor och anger vilka åtgärder som du kan utföra för att förbättra prestandan för din arbetsbelastning. Du kan granska rekommendationer och manuellt koppla dem, eller låta Azure SQL Database automatiskt tillämpa korrigerande åtgärder – den här funktionen kallas **automatiskt prestandajustering läge**. Automatisk justering kan aktiveras på servern eller databasnivå.

## <a name="enable-automatic-tuning-on-server"></a>Aktivera automatisk justering på servern
Du kan välja att ärva automatisk justering konfiguration från ”Azure standard” eller inte ärva konfigurationen på servernivå. Azure standardvärdena är FORCE_LAST_GOOD_PLAN aktiverad, CREATE_INDEX aktiverat och inaktiverat DROP_INDEX.

## <a name="configure-automiatic-tuning-e-mail-notifications"></a>Konfigurera automiatic justera e-postaviseringar

Se [automatisk justering e-postaviseringar](sql-database-automatic-tuning-email-notifications.md)

### <a name="azure-portal"></a>Azure Portal
Om du vill aktivera automatisk justering på Azure SQL Database-server, gå till servern i Azure-portalen och välj **automatisk justering** på menyn. Välj Automatisk justering alternativ som du vill aktivera och välja **Verkställ**:

![Server](./media/sql-database-automatic-tuning-enable/server.png)

Alternativ för automatisk justering på servern som tillämpas på alla databaser på servern. Som standard alla databaser ärver konfigurationen från sina överordnade servern, men detta kan åsidosättas och anges separat för varje databas.

### <a name="rest-api"></a>REST-API
[Klicka här om du vill läsa mer om hur du aktiverar automatisk justering på servernivå via REST API](https://docs.microsoft.com/rest/api/sql/serverautomatictuning)

## <a name="enable-automatic-tuning-on-an-individual-database"></a>Aktivera automatisk inställning på en individuell databas

Azure SQL-databasen kan du ange automatisk justering konfiguration individuellt på varje databas. Du kan välja att ärva automatisk justering konfiguration från överordnad server ”Azure standard” eller inte ärva konfigurationen på databasnivå. Azure standardvärdena är FORCE_LAST_GOOD_PLAN aktiverad, CREATE_INDEX aktiverat och inaktiverat DROP_INDEX.

> [!NOTE]
> Allmänt rekommenderar vi att hantera automatisk justering konfigurationen på servernivå så samma inställningar kan tillämpas på varje databas automatiskt. Konfigurera automatisk inställning på en individuell databas om databasen skiljer sig som andra på samma server.
>

### <a name="azure-portal"></a>Azure Portal

Om du vill aktivera automatisk inställning på en enskild databas, gå till databasen i Azure-portalen och sedan väljer **automatisk justering**. Du kan konfigurera en enskild databas om du vill ärva inställningar från servern genom att välja alternativet eller ange konfigurationen för en databas individuellt.

![Databas](./media/sql-database-automatic-tuning-enable/database.png)

När du har valt rätt konfiguration, klickar du på **tillämpa**.

### <a name="rest-api"></a>REST-API
[Klicka här om du vill läsa mer om hur du aktiverar automatisk inställning på en enskild databas via REST API](https://docs.microsoft.com/rest/api/sql/databaseautomatictuning)

### <a name="t-sql"></a>T-SQL

Om du vill aktivera automatisk inställning på en enskild databas via T-SQL, ansluta till databasen och kör följande fråga:

   ```T-SQL
   ALTER DATABASE current SET AUTOMATIC_TUNING = AUTO | INHERIT | CUSTOM
   ```
   
Ställa in automatisk justering Auto gäller Azure som standard. Ange värdet till ÄRV, ärvs automatisk justering konfiguration från den överordnade servern. Välja anpassade, behöver du manuellt konfigurera automatisk justering.

För att konfigurera enskilda alternativ för automatisk justering via T-SQL, ansluta till databasen och köra frågan som den här:

   ```T-SQL
   ALTER DATABASE current SET AUTOMATIC_TUNING (FORCE_LAST_GOOD_PLAN = ON, CREATE_INDEX = DEFAULT, DROP_INDEX = OFF)
   ```
   
Ange alternativet för enskilda prestandajustering on, åsidosätter inställningar som databasen ärvt och aktivera alternativet justering. Ange värdet till OFF, kommer också åsidosätter inställningar som databasen ärvt och inaktivera alternativet justering. Automatisk justering alternativet som standard anges ärver konfigurationen från automatisk justera inställningen databasnivå.  

## <a name="disabled-by-the-system"></a>Är inaktiverad av systemet
Automatisk justering övervakar de åtgärder som utförs på databasen och i vissa fall kan bestämma att automatisk justering inte fungerar korrekt på databasen. I den här situationen inaktiveras inställningsalternativ av systemet. I de flesta fall inträffar detta eftersom Query Store inte har aktiverats eller så är i skrivskyddat läge på en viss databas.

## <a name="next-steps"></a>Nästa steg
* Läs den [automatisk justering artikel](sql-database-automatic-tuning.md) lära dig mer om automatisk justering och hur det kan hjälpa dig att förbättra prestandan.
* Se [rekommendationer](sql-database-advisor.md) en översikt över Azure SQL Database prestanda rekommendationer.
* Se [insikter i frågeprestanda](sql-database-query-performance.md) mer information om hur du visar påverkan på prestandan för din de vanligaste frågorna.
