---
title: Migrera från Azure AD Access Control Service till SAS
description: Lär dig mer om att migrera från Azure Active Directory Access Control Service till signaturen för delad åtkomst.
services: service-bus-messaging
documentationcenter: ''
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/22/2018
ms.author: aschhab
ms.openlocfilehash: ae0dd3827e17cc63b4b698eb8d88a08799c7278f
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72790336"
---
# <a name="migrate-from-azure-active-directory-access-control-service-to-shared-access-signature-authorization"></a>Migrera från Azure Active Directory Access Control Service till signatur för delad åtkomst

Service Bus program har tidigare haft möjlighet att använda två olika auktoriseringsregler: den token för [signatur för delad åtkomst (SAS)](service-bus-sas.md) som tillhandahålls direkt av Service Bus och en federerad modell där hantering av auktoriseringsregler är hanteras i [Azure Active Directory](/azure/active-directory/) Access Control Service (ACS) och tokens som hämtats från ACS skickas till Service Bus för att auktorisera åtkomst till de önskade funktionerna.

ACS-auktoriseringsregeln har länge ersatts av SAS- [auktorisering](service-bus-authentication-and-authorization.md) som önskad modell, och all dokumentation, vägledning och exempel använder bara SAS idag. Dessutom går det inte längre att skapa nya Service Bus namn rymder som är kopplade till ACS.

SAS har fördelen att den inte är direkt beroende av en annan tjänst, men kan användas direkt från en klient utan några mellanhänder genom att ge klienten åtkomst till SAS-regelns namn och regel nyckeln. SAS kan också enkelt integreras med en metod där en klient måste skicka en verifierings kontroll med en annan tjänst och sedan utfärdas en token. Den senare metoden liknar ACS-användnings mönstret, men aktiverar utfärdande av åtkomsttoken baserat på programspecifika villkor som är svåra att uttrycka i ACS.

För alla befintliga program som är beroende av ACS uppmanar vi kunderna att migrera sina program så att de förlitar sig på SAS i stället.

## <a name="migration-scenarios"></a>Scenarier för migrering

ACS och Service Bus integreras genom delade kunskaper om en *signerings nyckel*. Signerings nyckeln används av ett ACS-namnområde för att signera tokens och den används av Service Bus för att kontrol lera att token har utfärdats av det kopplade ACS-namnutrymmet. ACS-namnrymden innehåller tjänst identiteter och auktoriseringsregler. Auktoriseringsregler definierar vilken tjänst identitet eller vilken token som utfärdats av en extern identitetsprovider som hämtar vilken typ av åtkomst till en del av diagrammet för Service Bus namnrymd, i form av en längsta prefix-matchning.

En ACS-regel kan till exempel ge **send** -anspråk i Path-prefixet `/` till en tjänst identitet, vilket innebär att en token som utfärdats av ACS baserat på regeln beviljar klient rättigheterna att skicka till alla entiteter i namn området. Om Path-prefixet är `/abc`är identiteten begränsad till att skickas till entiteter med namnet `abc` eller ordnas under det prefixet. Det förutsätts att läsarna av den här vägledningen för migrering redan är bekant med dessa begrepp.

Migreringen hamnar i tre olika kategorier:

1.  **Oförändrade standardvärden**. Vissa kunder använder ett [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider) -objekt, skickar den automatiskt genererade identiteten för **ägar** tjänsten och den hemliga nyckeln för ACS-namnrymden, tillsammans med Service Bus-namnrymden och lägger inte till nya regler.

2.  **Anpassade tjänst identiteter med enkla regler**. Vissa kunder lägger till nya tjänst identiteter och beviljar varje ny tjänst identitet **Skicka**, **Lyssna**och **Hantera** behörigheter för en viss entitet.

3.  **Anpassade tjänst identiteter med komplexa regler**. Mycket få kunder har komplexa regel uppsättningar där externt utfärdade token mappas till rättigheter för relä eller där en enskild tjänst identitet tilldelas differentierade rättigheter på flera namn områdes sökvägar genom flera regler.

Om du vill ha hjälp med migreringen av komplexa regel uppsättningar kan du kontakta [Azure-supporten](https://azure.microsoft.com/support/options/). De andra två scenarierna möjliggör enkel migrering.

### <a name="unchanged-defaults"></a>Oförändrade standardvärden

Om programmet inte har ändrat ACS-standardvärdena kan du ersätta all [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider) -användning med ett [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) -objekt och använda den förkonfigurerade **RootManageSharedAccessKey** i stället för ACS- **ägar** kontot. Observera att även om kontot för ACS- **ägare** , var den här konfigurationen (och fortfarande är) inte allmänt rekommenderat, eftersom det här kontot/regeln tillhandahåller fullständig hanterings auktoritet över namn området, inklusive behörighet att ta bort alla entiteter.

### <a name="simple-rules"></a>Enkla regler

Om programmet använder anpassade tjänst identiteter med enkla regler, är migreringen enkel i det fall där en ACS-tjänst identitet skapades för att ge åtkomst kontroll i en specifik kö. Det här scenariot är ofta fallet i SaaS-lösningar där varje kö används som en brygga till en klient plats eller avdelnings kontor och tjänst identiteten skapas för den specifika platsen. I det här fallet kan respektive tjänste identitet migreras till en signatur för delad åtkomst, direkt i kön. Namnet på tjänst identiteten kan bli namnet på SAS-regeln och tjänstens identitets nyckel kan bli SAS-regelsamlingen. Rättigheterna för SAS-regeln konfigureras sedan likvärdigt med tillämplig ACS-regel för entiteten.

Du kan göra den här nya och ytterligare konfigurationen av SAS på plats på alla befintliga namn områden som är federerade med ACS, och migreringen från ACS utförs senare med hjälp av [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) i stället för [ SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider). Namn området behöver inte vara olänkat från ACS.

### <a name="complex-rules"></a>Komplexa regler

SAS-regler är inte avsedda att vara konton, men har namngivna signerings nycklar kopplade till rättigheter. Scenarier där programmet skapar många tjänst identiteter och ger åtkomst rättigheter för flera entiteter eller hela namn området kräver fortfarande en mellanliggande token-utfärdande mellanhand. Du kan få vägledning för en sådan mellanhand genom att [kontakta supporten](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Nästa steg

Mer information om Service Bus autentisering finns i följande avsnitt:

* [Service Bus autentisering och auktorisering](service-bus-authentication-and-authorization.md)
* [Service Bus autentisering med signaturer för delad åtkomst](service-bus-sas.md)

