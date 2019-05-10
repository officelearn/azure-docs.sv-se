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
ms.openlocfilehash: 3181609bf34a04de4e31b73429f9bc5fa3fe3408
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65411903"
---
# <a name="azure-app-configuration-faq"></a>Azure Appkonfiguration vanliga frågor och svar

Den här artikeln adresser oftare frågor om konfiguration av Azure.

## <a name="how-is-app-configuration-different-from-key-vault"></a>Hur skiljer sig Appkonfiguration från Key Vault?

Konfiguration av har utformats för en specifik uppsättning användningsfall: Det hjälper utvecklare att hantera inställningar för program och kontrollera tillgängliga funktioner. Syftet är att förenkla uppgifter för att arbeta med komplexa konfigurationsdata. Det har stöd för hierarkiskt namnområde, etiketter, omfattande frågor, batch hämtning och specialiserade hanteringsåtgärder och användargränssnitt. Konfiguration av är kompletterande till Key Vault och två ska användas sida vid sida i de flesta programdistributioner.

## <a name="should-i-store-secrets-in-app-configuration"></a>Ska jag lagra hemligheter i Appkonfiguration?

Konfiguration av ger förstärkt säkerhet, är Key Vault fortfarande den bästa platsen för att lagra programhemligheter. Den tillhandahåller kryptering på enhetsnivå maskinvara, detaljerade åtkomstprinciper och hanteringsåtgärder, till exempel certifikatrotering.

## <a name="does-app-configuration-encrypt-my-data"></a>Krypterar Appkonfiguration Mina data?

Ja. Konfiguration av krypterar alla nyckelvärden som den innehåller och nätverkskommunikation. Nyckelnamn används som index i förväg för att hämta konfigurationsdata och som inte är krypterade.

## <a name="does-app-configuration-support-azure-virtual-network-vnet"></a>Stöder konfiguration av Azure Virtual Network (VNET)?

Inte ännu. VNET-stöd planeras för allmän tillgänglighet.

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>Hur ska jag lagra konfigurationer för flera miljöer (testning, mellanlagring, produktion och så vidare)?

För närvarande kan du styra vem som har åtkomst till konfiguration på en per store-nivå. Du bör använda ett annat lager för varje miljö som kräver olika behörigheter. Den här metoden ger dig bästa säkerhetsisolering.

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>Vad är den rekommenderade sätten att använda konfiguration?

Se [bästa praxis](./howto-best-practices.md).

## <a name="how-much-does-app-configuration-cost"></a>Hur mycket kostar konfiguration?

Tjänsten är kostnadsfritt att använda den offentliga förhandsversionen.

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>Hur kan jag rapportera ett problem eller ge förslag?

Du kan nå oss direkt på [GitHub](https://github.com/Azure/AppConfiguration/issues).

## <a name="next-steps"></a>Nästa steg

* [Om konfiguration av Azure](./overview.md)
