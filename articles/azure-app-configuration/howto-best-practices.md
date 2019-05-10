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
ms.openlocfilehash: d1275a48de5cad9321186ba20860d853b8ce55ad
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65413624"
---
# <a name="azure-app-configuration-best-practices"></a>Metodtips för Azure Appkonfiguration

Den här artikeln beskriver vanliga mönster och metoder när du använder Azure-Appkonfiguration.

## <a name="key-groupings"></a>Viktiga grupperingar

Konfiguration av ger två alternativ för att ordna nycklar: nyckeln prefix eller etiketter. Du kan använda antingen den ena eller båda.

Viktiga prefixen är början delar av nycklar. Du kan gruppera en uppsättning nycklar med hjälp av samma prefix i sina namn. Prefix kan innehålla flera komponenter som är anslutna till varandra med en avgränsare som `/`, vilket liknar en URL-sökväg för att skapa ett namnområde. Sådana hierarkier är användbara när du lagrar nycklar för många program, Komponenttjänster och miljöer i en konfiguration av butik. En viktig sak att tänka på är att nycklar vad din programkod refererar till om du vill hämta värdena för motsvarande inställningar. En nyckel får inte ändra eller så behöver du ändra koden varje gång som sker.

Etiketter är ett attribut för nycklar. De används för att skapa varianter av en nyckel. Du kan till exempel tilldela etiketter till flera versioner av en nyckel. En version kan vara en iteration, miljö eller övrig relevant information. Ditt program kan begära en helt annan uppsättning nyckel-värden genom att bara ange en annan etikett. Alla referenser till kan förblir oförändrade.

## <a name="key-value-compositions"></a>Nyckel / värde-sammansättningar

Konfiguration av behandlar alla nycklar som lagras med den som oberoende entiteter. Den försöker inte att härleda någon relation mellan nycklar eller ärver nyckelvärden baserat på deras hierarki. Du sammanställa flera uppsättningar med nycklar, men med hjälp av etiketter tillsammans med korrekt konfiguration stapling i programkoden.

Nu ska vi titta på ett exempel. Du har en inställning **Asset1** vars värde kan variera för ”utvecklingsmiljön”. Du kan skapa en nyckel med namnet ”Asset1” med en tom etikett och en etikett som heter ”utveckling”. Du lägger till standardvärdet för **Asset1** i den tidigare versionen och eventuella specifikt värde för ”utveckling” i det senare. I koden hämta du först nyckelvärdena utan vilken etikett som helst och sedan de med en ”utveckling”-etikett för att skriva över eventuella tidigare värden av samma nycklar. Om du använder en modern programmeringsramverket, till exempel .NET Core, får du den här stapla funktionen kostnadsfritt om du använder en intern konfigurationsprovider för att komma åt appen. Följande kodfragment visar hur du kan implementera stapling i ett .NET Core-program.

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

Du kan använda sin anslutningssträng som är tillgänglig i Azure-portalen för att komma åt en Appkonfiguration butik. Anslutningssträngar innehålla autentiseringsinformation och betraktas som hemligheter. De måste lagras i Key Vault. Ett bättre alternativ är att använda Azure-hanterad identitet. Med den här metoden behöver du bara konfiguration av slutpunkts-URL ska kunna starta åtkomst till din konfiguration av lagring. Du kan bädda in URL: en i din programkod (till exempel i den *appsettings.json* fil). Se [integrera med Azure hanterade identiteter](howto-integrate-azure-managed-service-identity.md) för mer information.

## <a name="web-app-or-function-access-to-app-configuration"></a>Webbapp eller funktion åtkomst till Appkonfiguration

Du kan ange anslutningssträngen till store Appkonfiguration i programinställningarna i App Service via Azure portal. Du kan också lagra det i Key Vault och [refereras till från App Service](https://docs.microsoft.com/azure/app-service/app-service-key-vault-references). Du kan också använda Azure-hanterad identitet för att få åtkomst till konfigurationslagringen. Se [integrera med Azure hanterade identiteter](howto-integrate-azure-managed-service-identity.md) för mer information.

Du kan också skicka konfiguration från Appkonfiguration till App Service. Konfiguration av ger en export-funktion (i Azure portal och CLI) som skickar data direkt i App Service. Med den här metoden kan behöver du inte ändra programkoden alls.

## <a name="next-steps"></a>Nästa steg

* [Nycklar och värden](./concept-key-value.md)
