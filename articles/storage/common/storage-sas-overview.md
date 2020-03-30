---
title: Bevilja begränsad åtkomst till data med signaturer för delad åtkomst (SAS)
titleSuffix: Azure Storage
description: Lär dig mer om hur du använder SIGNATURER för delad åtkomst (SAS) för att delegera åtkomst till Azure Storage-resurser, inklusive blobbar, köer, tabeller och filer.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 7a5967f52a187fe289c6fb1ca72af2d5fd17f010
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255241"
---
# <a name="grant-limited-access-to-azure-storage-resources-using-shared-access-signatures-sas"></a>Bevilja begränsad åtkomst till Azure Storage-resurser med hjälp av SIGNATURER för delad åtkomst (SAS)

En SAS -signatur (Shared Access Signature) ger säker delegerad åtkomst till resurser i ditt lagringskonto utan att äventyra säkerheten för dina data. Med en SAS har du detaljerad kontroll över hur en klient kan komma åt dina data. Du kan styra vilka resurser klienten kan komma åt, vilka behörigheter de har för dessa resurser och hur länge SAS är giltigt, bland andra parametrar.

## <a name="types-of-shared-access-signatures"></a>Typer av signaturer för delad åtkomst

Azure Storage stöder tre typer av signaturer för delad åtkomst:

- **Sas för användardelegation.** En sas för användardelegering är skyddad med Azure Active Directory (Azure AD) autentiseringsuppgifter och även av de behörigheter som angetts för SAS. En sas för användardelegering gäller endast Blob-lagring.

    Mer information om SAS för användardelegering finns i [Skapa en SAS (REST API) (User Delegation).](/rest/api/storageservices/create-user-delegation-sas)

- **Service SAS.** En service-SAS är skyddad med lagringskontonyckeln. En tjänst SAS-ombud har åtkomst till en resurs i endast en av Azure Storage-tjänsterna: Blob-lagring, Kölagring, Tabelllagring eller Azure-filer. 

    Mer information om tjänsten SAS finns i [Skapa en TJÄNST SAS (REST API)](/rest/api/storageservices/create-service-sas).

- **konto SAS.** Ett konto SAS är skyddat med lagringskontonyckeln. En konto-SAS delegerar åtkomst till resurser i en eller flera av lagringstjänsterna. Alla åtgärder som är tillgängliga via en tjänst eller användardelegation SAS är också tillgängliga via ett konto SAS. Dessutom, med kontot SAS, kan du delegera åtkomst till åtgärder som gäller på servicenivå, till exempel **Get/Set Service Properties** och **Get Service Stats-åtgärder.** Du kan också delegera åtkomst till läs-, skriv- och borttagningsåtgärder i blobcontainrar, tabeller, köer och filresurser som inte tillåts med en tjänst-SAS. 

    Om du vill ha mer information om kontot SAS [skapar du ett konto SAS (REST API)](/rest/api/storageservices/create-account-sas).

> [!NOTE]
> Microsoft rekommenderar att du använder Azure AD-autentiseringsuppgifter när det är möjligt som en säkerhetspraxis, i stället för att använda kontonyckeln, som lättare kan komprometteras. När programdesignen kräver signaturer med delad åtkomst för åtkomst till Blob-lagring använder du Azure AD-autentiseringsuppgifter för att skapa en SAS för användardelegering när det är möjligt för överlägsen säkerhet.

En signatur för delad åtkomst kan ha ett av två formulär:

- **Ad hoc SAS:** När du skapar en ad hoc SAS anges starttid, utgångstid och behörigheter för SAS i SAS URI (eller implicit, om starttid utelämnas). Alla typer av SAS kan vara en ad hoc SAS.
- **Service SAS med lagrad åtkomstprincip:** En lagrad åtkomstprincip definieras på en resursbehållare, som kan vara en blob-behållare, tabell, kö eller filresurs. Principen för lagrad åtkomst kan användas för att hantera villkor för en eller flera signaturer för delad åtkomst för en tjänst. När du associerar en service-SAS med en lagrad&mdash;åtkomstprincip ärver SAS begränsningarna&mdash;starttiden, utgångstiden och behörigheter som definierats för den lagrade åtkomstprincipen.

> [!NOTE]
> En sas eller ett konto SAS måste vara en ad hoc SAS. Principer för lagrad åtkomst stöds inte för SAS-användardelegeringen eller kontot SAS.

## <a name="how-a-shared-access-signature-works"></a>Så här fungerar en signatur för delad åtkomst

En signatur för delad åtkomst är en signerad URI som pekar på en eller flera lagringsresurser och innehåller en token som innehåller en särskild uppsättning frågeparametrar. Token anger hur resurserna kan nås av klienten. En av frågeparametrarna, signaturen, är konstruerad från SAS-parametrarna och signerad med nyckeln som användes för att skapa SAS. Den här signaturen används av Azure Storage för att auktorisera åtkomst till lagringsresursen.

### <a name="sas-signature"></a>SAS-signatur

Du kan signera en SAS på två sätt:

- Med en *användardelegeringsnyckel* som skapades med Azure Active Directory (Azure AD) autentiseringsuppgifter. En SAS för användardelegation signeras med användardelegeringsnyckeln.

    För att hämta användardelegeringsnyckeln och skapa SAS måste ett Azure AD-säkerhetsobjekt tilldelas en RBAC-roll (Role-based Access Control) som innehåller åtgärden **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey.** Detaljerad information om RBAC-roller med behörighet att hämta användardelegeringsnyckeln finns i [Skapa en SAS (REST API) (User Delegation SAS).](/rest/api/storageservices/create-user-delegation-sas)

- Med lagringskontonyckeln. Både en service-SAS och ett konto SAS är signerade med lagringskontonyckeln. Om du vill skapa en SAS som är signerad med kontonyckeln måste ett program ha åtkomst till kontonyckeln.

### <a name="sas-token"></a>SAS-token

SAS-token är en sträng som du genererar på klientsidan, till exempel genom att använda ett av Azure Storage-klientbiblioteken. SAS-token spåras inte av Azure Storage på något sätt. Du kan skapa ett obegränsat antal SAS-token på klientsidan. När du har skapat en SAS kan du distribuera den till klientprogram som kräver åtkomst till resurser i ditt lagringskonto.

När ett klientprogram tillhandahåller en SAS URI till Azure Storage som en del av en begäran, kontrollerar tjänsten SAS-parametrarna och signaturen för att verifiera att den är giltig för att godkänna begäran. Om tjänsten verifierar att signaturen är giltig godkänns begäran. Annars avvisas begäran med felkod 403 (Förbjuden).

Här är ett exempel på en tjänst SAS URI, som visar resursen URI och SAS-token:

![Komponenter i en tjänst SAS URI](./media/storage-sas-overview/sas-storage-uri.png)

## <a name="when-to-use-a-shared-access-signature"></a>När du ska använda en signatur för delad åtkomst

Använd en SAS när du vill ge säker åtkomst till resurser i ditt lagringskonto till alla klienter som annars inte har behörighet till dessa resurser.

Ett vanligt scenario där en SAS är användbar är en tjänst där användarna läser och skriver sina egna data till ditt lagringskonto. I ett scenario där ett lagringskonto lagrar användardata finns det två typiska designmönster:

1. Klienter laddar upp och laddar ned en via en klientproxytjänst, som utför autentisering. Den här proxytjänsten för frontend-proxy har fördelen att tillåta validering av affärsregler, men för stora mängder data eller transaktioner med stora volymer kan det vara dyrt eller svårt att skapa en tjänst som kan skalas för att matcha efterfrågan.

   ![Scenariodiagram: Proxytjänst för frontend](./media/storage-sas-overview/sas-storage-fe-proxy-service.png)

1. En förenklad tjänst autentiserar klienten efter behov och genererar sedan en SAS. När klientprogrammet tar emot SAS kan de komma åt lagringskontoresurser direkt med de behörigheter som definieras av SAS och för det intervall som tillåts av SAS. SAS minskar behovet att dirigera alla data genom klientproxytjänsten.

   ![Scenariodiagram: SAS-leverantörstjänst](./media/storage-sas-overview/sas-storage-provider-service.png)

Många verkliga tjänster kan använda en hybrid av dessa två metoder. Vissa data kan till exempel bearbetas och valideras via frontend-proxyn, medan andra data sparas och/eller läses direkt med SAS.

Dessutom krävs en SAS för att tillåta åtkomst till källobjektet i en kopiering i vissa scenarier:

- När du kopierar en blob till en annan blob som finns i ett annat lagringskonto måste du använda en SAS för att auktorisera åtkomst till källblobben. Du kan också använda en SAS för att auktorisera åtkomst till målblobben också.
- När du kopierar en fil till en annan fil som finns i ett annat lagringskonto måste du använda en SAS för att auktorisera åtkomst till källfilen. Du kan också använda en SAS för att auktorisera åtkomst till målfilen också.
- När du kopierar en blob till en fil eller en fil till en blob måste du använda en SAS för att auktorisera åtkomst till källobjektet, även om käll- och målobjekten finns i samma lagringskonto.

## <a name="best-practices-when-using-sas"></a>Metodtips när du använder SAS

När du använder signaturer med delad åtkomst i dina program måste du vara medveten om två potentiella risker:

- Om en SAS läcker kan den användas av alla som skaffar den, vilket kan äventyra ditt lagringskonto.
- Om en SAS som tillhandahålls ett klientprogram upphör att gälla och programmet inte kan hämta en ny SAS från din tjänst, kan programmets funktioner hindras.

Följande rekommendationer för användning av signaturer för delad åtkomst kan bidra till att minska dessa risker:

- **Använd alltid HTTPS** för att skapa eller distribuera en SAS. Om en SAS skickas över HTTP och fångas upp, en angripare som utför en man-in-the-middle attack kan läsa SAS och sedan använda den precis som den avsedda användaren kan ha, potentiellt äventyra känsliga data eller möjliggör data korruption av den illvilliga användaren.
- **Använd en SAS för användardelegation när det är möjligt.** En SAS för användardelegering ger överlägsen säkerhet till en service-SAS eller ett konto SAS. En sas för användardelegering är skyddad med Azure AD-autentiseringsuppgifter, så att du inte behöver lagra din kontonyckel med din kod.
- **Ha en återkallningsplan på plats för en SAS.** Se till att du är beredd att svara om en SAS äventyras.
- **Definiera en lagrad åtkomstprincip för en service-SAS.** Med principer för lagrad åtkomst kan du återkalla behörigheter för en service-SAS utan att behöva återskapa lagringskontonycklarna. Ställ in utgångsdatumet på dessa mycket långt i framtiden (eller oändlig) och se till att det uppdateras regelbundet för att flytta den längre in i framtiden.
- **Använd utgångstider på kort sikt för en ad hoc SAS-tjänst SAS eller konto SAS.** På så sätt gäller även om en SAS äventyras, det är giltigt endast för en kort tid. Den här metoden är särskilt viktig om du inte kan referera till en lagrad åtkomstprincip. På kort sikt förfallotider begränsar också mängden data som kan skrivas till en blob genom att begränsa den tid som är tillgänglig för överföring till den.
- **Låt klienter automatiskt förnya SAS om det behövs.** Klienter bör förnya SAS i god tid före förfallodatumet, för att ge tid för återförsök om tjänsten som tillhandahåller SAS inte är tillgänglig. Om din SAS är avsedd att användas för ett litet antal omedelbara, kortlivade åtgärder som förväntas slutföras inom utgångsperioden, kan detta vara onödigt eftersom SAS inte förväntas förnyas. Men om du har klient som rutinmässigt gör förfrågningar via SAS, då möjligheten att upphöra kommer in i bilden. Det viktigaste är att balansera behovet av att SAS blir kortlivade (som tidigare nämnts) med behovet av att se till att kunden begär förnyelse tillräckligt tidigt (för att undvika störningar på grund av att SAS löper ut innan den lyckades förnyas).
- **Var försiktig med SAS starttid.** Om du ställer in starttiden för en SAS till **nu**, sedan på grund av klocksnedställning (skillnader i aktuell tid beroende på olika datorer), kan fel observeras periodvis under de första minuterna. I allmänhet ställer du in starttiden till minst 15 minuter tidigare. Eller, inte ställa in det alls, vilket kommer att göra det giltigt omedelbart i alla fall. Detsamma gäller i allmänhet för utgångstid samt - kom ihåg att du kan observera upp till 15 minuters klocka skeva i båda riktningarna på en begäran. För klienter som använder en REST-version före 2012-02-12 är den maximala varaktigheten för en SAS som inte refererar till en lagrad åtkomstprincip 1 timme och alla principer som anger längre sikt än det kommer att misslyckas.
- **Var försiktig med SAS datetime-format.** Om du ställer in starttid och/eller utgång för en SAS, för vissa verktyg (till exempel för kommandoradsverktyget AzCopy) behöver du datetime-formatet vara '+%Y-%m-%dT%H:%M:%SZ', särskilt sekunder för att det ska fungera med SAS-token.  
- **Var specifik med den resurs som ska nås.** En bästa säkerhetspraxis är att ge en användare de lägsta behörigheterna som krävs. Om en användare bara behöver läsbehörighet till en enda entitet, ge dem sedan läsbehörighet till den enda entiteten och inte läs/skriv/ta bort åtkomst till alla entiteter. Detta bidrar också till att minska skadan om en SAS äventyras eftersom SAS har mindre ström i händerna på en angripare.
- **Förstå att ditt konto kommer att faktureras för all användning, inklusive via en SAS.** Om du ger skrivåtkomst till en blob kan en användare välja att ladda upp en 200 GB-blob. Om du har gett dem läsbehörighet också kan de välja att ladda ned den 10 gånger, vilket medför 2 TB i utgående kostnader för dig. Återigen, ge begränsade behörigheter för att minska potentiella åtgärder för skadliga användare. Använd kortlivade SAS för att minska detta hot (men var uppmärksam på klockan skeva på sluttiden).
- **Validera data som skrivits med hjälp av en SAS.** När ett klientprogram skriver data till ditt lagringskonto bör du tänka på att det kan vara problem med dessa data. Om ditt program kräver att data valideras eller auktoriseras innan det är klart att använda, bör du utföra den här valideringen när data har skrivits och innan de används av ditt program. Den här metoden skyddar också mot skadade eller skadliga data som skrivs till ditt konto, antingen av en användare som korrekt förvärvade SAS eller av en användare som utnyttjar en läckt SAS.
- **Vet när du inte ska använda en SAS.** Ibland överväger riskerna med en viss åtgärd mot ditt lagringskonto fördelarna med att använda en SAS. För sådana åtgärder skapar du en tjänst på mellannivå som skriver till ditt lagringskonto efter att affärsregelverifiering, autentisering och granskning har utförts. Ibland är det också enklare att hantera åtkomst på andra sätt. Om du till exempel vill göra alla blobbar i en behållare läsbara, kan du göra behållaren offentlig i stället för att tillhandahålla en SAS till varje klient för åtkomst.
- **Använd Azure Monitor- och Azure Storage-loggar för att övervaka ditt program.** Du kan använda Azure Monitor och lagringsanalysloggning för att observera eventuella ökningar av auktoriseringsfel på grund av ett avbrott i din SAS-providertjänst eller på oavsiktlig borttagning av en lagrad åtkomstprincip. Mer information finns [i Azure Storage-mått i Azure Monitor](storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) och Azure Storage Analytics [loggning](storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="get-started-with-sas"></a>Komma igång med SAS

Information om hur du kommer igång med signaturer för delad åtkomst finns i följande artiklar för varje SAS-typ.

### <a name="user-delegation-sas"></a>SAS för användardelegering

- [Skapa en SAS för användardelegering för en behållare eller blob med PowerShell](../blobs/storage-blob-user-delegation-sas-create-powershell.md)
- [Skapa en SAS för användardelegering för en behållare eller blob med Azure CLI](../blobs/storage-blob-user-delegation-sas-create-cli.md)
- [Skapa en SAS för användardelegering för en behållare eller blob med .NET](../blobs/storage-blob-user-delegation-sas-create-dotnet.md)

### <a name="service-sas"></a>Service SAS

- [Skapa en service-SAS för en behållare eller blob med .NET](../blobs/storage-blob-service-sas-create-dotnet.md)

### <a name="account-sas"></a>Sas för konto

- [Skapa ett konto SAS med .NET](storage-account-sas-create-dotnet.md)

## <a name="next-steps"></a>Nästa steg

- [Delegera åtkomst med en signatur för delad åtkomst (REST API)](/rest/api/storageservices/delegate-access-with-shared-access-signature)
- [Skapa en SAS -för användardelegation (REST API)](/rest/api/storageservices/create-user-delegation-sas)
- [Skapa en tjänst SAS (REST API)](/rest/api/storageservices/create-service-sas)
- [Skapa ett KONTO SAS (REST API)](/rest/api/storageservices/create-account-sas)
