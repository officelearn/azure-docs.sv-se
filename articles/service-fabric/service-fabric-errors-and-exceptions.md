---
title: Vanliga FabricClient undantag | Microsoft Docs
description: Beskriver de vanligaste undantag och fel som kan uppkomma FabricClient APIs vid utförandet av program och klusterhanteringsåtgärder.
services: service-fabric
documentationcenter: .net
author: oanapl
manager: chackdan
editor: ''
ms.assetid: bb821313-b221-479f-b08e-36cf07e60a07
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/20/2018
ms.author: oanapl
ms.openlocfilehash: 5bf17f4ced6bb01d8b62b6fa40ed1aeffe6f712f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60946558"
---
# <a name="common-exceptions-and-errors-when-working-with-the-fabricclient-apis"></a>Vanliga undantag och fel när du arbetar med FabricClient APIs
Den [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) API: er gör att kluster- och programhantering administratörer kan utföra administrativa uppgifter på en Service Fabric-program, tjänst eller -kluster. Till exempel programdistribution, uppgradering och borttagning av hälsokontrollen ett kluster eller testa en tjänst. Programutvecklare och klusteradministratörer kan använda FabricClient APIs för att utveckla verktyg för att hantera Service Fabric-kluster och program.

Det finns många olika typer av åtgärder som kan utföras med hjälp av FabricClient.  Varje metod orsaka undantag för fel på grund av ogiltiga indata, körningsfel eller infrastruktur för tillfälliga problem.  Finns i dokumentationen för API-referens för att hitta vilka undantag utlöses av en viss metod. Det finns vissa undantag, men som kan misslyckas på grund av många olika [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) API: er. I följande tabell visas de undantag som är gemensamma för FabricClient APIs.

| Undantag | Utlöses när |
| --- |:--- |
| [System.Fabric.FabricObjectClosedException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricobjectclosedexception) |Den [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) objektet är en stängd. Avyttra den [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) objekt du använder och skapa en instans av en ny [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) objekt. |
| [System.TimeoutException](https://docs.microsoft.com/dotnet/core/api/system.timeoutexception) |Tidsgränsen nåddes för åtgärden. [OperationTimedOut](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) returneras när åtgärden tar mer än MaxOperationTimeout att slutföra. |
| [System.UnauthorizedAccessException](https://docs.microsoft.com/dotnet/core/api/system.unauthorizedaccessexception) |Det gick inte att åtkomstkontrollen för åtgärden. E_ACCESSDENIED returneras. |
| [System.Fabric.FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception) |Ett körningsfel inträffade under åtgärden. Någon av metoderna FabricClient orsaka potentiellt [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception), [ErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception.ErrorCode) egenskapen anger den exakta orsaken för undantaget. Felkoder definieras i den [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) uppräkning. |
| [System.Fabric.FabricTransientException](https://docs.microsoft.com/dotnet/api/system.fabric.fabrictransientexception) |Åtgärden misslyckades på grund av ett tillfälligt fel villkor av något slag. Exempelvis kan en åtgärd misslyckas om ett kvorum av repliker inte kan tillfälligt nås. Tillfälliga undantag motsvarar misslyckade åtgärder som kan skickas på nytt. |

Några vanliga [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) fel som kan returneras i en [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception):

| Fel | Tillstånd |
| --- |:--- |
| CommunicationError |Ett kommunikationsfel orsakat att åtgärden misslyckas, försök igen. |
| InvalidCredentialType |Autentiseringsuppgiftstypen är ogiltig. |
| InvalidX509FindType |X509FindType är ogiltig. |
| InvalidX509StoreLocation |X509 lagringsplatsen är ogiltig. |
| InvalidX509StoreName |X509 lagernamnet är ogiltigt. |
| InvalidX509Thumbprint |X509 tumavtryckssträngen för certifikatet är ogiltigt. |
| InvalidProtectionLevel |Skyddsnivån är ogiltig. |
| InvalidX509Store |X509 inte går att öppna certifikatarkivet. |
| InvalidSubjectName |Namn på certifikatmottagare är ogiltig. |
| InvalidAllowedCommonNameList |På en gemensam lista sträng med formatet är ogiltigt. Det bör vara en kommaavgränsad lista. |

