---
title: Vanliga frågor och svar om konfiguration av Azure App
description: Vanliga frågor och svar om Azure App-konfiguration
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: lcozzens
ms.openlocfilehash: 25187fd055f40e8b32d840ead2a9c54882446b88
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348792"
---
# <a name="azure-app-configuration-faq"></a>Vanliga frågor och svar om konfiguration av Azure App

Den här artikeln besvarar vanliga frågor om Azure App-konfiguration.

## <a name="how-is-app-configuration-different-from-azure-key-vault"></a>Hur skiljer sig appkonfiguration från Azure Key Vault?

Appkonfiguration hjälper utvecklare att hantera programinställningar och kontrollera funktionstillgänglighet. Det syftar till att förenkla många av uppgifterna att arbeta med komplexa konfigurationsdata.

Appkonfiguration stöder:

- Hierarkiska namnrymder
- Märkning
- Omfattande frågor
- Batchhämtning
- Specialiserad ledningsverksamhet
- Ett användargränssnitt för funktionshantering

Appkonfiguration kompletterar Key Vault och de två ska användas sida vid sida i de flesta programdistributioner.

## <a name="should-i-store-secrets-in-app-configuration"></a>Ska jag lagra hemligheter i appkonfiguration?

Även om App Configuration ger härdade säkerhet, är Key Vault fortfarande den bästa platsen för att lagra programhemligheter. Key Vault tillhandahåller kryptering på maskinvarunivå, detaljerade åtkomstprinciper och hanteringsåtgärder som certifikatrotation.

Du kan skapa appkonfigurationsvärden som refererar till hemligheter som lagras i Key Vault. Mer information finns [i Använda key vault-referenser i en ASP.NET Core-app](./use-key-vault-references-dotnet-core.md).

## <a name="does-app-configuration-encrypt-my-data"></a>Krypterar appkonfiguration mina data?

Ja. Appkonfiguration krypterar alla nyckelvärden som den har och krypterar nätverkskommunikation. Nyckelnamn och etiketter används som index för att hämta konfigurationsdata och krypteras inte.

## <a name="how-is-app-configuration-different-from-azure-app-service-settings"></a>Hur skiljer sig appkonfiguration från Azure App Service-inställningarna?

Med Azure App Service kan du definiera appinställningar för varje App Service-instans. Dessa inställningar skickas som miljövariabler till programkoden. Du kan associera en inställning med en specifik distributionsplats om du vill. Mer information finns i [Konfigurera appinställningar](/azure/app-service/configure-common#configure-app-settings).

Med Azure App-konfiguration kan du däremot definiera inställningar som kan delas mellan flera appar. Detta inkluderar appar som körs i App Service, liksom andra plattformar. Din programkod kommer åt dessa inställningar via konfigurationsleverantörerna för .NET och Java, via Azure SDK eller direkt via REST API:er.

Du kan också importera och exportera inställningar mellan App Service och App Configuration. Med den här funktionen kan du snabbt konfigurera ett nytt App Configuration Store baserat på befintliga apptjänstinställningar. Du kan också dela konfigurationen med en befintlig app som är beroende av apptjänstinställningar.

## <a name="are-there-any-size-limitations-on-keys-and-values-stored-in-app-configuration"></a>Finns det några storleksbegränsningar för nycklar och värden som lagras i appkonfiguration?

Det finns en gräns på 10 kB för ett enda nyckelvärdesobjekt.

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>Hur ska jag lagra konfigurationer för flera miljöer (test, mellanlagring, produktion och så vidare)?

Du styr vem som kan komma åt appkonfiguration på en nivå per butik. Använd ett separat arkiv för varje miljö som kräver olika behörigheter. Den här metoden ger den bästa säkerhetsisoleringen.

Om du inte behöver säkerhetsisolering mellan miljöer kan du använda etiketter för att skilja mellan konfigurationsvärden. [Använd etiketter för att aktivera olika konfigurationer för olika miljöer](./howto-labels-aspnet-core.md) ger ett komplett exempel.

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>Vilka är de rekommenderade sätten att använda appkonfiguration?

Se [metodtips](./howto-best-practices.md).

## <a name="how-much-does-app-configuration-cost"></a>Hur mycket kostar appkonfiguration?

Det finns två prisnivåer:

- Kostnadsfri nivå
- Standardnivå.

Om du skapade en butik innan standardnivån introducerades flyttas den automatiskt till den kostnadsfria nivån vid allmän tillgänglighet. Du kan välja att uppgradera till standardnivån eller stanna kvar på den kostnadsfria nivån.

Du kan inte nedgradera en butik från standardnivån till den kostnadsfria nivån. Du kan skapa ett nytt arkiv på den kostnadsfria nivån och sedan importera konfigurationsdata till det arkivet.

## <a name="which-app-configuration-tier-should-i-use"></a>Vilken appkonfigurationsnivå ska jag använda?

Båda appkonfigurationsnivåerna erbjuder kärnfunktioner, inklusive konfigurationsinställningar, funktionsflaggor, Key Vault-referenser, grundläggande hanteringsåtgärder, mått och loggar.

Följande är överväganden för att välja en nivå.

- **Resurser per prenumeration**: En resurs består av ett enda konfigurationsarkiv. Varje prenumeration är begränsad till ett konfigurationsarkiv på den kostnadsfria nivån. Prenumerationer kan ha ett obegränsat antal konfigurationsarkiv på standardnivån.
- **Lagring per resurs**: På den kostnadsfria nivån är varje konfigurationsarkiv begränsat till 10 MB lagringsutrymme. På standardnivån kan varje konfigurationsarkiv använda upp till 1 GB lagringsutrymme.
- **Nyckelhistorik**: Appkonfiguration lagrar en historik över alla ändringar som gjorts i nycklar. På den kostnadsfria nivån lagras den här historiken i sju dagar. På standardnivån lagras den här historiken i 30 dagar.
- **Förfrågningar per dag:** Kostnadsfria nivåbutiker är begränsade till 1 000 begäranden per dag. När en butik når 1 000 begäranden returneras HTTP-statuskod 429 för alla begäranden fram till midnatt.

    För standardnivåbutiker ingår de första 200 000 förfrågningarna varje dag i den dagliga avgiften. Ytterligare begäranden faktureras som överanslag.

- **Servicenivåavtal**: Standardnivån har ett serviceavtal på 99,9 % tillgänglighet. Den kostnadsfria nivån har inget serviceavtal.
- **Säkerhetsfunktioner**: Båda nivåerna innehåller grundläggande säkerhetsfunktioner, inklusive kryptering med Microsoft-hanterade nycklar, autentisering via HMAC eller Azure Active Directory, RBAC-stöd och hanterad identitet. Standard-nivån erbjuder mer avancerade säkerhetsfunktioner, inklusive Private Link-support och kryptering med kundhanterade nycklar.
- **Kostnad:** Standardnivåbutiker har en daglig användningsavgift. Det finns också en överprissättning avgift för förfrågningar förbi den dagliga tilldelningen. Det kostar inget att använda en kostnadsfri nivåbutik.

## <a name="can-i-upgrade-a-store-from-the-free-tier-to-the-standard-tier-can-i-downgrade-a-store-from-the-standard-tier-to-the-free-tier"></a>Kan jag uppgradera en butik från den kostnadsfria nivån till standardnivån? Kan jag nedgradera en butik från standardnivån till den kostnadsfria nivån?

Du kan uppgradera från den kostnadsfria nivån till standardnivån när som helst.

Du kan inte nedgradera en butik från standardnivån till den kostnadsfria nivån. Du kan skapa ett nytt arkiv på den kostnadsfria nivån och sedan [importera konfigurationsdata till det arkivet](howto-import-export-data.md).

## <a name="are-there-any-limits-on-the-number-of-requests-made-to-app-configuration"></a>Finns det några begränsningar för antalet begäranden som görs till appkonfiguration?

Konfigurationsarkiv på den kostnadsfria nivån är begränsade till 1 000 begäranden per dag. Konfigurationsarkiv på standardnivån kan uppleva tillfällig begränsning när begäranden överstiger 20 000 begäranden per timme.

När ett arkiv når sin gräns returneras HTTP-statuskod 429 för alla begäranden som görs tills tidsperioden löper ut. Rubriken `retry-after-ms` i svaret ger en föreslagen väntetid (i millisekunder) innan du försöker igen begäran.

Om ditt program regelbundet får HTTP-statuskod 429-svar kan du överväga att omforma det för att minska antalet begäranden som görs. Mer information finns i [Minska begäranden till appkonfiguration](./howto-best-practices.md#reduce-requests-made-to-app-configuration)

## <a name="my-application-receives-http-status-code-429-responses-why"></a>Mitt program får HTTP-statuskod 429 svar. Varför det?

Du får ett HTTP-statuskod 429-svar under dessa omständigheter:

* Överskrider gränsen för daglig begäran för en butik på den kostnadsfria nivån.
* Tillfällig begränsning på grund av en hög begärandekostnad för en butik på standardnivån.
* Överdriven bandbreddsanvändning.
* Försöker skapa eller ändra en nyckel när lagringsofferten överskrids.

Kontrollera brödtexten i 429-svaret av den specifika anledningen till att begäran misslyckades.

## <a name="how-can-i-receive-announcements-on-new-releases-and-other-information-related-to-app-configuration"></a>Hur kan jag få meddelanden om nya versioner och annan information relaterad till appkonfiguration?

Prenumerera på våra [GitHub-meddelanden repo](https://github.com/Azure/AppConfiguration-Announcements).

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>Hur anmäler jag ett problem eller ger ett förslag?

Du når oss direkt på [GitHub.](https://github.com/Azure/AppConfiguration/issues)

## <a name="next-steps"></a>Nästa steg

* [Om Azure App Configuration](./overview.md)
