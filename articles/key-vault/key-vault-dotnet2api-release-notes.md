---
title: Viktiga valv .NET 2.x API-versionsanteckningar| Microsoft-dokument
description: .NET-utvecklare använder det här API:et för att koda för Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
editor: bryanla
ms.service: key-vault
ms.topic: conceptual
ms.date: 05/02/2017
ms.author: mbaldwin
ms.openlocfilehash: 07502b4c4487a7517e2fe73b62eae0a237a6f22b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70883266"
---
# <a name="azure-key-vault-net-20---release-notes-and-migration-guide"></a>Azure Key Vault .NET 2.0 – versionsanteckningar och migreringsguide
Följande information hjälper till att migrera till 2.0-versionen av Azure Key Vault-biblioteket för C# och .NET.  Appar som skrivits för tidigare versioner måste uppdateras för att stödja den senaste versionen.  Dessa ändringar behövs för att fullt ut stödja nya och förbättrade funktioner, till exempel **Key Vault-certifikat**.

## <a name="key-vault-certificates"></a>Certifikat för nyckelvalv

Key Vault-certifikat hanterar x509-certifikat och stöder följande beteenden:  

* Skapa certifikat genom en skapandeprocess för nyckelvalv eller importera befintligt certifikat. Detta inkluderar både självsignerade certifikat och certifikatutfärdare (CA) genererade certifikat.
* Lagra och hantera x509-certifikatlagring på ett säkert sätt utan interaktion med hjälp av privat nyckelmaterial.  
* Definiera principer som direkter Key Vault för att hantera certifikatets livscykel.  
* Ange kontaktinformation för livscykelhändelser, till exempel förfallovarningar och förnyelsemeddelanden.  
* Förnya certifikat automatiskt med valda utfärdare (Key Vault-partner X509-certifikatleverantörer och certifikatutfärdare).* Supportcertifikat från alternativa (icke-partner) tillhandahåller och certifikatutfärdare (stöder inte automatisk förnyelse).  

## <a name="net-support"></a>.NET-stöd

* **.NET 4.0** stöds inte av 2.0-versionen av Azure Key Vault .NET-biblioteket
* **.NET Framework 4.5.2** stöds av 2.0-versionen av Azure Key Vault .NET-biblioteket
* **.NET Standard 1.4** stöds av 2.0-versionen av Azure Key Vault .NET-biblioteket

## <a name="namespaces"></a>Namnområden

* Namnområdet för **modeller** ändras från **Microsoft.Azure.KeyVault** till **Microsoft.Azure.KeyVault.Models**.
* Namnområdet **Microsoft.Azure.KeyVault.Internal** har tagits bort.
* Följande Azure SDK-beroendennamnområden har 

    - **Hyak.Common** är nu **Microsoft.Rest**.
    - **Hyak.Common.Internals** är nu **Microsoft.Rest.Serialization**.

## <a name="type-changes"></a>Skriv ändringar

* *Hemligheten* har ändrats till *SecretBundle*
* *Ordlistan* har ändrats till *IDictionary*
* *Lista\<T>, sträng []* ändrats till *\<IList T>*
* *NextList* har ändrats till *NextPageLink*

## <a name="return-types"></a>Returtyper

* **KeyList** och **SecretList** returnerar nu *IPage\<T>* i stället för *ListKeysResponseMessage*
* Den genererade **BackupKeyAsync** returnerar nu *BackupKeyResult*, som innehåller *värde* (back-up blob). Tidigare var metoden insvept och returnerade bara värdet.

## <a name="exceptions"></a>Undantag

* *KeyVaultClientException* har ändrats till *KeyVaultErrorException*
* Servicefelet har ändrats från *undantag. Fel* i *undantagsfel. Body.Error.Message*.
* Tog bort ytterligare information från felmeddelandet för **[JsonExtensionData]**.

## <a name="constructors"></a>Konstruktorer

* I stället för att acceptera ett *HttpClient* som ett konstruktorarargument accepterar konstruktorn endast *HttpClientHandler* eller *DelegatingHandler[]*.

## <a name="downloaded-packages"></a>Nedladdade paket

När en klient bearbetar ett Key Vault-samband hämtas följande paket:

### <a name="previous-package-list"></a>Tidigare paketlista

* `package id="Hyak.Common" version="1.0.2" targetFramework="net45"`
* `package id="Microsoft.Azure.Common" version="2.0.4" targetFramework="net45"`
* `package id="Microsoft.Azure.Common.Dependencies" version="1.0.0" targetFramework="net45"`
* `package id="Microsoft.Azure.KeyVault" version="1.0.0" targetFramework="net45"`
* `package id="Microsoft.Bcl" version="1.1.9" targetFramework="net45"`
* `package id="Microsoft.Bcl.Async" version="1.0.168" targetFramework="net45"`
* `package id="Microsoft.Bcl.Build" version="1.0.14" targetFramework="net45"`
* `package id="Microsoft.Net.Http" version="2.2.22" targetFramework="net45"`

### <a name="current-package-list"></a>Aktuell paketlista

* `package id="Microsoft.Azure.KeyVault" version="2.0.0-preview" targetFramework="net45"`
* `package id="Microsoft.Rest.ClientRuntime" version="2.2.0" targetFramework="net45"`
* `package id="Microsoft.Rest.ClientRuntime.Azure" version="3.2.0" targetFramework="net45"`

## <a name="class-changes"></a>Klassändringar

* **UnixEpoch-klassen** har tagits bort.
* **Klassen Base64UrlConverter** har bytt namn till **Base64UrlJsonConverter**.

## <a name="other-changes"></a>Andra ändringar

* Stöd för konfigurationen av KV-åtgärdens återförsöksprincip vid tillfälliga fel har lagts till i den här versionen av API:et.

## <a name="microsoftazuremanagementkeyvault-nuget"></a>Microsoft.Azure.Management.KeyVault NuGet

* För de åtgärder som returnerade ett *valv*var returtypen en klass som innehöll **egenskapen Arkiv.** Returtypen är nu *Arkiv*.
* *BehörigheterTill tangenter* och *behörigheterTillSecrets* är nu *Permissions.Keys* and *Permissions.Secrets*
* Vissa ändringar av returtyper gäller även för styrplanet.

## <a name="microsoftazurekeyvaultextensions-nuget"></a>Microsoft.Azure.KeyVault.Extensions NuGet

* Paketet bryts upp till **Microsoft.Azure.KeyVault.Extensions** och **Microsoft.Azure.KeyVault.Cryptography** för kryptografins åtgärder.

