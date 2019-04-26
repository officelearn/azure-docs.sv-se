---
title: Migrera från Azure Active Directory Access Control Service till signatur för delad åtkomst-auktorisering | Microsoft Docs
description: Migrera program från Access Control Service till SAS
services: service-bus-relay
documentationcenter: ''
author: clemensv
manager: timlt
editor: ''
ms.service: service-bus-relay
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/20/2017
ms.author: spelluru
ms.openlocfilehash: 7f71b6884413309e6806658f25313c22e074a71b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60419636"
---
# <a name="migrate-from-azure-active-directory-access-control-service-to-shared-access-signature-authorization"></a>Migrera från Azure Active Directory Access Control Service för auktorisering med signatur för delad åtkomst

Azure Relay-appar tidigare hade ett urval av med två olika auktorisering modeller: den [signatur för delad åtkomst (SAS)](../service-bus-messaging/service-bus-sas.md) token modellen direkt från den vidarebefordrande tjänsten och en extern modell där hanteringen av auktoriseringsregler hanteras i av den [Azure Active Directory](/azure/active-directory/) Access Control Service (ACS), och token hämtas från ACS skickas till Relay för att auktorisera åtkomst till de önskade funktionerna.

ACS-auktoriseringsmodellen länge har ersatts av [SAS auktorisering](../service-bus-messaging/service-bus-authentication-and-authorization.md) som den önskade modellen och all dokumentation, vägledning och exempel exklusivt använder SAS idag. Dessutom är det inte längre går att skapa nya Relay-namnområden som är länkat till ACS.

SAS har fördelen eftersom det inte är direkt beroende av en annan tjänst, men kan användas direkt från en klient utan några mellanhänder genom att ge klientåtkomsten till regeln SAS nyckel namn och en regel. SAS kan även integreras enkelt med en metod där en klient måste först klara en kontroll av auktorisering med en annan tjänst och sedan utfärdas en token. Den senare metoden liknar användningsmönstret ACS, men gör det möjligt för utfärdande åtkomsttoken baserat på programspecifika villkor som är svåra att uttrycka i ACS.

Vi rekommenderar kunder att migrera sina program för att förlita dig på SAS istället för alla befintliga program som är beroende av ACS.

## <a name="migration-scenarios"></a>Migreringsscenarier

ACS och Relay är integrerade via den delat med sig av en *signeringsnyckel*. Signeringsnyckeln används av ett ACS-namnområde logga auktoriseringstoken och den används av Azure Relay för att verifiera att token har utfärdats av kopplat ACS-namnområde. ACS-namnområdet innehåller tjänstidentiteter och auktoriseringsregler. Auktoriseringsreglerna definierar vilka tjänstidentitet eller vilka token som utfärdas av en extern identitetsprovider hämtar vilken typ av åtkomst till en del av Relay-namnområde graph i form av en längsta prefix-matchning.

Till exempel en ACS-regel kan ge den **skicka** gör anspråk på sökvägen prefixet `/` till en tjänstidentitet, vilket innebär att en token utfärdad av ACS baserat på regeln ger klienten rättigheter att skicka till alla entiteter i namnområdet. Om sökvägen prefixet `/abc`, identiteten är begränsade till att skicka till entiteter med namnet `abc` eller ordnade under det prefixet. Det förutsätts att läsaren av den här migreringsvägledningen redan är bekant med de här principerna.

Migreringsscenarier delas in i tre olika kategorier:

1.  **Oförändrade standardvärden**. Vissa kunder använder en [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider) objekt som passerar den automatiskt genererade **ägare** tjänsten identitets- och den hemliga nyckeln för ACS-namnområde, tillsammans med Relay-namnområde och göra Lägg inte till nya regler.

2.  **Anpassade tjänstidentiteter med enkla regler**. Vissa kunder Lägg till ny tjänstidentiteter och tilldela varje ny tjänstidentitet **skicka**, **lyssna**, och **hantera** behörigheter för en specifik enhet.

3.  **Anpassade tjänstidentiteter med komplexa regler**. Ett fåtal kunder har komplexa regel som anger i vilken externt utfärdade token är mappade till behörighet på Relay eller där en enda tjänstidentitet tilldelas särskild behörighet på flera namnområde vägar via flera regler.

Om du behöver hjälp med migreringen av komplexa regeluppsättningar kan du kontakta [Azure-supporten](https://azure.microsoft.com/support/options/). De två scenarierna aktivera enkel migrering.

### <a name="unchanged-defaults"></a>Oförändrade standardvärden

Om ditt program inte har ändrats ACS standardvärden, du kan ersätta alla [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider) användning med en [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) objekt och användning av namnområdet förkonfigurerade  **RootManageSharedAccessKey** i stället för ACS **ägare** konto. Observera att även med ACS **ägare** konto, den här konfigurationen har (och fortfarande) inte brukar rekommenderas eftersom det här kontot/regel förser namnområdet, inklusive behörighet att ta bort alla utfärdare för fullständig hantering av entiteter.

### <a name="simple-rules"></a>Enkla regler

Om programmet använder anpassade tjänstidentiteter med enkla regler, är migreringen enkelt i de fall där en ACS-tjänstidentitet har skapats för att ge åtkomstkontroll på en specifik vidarebefordran. Det här scenariot är ofta fallet i SaaS-style-lösningar där varje relay används som en brygga till en plats eller avdelningskontor och tjänstidentiteten har skapats för den specifika platsen. I det här fallet kan respektive tjänstidentiteten migreras till en regel för signatur för delad åtkomst, direkt på relay. Namnet på tjänsten identitet kan bli Regelnamnet SAS och identitet tjänstnyckeln kan bli SAS-nyckel för regeln. Behörighet för SAS-regeln är konfigurerad motsvarighet till respektive gäller ACS-regel för entiteten.

Du kan göra den här nya och ytterligare konfigurationen av SAS på plats på alla befintliga namnområden som är federerad med ACS och migreringen från ACS utförs senare med hjälp av [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) i stället för [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider). Namnområdet behöver inte att ta bort kopplingen från ACS.

### <a name="complex-rules"></a>Avancerade regler

SAS-regler är inte avsedda att vara konton, men är namngivna Signeringsnycklar som är associerade med behörighet. Därför scenarier där programmet skapar många tjänstidentiteter och ger dem åtkomstbehörigheter för flera entiteter eller hela namnområdet behöver fortfarande en utfärdande av token mellanhand. Du kan få vägledning för sådana mellanhand av [att kontakta supporten](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om Azure Relay-autentisering finns i följande avsnitt:

* [Azure Relay-autentisering och auktorisering](relay-authentication-and-authorization.md)
* [Service Bus-autentisering med signaturer för delad åtkomst](../service-bus-messaging/service-bus-sas.md)


