---
title: Avancerat skydd för Azure Cosmos DB
description: Lär dig hur Azure Cosmos DB tillhandahåller kryptering av data i vila och hur de implementeras.
author: monhaber
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: v-mohabe
ms.custom: seodec18
ms.openlocfilehash: 0504da45cbbd60629954d3e3ca3230e05761c1d6
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640358"
---
# <a name="advanced-threat-protection-for-azure-cosmos-db"></a>Avancerat skydd för Azure Cosmos DB

Avancerat skydd för Azure Cosmos DB ger ytterligare ett lager med säkerhets information som identifierar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja Azure Cosmos DB-konton. Det här skydds lagret gör att du kan åtgärda hot, även utan att vara säkerhets expert, och integrera dem med centrala säkerhets övervaknings system.

Säkerhets varningar utlöses när avvikelser i aktivitet inträffar. Dessa säkerhets aviseringar är integrerade med  [Azure Security Center](https://azure.microsoft.com/services/security-center/)och skickas också via e-post till prenumerations administratörer med information om den misstänkta aktiviteten och rekommendationer om hur du undersöker och åtgärdar hoten.

> [!NOTE]
>
> * Avancerat skydd för Azure Cosmos DB är för närvarande endast tillgängligt för SQL-API: et.
> * Avancerat skydd för Azure Cosmos DB är för närvarande inte tillgängligt i moln regioner i Azure myndigheter och i vår suveräna region.

För en fullständig utredning av säkerhets aviseringar rekommenderar vi att du aktiverar [diagnostikloggning i Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/logging), som loggar åtgärder på själva databasen, inklusive CRUD åtgärder på alla dokument, behållare och databaser.

## <a name="set-up-advanced-threat-protection"></a>Konfigurera Avancerat skydd

### <a name="set-up-atp-using-the-portal"></a>Konfigurera ATP med portalen

1. Starta Azure Portal på  [https://portal.azure.com](https://portal.azure.com/).

2. Från Azure Cosmos DB-kontot går du till menyn **Inställningar** och väljer **avancerad säkerhet**.

    ![Konfigurera ATP](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp.png)

3. På bladet **Avancerad säkerhets** konfiguration:

    * Klicka på alternativet **Avancerat skydd** för att ställa in det på **på**.
    * Klicka på **Spara** för att spara den nya eller uppdaterade Advanced Threat Protection-principen.   

### <a name="set-up-atp-using-rest-api"></a>Konfigurera ATP med REST API

Använd REST API-kommandon för att skapa, uppdatera eller Hämta inställningen för avancerat skydd för ett bestämt Azure Cosmos DB konto.

* [Avancerat skydd – skapa](https://go.microsoft.com/fwlink/?linkid=2099745)
* [Avancerat skydd – Hämta](https://go.microsoft.com/fwlink/?linkid=2099643)

### <a name="set-up-atp-using-azure-powershell"></a>Konfigurera ATP med Azure PowerShell

Använd följande PowerShell-cmdletar:

* [Aktivera avancerat skydd](https://go.microsoft.com/fwlink/?linkid=2099607&clcid=0x409)
* [Hämta Avancerat skydd](https://go.microsoft.com/fwlink/?linkid=2099608&clcid=0x409)
* [Inaktivera Avancerat skydd](https://go.microsoft.com/fwlink/?linkid=2099709&clcid=0x409)

## <a name="manage-atp-security-alerts"></a>Hantera säkerhets aviseringar för ATP

När Azure Cosmos DB aktivitets avvikelser uppstår utlöses en säkerhets avisering med information om den misstänkta säkerhets händelsen. 

 Från Azure Security Center kan du granska och hantera dina aktuella [säkerhets aviseringar](../security-center/security-center-alerts-overview.md).  Klicka på en avisering i [Security Center](https://ms.portal.azure.com/#blade/Microsoft_Azure_Security/SecurityMenuBlade/0) om du vill visa möjliga orsaker och rekommenderade åtgärder för att undersöka och minimera det potentiella hotet. Följande bild visar ett exempel på aviserings information som finns i Security Center.

 ![Hotinformation](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert-details.png)

Ett e-postmeddelande skickas också med aviserings information och rekommenderade åtgärder. Följande bild visar ett exempel på ett e-postmeddelande om aviseringar.

 ![Aviseringsinformation](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert.png)

## <a name="cosmos-db-atp-alerts"></a>Cosmos DB ATP-aviseringar

 Om du vill se en lista över de aviseringar som genererats när du övervakar Azure Cosmos DB konton, se avsnittet [Cosmos DB aviseringar](../security-center/security-center-alerts-data-services.md#cosmos-db) i Security Center-dokumentationen.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [diagnostisk loggning i Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/logging#turn-on-logging-in-the-azure-portal)
* Läs mer om [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)