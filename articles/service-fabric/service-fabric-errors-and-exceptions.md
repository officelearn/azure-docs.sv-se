---
title: Vanliga FabricClient-undantag utlöst
description: 'Beskriver vanliga undantag och fel som kan uppstå i FabricClient-API: erna när du utför åtgärder för program-och kluster hantering.'
author: oanapl
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: oanapl
ms.openlocfilehash: 9ad3097a490d4728e05ea90652c17c24b79cac2c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75457929"
---
# <a name="common-exceptions-and-errors-when-working-with-the-fabricclient-apis"></a>Vanliga undantag och fel vid arbete med FabricClient-API: er
[FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) -API: er gör det möjligt för kluster-och program administratörer att utföra administrativa uppgifter på ett Service Fabric program, en tjänst eller ett kluster. Till exempel program distribution, uppgradering och borttagning, kontroll av hälso tillståndet för ett kluster eller testning av en tjänst. Programutvecklare och kluster administratörer kan använda FabricClient-API: er för att utveckla verktyg för att hantera Service Fabric kluster och program.

Det finns många olika typer av åtgärder som kan utföras med hjälp av FabricClient.  Varje metod kan orsaka undantag för fel på grund av felaktiga inkörningar, körnings fel eller problem med övergående infrastruktur.  Se API Reference-dokumentationen för att ta reda på vilka undantag som genereras av en speciell metod. Det finns vissa undantag, som kan genereras av många olika [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) -API: er. I följande tabell visas de undantag som är gemensamma för FabricClient-API: erna.

| Undantag | Utlöst när |
| --- |:--- |
| [System.Fabric.FabricObjectClosedException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricobjectclosedexception) |[FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) -objektet är i ett stängt tillstånd. Ta bort det [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) -objekt som du använder och instansiera ett nytt [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) -objekt. |
| [System.TimeoutException](https://docs.microsoft.com/dotnet/core/api/system.timeoutexception) |Tids gränsen för åtgärden uppnåddes. [OperationTimedOut](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) returneras när åtgärden tar fler än MaxOperationTimeout att slutföra. |
| [System.UnauthorizedAccessException](https://docs.microsoft.com/dotnet/core/api/system.unauthorizedaccessexception) |Åtkomst kontrollen för åtgärden misslyckades. E_ACCESSDENIED returneras. |
| [System.Fabric.FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception) |Ett körnings fel uppstod när åtgärden utfördes. Vilken som helst av FabricClient-metoderna kan resultera i [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception), egenskapen [ErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception.ErrorCode) anger den exakta orsaken till undantaget. Felkoder definieras i [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) -uppräkningen. |
| [System.Fabric.FabricTransientException](https://docs.microsoft.com/dotnet/api/system.fabric.fabrictransientexception) |Åtgärden misslyckades på grund av ett tillfälligt fel tillstånd av någon typ. En åtgärd kan till exempel Miss lyckas eftersom det inte går att komma åt ett kvorum med repliker. Tillfälliga undantag motsvarar misslyckade åtgärder som kan göras om. |

Några vanliga [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) -fel som kan returneras i en [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception):

| Fel | Villkor |
| --- |:--- |
| CommunicationError |Det gick inte att utföra åtgärden på grund av ett kommunikations fel. försök igen. |
| InvalidCredentialType |Autentiseringstypen är ogiltig. |
| InvalidX509FindType |X509FindType är ogiltig. |
| InvalidX509StoreLocation |X509-lagrings platsen är ogiltig. |
| InvalidX509StoreName |X509-arkivets namn är ogiltigt. |
| InvalidX509Thumbprint |Den X509-certifikatets tumavtryck-sträng är ogiltig. |
| InvalidProtectionLevel |Skydds nivån är ogiltig. |
| InvalidX509Store |Det går inte att öppna X509-certifikat arkivet. |
| InvalidSubjectName |Ämnes namnet är ogiltigt. |
| InvalidAllowedCommonNameList |Formatet på den gemensamma namn List strängen är ogiltig. Det ska vara en kommaavgränsad lista. |

