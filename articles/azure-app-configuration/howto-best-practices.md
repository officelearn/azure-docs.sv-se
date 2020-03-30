---
title: Metodtips för Konfiguration av Azure App | Microsoft-dokument
description: Lär dig hur du bäst använder Azure App-konfiguration
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
ms.openlocfilehash: df56f53b64a35737700529b80c004efeb31eaabc
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348667"
---
# <a name="azure-app-configuration-best-practices"></a>Metodtips för konfiguration av Azure App

I den här artikeln beskrivs vanliga mönster och metodtips när du använder Azure App Configuration.

## <a name="key-groupings"></a>Viktiga grupperingar

Appkonfiguration innehåller två alternativ för att ordna nycklar:

* Viktiga prefix
* Etiketter

Du kan använda antingen ett eller båda alternativen för att gruppera dina nycklar.

*Nyckelprefix* är början delar av nycklar. Du kan logiskt gruppera en uppsättning nycklar med samma prefix i deras namn. Prefix kan innehålla flera komponenter som `/`är anslutna med en avgränsare, till exempel , liknande en URL-sökväg, för att skapa ett namnområde. Sådana hierarkier är användbara när du lagrar nycklar för många program, komponenttjänster och miljöer i ett App Configuration Store.

En viktig sak att tänka på är att nycklar är vad din programkod refererar till för att hämta värdena för motsvarande inställningar. Nycklarna ska inte ändras, annars måste du ändra koden varje gång det händer.

*Etiketter* är ett attribut på tangenter. De används för att skapa varianter av en nyckel. Du kan till exempel tilldela etiketter till flera versioner av en nyckel. En version kan vara en iteration, en miljö eller annan kontextuell information. Ditt program kan begära en helt annan uppsättning nyckelvärden genom att ange en annan etikett. Därför förblir alla nyckelreferenser oförändrade i koden.

## <a name="key-value-compositions"></a>Nyckelvärdessammansättningar

Appkonfiguration behandlar alla nycklar som lagras med den som oberoende entiteter. Appkonfiguration försöker inte dra slutsatsen någon relation mellan nycklar eller att ärva nyckelvärden baserat på deras hierarki. Du kan dock aggregera flera uppsättningar nycklar genom att använda etiketter i kombination med korrekt konfigurationsstapling i programkoden.

Nu ska vi titta på ett exempel. Anta att du har en inställning med namnet **Asset1**, vars värde kan variera beroende på utvecklingsmiljön. Du skapar en nyckel med namnet "Asset1" med en tom etikett och en etikett med namnet "Utveckling". I den första etiketten sätter du standardvärdet för **Asset1**och lägger ett visst värde för "Utveckling" i den senare.

I koden hämtar du först nyckelvärdena utan etiketter och hämtar sedan samma uppsättning nyckelvärden en andra gång med etiketten "Utveckling". När du hämtar värdena andra gången skrivs nycklarnas tidigare värden över. Med .NET Core-konfigurationssystemet kan du "stapla" flera uppsättningar konfigurationsdata ovanpå varandra. Om det finns en nyckel i mer än en uppsättning används den sista uppsättningen som innehåller den. Med ett modernt programmeringsramverk, till exempel .NET Core, får du den här staplingsfunktionen gratis om du använder en inbyggd konfigurationsleverantör för att komma åt App-konfigurationen. Följande kodavsnitt visar hur du kan implementera stapling i ett .NET Core-program:

```csharp
// Augment the ConfigurationBuilder with Azure App Configuration
// Pull the connection string from an environment variable
configBuilder.AddAzureAppConfiguration(options => {
    options.Connect(configuration["connection_string"])
           .Select(KeyFilter.Any, LabelFilter.Null)
           .Select(KeyFilter.Any, "Development");
});
```

[Använd etiketter för att aktivera olika konfigurationer för olika miljöer](./howto-labels-aspnet-core.md) ger ett komplett exempel.

## <a name="app-configuration-bootstrap"></a>Bootstrap för appkonfiguration

Om du vill komma åt ett App Configuration Store kan du använda dess anslutningssträng, som är tillgänglig i Azure-portalen. Eftersom anslutningssträngar innehåller information om autentiseringsuppgifter betraktas de som hemligheter. Dessa hemligheter måste lagras i Azure Key Vault och din kod måste autentisera till Key Vault för att hämta dem.

Ett bättre alternativ är att använda funktionen hanterade identiteter i Azure Active Directory. Med hanterade identiteter behöver du bara slutpunkts-URL:en för appkonfiguration för att få åtkomst till appkonfigurationsarkivet. Du kan bädda in URL:en i programkoden (till exempel i *filen appsettings.json).* Mer information finns i [Integrera med Azure-hanterade identiteter.](howto-integrate-azure-managed-service-identity.md)

## <a name="app-or-function-access-to-app-configuration"></a>App- eller funktionsåtkomst till appkonfiguration

Du kan ge åtkomst till appkonfiguration för webbappar eller webbfunktioner med någon av följande metoder:

* Via Azure-portalen anger du anslutningssträngen till appkonfigurationsarkivet i programinställningarna för App-tjänsten.
* Lagra anslutningssträngen till App Configuration Store i Key Vault och [referera till den från App Service](https://docs.microsoft.com/azure/app-service/app-service-key-vault-references).
* Använd Azure-hanterade identiteter för att komma åt App Configuration Store. Mer information finns i [Integrera med Azure-hanterade identiteter](howto-integrate-azure-managed-service-identity.md).
* Push-konfiguration från appkonfiguration till apptjänst. Appkonfiguration ger en exportfunktion (i Azure-portalen och Azure CLI) som skickar data direkt till App Service. Med den här metoden behöver du inte ändra programkoden alls.

## <a name="reduce-requests-made-to-app-configuration"></a>Minska begäranden till appkonfiguration

Överdrivna begäranden till appkonfiguration kan leda till begränsnings- eller överkörningsavgifter. Så här minskar du antalet begäranden som görs:

* Öka tidsgränsen för uppdatering, särskilt om konfigurationsvärdena inte ändras ofta. Ange en ny tidsgränsen för uppdatering med [ `SetCacheExpiration` metoden](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.azureappconfigurationrefreshoptions.setcacheexpiration).

* Titta på en enda *sentinel nyckel*, snarare än att titta på enskilda nycklar. Uppdatera endast all konfiguration om indikatornyckeln ändras. Se [Använda dynamisk konfiguration i en ASP.NET Core-app](enable-dynamic-configuration-aspnet-core.md) för ett exempel.

* Använd Azure Event Grid för att ta emot meddelanden när konfigurationen ändras, i stället för att ständigt avsöka för eventuella ändringar. Mer information finns i Händelser för [konfiguration av Rout Azure-app till en webbslutpunkt](./howto-app-configuration-event.md) för mer information

## <a name="importing-configuration-data-into-app-configuration"></a>Importera konfigurationsdata till appkonfiguration

Appkonfiguration erbjuder möjligheten att [massimport importera](https://aka.ms/azconfig-importexport1) dina konfigurationsinställningar från dina aktuella konfigurationsfiler med antingen Azure-portalen eller CLI. Du kan också använda samma alternativ för att exportera värden från appkonfiguration, till exempel mellan relaterade butiker. Om du vill konfigurera en pågående synkronisering med din GitHub-repo kan du använda vår [GitHub-åtgärd](https://aka.ms/azconfig-gha2) så att du kan fortsätta använda dina befintliga källkontrollmetoder samtidigt som du får fördelarna med appkonfiguration.

## <a name="next-steps"></a>Nästa steg

* [Nycklar och värden](./concept-key-value.md)
