---
title: Moln lagring för att bygga mycket säkra, tåliga och skalbara appar med Azure Storage
description: Läs om tjänsterna för att lagra stora strukturerade och icke-strukturerade mobil program data i molnet.
author: elamalani
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 01407f843ef36095fc87feb3722e85dc477ad8bb
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795658"
---
# <a name="cloud-storage-for-highly-secure-durable-scalable-apps-with-azure-storage"></a>Moln lagring för mycket säkra, tåliga och skalbara appar med Azure Storage
[Azure Storage](https://azure.microsoft.com/services/storage/) är Microsofts moln lagrings lösning för moderna program som erbjuder en enorm skalbar objekt lagring för data objekt, en fil system tjänst för molnet, ett meddelande arkiv för Reliable Messaging och en NoSQL-butik. Azure Storage är:
- **Beständig och hög tillgänglig** – redundans garanterar att dina data är säkra vid tillfälliga maskin varu fel. Du kan också välja att replikera data i datacenter eller geografiska regioner för ytterligare skydd mot lokala allvarliga händelser eller naturkatastrofer. Data som replikeras på det här sättet förblir mycket tillgängliga vid ett oväntat avbrott.
- **Säker** – alla data som skrivs till Azure Storage krypteras av tjänsten. Med Azure Storage får du detaljerad kontroll över vem som har tillgång till dina data.
- **Skalbara** tjänster är utformade för att vara enorma skalbara för att möta data lagring och prestanda behov för dagens program.
- **Managed** – Azure hanterar maskin varu underhåll, uppdateringar och kritiska problem åt dig.
- Data är **tillgängliga** från var som helst i världen via http eller https. Microsoft tillhandahåller klient bibliotek på flera olika språk, inklusive .NET, Java, Node. js, python, PHP, ruby, go, och en vuxen REST API. Skript stöd finns i Azure PowerShell eller Azure CLI och Azure Portal och Azure Storage Explorer erbjuder enkla visuella lösningar för att arbeta med dina data.

Använd följande tjänster för att aktivera moln lagring i dina mobila appar.

## <a name="azure-blob-storage"></a>Azure Blob Storage
[Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) erbjuder objekt lagrings lösning för molnet och är optimerad för att lagra enorma mängder ostrukturerade data som inte följer en viss data modell eller definition, till exempel text eller binär. Den har stöd för olika språk för klient bibliotek och är utformat för:
- Leverera bilder eller dokument direkt till en webbläsare.
- Lagra filer för distribuerad åtkomst.
- Direktuppspelning av video och ljud.
- Skriva till loggfiler.
- Lagra data för säkerhetskopiering och återställning, haveriberedskap och arkivering.
- Lagra data för analys av en tjänst som kan vara lokal eller Azure-baserad.

**Reference**
- [Azure-portalen](https://portal.azure.com)
- [Handlingar](/azure/storage/blobs/storage-blobs-introduction)
- [Snabbstarter](/azure/storage/blobs/storage-quickstart-blobs-portal)
- [Exempel](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

## <a name="azure-table-storage"></a>Azure Table Storage
[Azure Table Storage](https://azure.microsoft.com/services/storage/tables/) är en tjänst som lagrar strukturerade NoSQL-data i molnet, vilket ger ett nyckel-attributarkiv med en schema lös design. Azure Table Storage lagrar stora mängder strukturerade data. Tjänsten är ett NoSQL data lager som accepterar autentiserade anrop inifrån och utanför Azure-molnet. Azure-tabeller passar utmärkt för att lagra strukturerade, icke-relationella data. Vanliga användningsområden för Table Storage är:
- Lagring av TBs med strukturerade data som kan betjäna webb Skale program.
- Lagring av data uppsättningar som inte kräver komplexa kopplingar, externa nycklar eller lagrade procedurer och som kan denormaliseras för snabb åtkomst.
- Fråga data snabbt med hjälp av ett grupperat index.
- Åtkomst till data med hjälp av OData-protokollet och LINQ-frågor med WCF data service .NET-bibliotek.

Du kan använda Table Storage för att lagra och fråga stora mängder strukturerad, icke-relationell data och dina tabeller skalar upp efter behov.

**Reference**
- [Azure-portalen](https://portal.azure.com)
- [Handlingar](/azure/storage/tables/table-storage-overview)
- [Exempel](/azure/cosmos-db/tutorial-develop-table-dotnet?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fstorage%2Ftables%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
- [Snabbstarter](/azure/storage/tables/table-storage-quickstart-portal)

## <a name="azure-files"></a>Azure Files
Med [Azure Files](https://azure.microsoft.com/services/storage/files/) kan du konfigurera nätverksfilresurser med hög tillgänglighet som kan nås via SMB-standardprotokollet (Server Message Block). Flera virtuella datorer kan dela samma filer med både Läs-och Skriv behörighet. Du kan också läsa filerna med hjälp av REST-gränssnittet eller klientbiblioteken för lagring. Du kan komma åt filerna var som helst i världen med en URL som pekar på filen och som innehåller en SAS-token (signatur för delad åtkomst). Du kan generera SAS-token, som ger specifik åtkomst till en privat resurs under en viss tidsperiod.

Azure-filresurser kan användas för att:
- Ersätt eller komplettera lokala fil servrar: populära operativ system som Windows, macOS och Linux kan direkt montera Azure-filresurser oavsett var de befinner sig i världen. Azure-filresurser kan också replikeras med Azure-filsynkronisering till Windows-servrar, antingen lokalt eller i molnet, för högpresterande och distribuerad cachelagring av data där den används.
- **Lyft och växla program** till molnet som förväntar sig en fil resurs för lagring av fil program eller användar data.
- **Förenkla moln utveckling**: Azure Files kan också användas på flera olika sätt för att förenkla nya moln utvecklings projekt. Exempel:
    - Delade program inställningar: ett vanligt mönster för distribuerade program är att ha konfigurationsfiler på en central plats där de kan nås från många program instanser. Programinstanser kan läsa in konfigurationen via File REST API och människor kan komma åt dem efter behov genom att montera SMB-resursen lokalt.
    - Diagnostisk resurs: en Azure-filresurs är en praktisk plats för moln program för att skriva loggar, mått och krasch dum par. Loggar kan skrivas av programinstanser via File REST API och utvecklare kan komma åt dem genom att montera filresursen på en lokal dator. Detta ger stor flexibilitet, eftersom utvecklarna kan använda molnutveckling utan att behöva överge eventuella befintliga verktyg som de kan och gillar att använda.

**Reference**
- [Azure-portalen](https://portal.azure.com)
- [Handlingar](/azure/storage/files/storage-files-introduction)
- [Snabbstarter](/azure/storage/files/storage-files-quick-create-use-windows)

## <a name="azure-queues"></a>Azure Queues
[Azure Queue Storage](https://azure.microsoft.com/services/storage/queues/) är en tjänst för att lagra ett stort antal meddelanden. Du kommer åt meddelanden från var som helst i världen via autentiserade anrop med HTTP eller HTTPS. Ett Queue-meddelande kan vara upp till 64 KB stort och en kö kan innehålla miljon tals meddelanden, upp till den totala kapacitets gränsen för ett lagrings konto. Köer används ofta för att skapa en efter släpning av arbete som ska bearbetas asynkront.

**Reference**
- [Azure-portalen](https://portal.azure.com)
- [Handlingar](/azure/storage/queues/)
- [Snabbstarter](/azure/storage/queues/storage-quickstart-queues-portal)
- [Exempel](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
