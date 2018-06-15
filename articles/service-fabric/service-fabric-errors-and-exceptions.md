---
title: Vanliga FabricClient undantag | Microsoft Docs
description: Beskriver de vanligaste undantag och fel som kan uppkomma FabricClient APIs under program- och klusterhanteringsåtgärder.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: bb821313-b221-479f-b08e-36cf07e60a07
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/18/2017
ms.author: ryanwi
ms.openlocfilehash: 31defde289643b2adfd6e15bb8417b5498ba65d1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212508"
---
# <a name="common-exceptions-and-errors-when-working-with-the-fabricclient-apis"></a>Vanliga undantag och fel när du arbetar med FabricClient APIs
Den [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient#System_Fabric_FabricClient) API: er kan klustret och programmet administratörer att utföra administrativa uppgifter på ett Service Fabric-program, tjänst eller klustret. Programdistribution, uppgradering och borttagning, kontrollerar hälsotillståndet för ett kluster eller testa en tjänst. Programutvecklare och klusteradministratörer kan använda FabricClient APIs för att utveckla verktyg för hantering av Service Fabric-kluster och program.

Det finns många olika typer av åtgärder som kan utföras med hjälp av FabricClient.  Varje metod kan utlösa undantag för fel på grund av felaktiga indata, körningsfel eller tillfälligt infrastruktur problem.  Finns i dokumentationen för API-referens för att hitta vilka undantag utlöses av en viss metod. Det finns vissa undantag, men som kan orsakas av många olika [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient#System_Fabric_FabricClient) API: er. I följande tabell visas de undantag som är gemensamma för FabricClient APIs.

| Undantag | Utlöses när |
| --- |:--- |
| [System.Fabric.FabricObjectClosedException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricobjectclosedexception#System_Fabric_FabricObjectClosedException) |Den [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient#System_Fabric_FabricClient) objektet är i ett stängt tillstånd. Avyttra den [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient#System_Fabric_FabricClient) objekt du använder och skapa en instans av en ny [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient#System_Fabric_FabricClient) objekt. |
| [System.TimeoutException](https://docs.microsoft.com/dotnet/core/api/system.timeoutexception#System_TimeoutException) |Tidsgränsen uppnåddes. [OperationTimedOut](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode#System_Fabric_FabricErrorCode) returneras när åtgärden tar mer än MaxOperationTimeout ska slutföras. |
| [System.UnauthorizedAccessException](https://docs.microsoft.com/dotnet/core/api/system.unauthorizedaccessexception#System_UnauthorizedAccessException) |Det gick inte att åtkomstkontrollen för åtgärden. E_ACCESSDENIED returneras. |
| [System.Fabric.FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception#System_Fabric_FabricException) |Ett körningsfel inträffade när åtgärden utfördes. Någon av metoderna FabricClient potentiellt kan utlösa [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception#System_Fabric_FabricException), [ErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception#System_Fabric_FabricException_ErrorCode) egenskapen anger den exakta orsaken till undantaget. Felkoder definieras i den [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode#System_Fabric_FabricErrorCode) uppräkning. |
| [System.Fabric.FabricTransientException](https://docs.microsoft.com/dotnet/api/system.fabric.fabrictransientexception#System_Fabric_FabricTransientException) |Åtgärden misslyckades på grund av ett tillfälligt fel villkor av något slag. Exempelvis kan en åtgärd misslyckas eftersom ett kvorum av repliker inte kan tillfälligt nås. Tillfälligt undantag motsvarar misslyckade åtgärder som kan göras. |

Några vanliga [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode#System_Fabric_FabricErrorCode) fel som kan returneras i en [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception#System_Fabric_FabricException):

| Fel | Villkor |
| --- |:--- |
| CommunicationError |Ett kommunikationsfel gjorde att åtgärden misslyckas, försök igen. |
| InvalidCredentialType |Autentiseringsuppgiftstypen är ogiltig. |
| InvalidX509FindType |X509FindType är ogiltig. |
| InvalidX509StoreLocation |X509 lagringsplatsen är ogiltig. |
| InvalidX509StoreName |X509 store-namn är ogiltigt. |
| InvalidX509Thumbprint |X509 certifikat-tumavtryck strängen är ogiltig. |
| InvalidProtectionLevel |Skyddsnivån är ogiltig. |
| InvalidX509Store |X509 inte går att öppna certifikatarkivet. |
| InvalidSubjectName |Ämnesnamnet är ogiltigt. |
| InvalidAllowedCommonNameList |Formatet för strängen vanliga namn är ogiltigt. Det bör vara en kommaavgränsad lista. |

