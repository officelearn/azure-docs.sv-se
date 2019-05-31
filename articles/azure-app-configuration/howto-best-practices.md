---
title: Metodtips för Azure Appkonfiguration | Microsoft Docs
description: Lär dig hur du bäst använder Azure-Appkonfiguration
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
ms.openlocfilehash: 3d9a597e7ced631627a121f3f0757e472f9a4bae
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393577"
---
# <a name="azure-app-configuration-best-practices"></a>Metodtips för Azure Appkonfiguration

Den här artikeln beskriver vanliga mönster och metodtips när du använder Azure-Appkonfiguration.

## <a name="key-groupings"></a>Viktiga grupperingar

Konfiguration av ger två alternativ för att ordna nycklar:

* Viktiga prefix
* Etiketter

Du kan använda en eller båda alternativen för att gruppera dina nycklar.

*Nyckeln prefix* början delar av nycklar. Du kan gruppera en uppsättning nycklar med hjälp av samma prefix i sina namn. Prefix kan innehålla flera komponenter som är anslutna via en avgränsare som `/`, vilket liknar en URL-sökväg för att skapa ett namnområde. Sådana hierarkier är användbara när du lagrar nycklar för många program, Komponenttjänster och miljöer i en konfiguration av store.

En viktig sak att tänka på är att nycklar vad din programkod refererar till om du vill hämta värdena för motsvarande inställningar. Nycklar bör inte ändra, eller så måste du ändra din kod varje gång som sker.

*Etiketter* är ett attribut för nycklar. De används för att skapa varianter av en nyckel. Du kan till exempel tilldela etiketter till flera versioner av en nyckel. En version kan vara en iteration, en miljö eller övrig relevant information. Ditt program kan begära en helt annan uppsättning värden genom att ange en annan etikett. Därför kan förblir alla referenser till oförändrade i din kod.

## <a name="key-value-compositions"></a>Nyckel / värde-sammansättningar

Konfiguration av behandlar alla nycklar som lagras med den som oberoende entiteter. Konfiguration av försöka inte att skapa någon relation mellan nycklar eller om du vill ärva nyckelvärden baserat på deras hierarki. Du kan aggregera flera uppsättningar med nycklar, men med hjälp av etiketter som tillsammans med korrekt konfiguration stapling i programkoden.

Nu ska vi titta på ett exempel. Anta att du har en inställning med namnet **Asset1**, vars värde kan skilja sig utifrån utvecklingsmiljön. Du skapar en nyckel med namnet ”Asset1” med en tom etikett och en etikett med namnet ”utveckling”. I den första etiketten, lägger du standardvärdet för **Asset1**, och du lägger till ett specifikt värde för ”utveckling” i det senare.

Du först hämta nyckelvärdena utan några etiketter i din kod, och sedan du hämta samma uppsättning värden en gång med etiketten ”utveckling”. När du hämtar värdena på den andra gången skrivs tidigare värden för nycklarna. Konfigurationssystemet för .NET Core kan du ”stack” flera uppsättningar med konfigurationsdata ovanpå varandra. Om det finns en nyckel i mer än en uppsättning, används den senaste uppsättningen som innehåller den. Med en modern programmeringsramverket, till exempel .NET Core, får du den här stapla funktionen kostnadsfritt om du använder en intern konfigurationsprovider för att komma åt appen. Följande kodfragment visar hur du kan implementera stapling i ett .NET Core-program:

```csharp
// Augment the ConfigurationBuilder with Azure App Configuration
// Pull the connection string from an environment variable
configBuilder.AddAzureAppConfiguration(options => {
    options.Connect(configuration["connection_string"])
           .Use(KeyFilter.Any, LabelFilter.Null)
           .Use(KeyFilter.Any, "Development");
});
```

## <a name="app-configuration-bootstrap"></a>Starttjänsten för App-konfiguration

Du kan använda sin anslutningssträng som är tillgänglig i Azure-portalen för att komma åt en Appkonfiguration butik. Eftersom anslutningssträngar innehålla autentiseringsinformation, betraktas de som hemligheter. Dessa hemligheter måste lagras i Azure Key Vault och din kod måste autentisera till Key Vault för att hämta dem.

Ett bättre alternativ är att använda funktionen hanterade identiteter i Azure Active Directory. Med hanterade identiteter behöver du bara konfiguration av slutpunkts-URL till bootstrap åtkomst till store Appkonfiguration. Du kan bädda in URL: en i din programkod (till exempel i den *appsettings.json* fil). Se [integrera med Azure hanterade identiteter](howto-integrate-azure-managed-service-identity.md) mer information.

## <a name="app-or-function-access-to-app-configuration"></a>App eller funktion åtkomst till Appkonfiguration

Du kan ge åtkomst till Appkonfiguration för webbappar eller funktioner genom att använda någon av följande metoder:

* Ange anslutningssträngen till store Appkonfiguration i programinställningarna i App Service via Azure-portalen.
* Store anslutningssträngen till din Appkonfiguration av lagra i Key Vault och [refereras till från App Service](https://docs.microsoft.com/azure/app-service/app-service-key-vault-references).
* Använd Azure hanterade identiteter åtkomst till konfiguration av store. Mer information finns i [integrera med Azure hanterade identiteter](howto-integrate-azure-managed-service-identity.md).
* Push-konfiguration från Appkonfiguration till App Service. Konfiguration av ger en export-funktion (i Azure-portalen och Azure CLI) som skickar data direkt i App Service. Med den här metoden behöver du inte ändra programkoden alls.

## <a name="next-steps"></a>Nästa steg

* [Nycklar och värden](./concept-key-value.md)
