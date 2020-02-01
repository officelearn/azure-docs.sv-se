---
title: Metod tips för Azure App konfiguration | Microsoft Docs
description: Lär dig hur du bäst använder Azure App konfiguration
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
ms.openlocfilehash: 37f93099027f810e8089119536e089e07080d0bc
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2020
ms.locfileid: "76898629"
---
# <a name="azure-app-configuration-best-practices"></a>Metod tips för Azure App konfiguration

I den här artikeln beskrivs vanliga mönster och bästa metoder när du använder Azure App konfiguration.

## <a name="key-groupings"></a>Nyckel grupper

Konfiguration av appar innehåller två alternativ för att ordna nycklar:

* Viktiga prefix
* Etiketter

Du kan använda antingen ett eller båda alternativen för att gruppera dina nycklar.

*Viktiga prefix* är start delarna av nycklar. Du kan logiskt gruppera en uppsättning nycklar genom att använda samma prefix i deras namn. Prefix kan innehålla flera komponenter som är anslutna med en avgränsare, till exempel `/`, ungefär som en URL-sökväg, för att bilda ett namn område. Sådana hierarkier är användbara när du lagrar nycklar för många program, komponent tjänster och miljöer i ett konfigurations lager för appar.

Ett viktigt att tänka på är att nycklarna är vad din program kod refererar till för att hämta värdena för motsvarande inställningar. Nycklar bör inte ändras eller så måste du ändra koden varje stund.

*Etiketter* är ett attribut för nycklar. De används för att skapa varianter av en nyckel. Du kan till exempel tilldela etiketter till flera versioner av en nyckel. En version kan vara en iteration, en miljö eller någon annan sammanhangsbaserad information. Ditt program kan begära en helt annan uppsättning nyckel värden genom att ange en annan etikett. Därför förblir alla viktiga referenser oförändrade i koden.

## <a name="key-value-compositions"></a>Nyckel värdes kompositioner

App Configuration behandlar alla nycklar som lagras med den som oberoende entiteter. App-konfigurationen försöker inte härleda någon relation mellan nycklar eller ärva nyckel värden baserat på deras hierarki. Du kan använda flera uppsättningar med nycklar, men med hjälp av etiketter tillsammans med korrekt konfigurations stack i program koden.

Nu ska vi titta på ett exempel. Anta att du har en inställning med namnet **Asset1**, vars värde kan variera beroende på utvecklings miljön. Du skapar en nyckel med namnet "Asset1" med en tom etikett och en etikett med namnet "Development". I den första etiketten sätter du standardvärdet för **Asset1**och du anger ett särskilt värde för "utveckling" i den senare.

I din kod hämtar du först nyckel värden utan några etiketter, och sedan hämtar du samma uppsättning nyckel värden en andra gång med etiketten "utveckling". När du hämtar värdena den andra gången skrivs de tidigare värdena i nycklarna över. Med konfigurations systemet för .NET Core kan du "stacka" flera uppsättningar konfigurations data ovanpå varandra. Om det finns en nyckel i fler än en uppsättning används den sista uppsättningen som innehåller den. Med ett modernt programmerings ramverk, till exempel .NET Core, får du den här stack funktionen kostnads fritt om du använder en inbyggd Konfigurationsprovider för att komma åt appens konfiguration. Följande kodfragment visar hur du kan implementera stackning i ett .NET Core-program:

```csharp
// Augment the ConfigurationBuilder with Azure App Configuration
// Pull the connection string from an environment variable
configBuilder.AddAzureAppConfiguration(options => {
    options.Connect(configuration["connection_string"])
           .Select(KeyFilter.Any, LabelFilter.Null)
           .Select(KeyFilter.Any, "Development");
});
```

## <a name="app-configuration-bootstrap"></a>Start av app-konfiguration

Om du vill komma åt ett konfigurations lager för appar kan du använda dess anslutnings sträng, som finns i Azure Portal. Eftersom anslutnings strängar innehåller autentiseringsinformation anses de vara hemligheter. Dessa hemligheter måste lagras i Azure Key Vault och din kod måste autentiseras för att Key Vault ska kunna hämta dem.

Ett bättre alternativ är att använda funktionen hanterade identiteter i Azure Active Directory. Med hanterade identiteter behöver du bara slut punkts-URL: en för programmets konfiguration för att starta åtkomst till appens konfigurations lager. Du kan bädda in webb adressen i din program kod (till exempel i filen *appSettings. JSON* ). Mer information finns i [integrera med Azure Managed Identities](howto-integrate-azure-managed-service-identity.md) .

## <a name="app-or-function-access-to-app-configuration"></a>App-eller funktions åtkomst till app-konfiguration

Du kan ge åtkomst till app-konfiguration för webbappar eller funktioner med någon av följande metoder:

* Ange anslutnings strängen till appens konfigurations Arkiv i program inställningarna för App Service Azure Portal.
* Lagra anslutnings strängen i appens konfigurations Arkiv i Key Vault och [referera den från App Service](https://docs.microsoft.com/azure/app-service/app-service-key-vault-references).
* Använd Azure Managed Identities för att få åtkomst till appens konfigurations arkiv. Mer information finns i [integrera med Azure Managed Identities](howto-integrate-azure-managed-service-identity.md).
* Push-konfiguration från App Configuration till App Service. App-konfigurationen tillhandahåller en export funktion (i Azure Portal och Azure CLI) som skickar data direkt till App Service. Med den här metoden behöver du inte ändra program koden alls.

## <a name="next-steps"></a>Nästa steg

* [Nycklar och värden](./concept-key-value.md)
