---
title: Azure Appkonfiguration vanliga frågor och svar | Microsoft Docs
description: Vanliga frågor och svar om konfiguration av Azure
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: e321c0b473b110597b5b87a6e67666737116daa2
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393354"
---
# <a name="azure-app-configuration-faq"></a>Azure Appkonfiguration vanliga frågor och svar

Den här artikeln tar upp vanliga frågor och svar om konfiguration av Azure.

## <a name="how-is-app-configuration-different-from-azure-key-vault"></a>Vad är skillnaden mellan App Configuration och Azure Key Vault?

Konfiguration av har utformats för en specifik uppsättning användningsfall: Det hjälper utvecklare att hantera inställningar för program och kontrollera tillgängliga funktioner. Syftet är att förenkla uppgifter för att arbeta med komplexa konfigurationsdata.

Har stöd för Appkonfiguration:

- Hierarkisk namnområden
- Märkning
- Omfattande frågor
- Batch-hämtning
- Specialiserad hanteringsåtgärder
- Ett användargränssnitt för hantering av funktionen

Konfiguration av är kompletterande till Key Vault och två ska användas sida vid sida i de flesta programdistributioner.

## <a name="should-i-store-secrets-in-app-configuration"></a>Ska jag lagra hemligheter i Appkonfiguration?

Även om Appkonfiguration ger förstärkt säkerhet, Key Vault är fortfarande den bästa platsen för att lagra programhemligheter. Key Vault ger kryptering på enhetsnivå maskinvara, detaljerade åtkomstprinciper och hanteringsåtgärder, till exempel certifikatrotering.

## <a name="does-app-configuration-encrypt-my-data"></a>Krypterar Appkonfiguration Mina data?

Ja. Konfiguration av krypterar alla nyckelvärden som den innehåller och krypteras nätverkskommunikation. Nyckelnamn används som index för att hämta konfigurationsdata och inte är krypterad.

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>Hur ska jag lagra konfigurationer för flera miljöer (testning, mellanlagring, produktion och så vidare)?

För närvarande kan du styra vem som har åtkomst till konfiguration på en per store-nivå. Använd ett annat lager för varje miljö som kräver olika behörigheter. Den här metoden ger dig bästa säkerhetsisolering.

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>Vad är den rekommenderade sätten att använda konfiguration?

Se [bästa praxis](./howto-best-practices.md).

## <a name="how-much-does-app-configuration-cost"></a>Hur mycket kostar konfiguration?

Tjänsten är kostnadsfritt att använda den offentliga förhandsversionen.

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>Hur kan jag rapportera ett problem eller ge förslag?

Du kan nå oss direkt på [GitHub](https://github.com/Azure/AppConfiguration/issues).

## <a name="next-steps"></a>Nästa steg

* [Om konfiguration av Azure](./overview.md)
