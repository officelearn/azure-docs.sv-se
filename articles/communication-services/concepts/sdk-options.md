---
title: 'Klient bibliotek och REST API: er för Azure Communication Services'
titleSuffix: An Azure Communication Services concept document
description: 'Läs mer om klient bibliotek och REST-API: er för Azure Communication Services.'
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/18/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: f39aa76e4bd2ce3d298e555f56b09d0218ef0862
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90937864"
---
# <a name="client-libraries-and-rest-apis"></a>Klient bibliotek och REST-API: er

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Azure Communication Services-funktionerna organiseras konceptuellt i sex områden. Vissa områden har helt klient bibliotek med öppen källkod. Det anropande klient biblioteket använder sig av patentskyddade nätverks gränssnitt och är för närvarande stängd-källa och chatt-biblioteket innehåller ett beroende för stängd källa. Länkar till alla SDK: er och exempel underhålls i [Azure Communication Services GitHub-lagrings platsen](https://github.com/Azure/communication).

## <a name="client-libraries"></a>Klientbibliotek

| Sammansättning               | Protokoll             |Öppen vs. stängd källa| Namnrymder                          | Funktioner                                                      |
| ---------------------- | --------------------- | ---|-------------------------- | --------------------------------------------------------------------------- |
| Azure Resource Manager | REST | Öppna            | Azure. ResourceManager.-kommunikation | Etablera och hantera resurser för kommunikations tjänster             |
| Common                 | REST | Öppna               | Azure. Communication. common          | Tillhandahåller bas typer för andra klient bibliotek |
| Administration         | REST |                | Azure. Communication. administration  | Hantera användare, åtkomsttoken och telefonnummer, allokera standard-kompatibla STUN och omvandla servrar |
| Chatt                   | REST med tillverkarspecifik signalering | Öppna med varnings paket med stängd källa    | Azure. Communication. Chat            | Lägg till text baserad chatt i real tid i dina program  |
| SMS                    | REST | Öppna              | Azure. Communication. SMS             | Skicka och ta emot SMS-meddelanden |
| Sänder                | Tillverkarspecifik transport | Stängda |Azure. Communication. Call         | Använd röst, video, skärm delning och andra funktioner för data kommunikation i real tid          |

### <a name="client-library-language-support"></a>Språk stöd för klient bibliotek

Tillgänglighets vägledning och tids linjer för enskilda klient biblioteks paket beskrivs nedan. [Azure-översikten](https://azure.microsoft.com/updates/) innehåller ytterligare information om kommande funktioner.

| Område           | JavaScript | .NET | Python | Java | SWIFT eller OBJ-C | Java (Android) | Övrigt                          |
| -------------- | ---------- | ---- | ------ | ---- | -------------- | -------------- | ------------------------------ |
| Azure Resource Manager | ✔️         | ✔️    | ✔️      | -    | -              | *Stöds inte ännu*  | GO och Azure CLI *stöds inte ännu* |
| Common         | ✔️         | ✔️    | -      | ✔️   | ✔️            | ✔️             | -                              |
| Administration | ✔️         | ✔️    | ✔️      | ✔️   | -              | -              | CLI                            |
| Chatt           | ✔️         | ✔️    | ✔️      | ✔️   | *Stöds inte ännu*  | *Stöds inte ännu*  | -                              |
| SMS            | ✔️         | ✔️    | ✔️      | ✔️   | -              | -              | -                              |
| Sänder        | ✔️         | -      | -      | -     | (OBJ-C) ✔️     | ✔️            | -                              |

### <a name="client-library-public-repository-support"></a>Stöd för offentliga lagrings platser i klient bibliotek

Kommunikations tjänster publicerar skapade bibliotek i flera offentliga databaser.

| Språk       | Optimerad för...                       | Paketering |
| -------------- | ------------------------------------ | --------- |
| .NET           | Plattformsoberoende                       | NuGet     |
| Python         | Windows & Linux-servrar              | Pypi      |
| Java (J2EE)    | JVM på Windows-eller Linux-servrar      | Maven     |
| Java (Android) | Android-klientprogram          | Maven     |
| JavaScript     | Webb läsar klient program och-nod | NPM       |

## <a name="rest-apis"></a>REST API:er

API: er för kommunikations tjänster dokumenteras tillsammans med andra Azure REST-API: er i [docs.Microsoft.com](https://docs.microsoft.com/rest/api/azure/). I den här dokumentationen får du lära dig hur du strukturerar dina HTTP-meddelanden och ger vägledning om hur du använder Postman. Den här dokumentationen erbjuds också i Swagger-format på [GitHub](https://github.com/Azure/azure-rest-api-specs).

## <a name="additional-support-details"></a>Ytterligare support information

### <a name="ios-and-android-support-details"></a>information om iOS och Android-support

- Kommunikations tjänster iOS klient bibliotek mål iOS version 13 + och Xcode 11 +.
- Android Java-klient bibliotek mål-Android API Level 21 + och Android Studio 4.0 +

### <a name="net-support-details"></a>.NET-support information

Med undantag för anrop av kommunikations tjänst paket riktar sig .NET standard 2,0 som stöder plattformarna som anges nedan.

Support via .NET Framework 4.6.1
- Windows 10, 8,1, 8 och 7
- Windows Server 2012 R2, 2012 och 2008 R2 SP1

Support via .NET Core 2,0:
- Windows 10 (1607 +), 7 SP1 +, 8,1
- Windows Server 2008 R2 SP1 +
- Högsta OS X-10.12 +
- Linux flera versioner/distributioner
- UWP 10.0.16299 (RS3) september 2017
- Unity 2018,1
- Mono 5,4
- Xamarin iOS 10,14
- Xamarin Mac 3,8

## <a name="api-stability-expectations"></a>Förväntningar för API-stabilitet 

> [!IMPORTANT]
> Det här avsnittet innehåller vägledning om REST-API: er och klient bibliotek som marker ATS som **stabila**. API: er som marker ATS för hands version, för hands version eller beta kan ändras eller föråldras **utan föregående meddelande**. Azure Communication Services finns i en **offentlig för hands version**och API: er är markerade som sådana.

I framtiden kan vi dra tillbaka versioner av klient biblioteken för kommunikations tjänster och vi kan införa ändringar i våra REST-API: er och klient bibliotek som har släppts. Azure Communication Services följer *vanligt vis* två support principer för att ta ur bruks versioner:

- Du får ett meddelande minst tre år innan det krävs för att ändra kod på grund av ett kommunikations tjänst gränssnitts ändringar. Alla dokumenterade REST API: er och klient biblioteks-API: er kan i allmänhet ha minst tre års varning innan gränssnitten inaktive ras.
- Du kommer att meddelas minst ett år innan du måste uppdatera klient biblioteks sammansättningar till den senaste lägre versionen. Dessa obligatoriska uppdateringar bör inte kräva några kod ändringar eftersom de har samma huvud version. Detta gäller särskilt för anropande och chat-bibliotek som har real tids komponenter som ofta kräver säkerhets-och prestanda uppdateringar. Vi rekommenderar starkt att du håller dina klient bibliotek för kommunikations tjänster uppdaterade.

### <a name="api-and-client-library-decommissioning-examples"></a>Inställnings exempel för API och klient bibliotek

**Du har integrerat v24-versionen av SMS-REST API i ditt program. Azure-kommunikation släpper v25.**

Du får 3 års varning innan dessa API: er slutar fungera och tvingas att uppdateras till v25. Den här uppdateringen kan kräva en kod ändring.

**Du har integrerat v 2.02-versionen av det anropande klient biblioteket i ditt program. Azure-kommunikation frigör v-2.05.**

Du kan behöva uppdatera till v 2.05-versionen av det anropande klient biblioteket inom 12 månader från lanseringen av v 2.05. Detta bör vara en enkel ersättning av artefakten utan att det krävs någon kod ändring eftersom v-2.05 finns i den version av v2 och inte har några större ändringar.

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande översikt över klient bibliotek:

- [Översikt över samtals klient bibliotek](../concepts/voice-video-calling/calling-sdk-features.md)
- [Översikt över Chat-klient bibliotek](../concepts/chat/sdk-features.md)
- [Översikt över SMS-klient bibliotek](../concepts/telephony-sms/sdk-features.md)

Så här kommer du igång med Azure Communication Services:

- [Skapa Azure-kommunikations resurser](../quickstarts/create-communication-resource.md)
- Generera [användar åtkomst-token](../quickstarts/access-tokens.md)
