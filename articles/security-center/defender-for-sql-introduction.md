---
title: Azure Defender för SQL – fördelar och funktioner
description: Lär dig mer om fördelarna och funktionerna i Azure Defender för SQL.
author: memildin
ms.author: memildin
ms.date: 11/17/2020
ms.topic: overview
ms.service: security-center
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: b4f046339c027a5ae66f7f7510d8d2b311abd6df
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94693468"
---
# <a name="introduction-to-azure-defender-for-sql"></a>Introduktion till Azure Defender för SQL

Azure Defender för SQL innehåller två Azure Defender-planer som utökar Azure Security Centers [data säkerhets paket](../azure-sql/database/azure-defender-for-sql.md) för att skydda dina databaser och deras data oavsett var de befinner sig. 

## <a name="availability"></a>Tillgänglighet

|Aspekt|Information|
|----|:----|
|Versions tillstånd:|**Azure Defender för Azure SQL Database-servrar** – allmänt tillgängliga (ga)<br>**Azure Defender för SQL-servrar på datorer – för** hands version<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)] |
|Priset|De två planer som utgör **Azure Defender för SQL** debiteras enligt [pris sidan](security-center-pricing.md)|
|Skyddade SQL-versioner:|Azure SQL Database <br>Hanterad Azure SQL-instans<br>Dedikerad SQL-pool i Azure Synapse (tidigare SQL DW)<br>SQL Server (alla versioner som stöds)|
|Moln|![Ja](./media/icons/yes-icon.png) Kommersiella moln<br>![Ja](./media/icons/yes-icon.png) US Gov<br>![Nej](./media/icons/no-icon.png) Kina gov, andra gov|
|||

## <a name="what-does-azure-defender-for-sql-protect"></a>Vad skyddar Azure Defender för SQL?

**Azure Defender för SQL består av** två separata Azure Defender-planer:

- **Azure Defender för Azure SQL Database-servrar** skyddar:
  - [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md)
  - [Hanterad Azure SQL-instans](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)
  - [Dedikerad SQL-pool i Azure Synapse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md#dedicated-sql-pool-in-azure-synapse)

- **Azure Defender för SQL-servrar på datorer (för hands version)** utökar skydds inställningarna för dina Azure-inhemska SQL-servrar för att fullständigt stödja hybrid miljöer och skydda SQL-servrar (all version som stöds) som finns i Azure, andra moln miljöer och även lokala datorer


## <a name="what-are-the-benefits-of-azure-defender-for-sql"></a>Vilka är fördelarna med Azure Defender för SQL?

Dessa två planer innehåller funktioner för att identifiera och åtgärda potentiella databas sårbarheter och identifiera avvikande aktiviteter som kan tyda på hot mot dina databaser:

- [Sårbarhets bedömning](../azure-sql/database/sql-vulnerability-assessment.md) – genomsöknings tjänsten för att identifiera, spåra och hjälpa dig att åtgärda potentiella databas sårbarheter. Utvärderings genomsökningar ger en översikt över säkerhets läget för SQL-datorer och information om eventuella säkerhets brister.

- [Avancerat skydd](../azure-sql/database/threat-detection-overview.md) – identifierings tjänsten som kontinuerligt övervakar dina SQL-servrar för hot som SQL-inmatning, brute-force-attacker och behörighets missbruk. Den här tjänsten innehåller åtgärds lösa säkerhets aviseringar i Azure Security Center med information om den misstänkta aktiviteten, vägledning om hur du minimerar hoten och alternativ för att fortsätta med din undersökning med Azure Sentinel.


## <a name="what-kind-of-alerts-does-azure-defender-for-sql-provide"></a>Vilken typ av aviseringar finns i Azure Defender för SQL?

Säkerhets aviseringar utlöses när det finns:

- **Potentiella SQL-injektering-attacker** – inklusive sårbarheter som identifieras när program genererar en felaktig SQL-instruktion i databasen
- **Avvikande databas åtkomst och fråge mönster** – till exempel ett onormalt stort antal misslyckade inloggnings försök med andra autentiseringsuppgifter (ett brutet Force-försök)
- **Misstänkt databas aktivitet** – till exempel en ändring i export målet för en import-och export åtgärd i SQL

Aviseringar innehåller information om den incident som utlöste dem, samt rekommendationer om hur du undersöker och åtgärdar hot.



## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om Azure Defender för SQL.

Information om relaterade material finns i följande artiklar: 

- [Så här aktiverar du Azure Defender för SQL-servrar på datorer](defender-for-sql-usage.md)
- [Så här aktiverar du Azure Defender för SQL Database-servrar](../azure-sql/database/azure-defender-for-sql.md)
- [Listan med Azure Defender-aviseringar för SQL](alerts-reference.md#alerts-sql-db-and-warehouse)