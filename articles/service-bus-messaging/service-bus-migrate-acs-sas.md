---
title: Migrera från Azure Active Directory Access Control Service till signatur för delad åtkomst auktorisering | Microsoft Docs
description: Migrera program från åtkomstkontrolltjänsten till SAS
services: service-bus-messaging
documentationcenter: ''
author: clemensv
manager: timlt
editor: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/21/2017
ms.author: sethm
ms.openlocfilehash: 7a2a55a6ad6a721a39c9f064aad817f841dd3235
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/22/2017
ms.locfileid: "27160273"
---
# <a name="migrate-from-azure-active-directory-access-control-service-to-shared-access-signature-authorization"></a>Migrera från Azure Active Directory Access Control Service till signatur för delad åtkomst auktorisering

Service Bus-program har tidigare hade ett val av två olika auktorisering modeller: den [delade signatur åtkomst (SAS)](service-bus-sas.md) token direkt från Service Bus-modell och en federerad modell där hanteringen av auktoriseringsregler hanteras i av den [Azure Active Directory](/azure/active-directory/) Access Control Service (ACS), och token som hämtades från ACS skickas till Service Bus för att auktorisera åtkomst till de önskade funktionerna.

ACS-auktoriseringsmodellen länge har ersatts av [SAS-auktorisering](service-bus-authentication-and-authorization.md) som den önskade modellen och all dokumentation, anvisningar och exempel uteslutande använder SAS idag. Det är dessutom inte längre möjligt att skapa nya Service Bus-namnområden som har parats ihop med ACS.

SAS har fördelen att den inte är direkt beroende av en annan tjänst, men kan användas direkt från en klient utan en mellanhand genom att ge klientåtkomst till nyckeln SAS regeln namn och regeln. SAS kan även integreras enkelt med en metod som en klient har att först skicka en kontroll av tillståndet med en annan tjänst och sedan utfärdas en token. Den senare metoden liknar användningsmönstret ACS, men gör utfärdande åtkomsttoken baserat på villkor som programspecifika data som är svåra att uttrycka i ACS.

Vi rekommenderar kunder att migrera sina program kan förlita sig på SAS i stället för alla befintliga program som är beroende av ACS.

## <a name="migration-scenarios"></a>Migreringsscenarier

ACS- och Service Bus är integrerade via delade kunskap om en *signeringsnyckel*. Signeringsnyckeln används av en ACS-namnområde för att signera auktorisering token och den används av Service Bus för att verifiera att token har utfärdats av parad ACS-namnområdet. ACS-namnområde innehåller tjänsteidentiteter och auktoriseringsregler. Auktoriseringsregler definierar vilka tjänstidentiteten eller vilka token som utfärdas av en extern identitetsleverantör hämtar vilken typ av åtkomst till en del av namnområdet för Service Bus graph i form av en längsta prefix-matchning.

Till exempel en regel för ACS kan ge den **skicka** anspråk på sökvägen prefixet `/` för tjänstidentiteten, vilket innebär att en token som utfärdas av ACS baserat på regeln ger klienten rättigheter att skicka till alla entiteter i namnområdet. Om sökvägen prefixet `/abc`, identiteten är begränsade till att skicka till enheter med namnet `abc` eller ordnade under det prefixet. Det förutsätts att läsare av vägledningen migrering redan är bekant med principerna.

Migreringsscenarier delas in i tre kategorier:

1.  **Oförändrade standardvärden**. Vissa kunder använder en [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider) objekt som passerar den automatiskt genererade **ägare** service identitet och dess hemlig nyckel för namnområdet ACS paras ihop med Service Bus-namnområde och Lägg inte till nya regler.

2.  **Anpassad tjänstidentiteter med enkla regler**. Vissa kunder lägga till den nya tjänstidentiteter och bevilja varje ny tjänstidentiteten **skicka**, **lyssna**, och **hantera** behörigheter för en specifik entitet.

3.  **Anpassad tjänstidentiteter med komplexa regler**. Ett fåtal kunder har komplexa regel anger i vilken externt utfärdade token är mappade till rättigheter på Relay eller där en enda tjänstidentitet tilldelas differentierade rättigheter på flera namnområde sökvägar till flera regler.

Om du behöver hjälp med migreringen av komplexa uppsättningar kan du kontakta [Azure-supporten](https://azure.microsoft.com/support/options/). De två scenarierna aktivera enkla migrering.

### <a name="unchanged-defaults"></a>Oförändrade standardvärden

Om ditt program inte har ändrats ACS-standarder, kan du ersätta alla [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider) med en [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) objekt och använder namnområdet förkonfigurerade  **RootManageSharedAccessKey** i stället för ACS **ägare** konto. Observera att även med ACS **ägare** kontot den här konfigurationen har (och fortfarande) inte generellt sätt rekommenderat, eftersom kontot/regeln ger fullständig hanterare över namnområde, inklusive behörighet att ta bort entiteter.

### <a name="simple-rules"></a>Enkla regler

Om programmet använder anpassade tjänsteidentiteter med enkla regler, är migreringen enkla i de fall där en identitet för ACS-tjänsten har skapats för att ge åtkomstkontroll på en särskild kö. Det här scenariot är ofta fallet i SaaS-format lösningar där varje kö används som en brygga till en plats eller avdelningskontor och tjänstidentiteten skapas för den specifika platsen. I det här fallet kan respektive tjänstidentiteten migreras till en regel för signatur för delad åtkomst, direkt i kön. Identitet tjänstnamnet kan bli namn på SAS och identitetsnyckel tjänsten kan bli regeln SAS-nyckeln. Rättigheter SAS-regeln är konfigurerad motsvarar respektive tillämpliga ACS-regel för entiteten.

Du kan göra den här nya och ytterligare konfigurationen av SAS lokalt på befintliga namnområden som är federerat med ACS och migrering från ACS utförs senare med hjälp av [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) i stället för [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider). Namnområdet behöver inte att ta bort kopplingen från ACS.

### <a name="complex-rules"></a>Komplexa regler

SAS-regler avsett inte att vara konton, men är namngivna Signeringsnycklar som är associerade med rättigheter. Därför scenarier där programmet skapar många tjänsteidentiteter och ger dem behörighet för flera entiteter eller hela namnområdet kräver ett token utfärdas mellanhand. Du kan få riktlinjer för sådana mellanhand av [att kontakta supporten](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Nästa steg

Mer information om Service Bus-autentisering finns i följande avsnitt:

* [Service Bus-autentisering och auktorisering](service-bus-authentication-and-authorization.md)
* [Service Bus-autentisering med signaturer för delad åtkomst](service-bus-sas.md)
* [Service Bus-grunder](service-bus-fundamentals-hybrid-solutions.md)

