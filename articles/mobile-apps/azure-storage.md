---
title: Moln lagring för att bygga mycket säkra, tåliga och skalbara appar med Azure Storage
description: Läs om tjänsterna för att lagra stora strukturerade och icke-strukturerade mobil program data i molnet.
author: codemillmatt
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 682f06936480d9018d759b4df3bed80be1206faf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80240979"
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

## <a name="azure-files"></a>Azure Files
Med [Azure Files](https://azure.microsoft.com/services/storage/files/)kan du konfigurera nätverks fil resurser med hög tillgänglighet som kan nås med hjälp av SMB-standardprotokollet (Server Message Block). Flera virtuella datorer kan dela samma filer med både Läs-och Skriv behörighet. Du kan också läsa filerna med hjälp av REST-gränssnittet eller lagrings klient biblioteken. Du kan komma åt filerna var som helst i världen med en URL som pekar på filen och som innehåller en SAS-token (signatur för delad åtkomst). Du kan generera SAS-token. De tillåter speciell åtkomst till en privat till gång under en angiven tids period.

Azure-filresurser kan användas för att:
- **Ersätt eller komplettera lokala fil servrar:** Populära operativ system, till exempel Windows, macOS och Linux kan direkt montera Azure-filresurser var de än befinner sig i världen. Azure-filresurser kan också replikeras med Azure-filsynkronisering till Windows-servrar, antingen lokalt eller i molnet, för högpresterande och distribuerad cachelagring av data där den används.
- **Lyft och Shift-program:** Migrera program till molnet som förväntar sig en fil resurs för lagring av fil program eller användar data.
- **Förenkla moln utveckling:** Azure Files kan också användas på flera olika sätt för att förenkla nya moln utvecklings projekt. Ett exempel:
    - **Inställningar för delade program:** Ett vanligt mönster för distribuerade program är att ha konfigurationsfiler på en central plats där de kan nås från många program instanser. Program instanser kan läsa in konfigurationen via filen REST API. Användare kan komma åt dem efter behov genom att montera SMB-resursen lokalt.
    - **Diagnostisk resurs:** En Azure-filresurs är en praktisk plats för moln program för att skriva loggar, mått och krasch dum par. Loggar kan skrivas av program instanserna via filen REST API. Utvecklare kan komma åt dem genom att montera fil resursen på den lokala datorn. Den här funktionen ger stor flexibilitet. Utvecklare kan använda moln utveckling utan att behöva överge de befintliga verktyg som de känner till.

**Referenser**
- [Azure Portal](https://portal.azure.com)
- [Dokumentation om Azure Files](/azure/storage/files/storage-files-introduction)
- [Snabbstarter](/azure/storage/files/storage-files-quick-create-use-windows)

## <a name="azure-queue-storage"></a>Azure Queue Storage
[Azure Queue Storage](https://azure.microsoft.com/services/storage/queues/) är en tjänst för att lagra ett stort antal meddelanden. Du kommer åt meddelanden från var som helst i världen via autentiserade anrop med HTTP eller HTTPS. Ett Queue-meddelande kan vara upp till 64 KB stort. En kö kan innehålla miljon tals meddelanden, upp till den totala kapacitets gränsen för ett lagrings konto. Köer används ofta för att skapa en efter släpning av arbete som ska bearbetas asynkront.

**Referenser**
- [Azure Portal](https://portal.azure.com)
- [Dokumentation om Azure Queue Storage](/azure/storage/queues/)
- [Snabbstarter](/azure/storage/queues/storage-quickstart-queues-portal)
- [Exempel](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
