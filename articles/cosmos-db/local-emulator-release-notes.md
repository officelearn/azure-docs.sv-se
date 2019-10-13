---
title: Hämtnings-och viktig information för Azure Cosmos-emulatorn
description: Läs viktig information om Azure Cosmos-emulatorn och hämta den.
ms.service: cosmos-db
ms.topic: tutorial
author: markjbrown
ms.author: mjbrown
ms.date: 06/20/2019
ms.openlocfilehash: cda3b0db1a4f1b098d8ea57887a047fcdef04243
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286503"
---
# <a name="azure-cosmos-emulator---release-notes-and-download-information"></a>Azure Cosmos-emulator – viktig information och information om hämtning

Den här artikeln visar viktig information om Azure Cosmos-emulatorn med en lista över funktions uppdateringar som har gjorts i varje version. Den visar även den senaste versionen av emulatorn för att hämta och använda.

## <a name="download"></a>Ladda ned

| | |
|---------|---------|
|**Ladda ned MSI**|[Microsoft Download Center](https://aka.ms/cosmosdb-emulator)|
|**Komma igång**|[Utveckla lokalt med Azure Cosmos-emulatorn](local-emulator.md)|

## <a name="release-notes"></a>Viktig information

### <a name="270"></a>2.7.0

- Den här versionen åtgärdar en regression som hindrar användare från att köra frågor mot SQL API-kontot från emulatorn vid användning av .NET Core-eller x86 .NET-baserade klienter.

### <a name="246"></a>2.4.6

- Den här versionen innehåller paritet med funktionerna i Azure Cosmos-tjänsten från och med den 2019 juli, med undantag som anges i [utveckla lokalt med Azure Cosmos-emulatorn](local-emulator.md). Den åtgärdar också flera buggar som är relaterade till emulatorn stängs av när de anropas via kommando raden och interna IP-adresser åsidosätter för SDK-klienter som använder anslutning med direkt läge.

### <a name="243"></a>2.4.3

- Inaktive rad startar MongoDB-tjänsten som standard. Endast SQL-slutpunkten är aktive rad som standard. Användaren måste starta slut punkten manuellt med hjälp av kommando rads alternativet "/EnableMongoDbEndpoint" i emulatorn. Nu är det som alla andra tjänst slut punkter, till exempel Gremlin, Cassandra och Table.
- En bugg har åtgärd ATS i emulatorn vid start med "/AllowNetworkAccess" där Gremlin, Cassandra och table-slutpunkterna inte korrekt hanterade begär Anden från externa klienter.
- Lägg till direkta anslutnings portar i brand Väggs reglernas inställningar.

### <a name="240"></a>2.4.0

- Ett problem har åtgärd ATS med att emulatorn inte kan starta när appar för nätverks övervakning, till exempel Pulse client, finns på värddatorn.
