---
title: Oföränderlig blob-lagring – Azure Storage
description: Azure Storage erbjuder WORM (Write Once, Read Many) stöd för Blob (objekt) lagring som gör det möjligt för användare att lagra data i ett icke-raderbart, icke-ändringsbart tillstånd för ett angivet intervall.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: tamram
ms.reviewer: hux
ms.subservice: blobs
ms.openlocfilehash: a980c7bd068a463956191eece43ec1be233e7890
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367626"
---
# <a name="store-business-critical-blob-data-with-immutable-storage"></a>Lagra affärskritiska blob-data med oföränderlig lagring

Oföränderlig lagring för Azure Blob-lagring gör det möjligt för användare att lagra affärskritiska dataobjekt i ett WORM-tillstånd (Write Once, Read Many). Det här tillståndet gör att data inte kan raderas och inte kan ändras för ett användarspecificerat intervall. Under kvarhållningsintervallets varaktighet kan blobbar skapas och läsas, men kan inte ändras eller tas bort. Oföränderlig lagring är tillgänglig för allmänna v1-konton v1, general v2, BlobStorage och BlockBlobStorage i alla Azure-regioner.

Information om hur du anger och rensar juridiska spärrar eller skapar en tidsbaserad bevarandeprincip med Azure-portalen, PowerShell eller Azure CLI finns i [Ange och hantera principer för oföränderlighet för Blob-lagring](storage-blob-immutability-policies-manage.md).

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="about-immutable-blob-storage"></a>Om oföränderlig Blob-lagring

Oföränderlig lagring hjälper hälso- och&mdash;sjukvårdsorganisationer,&mdash;finansinstitut och relaterade branscher, särskilt mäklarorganisationer, att lagra data på ett säkert sätt. Oföränderlig lagring kan också utnyttjas i alla scenarier för att skydda kritiska data mot ändringar eller radering.

Vanliga program innehåller:

- **Regelefterlevnad**: Oföränderlig lagring för Azure Blob-lagring hjälper organisationer att hantera SEK 17a-4(f), CFTC 1.31(d), FINRA och andra föreskrifter. En teknisk whitepaper från Cohasset Associates beskriver hur oföränderlig lagring åtgärdar dessa lagstadgade krav kan laddas ner via [Microsoft Service Trust Portal](https://aka.ms/AzureWormStorage). [Azure Trust Center](https://www.microsoft.com/trustcenter/compliance/compliance-overview) innehåller detaljerad information om våra efterlevnadscertifieringar.

- **Säker dokumentkvarhållning**: Oföränderlig lagring för Azure Blob-lagring säkerställer att data inte kan ändras eller tas bort av någon användare, inklusive användare med administratörsbehörighet för konton.

- **Juridisk spärr:** Oföränderlig lagring för Azure Blob-lagring gör det möjligt för användare att lagra känslig information som är avgörande för rättstvister eller företagsanvändning i ett manipuleringssäkert tillstånd under önskad tid tills spärren tas bort. Den här funktionen är inte begränsad endast till juridiska användningsfall utan kan också ses som en händelsebaserad spärr eller ett företagslås, där behovet av att skydda data baserat på händelseutlösare eller företagspolicy krävs.

Oföränderlig lagring stöder följande funktioner:

- **[Tidsbaserat bevarandeprincipstöd](#time-based-retention-policies)**: Användare kan ange principer för att lagra data för ett angivet intervall. När en tidsbaserad bevarandeprincip har angetts kan blobbar skapas och läsas, men inte ändras eller tas bort. När kvarhållningsperioden har gått ut kan blobbar tas bort men inte skrivas över.

- **[Principstöd för juridiska spärrar](#legal-holds)**: Om kvarhållningsintervallet inte är känt kan användare ange juridiska spärrar för att lagra oföränderliga data tills den juridiska spärren har rensats.  När en princip för juridisk spärr har angetts kan blobbar skapas och läsas, men inte ändras eller tas bort. Varje juridisk spärr är associerad med en användardefinierad alfanumerisk tagg (till exempel ett ärende-ID, händelsenamn osv.) som används som en identifierare sträng. 

- **Stöd för alla blob-nivåer**: WORM-principer är oberoende av Azure Blob-lagringsnivån och gäller för alla nivåer: hot, cool och archive. Användarna kan flytta data till den mest kostnadsoptimerade nivån för arbetsbelastningen samtidigt som data förblir oförändrade.

- **Konfiguration på behållarnivå:** Användare kan konfigurera tidsbaserade bevarandeprinciper och juridiska spärra-taggar på behållarnivå. Användare kan bland annat skapa och låsa tidsbaserade kvarhållningspolicyer, utöka kvarhållningsintervaller, ställa in och ta bort kvarhållning via enkla inställningar på containernivå. De här policyerna gälle för alla blobar i containern, både befintliga och nya.

- **Stöd för granskningloggning**: Varje behållare innehåller en principgranskningslogg. Den visar upp till sju tidsbaserade kvarhållningskommandon för låsta tidsbaserade bevarandeprinciper och innehåller användar-ID, kommandotyp, tidsstämplar och kvarhållningsintervall. För kvarhållning av juridiska skäl innehåller loggen användar-ID, typ av kommando, tidsstämplar och etiketter för kvarhållningen av juridiska skäl. Denna logg behålls under policyns livstid, i enlighet med SEC 17a-4(f) regulatoriska riktlinjer. [Azure-aktivitetsloggen](../../azure-monitor/platform/platform-logs-overview.md) visar en mer omfattande logg över alla kontrollplansaktiviteter. samtidigt som [Azure Diagnostic Logs aktiveras](../../azure-monitor/platform/platform-logs-overview.md) och dataplanåtgärder visas. Det är användarens ansvar att lagra de här loggarna beständigt såsom krävs enligt regelverk eller andra ändamål.

## <a name="how-it-works"></a>Hur det fungerar

I den oföränderliga lagringen för Azure-blobar finns stöd för två olika typer av WORM- eller oföränderliga policyer: tidsbaserad kvarhållning och kvarhållning av juridiska skäl. När en tidsbaserad bevarandeprincip eller juridisk spärr tillämpas på en behållare flyttas alla befintliga blobbar till ett oföränderligt MASK-tillstånd på mindre än 30 sekunder. Alla nya blobbar som överförs till den principskyddade behållaren kommer också att gå in i ett oföränderligt tillstånd. När alla blobbar är i ett oföränderligt tillstånd bekräftas den oföränderliga principen och eventuella överskrivnings- eller borttagningsåtgärder i den oföränderliga behållaren tillåts inte.

Borttagning av behållar- och lagringskonto är inte heller tillåtet om det finns några blobbar i en behållare som skyddas av en juridisk spärr eller en låst tidsbaserad princip. En princip för juridisk spärr skyddar mot blob, behållare och lagringskontoborttagning. Både olåst och låsta tidsbaserade principer skyddar mot blob-borttagning under den angivna tiden. Både olåst och låsta tidsbaserade principer skyddar mot borttagning av behållare endast om det finns minst en blob i behållaren. Endast en behållare med *låst* tidsbaserad princip skyddar mot borttagning av lagringskonto. behållare med olåst tidsbaserade principer erbjuder inte skydd för borttagning av lagringskonto eller efterlevnad.

Mer information om hur du ställer in och låser tidsbaserade bevarandeprinciper finns i [Ange och hantera principer för oföränderlighet för Blob-lagring](storage-blob-immutability-policies-manage.md).

## <a name="time-based-retention-policies"></a>Tidsbaserade bevarandeprinciper

> [!IMPORTANT]
> En tidsbaserad bevarandeprincip måste *låsas* för att blobben ska vara i ett kompatibelt oföränderligt tillstånd (skriv och ta bort skyddad) för SEK 17a-4(f) och annan regelefterlevnad. Vi rekommenderar att du låser principen inom en rimlig tid, vanligtvis mindre än 24 timmar. Det ursprungliga tillståndet för en tillämpad tidsbaserad bevarandeprincip *låses upp,* så att du kan testa funktionen och göra ändringar i principen innan du låser den. Det *olåsta* tillståndet ger immutability skydd, vi rekommenderar inte att du använder *olåst* tillstånd för något annat syfte än kortsiktiga funktionsförsök. 

När en tidsbaserad bevarandeprincip tillämpas på en behållare, förblir alla blobbar i behållaren i oföränderligt tillstånd under den *effektiva* kvarhållningsperioden. Den effektiva kvarhållningsperioden för blobbar är lika med skillnaden mellan blobens **skapandetid** och det användarspecificerade kvarhållningsintervallet. Eftersom användarna kan utöka kvarhållningsintervallet används det senaste värdet för det användardefinierade kvarhållningsintervallet när den effektiva kvarhållningsperioden ska beräknas.

Anta till exempel att en användare skapar en tidsbaserad bevarandeprincip med ett kvarhållningsintervall på fem år. En befintlig blob i den behållaren, _testblob1_, skapades för ett år sedan. den effektiva retentionstiden för _testblob1_ är fyra år. När en ny blob, _testblob2_, överförs till behållaren, är den effektiva kvarhållningsperioden för _testblob2_ fem år från den tidpunkt då den skapades.

En olåst tidsbaserad lagringsprincip rekommenderas endast för funktionstestning och en princip måste låsas för att vara kompatibel med SEK 17a-4(f) och annan regelefterlevnad. När en tidsbaserad bevarandeprincip har låsts kan principen inte tas bort och högst fem ökningar av den faktiska lagringsperioden tillåts.

Följande begränsningar gäller för bevarandeprinciper:

- För ett lagringskonto är det maximala antalet behållare med låsta tidsbaserade oföränderliga principer 10 000.
- Det minsta kvarhållningsintervallet är 1 dag. Den maximala är 146.000 dagar (400 år).
- För en behållare är det maximala antalet redigeringar för att utöka ett kvarhållningsintervall för låsta tidsbaserade oföränderliga principer 5.
- För en behållare behålls högst sju tidsbaserade granskningsloggar för bevarandeprinciper för en låst princip.

### <a name="allow-protected-append-blobs-writes"></a>Tillåt skyddade till tillägg blobbar skrivningar

Tillägg blobbar består av datablock och optimeras för datatillägg åtgärder som krävs av granskning och loggning scenarier. Genom att lägga till blobbar tillåter endast tillägg av nya block i slutet av blobben. Oavsett oföränderlighet tillåts eller tas bort befintliga block i en tilläggsblobb i grunden inte tillåtet. Mer information om tillägg blobbar finns i [Om Tillägg Blobbar](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs).

Endast tidsbaserade bevarandeprinciper har en `allowProtectedAppendWrites` inställning som gör det möjligt att skriva nya block till en tilläggsblob samtidigt som immuterbarhetsskydd och efterlevnad bibehålls. Om det är aktiverat kan du skapa en tilläggsblob direkt i den principskyddade behållaren och fortsätta att lägga till nya datablock i slutet av befintliga tilläggsblobbar med hjälp av *AppendBlock* API. Endast nya block kan läggas till och befintliga block kan inte ändras eller tas bort. Skydd för omföränderlighet för tidsbehållande gäller fortfarande, vilket förhindrar att tilläggsblobben tas bort tills den faktiska kvarhållningsperioden har förflutit. Om du aktiverar den här inställningen påverkas inte problemet med blockblobar eller sidblobar.

Eftersom den här inställningen är en del av en tidsbaserad bevarandeprincip, förblir tilläggsblobar fortfarande i oföränderligt tillstånd under den *faktiska* kvarhållningsperioden. Eftersom nya data kan läggas till utöver det första skapandet av den bifogade bloben, finns det en liten skillnad i hur kvarhållningsperioden bestäms. Den effektiva kvarhållningen är skillnaden mellan tilläggsblolobens **senaste ändringstid** och det användarspecificerade kvarhållningsintervallet. På samma sätt när kvarhållningsintervallet utökas använder oföränderlig lagring det senaste värdet i det användarspecificerade kvarhållningsintervallet för att beräkna den effektiva kvarhållningsperioden.

Anta till exempel att en användare skapar en `allowProtectedAppendWrites` tidsbaserad bevarandeprincip med aktiverat och ett kvarhållningsintervall på 90 dagar. En tilläggsblob, _logblob1_, skapas i behållaren idag, nya loggar fortsätter att läggas till i tillägg blob för de kommande 10 dagarna; så är den effektiva lagringsperioden för _logblob1_ 100 dagar från idag (tiden för dess sista tillägg + 90 dagar).

Olåsta tidsbaserade bevarandeprinciper `allowProtectedAppendWrites` gör att inställningen aktiveras och inaktiveras när som helst. När den tidsbaserade bevarandeprincipen `allowProtectedAppendWrites` har låsts kan inställningen inte ändras.

Principer för juridiska spärrar kan inte aktivera `allowProtectedAppendWrites` och alla juridiska spärrar kommer att upphäva egenskapen allowProtectedAppendWrites. Om en juridisk spärr tillämpas på en `allowProtectedAppendWrites` tidsbaserad bevarandeprincip med aktiverad, *misslyckas AppendBlock-API:et tills* den juridiska spärren har hävts.

## <a name="legal-holds"></a>Bevarande av juridiska skäl

Juridiska spärrar är tillfälliga spärrar som kan användas för rättsliga utredningar eller allmänna skyddsprinciper. Varje princip för juridiska spärrar måste associeras med en eller flera taggar. Taggar används som en namngiven identifierare, till exempel ett ärende-ID eller en händelse, för att kategorisera och beskriva syftet med spärren.

En behållare kan ha både en juridisk spärr och en tidsbaserad bevarandeprincip samtidigt. Alla blobar i containern förblir då i det oföränderliga tillståndet tills alla juridiska skäl tas bort, även om den effektiva kvarhållningsperioden har upphört. På motsvarande sätt förblir blobarna i det oföränderliga tillståndet tills den effektiva kvarhållningsperioden upphör även om alla juridiska skäl tas bort.

Följande gränser gäller för juridiska behåll:

- För ett lagringskonto är det maximala antalet behållare med en juridisk spärrinställning 10 000.
- För en behållare är det maximala antalet juridiska spärra taggar 10.
- Minsta längd för en juridisk spärrtagg är tre alfanumeriska tecken. Den maximala längden är 23 alfanumeriska tecken.
- För en behållare behålls högst 10 granskningsloggar för juridiska spärrar under principens varaktighet.

## <a name="scenarios"></a>Scenarier
I följande tabell visas de typer av Blob-lagringsåtgärder som är inaktiverade för de olika oföränderliga scenarierna. Mer information finns i AZURE [Blob Service REST API-dokumentationen.](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api)

|Scenario  |Blob-tillstånd  |Blob-åtgärder nekas  |Skydd för behållare och konton
|---------|---------|---------|---------|
|Effektivt kvarhållningsintervall för blobben har ännu inte gått ut och/eller bevarande av juridiska skäl har angetts     |Oåterkallelig: både ta bort- och skrivskyddad         | Sätt Blob<sup>1</sup>, Put Block<sup>1</sup>, Put Block List<sup>1</sup>, Ta bort behållare, Ta bort blob, ange BlobMetadata, Placera sida, Ange Blob-egenskaper, Ögonblicksbildblo, Inkrementell kopia blob, Lägg till Block<sup>2</sup>         |Borttagning av behållare nekad. Borttagning av lagringskonto nekad         |
|Det gällande kvarhållningsintervallet för blobben har upphört att gälla och ingen juridisk spärr har angetts    |Skrivskyddad endast (ta bort tillåts)         |Sätt Blob<sup>1</sup>, Put Block<sup>1</sup>, Sätt blocklista<sup>1</sup>, Ange BlobMetadata, Placera sida, Ange Blob-egenskaper, Ögonblicksbildblo, Inkrementell kopia blob, Lägg till block<sup>2</sup>         |Borttagning av behållare nekad om det finns minst 1 blob i skyddad behållare. Borttagning av lagringskonto nekas endast för *låsta* tidsbaserade principer         |
|Ingen WORM-princip tillämpas (ingen tidsbaserad kvarhållning och ingen juridisk spärrtagg)     |Föränderlig         |Inget         |Inget         |

<sup>1</sup> Blob-tjänsten gör att dessa åtgärder kan skapa en ny blob en gång. Alla efterföljande överskrivningsåtgärder på en befintlig blob-sökväg i en oföränderlig behållare är inte tillåtna.

<sup>2</sup> Tilläggsblock är endast tillåtet för tidsbaserade bevarandeprinciper med egenskapen `allowProtectedAppendWrites` aktiverad. Mer information finns i avsnittet [Tillåt skyddade tilläggsblobbar.](#allow-protected-append-blobs-writes)

## <a name="pricing"></a>Prissättning

Det finns ingen extra kostnad för att använda den här funktionen. Oföränderliga data prissätts på samma sätt som föränderliga data. Information om priser för Azure Blob-lagring finns på [prissidan för Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

**Kan du tillhandahålla dokumentation om WORM-efterlevnad?**

Ja. För att dokumentera efterlevnad behöll Microsoft ett ledande oberoende utvärderingsföretag som specialiserat sig på hantering av arkivhandlingar och informationsstyrning, Cohasset Associates, för att utvärdera oföränderlig Blob-lagring och dess efterlevnad av krav som är specifika för industrin för finansiella tjänster. Cohasset validerade att oföränderlig Blob-lagring, när den används för att behålla tidsbaserade Blobbar i ett WORM-tillstånd, uppfyller de relevanta lagringskraven i CFTC Regel 1.31(c)-d), FINRA Regel 4511 och SEC Regel 17a-4. Microsoft riktade in sig på den här uppsättningen regler, eftersom de representerar den mest normativa vägledningen globalt för lagring av poster för finansinstitut. Cohasset-rapporten är tillgänglig i [Microsoft Service Trust Center](https://aka.ms/AzureWormStorage). Om du vill begära ett intyg från Microsoft om WORM-efterlevnad av oföränderlighet kontaktar du Azure-supporten.

**Gäller funktionen endast för att blockera blobbar och lägga till blobbar, eller även för sidblobar?**

Oföränderlig lagring kan användas med valfri blob-typ eftersom den är inställd på behållarnivå, men vi rekommenderar att du använder WORM för behållare som huvudsakligen lagrar blockblobar och lägger till blobbar. Befintliga blobbar i en behållare skyddas av en nyligen angiven WORM-princip. Men alla nya sidblobar måste skapas utanför MASK-behållaren och sedan kopieras in. När du har kopierat till en WORM-behållare tillåts inga ytterligare ändringar i en sidblob. Att ange en WORM-princip på en behållare som lagrar virtuella hårddiskar (sidblobar) för alla aktiva virtuella datorer rekommenderas eftersom det låser vm-disken. Vi rekommenderar att du noggrant granskar dokumentationen och testar dina scenarier innan du låser några tidsbaserade principer.

**Måste jag skapa ett nytt lagringskonto för att kunna använda den här funktionen?**

Nej, du kan använda oföränderlig lagring med befintliga eller nyligen skapade generella v1-konton för virtuella v2-, BlobStorage- eller BlockBlobStorage-konton. Generella v1-lagringskonton stöds, men vi rekommenderar att du uppgraderar till allmänt v2 så att du kan dra nytta av fler funktioner. Information om hur du uppgraderar ett befintligt v1-lagringskonto i [Uppgradera ett lagringskonto](../common/storage-account-upgrade.md).

**Kan jag tillämpa både en juridisk spärr och en tidsbaserad bevarandeprincip?**

Ja, en behållare kan ha både en juridisk spärr och en tidsbaserad bevarandeprincip samtidigt. Inställningen "allowProtectedAppendWrites" gäller dock inte förrän den juridiska spärren har rensats. Alla blobar i containern förblir då i det oföränderliga tillståndet tills alla juridiska skäl tas bort, även om den effektiva kvarhållningsperioden har upphört. På motsvarande sätt förblir blobarna i det oföränderliga tillståndet tills den effektiva kvarhållningsperioden upphör även om alla juridiska skäl tas bort. 

**Finns det principer för juridiska skäl endast för rättsliga förfaranden eller finns det andra användningsscenarier?**

Nej, Juridisk spärr är bara den allmänna termen som används för en icke-tidsbaserad bevarandeprincip. Det behöver inte bara användas för processrättsliga förfaranden. Principer för juridisk spärr är användbara för att inaktivera överskrivning och borttagningar för att skydda viktiga WORM-data för företag, där lagringsperioden är okänd. Du kan använda den som en företagsprincip för att skydda dina verksamhetskritiska WORM-arbetsbelastningar eller använda den som en mellanlagringsprincip innan en anpassad händelseutlösare kräver användning av en tidsbaserad bevarandeprincip. 

**Kan jag ta bort en _låst_ tidsbaserad bevarandeprincip eller juridisk spärr?**

Endast olåst tidsbaserade bevarandeprinciper kan tas bort från en behållare. När en tidsbaserad bevarandeprincip har låsts kan den inte tas bort. endast effektiva förlängningar av kvarhållandeperioden är tillåtna. Juridiska spärrtaggar kan tas bort. När alla juridiska taggar tas bort tas den juridiska spärren bort.

**Vad händer om jag försöker ta bort en behållare med en tidsbaserad bevarandeprincip eller juridisk spärr?**

Åtgärden Ta bort behållare misslyckas om det finns minst en blob i behållaren med antingen en låst eller olåst tidsbaserad bevarandeprincip eller om behållaren har en juridisk spärr. Åtgärden Ta bort behållare lyckas endast om det inte finns några blobbar i behållaren och det inte finns några juridiska spärrar. 

**Vad händer om jag försöker ta bort ett lagringskonto med en behållare som har en tidsbaserad bevarandeprincip eller juridisk spärr?**

Borttagningen av lagringskontot misslyckas om det finns minst en behållare med en juridisk spärruppsättning eller en **låst** tidsbaserad princip. En behållare med en olåst tidsbaserad princip skyddar inte mot borttagning av lagringskonto. Du måste ta bort alla juridiska spärrar och ta bort alla **låsta** behållare innan du kan ta bort lagringskontot. Information om borttagning av behållare finns i föregående fråga. Du kan också använda ytterligare borttagningsskydd för ditt lagringskonto med [Azure Resource Manager-lås](../../azure-resource-manager/management/lock-resources.md).

**Kan jag flytta data över olika blob-nivåer (heta, coola, arkivera) när blobben är i oföränderligt tillstånd?**

Ja, du kan använda kommandot Ange Blob-nivå för att flytta data över blob-nivåerna samtidigt som du behåller data i det kompatibla oföränderliga tillståndet. Oföränderlig lagring stöds på frekventa, svala och arkivblobbnivåer.

**Vad händer om jag inte betalat och mitt kvarhållningsintervall inte har gått ut?**

När det gäller utebliven betalning gäller normala datalagringsprinciper i enlighet med villkoren i ditt avtal med Microsoft. Allmän information finns i [Datahantering på Microsoft](https://www.microsoft.com/en-us/trust-center/privacy/data-management). 

**Finns det en utvärderingsversion eller en respitperiod för att bara testa funktionen?**

Ja. När en tidsbaserad bevarandeprincip först skapas är den i *olåst* tillstånd. I det här tillståndet kan du göra önskade ändringar i kvarhållningsintervallet, till exempel öka eller minska och till och med ta bort principen. När principen är låst förblir den låst tills kvarhållningsintervallet upphör att gälla. Den här låsta principen förhindrar borttagning och ändring av kvarhållningsintervallet. Vi rekommenderar starkt att du använder det *olåst tillstånd* som endast används för utvärderingsändamål och låser principen inom en 24-timmarsperiod. Dessa metoder hjälper dig att följa SEC 17a-4(f) och andra regler.

**Kan jag använda mjuk borttagning tillsammans med principer för oföränderlig blob?**

Ja, om dina efterlevnadskrav gör det möjligt att aktivera mjuk borttagning. [Mjuk borttagning för Azure Blob-lagring](storage-blob-soft-delete.md) gäller för alla behållare i ett lagringskonto oavsett en juridisk spärr eller tidsbaserad bevarandeprincip. Vi rekommenderar att du aktiverar mjuk borttagning för ytterligare skydd innan några oföränderliga WORM-principer tillämpas och bekräftas.

## <a name="next-steps"></a>Nästa steg

- [Ange och hantera principer för oföränderlighet för Blob-lagring](storage-blob-immutability-policies-manage.md)
- [Ange regler för att automatiskt nivå och ta bort blob-data med livscykelhantering](storage-lifecycle-management-concepts.md)
- [Mjuk borttagning för Azure Storage-blobar](../blobs/storage-blob-soft-delete.md)
- [Skydda prenumerationer, resursgrupper och resurser med Azure Resource Manager-lås](../../azure-resource-manager/management/lock-resources.md).
