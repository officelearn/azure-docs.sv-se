---
title: 'Kända problem: Migrera från MongoDB till Azure CosmosDB'
titleSuffix: Azure Database Migration Service
description: Lär dig mer om kända problem och migreringsbegränsningar med migreringar från MongoDB till Azure Cosmos DB med hjälp av Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/27/2020
ms.openlocfilehash: ae5d5f2d282c546f5172ca1c8cb0e420d3b6e96b
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878059"
---
# <a name="known-issuesmigration-limitations-with-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>Kända problem/migreringsbegränsningar med migreringar från MongoDB till Azure Cosmos DB:s API för MongoDB

Kända problem och begränsningar som är associerade med migreringar från MongoDB till Cosmos DB:s API för MongoDB beskrivs i följande avsnitt.

## <a name="migration-fails-as-a-result-of-using-the-incorrect-ssl-cert"></a>Migreringen misslyckas på grund av att fel SSL-certifikat används

* **Symptom**: Det här problemet är uppenbart när en användare inte kan ansluta till MongoDB-källservern. Trots att alla brandväggsportar är öppna kan användaren fortfarande inte ansluta.

| Orsak         | Lösning |
| ------------- | ------------- |
| Om du använder ett självsignerat certifikat i Azure Database Migration Service kan det leda till att migreringen misslyckas på grund av det felaktiga SSL-certifikatet. Felmeddelandet kan innehålla "Fjärrcertifikatet är ogiltigt enligt valideringsproceduren."The Error message may include "Fjärrcertifikatet är ogiltigt enligt valideringsproceduren."The Error message may include "Fjärrcertifikatet är ogiltigt enligt valideringsproceduren." | Använd ett äkta certifikat från certifikatutfärdaren.  Självsignerade cert används i allmänhet endast i interna tester. När du installerar ett äkta certifikat från en certifikatutfärdare kan du sedan använda SSL i Azure Database Migration Service utan problem (anslutningar till Cosmos DB använder SSL över Mongo API).<br><br> |

## <a name="unable-to-get-the-list-of-databases-to-map-in-dms"></a>Det går inte att hämta listan över databaser att mappa i DMS

* **Symptom:** Det går inte att hämta DB-listan på **databasinställningsbladet** när data **från Azure Storage-läge** på **bladet Välj källa.**

| Orsak         | Lösning |
| ------------- | ------------- |
| Anslutningssträngen för lagringskonto saknas SAS-informationen och kan därför inte autentiseras. | Skapa SAS på blob-behållaren i Storage Explorer och använd URL:en med sas-information för behållare som anslutningssträng för källinformation.<br><br> |

## <a name="using-an-unsupported-version-of-the-database"></a>Använda en version av databasen som inte stöds

* **Symptom**: Migreringen misslyckas.

| Orsak         | Lösning |
| ------------- | ------------- |
| Du försöker migrera till Azure Cosmos DB från en version av MongoDB som inte stöds. | När nya versioner av MongoDB släpps testas de för att säkerställa kompatibilitet med Azure Database Migration Service och tjänsten uppdateras regelbundet för att acceptera den senaste versionen(er). Om det finns ett omedelbart behov av att migrera kan du som en lösning exportera databaser/samlingar till Azure Storage och sedan peka källan till den resulterande dumpen. Skapa SAS på blob-behållaren i Storage Explorer och använd sedan URL:en med sas-information för behållare som anslutningssträng för källinformation.<br><br> |

## <a name="next-steps"></a>Nästa steg

* Visa självstudien [Migrera MongoDB till Azure Cosmos DB:s API för MongoDB online med DMS](tutorial-mongodb-cosmos-db-online.md).
* Visa självstudien [Migrera MongoDB till Azure Cosmos DB:s API för MongoDB offline med DMS](tutorial-mongodb-cosmos-db.md).