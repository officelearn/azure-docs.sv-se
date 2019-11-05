---
title: Vanliga frågor om Azure App konfiguration | Microsoft Docs
description: Vanliga frågor om Azure App konfiguration
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: dbfb6a1c4c53b1bd255560e688d3dc0cf3835a3a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73469634"
---
# <a name="azure-app-configuration-faq"></a>Vanliga frågor om Azure App konfiguration

Den här artikeln handlar om vanliga frågor om Azure App-konfiguration.

## <a name="how-is-app-configuration-different-from-azure-key-vault"></a>Vad är skillnaden mellan App Configuration och Azure Key Vault?

App-konfigurationen är utformad för en distinkt uppsättning användnings fall: den hjälper utvecklare att hantera program inställningar och kontrol lera tillgänglighet för funktioner. Det syftar till att förenkla många av aktiviteterna för att arbeta med komplexa konfigurations data.

Konfiguration av appar stöder:

- Hierarkiska namn områden
- märkning
- Omfattande frågor
- Hämta batch
- Specialiserade hanterings åtgärder
- Ett användar gränssnitt för funktions hantering

Konfiguration av appar kompletterar Key Vault och de två bör användas sida vid sida i de flesta program distributioner.

## <a name="should-i-store-secrets-in-app-configuration"></a>Bör jag lagra hemligheter i appens konfiguration?

Även om app-konfigurationen tillhandahåller härdnings säkerhet är Key Vault fortfarande den bästa platsen för att lagra program hemligheter. Key Vault tillhandahåller kryptering på maskin varu nivå, detaljerade åtkomst principer och hanterings åtgärder, till exempel certifikat rotation.

## <a name="does-app-configuration-encrypt-my-data"></a>Krypterar app-konfigurationen mina data?

Ja. App-konfigurationen krypterar alla nyckel värden som den innehåller och krypterar nätverkskommunikationen. Nyckel namn används som index för att hämta konfigurations data och är inte krypterade.

## <a name="are-there-any-size-limitations-on-keys-and-values-stored-in-app-configuration"></a>Finns det några storleks begränsningar för nycklar och värden som lagras i appens konfiguration?

Det finns en gräns på 10 kB för ett enskilt nyckel värdes objekt.

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>Hur ska jag lagra konfigurationer för flera miljöer (test, mellanlagring, produktion och så vidare)?

För närvarande kan du kontrol lera vem som har åtkomst till app-konfigurationen på per lagrings nivå. Använd en separat lagrings plats för varje miljö som kräver olika behörigheter. Den här metoden ger dig den bästa säkerhets isoleringen.

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>Vilka är de rekommenderade sätten att använda app Configuration?

Se [metod tips](./howto-best-practices.md).

## <a name="how-much-does-app-configuration-cost"></a>Hur mycket kostar det att konfigurera appar?

Tjänsten är kostnads fri att användas under den offentliga för hands versionen.

## <a name="how-can-i-receive-announcements-on-new-releases-and-other-information-related-to-app-configuration"></a>Hur kan jag få meddelanden om nya versioner och annan information om app-konfigurationen?

Prenumerera på vår [GitHub-annonsering lagrings platsen](https://github.com/Azure/AppConfiguration-Announcements).

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>Hur kan jag rapportera ett ärende eller lämna ett förslag?

Du kan kontakta oss direkt på [GitHub](https://github.com/Azure/AppConfiguration/issues).

## <a name="next-steps"></a>Nästa steg

* [Om Azure App konfiguration](./overview.md)
