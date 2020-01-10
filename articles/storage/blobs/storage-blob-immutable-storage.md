---
title: Oåterkalleligt blob-lagring – Azure Storage
description: Azure Storage erbjuder mask (Skriv en gång, Läs många) stöd för blob-lagring (objekt) som gör det möjligt för användare att lagra data i ett icke-redigerbart tillstånd för ett visst intervall.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: tamram
ms.reviewer: hux
ms.subservice: blobs
ms.openlocfilehash: 9d0919651842a6f6f935c9f1e338c9d335b80f47
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2020
ms.locfileid: "75749153"
---
# <a name="store-business-critical-blob-data-with-immutable-storage"></a>Lagra affärs kritiska BLOB-data med oföränderlig lagring

Oföränderlig lagring för Azure Blob Storage gör det möjligt för användare att lagra affärs kritiska data objekt i en mask (Skriv en gång, läsa många). Det här läget gör att data inte kan raderas och inte kan ändras för ett användardefinierat intervall. För varaktigheten för kvarhållningsintervallet kan blobbar skapas och läsas, men kan inte ändras eller tas bort. Oföränderlig lagring är tillgängligt för generella v2-och Blob Storage-konton i alla Azure-regioner.

Information om hur du ställer in och rensar juridiska undantag eller skapar en tidsbaserad bevarande princip med hjälp av Azure Portal, PowerShell eller Azure CLI finns i [Ange och hantera oföränderlighets-principer för Blob Storage](storage-blob-immutability-policies-manage.md).

## <a name="about-immutable-blob-storage"></a>Om oföränderligt Blob Storage

Med oföränderlig lagring får sjukvårds organisationer, finansiella institutioner och relaterade branscher&mdash;särskilt Broker-handlare organisationer&mdash;för att lagra data på ett säkert sätt. Oföränderlig lagring kan också utnyttjas i alla scenarier för att skydda viktiga data mot ändringar eller borttagningar.

Vanliga program innehåller:

- **Efterlevnad**: oföränderlig lagring för Azure Blob Storage hjälper organisationers adress SEC 17a-4 (f), CFTC 1.31 (d), FINRA och andra bestämmelser. Ett tekniskt informations dokument från Cohasset Associates beskriver hur oföränderligt lagrings utrymme som de här regleringsmyndigheterna kan hämtas via [Microsoft Service Trust Portal](https://aka.ms/AzureWormStorage). [Azure Säkerhetscenter](https://www.microsoft.com/trustcenter/compliance/compliance-overview) innehåller detaljerad information om våra certifieringar för efterlevnad.

- **Kvarhållning av säkra dokument**: oföränderligt lagrings utrymme för Azure Blob Storage garanterar att data inte kan ändras eller tas bort av någon användare, inklusive användare med administratörs behörighet för kontot.

- **Juridiskt bevarande**: oföränderlig lagring för Azure Blob Storage gör det möjligt för användare att lagra känslig information som är viktig för tvister eller företags användning i ett Manipulerings bevis för önskad varaktighet tills spärren tas bort. Den här funktionen är inte begränsad till juridiska användnings fall, men kan även betraktas som ett händelsebaserade undantag eller ett företags lås, där behovet av att skydda data baserat på händelse utlösare eller företags princip krävs.

Oföränderlig lagring stöder följande funktioner:

- **[Stöd för tidsbaserad bevarande princip](#time-based-retention-policies)** : användare kan ange principer för att lagra data för ett angivet intervall. När en tidsbaserad bevarande princip anges kan blobbar skapas och läsas, men inte ändras eller tas bort. När kvarhållningsperioden har upphört att gälla kan blobbar tas bort men inte skrivas över.

- **[Support för juridiskt undantag](#legal-holds)** : om kvarhållningsintervallet inte är känt kan användarna ange juridiska undantag för att lagra oföränderliga data tills det juridiska undantaget är avmarkerat.  När en princip för juridiskt undantag anges kan blobbar skapas och läsas, men inte ändras eller tas bort. Varje juridiskt undantag är associerat med en användardefinierad alfanumerisk tagg (t. ex. ett ärende-ID, händelse namn osv.) som används som en ID-sträng. 

- **Stöd för alla BLOB-nivåer**: Worm-principer är oberoende av Azure Blob Storage-nivån och gäller för alla nivåer: frekvent, låg frekvent och Arkiv lag ring. Användarna kan flytta data till den mest kostnadsoptimerade nivån för arbetsbelastningen samtidigt som data förblir oförändrade.

- **Konfiguration av container nivå**: användare kan konfigurera tidsbaserade bevarande principer och juridiska undantags taggar på behållar nivån. Användare kan bland annat skapa och låsa tidsbaserade kvarhållningspolicyer, utöka kvarhållningsintervaller, ställa in och ta bort kvarhållning via enkla inställningar på containernivå. De här policyerna gälle för alla blobar i containern, både befintliga och nya.

- **Stöd för gransknings loggning**: varje behållare innehåller en princip Gransknings logg. Det visar upp till sju tidsbaserade kvarhållning-kommandon för låsta tidsbaserade bevarande principer och innehåller användar-ID, kommando typ, tidsstämplar och kvarhållningsintervall. För kvarhållning av juridiska skäl innehåller loggen användar-ID, typ av kommando, tidsstämplar och etiketter för kvarhållningen av juridiska skäl. Den här loggen behålls för principens livs längd i enlighet med rikt linjerna för s 17a-4 (f). [Azure aktivitets loggen](../../azure-monitor/platform/platform-logs-overview.md) visar en mer omfattande logg över alla kontroll Plans aktiviteter. När du aktiverar [Azure Diagnostic-loggar](../../azure-monitor/platform/platform-logs-overview.md) behålls och visas data Plans åtgärder. Det är användarens ansvar att lagra de här loggarna beständigt såsom krävs enligt regelverk eller andra ändamål.

## <a name="how-it-works"></a>Så här fungerar det

I den oföränderliga lagringen för Azure-blobar finns stöd för två olika typer av WORM- eller oföränderliga policyer: tidsbaserad kvarhållning och kvarhållning av juridiska skäl. När en tidsbaserad bevarande princip eller ett juridiskt undantag tillämpas på en behållare flyttas alla befintliga blobar till ett oföränderligt WORM-tillstånd på mindre än 30 sekunder. Alla nya blobbar som överförs till behållaren flyttas också till det oföränderliga läget. När alla blobbar har flyttats till det oföränderliga läget bekräftas den oföränderliga principen och alla Skriv-och borttagnings åtgärder för befintliga och nya objekt i den oföränderliga behållaren är inte tillåtna.

Borttagning av behållare och lagrings konto tillåts inte om det finns blobbar i behållaren eller lagrings kontot som skyddas av en princip som inte kan ändras. Det går inte att ta bort containern om det finns minst en blob med en låst tidsbaserad bevarande princip eller ett juridiskt undantag. Det går inte att ta bort lagrings kontot om det finns minst en WORM-behållare med ett juridiskt undantag eller en blob med ett aktivt kvarhållningsintervall.

### <a name="time-based-retention-policies"></a>Tidsbaserade bevarande principer

> [!IMPORTANT]
> En tidsbaserad bevarande princip måste vara *låst* för att blobben ska vara i ett inkompatibelt läge (Write and delete Protected) för SEC 17a-4 (f) och annan myndighets efterlevnad. Vi rekommenderar att du låser principen inom rimlig tid, vanligt vis mindre än 24 timmar. Det ursprungliga läget för en tillämpad tidsbaserad bevarande princip *låses upp*, så att du kan testa funktionen och göra ändringar i principen innan du låser den. Även om det *upplåsta* läget ger oföränderlighets skydd rekommenderar vi inte att du använder det *upplåsta* läget för något annat ändamål än kortsiktiga funktions försök. 

När en tidsbaserad bevarande princip tillämpas på en behållare kommer alla blobar i behållaren att stanna kvar i det oföränderliga läget under den *gällande* kvarhållningsperioden. Den effektiva kvarhållningsperioden för befintliga blobbar är lika med skillnaden mellan tiden för skapandet av bloben och det användardefinierade kvarhållningsintervallet.

För nya blobbar är den effektiva kvarhållningsperioden lika med det kvarhållningsintervall som angetts av användaren. Eftersom användarna kan utöka kvarhållningsintervallet används det senaste värdet för det användardefinierade kvarhållningsintervallet när den effektiva kvarhållningsperioden ska beräknas.

Anta till exempel att en användare skapar en tidsbaserad bevarande princip med ett kvarhållningsintervall på fem år. En befintlig BLOB i behållaren, _testblob1_, har skapats för ett år sedan. Den effektiva kvarhållningsperioden för _testblob1_ är fyra år. När en ny BLOB, _testblob2_, överförs till behållaren, är den effektiva kvarhållningsperioden för den nya blobben fem år.

En upplåst tidsbaserad bevarande princip rekommenderas endast för funktions testning och en princip måste vara låst för att vara kompatibel med s 17a-4 (f) och annan myndighets efterlevnad. När en tidsbaserad bevarande princip är låst går det inte att ta bort principen och maximalt fem ökningar tillåts för den gällande kvarhållningsperioden. Mer information om hur du ställer in och låser tidsbaserade bevarande principer finns i [Ange och hantera oföränderlighets-principer för Blob Storage](storage-blob-immutability-policies-manage.md).

Följande begränsningar gäller för bevarande principer:

- För ett lagrings konto är det maximala antalet behållare med låsta tidsbaserade, oföränderliga principer som är 1 000.
- Det minsta kvarhållningsintervallet är en dag. Det maximala värdet är 146 000 dagar (400 år).
- För en behållare är det maximala antalet redigeringar som utökar ett kvarhållningsintervall för låsta tidsbaserade, oföränderliga principer 5.
- För en behållare behålls högst sju tidsbaserade tidsbaserade logg gransknings loggar för en låst princip.

### <a name="legal-holds"></a>Bevarande av juridiska skäl

När du ställer in ett juridiskt undantag förblir alla befintliga och nya blobbar i det oföränderliga läget tills det juridiska undantaget är avmarkerat. Mer information om hur du ställer in och rensar juridiska undantag finns i [Ange och hantera oföränderlighets-principer för Blob Storage](storage-blob-immutability-policies-manage.md).

En behållare kan ha både ett juridiskt undantag och en tidsbaserad bevarande princip på samma gång. Alla blobar i containern förblir då i det oföränderliga tillståndet tills alla juridiska skäl tas bort, även om den effektiva kvarhållningsperioden har upphört. På motsvarande sätt förblir blobarna i det oföränderliga tillståndet tills den effektiva kvarhållningsperioden upphör även om alla juridiska skäl tas bort.

I följande tabell visas de typer av Blob Storage-åtgärder som har inaktiverats för olika scenarier. Mer information finns i REST API dokumentationen för [Azure Blob service](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api) .

|Scenario  |BLOB-tillstånd  |BLOB-åtgärder tillåts inte  |
|---------|---------|---------|
|Effektivt kvarhållningsintervall för blobben har ännu inte gått ut och/eller bevarande av juridiska skäl har angetts     |Oåterkallelig: både ta bort- och skrivskyddad         | Lägg till BLOB<sup>1</sup>, sätt block<sup>1</sup>, list block lista<sup>1</sup>, ta bort behållare, ta bort BLOB, ange BLOB-metadata, sätt sida, ange BLOB-egenskaper, ögonblicks bilds-BLOB, stegvis kopia av BLOB, tilläggs block         |
|Effektivt kvarhållningsintervall på blobben har upphört att gälla     |Skrivskyddad endast (ta bort tillåts)         |Lägg till BLOB<sup>1</sup>, sätt block<sup>1</sup>, list block lista<sup>1</sup>, ange BLOB-metadata, placerings sida, ange BLOB-egenskaper, ögonblicks bilds-BLOB, stegvis kopia-BLOB, tilläggs block         |
|Alla juridiska undantag rensas och ingen tidsbaserad bevarande princip har angetts på behållaren     |Föränderlig         |Inget         |
|Ingen mask policy skapas (tidsbaserad kvarhållning eller juridiskt undantag)     |Föränderlig         |Inget         |

<sup>1</sup> programmet tillåter dessa åtgärder att skapa en ny BLOB en gång. Alla efterföljande överskrivnings åtgärder på en befintlig BLOB-sökväg i en oföränderlig container är inte tillåtna.

Följande begränsningar gäller för juridiska undantag:

- För ett lagrings konto är det maximala antalet behållare med en juridisk undantags inställning 1 000.
- För en behållare är det maximala antalet juridiska undantags etiketter 10.
- Den minsta längden för en juridiskt Hold-tagg består av tre alfanumeriska tecken. Den maximala längden är 23 alfanumeriska tecken.
- För en behållare behålls det maximala antalet gransknings loggar för den här principen.

## <a name="pricing"></a>Prissättning

Det kostar inget extra att använda den här funktionen. Oföränderliga data priss ätts på samma sätt som föränderligt-data. Pris information om Azure Blob Storage finns på sidan med [Azure Storage priser](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="faq"></a>FAQ

**Kan du ge dokumentation om WORM-kompatibilitet?**

Ja. För att dokumentera efterlevnaden har Microsoft bevarat en ledande oberoende utvärderings verksamhet som specialiserar sig på hantering av registrering och informations styrning, Cohassete Associates, för att utvärdera oföränderligt blob-lagring och dess efterlevnad av krav som är särskilt kopplade till finans Services-bransch. Cohasset validerade att den oföränderliga blob-lagringen, när den används för att bevara tidsbaserade blobbar i ett WORM-tillstånd, uppfyller relevanta lagrings krav för CFTC Rule 1.31 (c)-(d), FINRA-regel 4511 och SEC-regel 17a-4. Microsoft riktade sig på den här uppsättningen regler eftersom de representerar de mest förenklade vägledningarna globalt för poster som kvarhålls för finansiella institutioner. Cohasset-rapporten finns i [Microsoft Service Trust Center](https://aka.ms/AzureWormStorage). Kontakta Azure-supporten om du vill begära en skrivelse från Microsoft angående WORM-efterlevnad.

**Gäller funktionen bara block-blobbar, eller till sid-och bifogade blobbar.**

Oåterkalleligt lagrings utrymme kan användas med valfri Blob-typ som anges på behållar nivån, men vi rekommenderar att du använder mask för behållare som huvudsakligen lagrar block-blobbar. Till skillnad från block-blobbar måste eventuella nya sid-blobar och bifogade blobbar skapas utanför en WORM-behållare och sedan kopieras i. När du har kopierat dessa blobbar till en WORM-behållare tillåts inga ytterligare *tillägg* till en tilläggs-BLOB eller ändringar av en sid-blob. Att ställa in en mask princip på en behållare som lagrar virtuella hård diskar (Page blobbar) för aktiva virtuella datorer, rekommenderas inte eftersom den kommer att låsa den virtuella dator disken.

**Måste jag skapa ett nytt lagrings konto för att använda den här funktionen?**

Nej, du kan använda oföränderligt lagrings utrymme med befintliga eller nyligen skapade redovisnings-eller Blob Storage-konton som skapats av generell användning. Den här funktionen är avsedd för användning med block-blobbar i GPv2-och Blob Storage-konton. Generella v1-lagrings konton stöds inte, men de kan enkelt uppgraderas till generell användning v2. Information om hur du uppgraderar ett befintligt allmänt v1-lagrings konto finns i [uppgradera ett lagrings konto](../common/storage-account-upgrade.md).

**Kan jag använda både en juridisk princip för kvarhållning och tidsbaserad lagring?**

Ja, en behållare kan ha både ett juridiskt undantag och en tidsbaserad bevarande princip på samma gång. Alla blobar i containern förblir då i det oföränderliga tillståndet tills alla juridiska skäl tas bort, även om den effektiva kvarhållningsperioden har upphört. På motsvarande sätt förblir blobarna i det oföränderliga tillståndet tills den effektiva kvarhållningsperioden upphör även om alla juridiska skäl tas bort.

**Finns det bara juridiska undantags principer för rättsliga förfaranden eller andra användnings scenarier?**

Nej, juridiskt bevarande är bara den allmänna termen som används för en icke-baserad bevarande princip. Den behöver inte bara användas för tvister som rör tvister. Principer för juridiskt bevarande är användbara för att inaktivera Skriv över och ta bort för att skydda viktiga Enterprise WORM-data, där kvarhållningsperioden är okänt. Du kan använda den som en företags princip för att skydda dina verksamhets kritiska mask arbets belastningar eller använda den som en princip för en mellanlagrings princip innan en anpassad händelse utlösare kräver att en tidsbaserad bevarande princip används. 

**Kan jag ta bort en _låst_ tidsbaserad bevarande princip eller ett juridiskt undantag?**

Endast upplåsta tidsbaserade bevarande principer kan tas bort från en behållare. När en tidsbaserad bevarande princip är låst kan den inte tas bort. endast tillägg för gällande kvarhållningsperiod är tillåtna. Juridiska undantags taggar kan tas bort. När alla juridiska taggar tas bort tas det juridiska undantaget bort.

**Vad händer om jag försöker ta bort en container med en *låst* tidsbaserade bevarandeprincip eller bevarande av juridiska skäl?**

Det går inte att ta bort container om det finns minst en blob med en låst tidsbaserad bevarande princip eller ett juridiskt undantag. Åtgärden för att ta bort behållare lyckas bara om det inte finns någon BLOB med ett aktivt kvarhållningsintervall och det inte finns några juridiska undantag. Du måste ta bort Blobbarna innan du kan ta bort behållaren.

**Vad händer om jag försöker ta bort ett lagringskonto med en WORM-container som har en *låst* tidsbaserad bevarandeprincip eller ett bevarande av juridiska skäl?**

Borttagningen av lagringskontot misslyckas om det finns minst en WORM-container med bevarande av juridiska skäl eller en blob med ett aktivt kvarhållningsintervall. Du måste ta bort alla mask behållare innan du kan ta bort lagrings kontot. Information om borttagning av behållare finns i föregående fråga.

**Kan jag flytta data över olika blob-nivåer (frekvent, lågfrekvent, kall) när blobben är i oförändrat tillstånd?**

Ja, du kan använda kommandot Set BLOB Tier för att flytta data mellan BLOB-nivåerna samtidigt som data hålls i ett kompatibelt tillstånd. Oföränderligt lagrings utrymme stöds på BLOB-nivåer för frekvent, låg frekvent och arkiv.

**Vad händer om jag inte betalat och mitt kvarhållningsintervall inte har gått ut?**

Vid utebliven betalning tillämpas normala data bevarande principer enligt vad som anges i avtals villkoren i avtalet med Microsoft.

**Finns det en utvärderingsversion eller en respitperiod för att bara testa funktionen?**

Ja. När en tidsbaserad bevarande princip först skapas är den i ett *låst* läge. I det här läget kan du göra önskade ändringar i kvarhållningsintervallet, till exempel ökning eller minskning och till och med ta bort principen. När principen är låst förblir den låst tills kvarhållningsintervallet upphör att gälla. Den här låsta principen förhindrar borttagning och ändring av kvarhållningsintervallet. Vi rekommenderar starkt att du bara använder det *upplåsta* läget för utvärderings syfte och låser principen inom en 24-timmarsperiod. Dessa metoder hjälper dig att följa s 17a-4 (f) och andra bestämmelser.

**Kan jag använda mjuk borttagning tillsammans med oföränderliga BLOB-principer?**

Ja. [Mjuk borttagning för Azure Blob Storage](storage-blob-soft-delete.md) gäller för alla behållare i ett lagrings konto, oavsett en juridisk undantags-eller tidsbaserad bevarande princip. Vi rekommenderar att du aktiverar mjuk borttagning för ytterligare skydd innan eventuella mask principer som inte kan användas och bekräftas.

**Var finns funktionen?**

Oåterkalleligt lagrings utrymme är tillgängligt i Azures offentliga, Kina och myndighets regioner. Om oföränderlig lagring inte är tillgängligt i din region kan du kontakta support och e-azurestoragefeedback@microsoft.com.

## <a name="next-steps"></a>Nästa steg

[Ange och hantera oföränderlighets-principer för Blob Storage](storage-blob-immutability-policies-manage.md)