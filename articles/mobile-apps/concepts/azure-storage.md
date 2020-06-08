---
title: Moln lagring för att bygga mycket säkra, tåliga och skalbara appar med Azure Storage
description: Läs om tjänsterna för att lagra stora strukturerade och icke-strukturerade mobil program data i molnet.
author: codemillmatt
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: mobile-services
ms.topic: article
ms.date: 06/05/2020
ms.author: masoucou
ms.openlocfilehash: e45726cfc54963c546274e0777dd0a10d89da72d
ms.sourcegitcommit: f57fa5f3ce40647eda93f8be4b0ab0726d479bca
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2020
ms.locfileid: "84483313"
---
# <a name="cloud-storage-for-highly-secure-durable-scalable-apps-with-azure-storage"></a>Moln lagring för mycket säkra, tåliga och skalbara appar med Azure Storage
[Azure Storage](https://azure.microsoft.com/services/storage/) är Microsofts moln lagrings lösning för moderna program som erbjuder en enorm skalbar objekt lagring för data objekt, en fil system tjänst för molnet, ett meddelande arkiv för Reliable Messaging och en NoSQL-butik. Azure Storage är:
- **Tålig och hög tillgänglighet:** Redundans garanterar att dina data är säkra i händelse av tillfälliga maskin varu fel. Du kan också välja att replikera data i datacenter eller geografiska regioner för ytterligare skydd mot lokala allvarliga händelser eller naturkatastrofer. Data som replikeras på det här sättet förblir mycket tillgängliga vid ett oväntat avbrott.
- **Säker:** Alla data som skrivs till Azure Storage krypteras av tjänsten. Med Azure Storage får du detaljerad kontroll över vem som har tillgång till dina data.
- **Skalbarhet:** Tjänsterna är utformade för att vara enorma skalbara så att de uppfyller dagens programs data lagrings-och prestanda behov.
- **Hanterade:** Azure hanterar maskin varu underhåll, uppdateringar och kritiska problem åt dig.
- **Tillgängligt:** Data är tillgängliga från var som helst i världen via HTTP eller HTTPS. Microsoft tillhandahåller klient bibliotek på flera olika språk, t. ex. .NET, Java, Node. js, python, PHP, ruby och go, och en vuxen REST API. Skript stöd finns i Azure PowerShell eller i Azure CLI. Azure Portal och Azure Storage Explorer erbjuder enkla visuella lösningar för att arbeta med dina data.

Använd följande tjänster för att aktivera moln lagring i dina mobila appar.

## <a name="azure-blob-storage"></a>Azure Blob Storage
[Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) erbjuder en objekt lagrings lösning för molnet. Blob Storage är optimerat för att lagra enorma mängder ostrukturerade data som inte följer en viss data modell eller definition, till exempel text eller binär. Det stöder flera olika språk som klient bibliotek använder. Blob Storage är utformat för att:
- Hantera bilder eller dokument direkt i en webbläsare.
- Lagra filer för distribuerad åtkomst.
- Strömma video och ljud.
- Skriv till loggfiler.
- Lagra data för säkerhets kopiering och återställning, haveri beredskap och arkivering.
- Lagra data för analys av en lokal eller Azure-värdbaserad tjänst.

**Referenser**
- [Azure Portal](https://portal.azure.com)
- [Dokumentation om Azure Blob Storage](/azure/storage/blobs/storage-blobs-introduction)
- [Snabbstarter](/azure/storage/blobs/storage-quickstart-blobs-portal)
- [Exempel](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

## <a name="azure-table-storage"></a>Azure Table Storage
[Azure Table Storage](https://azure.microsoft.com/services/storage/tables/) är en tjänst som lagrar strukturerade NoSQL-data i molnet och tillhandahåller ett nyckel-eller attributarkiv med en schema lös design. Azure Table Storage lagrar stora mängder strukturerade data. Tjänsten är ett NoSQL data lager som accepterar autentiserade anrop inifrån och utanför Azure-molnet. Azure-tabeller är idealiska för lagring av strukturerade, inte relationella data. Table Storage används vanligt vis för att:
- Lagra terabyte strukturerade data som kan betjäna program för webb skala.
- Lagra data uppsättningar som inte kräver komplexa kopplingar, externa nycklar eller lagrade procedurer och som kan denormaliseras för snabb åtkomst.
- Fråga data snabbt med hjälp av ett grupperat index.
- Få åtkomst till data med hjälp av OData-protokollet och LINQ-frågor med Windows Communication Foundation (WCF) Data Services .NET-bibliotek.

Du kan använda Table Storage för att lagra och ställa frågor till enorma mängder strukturerade, inrelationella data. Dina tabeller skalas när efter frågan ökar.

**Referenser**
- [Azure Portal](https://portal.azure.com)
- [Dokumentation om Azure Table Storage](/azure/storage/tables/table-storage-overview)
- [Exempel](/azure/cosmos-db/tutorial-develop-table-dotnet?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fstorage%2Ftables%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
- [Snabbstarter](/azure/storage/tables/table-storage-quickstart-portal)

## <a name="azure-queue-storage"></a>Azure Queue Storage
[Azure Queue Storage](https://azure.microsoft.com/services/storage/queues/) är en tjänst för att lagra ett stort antal meddelanden. Du kommer åt meddelanden från var som helst i världen via autentiserade anrop med HTTP eller HTTPS. Ett Queue-meddelande kan vara upp till 64 KB stort. En kö kan innehålla miljon tals meddelanden, upp till den totala kapacitets gränsen för ett lagrings konto. Köer används ofta för att skapa en efter släpning av arbete som ska bearbetas asynkront.

**Referenser**
- [Azure Portal](https://portal.azure.com)
- [Dokumentation om Azure Queue Storage](/azure/storage/queues/)
- [Snabbstarter](/azure/storage/queues/storage-quickstart-queues-portal)
- [Exempel](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
