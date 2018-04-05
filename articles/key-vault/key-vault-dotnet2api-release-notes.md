---
title: Nyckeln valvet .NET-API 2.x viktig information | Microsoft Docs
description: .NET-utvecklare använder detta API till kod för Azure Key Vault
services: key-vault
author: lleonard-msft
manager: mbaldwin
editor: alleonar
ms.assetid: 1cccf21b-5be9-4a49-8145-483b695124ba
ms.service: key-vault
ms.devlang: CSharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/02/2017
ms.author: alleonar
ms.openlocfilehash: a7735f8c1c4332bf2472bc83c0c37baf49019004
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="azure-key-vault-net-20---release-notes-and-migration-guide"></a>Azure Key Vault .NET 2.0 - viktig information och Migreringsguide
Följande information hjälper till att migrera till version 2.0 av Azure Key Vault-bibliotek för C# och .NET.  Appar som utvecklats för tidigare versioner måste du uppdatera för att stödja den senaste versionen.  De här ändringarna krävs för att fullständigt stöd för nya och förbättrade funktioner som **Key Vault certifikat**.

## <a name="key-vault-certificates"></a>Key Vault-certifikat

Key Vault certifikat hantera x509 certifikat och har stöd för följande:  

* Skapa certifikat via en process för att skapa Nyckelvalvet eller importera befintligt certifikat. Detta inkluderar både självsignerat och certifikatutfärdaren (CA) genereras certifikat.
* Lagra och hantera x509 säker lagring av certifikat utan interaktion med material för privat nyckel.  
* Definiera principer som direkt Key Vault för att hantera livscykeln för användarcertifikatet.  
* Ange kontaktinformation för Livscykelhändelser, som upphör att gälla varningar och meddelanden för förnyelse.  
* Automatiskt förnya certifikat med valda utfärdare (Key Vault partner X509 certifikatproviders och certifikatutfärdare). * stöd certifikat från en alternativ (icke-partner) ger och certifikatutfärdare (stöder inte automatisk förnyelse).  

## <a name="net-support"></a>Stöd för .NET

* **.NET 4.0** stöds inte av version 2.0 av Azure Key Vault .NET-bibliotek
* **.NET framework 4.5.2** stöds av version 2.0 av Azure Key Vault .NET-bibliotek
* **.NET standard 1.4** stöds av version 2.0 av Azure Key Vault .NET-bibliotek

## <a name="namespaces"></a>Namnområden

* Namnområdet för **modeller** ändras från **Microsoft.Azure.KeyVault** till **Microsoft.Azure.KeyVault.Models**.
* Den **Microsoft.Azure.KeyVault.Internal** namnområde har släppts.
* Följande Azure SDK beroenden namnområden har 

    - **Hyak.Common** är nu **Microsoft.Rest**.
    - **Hyak.Common.Internals** är nu **Microsoft.Rest.Serialization**.

## <a name="type-changes"></a>Ändringar av typen

* *Hemligt* ändras till *SecretBundle*
* *Ordlistan* ändras till *IDictionary*
* *Lista<T>, string []* ändras till *IList<T>*
* *NextList* ändras till *NextPageLink*

## <a name="return-types"></a>Returtyper

* **KeyList** och **SecretList** returnerar nu *IPage<T>*  i stället för *ListKeysResponseMessage*
* Den genererade **BackupKeyAsync** returnerar nu *BackupKeyResult*, som innehåller *värdet* (säkerhetskopiera blob). Metoden har tidigare omsluten och returneras bara värdet.

## <a name="exceptions"></a>Undantag

* *KeyVaultClientException* ändras till *KeyVaultErrorException*
* Fel i tjänsten har ändrats från *undantag. Fel* till *undantag. Body.Error.Message*.
* Ytterligare information bort från felmeddelandet för **[JsonExtensionData]**.

## <a name="constructors"></a>Konstruktorer

* I stället för att acceptera ett *HttpClient* som ett konstruktorargument konstruktorn accepterar bara *HttpClientHandler* eller *DelegatingHandler []*.

## <a name="downloaded-packages"></a>Hämtade paket

När en klient bearbetar ett Key Vault beroende, hämtas följande paket:

### <a name="previous-package-list"></a>Tidigare paketlistan

* `package id="Hyak.Common" version="1.0.2" targetFramework="net45"`
* `package id="Microsoft.Azure.Common" version="2.0.4" targetFramework="net45"`
* `package id="Microsoft.Azure.Common.Dependencies" version="1.0.0" targetFramework="net45"`
* `package id="Microsoft.Azure.KeyVault" version="1.0.0" targetFramework="net45"`
* `package id="Microsoft.Bcl" version="1.1.9" targetFramework="net45"`
* `package id="Microsoft.Bcl.Async" version="1.0.168" targetFramework="net45"`
* `package id="Microsoft.Bcl.Build" version="1.0.14" targetFramework="net45"`
* `package id="Microsoft.Net.Http" version="2.2.22" targetFramework="net45"`

### <a name="current-package-list"></a>Aktuella paketlistan

* `package id="Microsoft.Azure.KeyVault" version="2.0.0-preview" targetFramework="net45"`
* `package id="Microsoft.Rest.ClientRuntime" version="2.2.0" targetFramework="net45"`
* `package id="Microsoft.Rest.ClientRuntime.Azure" version="3.2.0" targetFramework="net45"`

## <a name="class-changes"></a>Klassen ändringar

* **UnixEpoch** klass har tagits bort.
* **Base64UrlConverter** klass har bytt namn till **Base64UrlJsonConverter**.

## <a name="other-changes"></a>Andra ändringar

* Stöd för konfigurationen av KV åtgärden återförsöksprincip på tillfälliga fel har lagts till den här versionen av API: et.

## <a name="microsoftazuremanagementkeyvault-nuget"></a>Microsoft.Azure.Management.KeyVault NuGet

* För de åtgärder som returnerade en *valvet*, Returtypen har en klass som innehåller en **valvet** egenskapen. Returtypen är nu *valvet*.
* *PermissionsToKeys* och *PermissionsToSecrets* är nu *Permissions.Keys* och *Permissions.Secrets*
* Vissa returtyper ändringarna för kontroll-plan samt.

## <a name="microsoftazurekeyvaultextensions-nuget"></a>Microsoft.Azure.KeyVault.Extensions NuGet

* Paketet har brutits till **Microsoft.Azure.KeyVault.Extensions** och **Microsoft.Azure.KeyVault.Cryptography** för kryptografiska åtgärder.

