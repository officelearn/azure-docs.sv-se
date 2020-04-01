---
title: Ladda ned och släppa ut anteckningar för Azure Cosmos Emulator
description: Hämta azure Cosmos-emulatorns viktig information för olika versioner och hämta information.
ms.service: cosmos-db
ms.topic: tutorial
author: milismsft
ms.author: adrianmi
ms.date: 06/20/2019
ms.openlocfilehash: 4dffe169908d0dd3effa4e46140b5f6696805a3e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77168652"
---
# <a name="azure-cosmos-emulator---release-notes-and-download-information"></a>Azure Cosmos Emulator - Viktig information och hämta information

Den här artikeln visar Azure Cosmos emulator viktig information med en lista över funktionsuppdateringar som gjordes i varje utgåva. Den listar också den senaste versionen av emulatorn att ladda ner och använda.

## <a name="download"></a>Ladda ned

| | |
|---------|---------|
|**MSI ladda ner**|[Microsoft Download Center](https://aka.ms/cosmosdb-emulator)|
|**Kom igång**|[Utveckla lokalt med Azure Cosmos emulator](local-emulator.md)|

## <a name="release-notes"></a>Viktig information

### <a name="291"></a>2.9.1

- Den här versionen åtgärdar parproblem i fråge-API-stöd och återställer kompatibilitet med äldre OS:er som Windows Server 2012.

### <a name="290"></a>2.9.0

- Den här versionen lägger till alternativet för att ange konsekvensen till konsekventprefix och öka maxgränserna för användare och behörigheter.

### <a name="272"></a>2.7.2

- Den här versionen lägger MongoDB version 3.6 server stöd till Cosmos Emulator. Om du vill starta en MongoDB-slutpunkt som målversion 3.6 av tjänsten startar du emulatorn från en administratörskommandorad med alternativet "/EnableMongoDBEndpoint=3.6".

### <a name="270"></a>2.7.0

- Den här versionen åtgärdar en regression som hindrade användare från att köra frågor mot SQL API-kontot från emulatorn när de använder .NET-kärna eller x86 .NET-baserade klienter.

### <a name="246"></a>2.4.6

- Den här versionen ger paritet med funktionerna i Azure Cosmos-tjänsten från och med juli 2019, med undantagen som anges i [Utveckla lokalt med Azure Cosmos emulator](local-emulator.md). Det fixar också flera buggar relaterade till emulator avstängning när anropas via kommandoraden och interna IP-adress åsidosättningar för SDK-klienter med direkt läge anslutning.

### <a name="243"></a>2.4.3

- Inaktiverad start av MongoDB-tjänsten som standard. Endast SQL-slutpunkten är aktiverad som standard. Användaren måste starta slutpunkten manuellt med hjälp av emulatorns kommandoradsalternativ "/EnableMongoDbEndpoint". Nu är det som alla andra tjänstslutpunkter, till exempel Gremlin, Cassandra och Table.
- Fixade ett fel i emulatorn när du började med "/AllowNetworkAccess" där Gremlin-, Cassandra- och Table-slutpunkterna inte hanterade begäranden korrekt från externa klienter.
- Lägg till direktanslutningsportar i inställningarna för brandväggsregler.

### <a name="240"></a>2.4.0

- Åtgärdade ett problem med att emulatorn inte startar när nätverksövervakningsappar, till exempel Pulsklient, finns på värddatorn.
