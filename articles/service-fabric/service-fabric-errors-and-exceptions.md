---
title: Vanliga FabricClient undantag kastas
description: Beskriver de vanliga undantag och fel som kan genereras av FabricClient API:er när program- och klusterhanteringsåtgärder utförs.
author: oanapl
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: oanapl
ms.openlocfilehash: 9ad3097a490d4728e05ea90652c17c24b79cac2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75457929"
---
# <a name="common-exceptions-and-errors-when-working-with-the-fabricclient-apis"></a>Vanliga undantag och fel när du arbetar med FabricClient-API:er
[FabricClient-API:erna](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) gör det möjligt för kluster- och programadministratörer att utföra administrativa uppgifter i ett Service Fabric-program, tjänst eller kluster. Till exempel programdistribution, uppgradering och borttagning, kontroll av hälsotillståndet ett kluster eller testning av en tjänst. Programutvecklare och klusteradministratörer kan använda FabricClient-API:erna för att utveckla verktyg för att hantera Service Fabric-klustret och programmen.

Det finns många olika typer av operationer som kan utföras med FabricClient.  Varje metod kan utlösa undantag för fel på grund av felaktiga indata-, körningsfel eller tillfälliga infrastrukturproblem.  Se API-referensdokumentationen för att hitta vilka undantag som genereras av en viss metod. Det finns dock några undantag som kan kastas av många olika [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) API:er. I följande tabell visas de undantag som är vanliga i FabricClient-API:erna.

| Undantag | Kastas när |
| --- |:--- |
| [System.Fabric.FabricObjectClosedException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricobjectclosedexception) |[FabricClient-objektet](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) är i stängt tillstånd. Kassera [Det FabricClient-objekt](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) som du använder och instansiera ett nytt [FabricClient-objekt.](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) |
| [System.TimeoutException](https://docs.microsoft.com/dotnet/core/api/system.timeoutexception) |Tidssstruggningen har åtgärdats. [OperationTimedOut](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) returneras när åtgärden tar mer än MaxOperationTimeout att slutföras. |
| [System.UnauthorizedAccessException](https://docs.microsoft.com/dotnet/core/api/system.unauthorizedaccessexception) |Åtkomstkontrollen för åtgärden misslyckades. E_ACCESSDENIED returneras. |
| [System.Fabric.FabricUtception](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception) |Ett körningsfel uppstod när åtgärden utfördes. Alla FabricClient-metoder kan potentiellt kasta [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception), [errorcode-egenskapen](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception.ErrorCode) anger den exakta orsaken till undantaget. Felkoder definieras i [FabricErrorCode-uppräkningen.](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) |
| [System.Fabric.FabricTransientException](https://docs.microsoft.com/dotnet/api/system.fabric.fabrictransientexception) |Åtgärden misslyckades på grund av ett tillfälligt feltillstånd av något slag. En åtgärd kan till exempel misslyckas eftersom kvorumet för repliker tillfälligt inte kan nås. Tillfälliga undantag motsvarar misslyckade åtgärder som kan göras om. |

Några vanliga [FabricErrorCode-fel](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) som kan returneras i en [FabricException:](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception)

| Fel | Villkor |
| --- |:--- |
| CommunicationError (kommunikationare) |Ett kommunikationsfel gjorde att åtgärden misslyckades och försökte igen åtgärden. |
| Ogiltigknurenstyp |Autentiseringstypen är ogiltig. |
| OgiltigX509FindTyp |X509FindType är ogiltig. |
| OgiltigX509StoreLocation |Arkivet X509 är ogiltig. |
| OgiltigtX509StoreName |Arkivet X509 är ogiltigt. |
| OgiltigtX509Dumbavtryck |Tumavtryckssträngen för X509-certifikatet är ogiltig. |
| InvalidProtectionLevel |Skyddsnivån är ogiltig. |
| Ogiltigx509Store |Det går inte att öppna X509-certifikatarkivet. |
| Ogiltigtobjektnamn |Ämnesnamnet är ogiltigt. |
| OgiltigtEfterdömdCommonNameList |Formatet för den gemensamma namnliststrängen är ogiltigt. Det bör vara en kommaavgränsad lista. |

