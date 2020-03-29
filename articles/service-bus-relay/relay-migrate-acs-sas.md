---
title: Azure Relay – migrera till auktorisering av delad åtkomstsignatur
description: Beskriver hur du migrerar Azure Relay-program från att använda Azure Active Directory Access Control Service till Shared Access Signature auktorisering.
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
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 59b9e734526c56016e2ddf59c2afb5b8f7b4ad09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514909"
---
# <a name="azure-relay---migrate-from-azure-active-directory-access-control-service-to-shared-access-signature-authorization"></a>Azure Relay – migrera från Azure Active Directory Access Control Service till auktorisering av delad åtkomstsignatur

Azure Relay-program har historiskt sett valt att använda två olika auktoriseringsmodeller: [SAS-tokenmodellen (Shared Access Signature) som](../service-bus-messaging/service-bus-sas.md) tillhandahålls direkt av Relay-tjänsten och en federerad modell där hanteringen av auktoriseringsregler hanteras inuti av [Azure Active Directory](/azure/active-directory/) Access Control Service (ACS) och token som hämtas från ACS skickas till Relay för att godkänna åtkomst till önskade funktioner.

ACS-auktoriseringsmodellen har länge ersatts av [SAS-auktorisering](../service-bus-messaging/service-bus-authentication-and-authorization.md) som den föredragna modellen, och all dokumentation, vägledning och alla prover använder endast SAS idag. Dessutom är det inte längre möjligt att skapa nya Relay-namnområden som är ihopkopplade med ACS.

SAS har fördelen genom att det inte är omedelbart beroende av en annan tjänst, men kan användas direkt från en klient utan mellanhänder genom att ge klienten tillgång till SAS regelnamn och regelnyckel. SAS kan också enkelt integreras med en metod där en klient först måste passera en auktoriseringskontroll med en annan tjänst och sedan utfärdas en token. Den senare metoden liknar ANVÄNDNINGSMÖNSTRET FÖR ACS, men gör det möjligt att utfärda åtkomsttoken baserat på programspecifika villkor som är svåra att uttrycka i ACS.

För alla befintliga program som är beroende av ACS uppmanar vi kunderna att migrera sina program för att förlita sig på SAS istället.

## <a name="migration-scenarios"></a> Migreringsscenarier

ACS och Relay integreras genom delad kunskap om en *signeringsnyckel.* Signeringsnyckeln används av ett ACS-namnområde för att signera auktoriseringstoken och den används av Azure Relay för att verifiera att token har utfärdats av det parade ACS-namnområdet. ACS-namnområdet innehåller tjänstidentiteter och auktoriseringsregler. Auktoriseringsreglerna definierar vilken tjänstidentitet eller vilken token som utfärdats av en extern identitetsprovider som får vilken typ av åtkomst till en del av relay-namnområdet, i form av en matchning med längst prefix.

En ACS-regel kan till exempel bevilja **skicka-anspråket** på sökvägsprefixet `/` till en tjänstidentitet, vilket innebär att en token som utfärdats av ACS baserat på den regeln ger klienträttigheterna att skicka till alla entiteter i namnområdet. Om sökvägsprefixet är `/abc`är det begränsat `abc` till att skicka till entiteter som namnges eller är organiserade under prefixet. Det antas att läsarna av denna migration vägledning redan är bekant med dessa begrepp.

Migreringsscenarierna kan delas in i tre breda kategorier:

1.  **Oförändrade standardvärden**. Vissa kunder använder ett [SharedSecretTokenProvider-objekt,](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider) skickar den automatiskt genererade **ägarens** tjänstidentitet och dess hemliga nyckel för ACS-namnområdet, parat med Relay-namnområdet och lägger inte till nya regler.

2.  **Anpassade tjänstidentiteter med enkla regler**. Vissa kunder lägger till nya tjänstidentiteter och beviljar varje ny tjänstidentitet **Skicka,** **Lyssna**och **Hantera** behörigheter för en viss entitet.

3.  **Anpassade tjänstidentiteter med komplexa regler**. Mycket få kunder har komplexa regeluppsättningar där externt utfärdade token mappas till rättigheter på Relay, eller där en enda tjänstidentitet tilldelas differentierade rättigheter på flera namnområdessökvägar via flera regler.

Om du vill ha hjälp med migrering av komplexa regeluppsättningar kan du kontakta [Azure-supporten](https://azure.microsoft.com/support/options/). De andra två scenarierna möjliggör enkel migrering.

### <a name="unchanged-defaults"></a>Oförändrade standardvärden

Om ditt program inte har ändrat ACS-standardvärden kan du ersätta all [SharedSecretTokenProvider-användning](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider) med ett [SharedAccessSignatureTokenProvider-objekt](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) och använda namnområdets förkonfigurerade **RootManageSharedAccessKey** i stället för ACS-ägarkontot. **owner** Observera att även med **ACS-ägarkontot** var den här konfigurationen (och är fortfarande) vanligtvis inte rekommenderad, eftersom det här kontot/regeln ger fullständig hanteringsbehörighet över namnområdet, inklusive behörighet att ta bort alla entiteter.

### <a name="simple-rules"></a>Enkla regler

Om programmet använder anpassade tjänstidentiteter med enkla regler är migreringen enkel om en ACS-tjänstidentitet skapades för att ge åtkomstkontroll på ett visst relä. Det här scenariot är ofta fallet i SaaS-liknande lösningar där varje relä används som en brygga till en klientplats eller filialkontor och tjänstidentiteten skapas för den platsen. I det här fallet kan respektive tjänstidentitet migreras till en regel för delad åtkomstsignatur, direkt på reläet. Tjänstidentitetsnamnet kan bli SAS-regelnamnet och tjänstidentitetsnyckeln kan bli SAS-regelnyckeln. Rättigheterna för SAS-regeln konfigureras sedan som motsvarar den respektive tillämpliga ACS-regeln för entiteten.

Du kan göra den här nya och ytterligare konfigurationen av SAS på plats på alla befintliga namnområden som är federerade med ACS, och migreringen bort från ACS utförs därefter med [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) i stället för [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider). Namnområdet behöver inte kopplas bort från ACS.

### <a name="complex-rules"></a>Komplexa regler

SAS-regler är inte avsedda att vara konton, men namnges signeringsnycklar som är associerade med rättigheter. Scenarier där programmet skapar många tjänstidentiteter och ger dem åtkomsträttigheter för flera entiteter eller hela namnområdet kräver fortfarande en tokenutgivande mellanhand. Du kan få vägledning för en sådan mellanhand genom [att kontakta supporten](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Relay-autentisering finns i följande avsnitt:

* [Azure Relay-autentisering och auktorisering](relay-authentication-and-authorization.md)
* [Service Bus-autentisering med signaturer för delad åtkomst](../service-bus-messaging/service-bus-sas.md)


