---
title: Avancerat skydd mot azure cosmos-dator
description: Lär dig hur Azure Cosmos DB tillhandahåller kryptering av data i vila och hur den implementeras.
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: seodec18
ms.author: memildin
author: memildin
manager: rkarlin
ms.openlocfilehash: bcc1c6ffe7cdec4aed325a67969235ae993a5109
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77614833"
---
# <a name="advanced-threat-protection-for-azure-cosmos-db-preview"></a>Avancerat skydd mot hot för Azure Cosmos DB (förhandsversion)

Avancerat skydd mot hot för Azure Cosmos DB ger ytterligare ett lager av säkerhetsinformation som identifierar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja Azure Cosmos DB-konton. Med det här skyddsskiktet kan du hantera hot, även utan att vara säkerhetsexpert, och integrera dem med centrala säkerhetsövervakningssystem.

Säkerhetsaviseringar utlöses när avvikelser i aktivitet inträffar. Dessa säkerhetsaviseringar är integrerade med [Azure Security Center](https://azure.microsoft.com/services/security-center/)och skickas även via e-post till prenumerationsadministratörer, med information om den misstänkta aktiviteten och rekommendationer om hur du undersöker och åtgärdar hoten.

> [!NOTE]
>
> * Avancerat skydd mot azure cosmos-D2 är för närvarande endast tillgängligt för SQL API.
> * Avancerat skydd mot hot för Azure Cosmos DB är för närvarande inte tillgängligt i Azure-myndigheter och suveräna molnregioner.

För en fullständig undersökningsupplevelse av säkerhetsaviseringarna rekommenderar vi att du aktiverar [diagnostikloggning i Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/logging), som loggar åtgärder på själva databasen, inklusive CRUD-åtgärder på alla dokument, behållare och databaser.

## <a name="threat-types"></a>Hottyper

Avancerat skydd mot azure cosmos DB identifierar avvikande aktiviteter som indikerar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser. Det kan för närvarande utlösa följande aviseringar:

- **Åtkomst från ovanliga platser**: Den här aviseringen utlöses när åtkomstmönstret ändras till ett Azure Cosmos-konto, där någon har anslutit till Azure Cosmos DB-slutpunkten från en ovanlig geografisk plats. I vissa fall identifierar aviseringen en legitim åtgärd, vilket innebär ett nytt program eller en utvecklares underhållsåtgärd. I andra fall upptäcker aviseringen en skadlig åtgärd från en tidigare anställd, extern angripare, etc.

- **Ovanlig datautvinning:** Den här aviseringen utlöses när en klient extraherar en ovanlig mängd data från ett Azure Cosmos DB-konto. Detta kan vara symptomet på vissa dataexfiltration som utförs för att överföra alla data som lagras i kontot till ett externt datalager.

## <a name="set-up-advanced-threat-protection"></a>Konfigurera avancerat hotskydd

### <a name="set-up-atp-using-the-portal"></a>Konfigurera ATP med hjälp av portalen

1. Starta Azure-portalen på [https://portal.azure.com](https://portal.azure.com/).

2. Välj **Avancerad säkerhet**på Azure Cosmos DB-kontot på **menyn Inställningar** .

    ![Konfigurera ATP](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp.png)

3. I bladet **avancerad säkerhetskonfiguration:**

    * Klicka på alternativet **Avancerat skydd mot hot** om du vill ställa in det **på PÅ**.
    * Klicka på **Spara** för att spara den nya eller uppdaterade Advanced Threat Protection-principen.   

### <a name="set-up-atp-using-rest-api"></a>Konfigurera ATP med REST API

Använd Rest API-kommandon för att skapa, uppdatera eller hämta inställningen Avancerat skyddsskydd för ett specifikt Azure Cosmos DB-konto.

* [Avancerat skydd mot hot - Skapa](https://go.microsoft.com/fwlink/?linkid=2099745)
* [Avancerat skydd mot hot – hämta](https://go.microsoft.com/fwlink/?linkid=2099643)

### <a name="set-up-atp-using-azure-powershell"></a>Konfigurera ATP med Azure PowerShell

Använd följande PowerShell-cmdlets:

* [Aktivera Advanced Threat Protection](https://go.microsoft.com/fwlink/?linkid=2099607&clcid=0x409)
* [Få avancerat hotskydd](https://go.microsoft.com/fwlink/?linkid=2099608&clcid=0x409)
* [Inaktivera avancerat hotskydd](https://go.microsoft.com/fwlink/?linkid=2099709&clcid=0x409)

### <a name="using-azure-resource-manager-templates"></a>Använda Azure Resource Manager-mallar

Använd en Azure Resource Manager-mall för att konfigurera Cosmos DB med avancerat skydd mot hot.
Mer information finns i [Skapa ett CosmosDB-konto med avancerat skydd mot hot](https://azure.microsoft.com/resources/templates/201-cosmosdb-advanced-threat-protection-create-account/).

### <a name="using-azure-policy"></a>Använda Azure-princip

Använd en Azure-princip för att aktivera avancerat skydd mot cosmos DB.

1. Starta sidan Azure **Policy - Definitioner** och sök efter principen **Distribuera avancerat skydd mot kosmos.**

    ![Sökpolicy](./media/cosmos-db-advanced-threat-protection/cosmos-db.png) 

1. Klicka på principen **Distribuera avancerat skydd mot hot för CosmosDB** och klicka sedan på **Tilldela**.

    ![Välj prenumeration eller grupp](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-policy.png)


1. Klicka på de tre punkterna i fältet **Scope,** välj en Azure-prenumeration eller resursgrupp och klicka sedan på **Välj**.

    ![Sidan Principdefinitioner](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-details.png)


1. Ange de andra parametrarna och klicka på **Tilldela**.

## <a name="manage-atp-security-alerts"></a>Hantera ATP-säkerhetsaviseringar

När Azure Cosmos DB-aktivitetsavvikelser inträffar utlöses en säkerhetsavisering med information om den misstänkta säkerhetshändelsen. 

 Från Azure Security Center kan du granska och hantera dina aktuella [säkerhetsaviseringar](../security-center/security-center-alerts-overview.md).  Klicka på en specifik avisering i [Security Center](https://ms.portal.azure.com/#blade/Microsoft_Azure_Security/SecurityMenuBlade/0) för att visa möjliga orsaker och rekommenderade åtgärder för att undersöka och minska det potentiella hotet. Följande bild visar ett exempel på varningsinformation som finns i Security Center.

 ![Information om hot](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert-details.png)

Ett e-postmeddelande skickas också med varningsinformation och rekommenderade åtgärder. Följande bild visar ett exempel på ett e-postmeddelande.

 ![Aviseringsinformation](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert.png)

## <a name="cosmos-db-atp-alerts"></a>Cosmos DB ATP-varningar

 Information om hur du visar en lista över de aviseringar som genereras vid övervakning av Azure Cosmos DB-konton finns i avsnittet [Cosmos DB-aviseringar](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurecosmos) i Dokumentationen till Azure Security Center.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [diagnostikloggning i Azure Cosmos DB](cosmosdb-monitor-resource-logs.md)
* Läs mer om [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
