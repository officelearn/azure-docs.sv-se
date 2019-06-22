---
title: Azure Cosmos-emulatorn nedladdning och viktig information
description: Läs viktig information Azure Cosmos-emulatorn och ladda ned den.
ms.service: cosmos-db
ms.topic: tutorial
author: markjbrown
ms.author: mjbrown
ms.date: 06/20/2019
ms.openlocfilehash: 5985d0d82341c76993ee91b8dff6927edd1ed8b4
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332121"
---
# <a name="use-the-azure-cosmos-emulator-for-local-development-and-testing"></a>Använda Azure Cosmos-emulatorn för lokal utveckling och testning

I den här artikeln visas i Azure Cosmos-emulatorn viktig information med en lista över funktionsuppdateringar som har gjorts i varje version. Här visas även den senaste versionen av emulatorn för att hämta och använda.

## <a name="download"></a>Ladda ned

| | |
|---------|---------|
|**Ladda ned MSI**|[Microsoft Download Center](https://aka.ms/cosmosdb-emulator)|
|**Kom igång**|[Utveckla lokalt med Azure Cosmos-emulatorn](local-emulator.md)|

## <a name="release-notes"></a>Viktig information

### <a name="243"></a>2.4.3

- Inaktiverad startar tjänsten MongoDB som standard. Endast SQL-slutpunkten är aktiverad som standard. Användaren måste starta slutpunkten för manuellt med hjälp av emulatorn ”/ EnableMongoDbEndpoint” kommandoradsalternativ. Nu är det som alla andra tjänstslutpunkter som Gremlin, Cassandra och tabellen.
- Ett fel har åtgärdats i emulatorn när du börjar med ”/ AllowNetworkAccess” där Gremlin, Cassandra och tabellen slutpunkter inte korrekt hantering av begäranden från externa klienter.
- Lägga till direktanslutning portar i inställningarna för brandväggsregler.

### <a name="240"></a>2.4.0

- Ett problem har åtgärdats med emulator misslyckas med att starta när nätverket övervakning appar, till exempel Pulse-klienten finns på värddatorn.
