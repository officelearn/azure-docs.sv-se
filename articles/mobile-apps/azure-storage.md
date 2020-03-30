---
title: Molnlagring för att skapa mycket säkra, hållbara, skalbara appar med Azure Storage
description: Lär dig mer om tjänsterna för att lagra stora strukturerade och icke-strukturerade mobilprogramdata i molnet.
author: codemillmatt
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 682f06936480d9018d759b4df3bed80be1206faf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240979"
---
# <a name="cloud-storage-for-highly-secure-durable-scalable-apps-with-azure-storage"></a>Molnlagring för mycket säkra, hållbara, skalbara appar med Azure Storage
[Azure Storage](https://azure.microsoft.com/services/storage/) är Microsofts molnlagringslösning för moderna program som erbjuder ett enormt skalbart objektlager för dataobjekt, en filsystemtjänst för molnet, ett meddelandearkiv för tillförlitliga meddelanden och en NoSQL-butik. Azure Storage är:
- **Hållbar och högtillgängd:** Redundans säkerställer att dina data är säkra i händelse av tillfälliga maskinvarufel. Du kan också välja att replikera data i datacenter eller geografiska regioner för ytterligare skydd mot lokala allvarliga händelser eller naturkatastrofer. Data som replikeras på det här sättet förblir mycket tillgängliga vid ett oväntat avbrott.
- **Säker:** Alla data som skrivs till Azure Storage krypteras av tjänsten. Med Azure Storage får du detaljerad kontroll över vem som har tillgång till dina data.
- **Skalbar:** Tjänsterna är utformade för att vara enormt skalbara för att uppfylla datalagrings- och prestandabehoven i dagens program.
- **Hanterad:** Azure hanterar maskinvaruunderhåll, uppdateringar och kritiska problem åt dig.
- **Tillgänglig:** Data är tillgängliga från var som helst i världen över HTTP eller HTTPS. Microsoft tillhandahåller klientbibliotek på en mängd olika språk, till exempel .NET, Java, Node.js, Python, PHP, Ruby och Go och ett moget REST API. Skript stöds i Azure PowerShell eller Azure CLI. Azure-portalen och Azure Storage Explorer erbjuder enkla visuella lösningar för att arbeta med dina data.

Använd följande tjänster för att aktivera molnlagring i dina mobilappar.

## <a name="azure-blob-storage"></a>Azure Blob Storage
[Azure Blob storage](https://azure.microsoft.com/services/storage/blobs/) erbjuder en objektlagringslösning för molnet. Blob-lagring är optimerad för lagring av stora mängder ostrukturerade data som inte följer en viss datamodell eller definition, till exempel text eller binär. Den stöder olika språk som klientbibliotek använder. Blob-lagring är utformad för att:
- Visa bilder eller dokument direkt i en webbläsare.
- Lagra filer för distribuerad åtkomst.
- Strömma video och ljud.
- Skriv till loggfiler.
- Lagra data för säkerhetskopiering och återställning, haveriberedskap och arkivering.
- Lagra data för analys av en lokal eller Azure-värd tjänst.

**Referenser**
- [Azure-portal](https://portal.azure.com)
- [Dokumentation om Azure Blob Storage](/azure/storage/blobs/storage-blobs-introduction)
- [Snabbstarter](/azure/storage/blobs/storage-quickstart-blobs-portal)
- [Prover](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

## <a name="azure-table-storage"></a>Azure Table Storage
[Azure Table storage](https://azure.microsoft.com/services/storage/tables/) är en tjänst som lagrar strukturerade NoSQL-data i molnet och tillhandahåller ett nyckel- eller attributarkiv med en schemalös design. Azure Table Storage lagrar stora mängder strukturerade data. Tjänsten är ett NoSQL-datalager som accepterar autentiserade samtal från och utanför Azure-molnet. Azure-tabeller är idealiska för lagring av strukturerade, icke-relationella data. Tabelllagring används vanligtvis för att:
- Lagra terabyte med strukturerade data som kan hantera webbskalasprogram.
- Lagra datauppsättningar som inte kräver komplexa kopplingar, externa nycklar eller lagrade procedurer och som kan avnormaliseras för snabb åtkomst.
- Fråga snabbt data med hjälp av ett klustrade index.
- Komma åt data med hjälp av OData-protokollet och LINQ-frågorna med WCF-datatjänst (Windows Communication Foundation) .NET-bibliotek.

Du kan använda Tabelllagring för att lagra och fråga stora uppsättningar strukturerade, icke-relationella data. Tabellerna skalas i takt med att efterfrågan ökar.

**Referenser**
- [Azure-portal](https://portal.azure.com)
- [Azure Table Storage-dokumentation](/azure/storage/tables/table-storage-overview)
- [Prover](/azure/cosmos-db/tutorial-develop-table-dotnet?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fstorage%2Ftables%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
- [Snabbstarter](/azure/storage/tables/table-storage-quickstart-portal)

## <a name="azure-files"></a>Azure Files
Med [Azure Files](https://azure.microsoft.com/services/storage/files/)kan du konfigurera högtillgängligt nätverksfilresurser som kan nås med hjälp av standardprotokollet För servermeddelandeblock (SMB). Flera virtuella datorer kan dela samma filer med både läs- och skrivåtkomst. Du kan också läsa filerna med hjälp av REST-gränssnittet eller lagringsklientbiblioteken. Du kan komma åt filerna var som helst i världen med hjälp av en URL som pekar på filen och innehåller en SAS-token (Shared Access Signature). Du kan generera SAS-token. De ger specifik åtkomst till en privat tillgång under en viss tid.

Azure-filresurser kan användas för att:
- **Ersätt eller komplettera lokala filservrar:** Populära operativsystem som Windows, macOS och Linux kan direkt montera Azure-filresurser var de än befinner sig i världen. Azure-filresurser kan också replikeras med Azure-filsynkronisering till Windows-servrar, antingen lokalt eller i molnet, för högpresterande och distribuerad cachelagring av data där den används.
- **Lyft- och skiftapplikationer:** Migrera program till molnet som förväntar sig att en filresurs ska lagra filprogram eller användardata.
- **Förenkla molnutveckling:** Azure-filer kan också användas på många sätt för att förenkla nya molnutvecklingsprojekt. Ett exempel:
    - **Inställningar för delade program:** Ett vanligt mönster för distribuerade program är att ha konfigurationsfiler på en centraliserad plats där de kan nås från många programinstanser. Programinstanser kan läsa in konfigurationen via FILE REST API. Användare kan komma åt dem efter behov genom att montera SMB-resursen lokalt.
    - **Diagnostisk resurs:** En Azure-filresurs är en praktisk plats för molnprogram att skriva sina loggar, mått och kraschdumpar. Loggar kan skrivas av programinstanserna via FILE REST API. Utvecklare kan komma åt dem genom att montera filresursen på sin lokala dator. Den här funktionen ger stor flexibilitet. Utvecklare kan anamma molnutveckling utan att behöva överge de befintliga verktygen de känner till.

**Referenser**
- [Azure-portal](https://portal.azure.com)
- [Dokumentation om Azure Files](/azure/storage/files/storage-files-introduction)
- [Snabbstarter](/azure/storage/files/storage-files-quick-create-use-windows)

## <a name="azure-queue-storage"></a>Azure Queue Storage
[Azure Queue storage](https://azure.microsoft.com/services/storage/queues/) är en tjänst för lagring av ett stort antal meddelanden. Du kommer åt meddelanden från var som helst i världen via autentiserade samtal med hjälp av HTTP eller HTTPS. Ett kömeddelande kan vara upp till 64 kB i storlek. En kö kan innehålla miljontals meddelanden, upp till den totala kapacitetsgränsen för ett lagringskonto. Köer används ofta för att skapa en eftersläpning av arbete för att bearbeta asynkront.

**Referenser**
- [Azure-portal](https://portal.azure.com)
- [Dokumentation om Azure Queue Storage](/azure/storage/queues/)
- [Snabbstarter](/azure/storage/queues/storage-quickstart-queues-portal)
- [Prover](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
