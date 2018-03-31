---
title: Säkerhetsguiden för Azure Storage | Microsoft Docs
description: Information på många metoder för att skydda Azure Storage, inklusive men inte begränsat till RBAC, Storage Service-kryptering, kryptering på klientsidan, SMB 3.0 och Azure Disk Encryption.
services: storage
author: craigshoemaker
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 03/06/2018
ms.author: cshoe
ms.openlocfilehash: 73353d3b27b65298d804a138b33cdf2de23726fe
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2018
---
# <a name="azure-storage-security-guide"></a>Azure Storage-säkerhetsguiden

## <a name="overview"></a>Översikt

Azure Storage tillhandahåller en omfattande uppsättning säkerhetsfunktioner som tillsammans ger utvecklare möjligheten att skapa säkra program:

- Alla data som skrivs till Azure Storage krypteras automatiskt med [Storage Service kryptering (SSE)](storage-service-encryption.md). Mer information finns i [om standard kryptering för Azure-Blobbar, filer, tabell och Queue Storage](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).
- Lagringskontot själva kan skyddas med hjälp av rollbaserad åtkomstkontroll och Azure Active Directory. 
- Data kan skyddas vid överföring mellan en program- och Azure med hjälp av [Client Side Encryption](../storage-client-side-encryption.md), HTTPS- eller SMB 3.0.  
- Operativsystemet och datadiskarna som används av virtuella Azure-datorer kan krypteras med [Azure Disk Encryption](../../security/azure-security-disk-encryption.md). 
- Delegerad åtkomst till dataobjekt i Azure Storage kan beviljas med [signaturer för delad åtkomst](../storage-dotnet-shared-access-signature-part-1.md).

Den här artikeln innehåller en översikt över var och en av dessa funktioner som kan användas med Azure Storage. Länkar tillhandahålls till artiklar som ger information om varje funktion så kan du enkelt göra ytterligare undersökningar på varje avsnitt.

Här följer ämnena i den här artikeln:

* [Hantering av plan säkerhet](#management-plane-security) – skydda ditt Lagringskonto

  Management-plan består av de resurser som används för att hantera ditt lagringskonto. Det här avsnittet beskriver distributionsmodell Azure Resource Manager och hur du använder rollbaserad åtkomstkontroll (RBAC) för att styra åtkomsten till dina lagringskonton. Det åtgärdas även hantera din lagringskontonycklar och hur du återskapa dem.
* [Data plan säkerhet](#data-plane-security) – skydda åtkomsten till dina Data

  I det här avsnittet ska vi titta på att tillåta åtkomst till de faktiska dataobjekt i ditt lagringskonto, till exempel blobbar, filer, köer och tabeller som använder signaturer för delad åtkomst och åtkomstregler lagras. Vi tar upp både servicenivåer SAS och konto-nivå SAS. Vi också se hur du begränsar åtkomsten till en specifik IP-adress (eller intervall av IP-adresser), hur du begränsar det protokoll som används för HTTPS och hur återkalla en signatur för delad åtkomst utan att vänta på att gå ut.
* [Kryptering vid överföring](#encryption-in-transit)

  Det här avsnittet beskrivs hur du skyddar data vid överföring till eller från Azure Storage. Lär dig om att rekommenderade använda HTTPS och kryptering som används av SMB 3.0 för Azure-filresurser. Vi kommer också ta en titt på klientsidan kryptering, som gör det möjligt att kryptera data innan det överförs till lagring i ett klientprogram och dekryptera data när det överförs utanför lagring.
* [Kryptering i vila](#encryption-at-rest)

  Vi kommer om Storage Service kryptering (SSE), som nu aktiveras automatiskt för nya och befintliga lagringskonton. Vi kommer också titta på hur du kan använda Azure Disk Encryption och utforska grundläggande skillnader och fall av diskkryptering jämfört med SSE jämfört med kryptering på klientsidan. Vi ser kort på FIPS-kompatibilitet för USA Offentliga datorer.
* Med hjälp av [Storage Analytics](#storage-analytics) granska åtkomsten till Azure Storage

  Det här avsnittet beskrivs hur du hittar information i loggarna storage analytics för en begäran. Vi ta en titt på verkliga storage analytics loggdata och se hur du fram om en begäran görs med lagringskontonyckel med en signatur för delad åtkomst eller anonymt, och om den har lyckats eller misslyckats.
* [Aktivera webbläsarbaserade klienter med hjälp av CORS](#Cross-Origin-Resource-Sharing-CORS)

  Det här avsnittet innehåller information om hur du tillåter resursdelning för korsande ursprung (CORS). Lär dig om korsdomänåtkomst och hur du hanterar med CORS inbyggda funktioner i Azure Storage.

## <a name="management-plane-security"></a>Hantering av plan säkerhet
Management-plan består av åtgärder som påverkar det storage-kontot. Du kan till exempel skapa eller ta bort ett lagringskonto, hämta en lista över storage-konton i en prenumeration, hämta lagringskontonycklarna eller återskapa lagringskontonycklar.

När du skapar ett nytt lagringskonto, Välj en distributionsmodell för klassiska eller Resource Manager. Den klassiska modellen för att skapa resurser i Azure kan endast fullständiga åtkomst till prenumerationen och i sin tur storage-konto.

Den här guiden fokuserar på Resource Manager-modellen är det rekommenderade sättet för att skapa storage-konton. Med den Resource Manager storage-konton, snarare än ger tillgång till hela prenumerationen, kan du styra åtkomsten på en mer begränsad hantering plan med hjälp av rollbaserad åtkomstkontroll (RBAC).

### <a name="how-to-secure-your-storage-account-with-role-based-access-control-rbac"></a>Hur du skyddar ditt lagringskonto med rollbaserad åtkomstkontroll (RBAC)
Vi pratar om RBAC är och hur du kan använda den. Varje Azure-prenumerationen har en Azure Active Directory. Användare, grupper och program från katalogen kan beviljas åtkomst för att hantera resurser i Azure-prenumeration som använder Resource Manager-distributionsmodellen. Den här typen av säkerhet kallas för rollbaserad åtkomstkontroll (RBAC). Du kan använda för att hantera åtkomst i [Azure-portalen](https://portal.azure.com/), [Azure CLI-verktygen](../../cli-install-nodejs.md), [PowerShell](/powershell/azureps-cmdlets-docs), eller [Azure Storage Resource Provider REST API: er för](https://msdn.microsoft.com/library/azure/mt163683.aspx).

Med Resource Manager-modellen placera storage-konto i en grupp och kontrollera åtkomst till företagsresurser med den specifika storage-konto med Azure Active Directory management plan. Du kan till exempel ge särskilda användare möjlighet att komma åt lagringskontonycklar, medan andra användare kan visa information om lagringskontot, men det går inte att komma åt lagringskontonycklarna.

#### <a name="granting-access"></a>Att bevilja åtkomst
Genom att tilldela användare, grupper och program i det högra omfånget rollen RBAC beviljas åtkomst. Om du vill bevilja åtkomst till hela prenumerationen kan du tilldela en roll på prenumerationsnivån. Du kan bevilja åtkomst till alla resurser i en resursgrupp genom att bevilja behörigheter till resursgruppen. Du kan också tilldela specifika roller till specifika resurser, till exempel storage-konton.

Här är de viktigaste aspekterna som du behöver veta om att använda RBAC att komma åt hanteringsåtgärder för ett Azure Storage-konto:

* När du tilldelar åtkomst, tilldela en roll i princip för kontot som du vill ha åtkomst. Du kan styra åtkomst till de åtgärder som används för att hantera detta lagringskonto, men inte dataobjekt i kontot. Exempelvis kan du bevilja behörigheten att hämta egenskaperna för storage-konto (till exempel redundans), men inte till en behållare eller data i en behållare i Blob Storage.
* Du kan ge dem behörighet att läsa lagringskontonycklarna för någon behörighet att komma åt dataobjekt i storage-konto och användaren kan sedan använda dessa nycklar för att få åtkomst till blobbar, köer, tabeller och filer.
* Roller kan tilldelas till ett specifikt användarkonto, en grupp av användare eller till ett visst program.
* Varje roll har en lista över och inte åtgärder. Till exempel virtuella deltagarrollen har en åtgärd av ”listKeys” som gör det möjligt för lagringskontonycklar att läsas. Deltagaren har ”inte åtgärder”, som uppdatering åtkomst för användare i Active Directory.
* Roller för lagring inkluderar (men är inte begränsade till) följande roller:

  * Ägaren – de kan hantera allt, inklusive åtkomst.
  * Deltagare – de kan göra något ägaren kan förutom tilldela åtkomst. Någon med den här rollen kan visa och återskapa lagringskontonycklar. De kan lagringskontonycklar, för att komma åt dataobjekt.
  * Läsare kan visa information om lagringskontot utom hemligheter. Till exempel om du tilldelar en roll med reader-behörighet för storage-konto till någon de kan visa egenskaperna för storage-konto, men de kan inte göra ändringar i egenskaperna för eller visa lagringskontonycklarna.
  * Storage-konto deltagare – de kan hantera storage-konto – de kan läsa prenumerationens resursgrupper och resurser, och skapa och hantera distributionen av resursgrupper prenumeration. De kan också komma åt lagringskontonycklar, vilket i sin tur innebär att de kan komma åt dataplan.
  * Administratör för användaråtkomst – de hantera användarnas åtkomst till lagringskontot. De kan till exempel ge läsaren åtkomst till en specifik användare.
  * Virtual Machine-deltagare – som de kan hantera virtuella datorer men inte lagringskontot som de är anslutna. Den här rollen kan visa lagringskontonycklar, vilket innebär att användaren som du tilldelar den här rollen kan uppdatera dataplan.

    En användare kan skapa en virtuell dator ha måste de för att kunna skapa motsvarande VHD-filen i ett lagringskonto. För att göra det, behöver de för att kunna hämta lagringskontots åtkomstnyckel och skickar den till API: N att skapa den virtuella datorn. De måste därför ha den här behörigheten så att de kan visa en lista över nycklar till lagringskontot.
* Möjligheten att definiera anpassade roller är en funktion som hjälper dig att skapa en uppsättning åtgärder från en lista över tillgängliga åtgärder kan utföras på Azure-resurser.
* Användaren måste anges i Azure Active Directory innan du kan tilldela en roll till dem.
* Du kan skapa en rapport över som beviljats/återkallas vilken typ av åtkomst till och från vilken och på vilka scope med PowerShell eller Azure CLI.

#### <a name="resources"></a>Resurser
* [Azure Active Directory rollbaserad åtkomstkontroll](../../active-directory/role-based-access-control-configure.md)

  Den här artikeln förklarar Azure Active Directorys rollbaserade åtkomstkontroll och hur den fungerar.
* [RBAC: inbyggda roller](../../active-directory/role-based-access-built-in-roles.md)

  Den här artikeln beskrivs alla inbyggda roller finns i RBAC.
* [Förstå Resource Manager-distribution och klassisk distribution](../../azure-resource-manager/resource-manager-deployment-model.md)

  Den här artikeln beskriver Resource Manager-distribution och klassiska distributionsmodeller och förklarar fördelarna med att använda Resource Manager och resursgrupper. Den förklarar hur Azure Compute, nätverk och lagring leverantörer fungerar med Resource Manager-modellen.
* [Hantera rollbaserad åtkomstkontroll med REST API](../../active-directory/role-based-access-control-manage-access-rest.md)

  Den här artikeln visar hur du använder REST API för att hantera RBAC.
* [Azure Storage Resource Provider REST API-referens](https://msdn.microsoft.com/library/azure/mt163683.aspx)

  Den här API-referensen beskriver API: er som du kan använda för att hantera ditt lagringskonto via programmering.
* [Utvecklarhandbok för autentisering med Azure Resource Manager API](http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/)

  Den här artikeln visar hur du autentiserar med Resource Manager-API: er.
* [Rollbaserad åtkomstkontroll i Microsoft Azure från Ignite](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

  Det här är en länk till en video på Channel 9 från 2015 MS Ignite-konferensen. I den här sessionen talar de om åtkomsthantering och rapporteringsfunktioner i Azure och utforskar bästa praxis när det gäller att säkra åtkomst till Azure-prenumerationer med Azure Active Directory.

### <a name="managing-your-storage-account-keys"></a>Hantera dina nycklar för Lagringskonto
Lagringskontonycklar är 512-bitars strängar som skapats av Azure som tillsammans med lagringskontots namn, kan användas för åtkomst till de dataobjekt som lagras i lagringskontot, till exempel, blobbar, entiteter i en tabell, Kömeddelanden och filer på en filresurs på Azure. Kontrollera åtkomst till lagring konto nycklar kontroller har åtkomst till dataplan för det lagringskontot.

Varje lagringskonto har två nycklar som kallas ”nyckel 1” och ”nyckel 2” i den [Azure-portalen](http://portal.azure.com/) och i PowerShell-cmdlets. Dessa kan återskapas manuellt med hjälp av en av flera metoder, inklusive men inte begränsat till med hjälp av den [Azure-portalen](https://portal.azure.com/), PowerShell, Azure CLI eller via programmering med Storage-klientbiblioteket för .NET eller Azure Storage Services REST API.

Det finns många anledningar för att återskapa nycklar för ditt lagringskonto.

* Du kan återskapa dem regelbundet av säkerhetsskäl.
* Du kan återskapa din lagringskontonycklar om någon hanteras och hacka till ett program som du kan hämta den nyckel som hårdkodad eller sparats i en konfigurationsfil och få fullständig åtkomst till ditt lagringskonto.
* En annan fallet för nycklar är om ditt team använder ett Lagringsutforskaren program som behåller lagringskontots åtkomstnyckel och en av gruppmedlemmarna lämnar. Programmet fortsätter att arbeta, vilket ger dem åtkomst till ditt lagringskonto när de är borta. Det är faktiskt primärt därför de skapats kontonivå signaturer för delad åtkomst – du kan använda en kontonivå SAS istället för att lagra åtkomstnycklarna i en konfigurationsfil.

#### <a name="key-regeneration-plan"></a>Nyckeln återskapas plan
Du behöver bara återskapa nyckeln som du använder utan lite planering. Om du gör det kan du beskärs all åtkomst till den storage-konto, vilket kan orsaka avbrott i större. Det är därför det finns två nycklar. Du bör Generera en nyckel i taget.

Innan du återskapar dina nycklar måste du ha en lista över alla program som är beroende av lagringskontot, samt andra tjänster som du använder i Azure. Till exempel om du använder Azure Media Services som är beroende av ditt lagringskonto, måste du synkronisera åtkomstnycklarna med medietjänsten när du återskapar nyckeln. Om du använder alla program, till exempel en lagringsutforskare behöver du nya nycklar för dessa program samt. Om du har virtuella datorer vars VHD-filer som lagras i lagringskontot kan påverkas de inte av de lagringskontonycklarna genereras.

Du kan återskapa dina nycklar i Azure-portalen. De kan ta upp till 10 minuter att synkroniseras mellan lagringstjänster när nycklar genereras.

När du är klar kan du här är den allmänna processen med information om hur du ska ändra din nyckel. I det här fallet är förutsätter att du använder nyckel 1 och du ska ändra allt för att använda nyckel 2 i stället.

1. Återskapa nyckeln 2 för att säkerställa att den är skyddad. Du kan göra detta i Azure-portalen.
2. Ändra lagringsnyckel om du vill använda nyckel 2 nytt värde i alla program där lagringsnyckeln lagras. Testa och publicera programmet.
3. När alla program och tjänster som är igång och körs korrekt återskapa nyckel 1. Detta säkerställer att vem som helst som du inte har uttryckligen gett den nya nyckeln kommer inte längre åtkomst till lagringskontot.

Om du använder nyckel 2 kan du använda samma process, men omvänd nyckelnamnen.

Du kan migrera över ett par dagar, ändra varje program för att använda den nya nyckeln och publicera den. När alla är klar bör du gå tillbaka och återskapa den gamla nyckeln så att den inte längre fungerar.

Ett annat alternativ är att placera lagringskontots åtkomstnyckel i en [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) som en hemlighet och ha dina program hämta nyckeln därifrån. När du återskapa nyckeln och uppdaterar Azure Key Vault måste sedan program som inte distribueras eftersom de ska hämta den nya nyckeln från Azure Key Vault automatiskt. Observera att du kan låta programmet läsa nyckeln varje gång du behöver den eller du kan cachelagra i minnet och om det misslyckas när du använder den, hämta nyckeln igen från Azure Key Vault.

Med hjälp av Azure Key Vault lägger till ytterligare en säkerhetsnivå för dina lagringsnycklar. Om du använder den här metoden kan har du aldrig lagring viktiga hårdkodad i en konfigurationsfil, vilket tar bort den minimering av någon får tillgång till nycklarna utan uttryckligt tillstånd.

En annan fördel med att använda Azure Key Vault är att du kan också styra åtkomst till dina nycklar med Azure Active Directory. Det innebär att du kan bevilja åtkomst till program som behöver hämta nycklarna från Azure Key Vault och vet att andra program inte tillgång till nycklarna utan att ge dem behörighet specifikt handfull.

Obs: Det rekommenderas att använda endast en av nycklarna i alla program på samma gång. Om du använder nyckel 1 på vissa platser och nyckel 2 i andra, kan du inte rotera dina nycklar utan ett program att förlora åtkomsten.

#### <a name="resources"></a>Resurser
* [Om Azure Storage-konton](storage-create-storage-account.md#regenerate-storage-access-keys)

  Den här artikeln ger en översikt över lagringskonton och beskriver visa, kopiera och återskapa åtkomstnycklar för lagring.
* [Azure Storage Resource Provider REST API-referens](https://msdn.microsoft.com/library/mt163683.aspx)

  Den här artikeln innehåller länkar till specifika artiklar om hur du hämtar lagringskontonycklar och återskapa lagringskontonycklar för ett Azure-konto med hjälp av REST-API. Obs: Detta är Resource Manager storage-konton.
* [Åtgärder på storage-konton](https://msdn.microsoft.com/library/ee460790.aspx)

  Den här artikeln i Storage Service Manager REST API Reference innehåller länkar till specifika artiklar om hämtning och återskapa lagringskontonycklar med hjälp av REST-API. Obs: Detta är om klassiska lagringskonton.
* [Lämna för att nyckelhantering – hantera åtkomst till Azure Storage-data med hjälp av Azure AD](http://www.dushyantgill.com/blog/2015/04/26/say-goodbye-to-key-management-manage-access-to-azure-storage-data-using-azure-ad/)

  Den här artikeln visar hur du använder Active Directory för att styra åtkomsten till din Azure Storage-nycklar i Azure Key Vault. Den visar även hur du använder ett Azure Automation-jobb för att återskapa nycklar timme.

## <a name="data-plane-security"></a>Säkerhet för data-plan
Plan för datasäkerhet refererar till de metoder som används för att säkra dataobjekt som lagras i Azure Storage-blobbar, köer, tabeller och filer. Vi har sett metoder för att kryptera data och säkerhet under överföring av data, men hur skaffar du om hur du styr åtkomst till objekten?

Det finns två metoder för att auktorisera åtkomst till dataobjekt sig själva. Dessa inkluderar Kontrollera åtkomst till lagringskontonycklarna och använder signaturer för delad åtkomst för att bevilja åtkomst till specifika dataobjekt för en viss tidsperiod.

Dessutom för Blob Storage, kan du tillåta allmän åtkomst till dina blobbar genom att ange åtkomstnivån för den behållare som innehåller blobar i enlighet med detta. Om du ställer in åtkomst för en behållare för Blob eller behållare kommer du att kunna offentlig läsbehörighet för blobbar i behållaren. Det innebär att alla med en URL som pekar på en blobb i behållaren kan öppna den i en webbläsare utan hjälp av en signatur för delad åtkomst eller lagringskontonycklarna.

Förutom att begränsa åtkomst via auktorisering kan du också använda [brandväggar och virtuella nätverk](storage-network-security.md) att begränsa åtkomsten till lagringskontot baserat på Nätverksregler.  I det här sättet kan du neka åtkomst till offentliga internet-trafik och för att bevilja åtkomst till vissa specifika virtuella Azure-nätverk eller offentliga internet IP-adressintervall.


### <a name="storage-account-keys"></a>Lagringskontonycklar
Lagringskontonycklar är 512-bitars strängar som skapats av Azure som tillsammans med lagringskontots namn, kan användas för åtkomst till dataobjekt som lagras i lagringskontot.

Du kan till exempel läsa blobbar, skriva till köer, skapa tabeller och ändra filer. Många av de här åtgärderna kan utföras via Azure-portalen eller med hjälp av ett av många Lagringsutforskaren program. Du kan också skriva kod för att använda REST-API: et eller någon av Lagringsklientbiblioteken för att utföra dessa åtgärder.

Enligt beskrivningen i avsnittet på den [Management plan säkerhet](#management-plane-security), ha åtkomst till av lagringsnycklar för ett klassiskt storage-konto kan beviljas genom att ge fullständig åtkomst till Azure-prenumerationen. Åtkomst till av lagringsnycklar för ett lagringskonto med hjälp av Azure Resource Manager-modellen kan kontrolleras via rollbaserad åtkomstkontroll (RBAC).

### <a name="how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies"></a>Hur du delegerar åtkomst till objekt i ditt konto med signaturer för delad åtkomst och åtkomstregler lagras
En signatur för delad åtkomst är en sträng som innehåller en säkerhetstoken som kan kopplas till en URI som hjälper dig att delegera åtkomst till lagringsobjekt och ange begränsningarna, t.ex behörigheter och datum/tid-intervall för åtkomst.

Du kan bevilja åtkomst till BLOB-behållare, Kömeddelanden, filer och tabeller. Med tabeller, kan du faktiskt bevilja behörighet att komma åt en mängd entiteter i tabellen genom att ange de partition och raden nyckelintervall som du vill att användaren ska ha åtkomst. Till exempel om du har data som lagras med en partitionsnyckel för geografiska tillstånd, du kan ge någon åtkomst till endast data för California.

Ett annat exempel är kan du ge en SAS-token som gör det möjligt att skriva poster till en kö för ett webbprogram och ger en arbetare rollen programmet en SAS-token hämta meddelanden från kön och bearbetar dem. Eller du kan ge en kund en SAS-token som de kan använda för att ladda upp bilder till en behållare i Blob Storage och ge web application behörighet att läsa dessa bilder. I båda fallen finns en uppdelning av säkerhetsskäl – varje program kan få precis den åtkomsten som krävs för att kunna utföra sina uppgifter. Detta är möjligt genom att använda signaturer för delad åtkomst.

#### <a name="why-you-want-to-use-shared-access-signatures"></a>Varför du vill använda signaturer för delad åtkomst
Varför kan du använda en SAS istället för att bara ge ut din lagringskontonyckel som är mycket enklare? Lämnar ut din lagringskontonyckel är som att dela nycklar kungarikets din lagring. Fullständig åtkomst beviljas. Någon kan använda dina nycklar och överföra sina hela musikbibliotek till ditt lagringskonto. De kan även ersätta filerna med virus infekterade versioner eller stjäla dina data. Ge bort obegränsad åtkomst till ditt lagringskonto är något som inte bör noga.

Du kan ge bara de behörigheter som krävs för en begränsad tidsperiod på en klient med signaturer för delad åtkomst. Till exempel om någon laddar upp en blobb till ditt konto, ge du dem skrivbehörighet för bara tillräckligt med tid att överföra blobben (beroende på storleken på blobben, naturligtvis). Och om du ändrar dig kan du återkalla den åtkomsten.

Dessutom kan du ange att begäranden som görs med hjälp av en SAS begränsas till vissa IP-adress eller IP-adressintervall externa till Azure. Du kan också kräva att begäranden som görs med ett visst protokoll (HTTPS eller HTTP/HTTPS). Detta innebär att om du bara vill tillåta HTTPS-trafik, du kan ange det begärda protokollet till HTTPS endast och HTTP-trafik blockeras.

#### <a name="definition-of-a-shared-access-signature"></a>Definition av en signatur för delad åtkomst
En signatur för delad åtkomst är en uppsättning frågeparametrar läggs till URL: er för resursen

som innehåller information om åtkomst tillåts och hur lång tid som åtkomst tillåts. Här är ett exempel. den här URI ger läsbehörighet till en blobb i fem minuter. Observera att SAS fråga parametrar måste vara URL-kodade, till exempel % 3A för kolon (:) eller % 20 för ett blanksteg.

```
http://mystorage.blob.core.windows.net/mycontainer/myblob.txt (URL to the blob)
?sv=2015-04-05 (storage service version)
&st=2015-12-10T22%3A18%3A26Z (start time, in UTC time and URL encoded)
&se=2015-12-10T22%3A23%3A26Z (end time, in UTC time and URL encoded)
&sr=b (resource is a blob)
&sp=r (read access)
&sip=168.1.5.60-168.1.5.70 (requests can only come from this range of IP addresses)
&spr=https (only allow HTTPS requests)
&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D (signature used for the authentication of the SAS)
```

#### <a name="how-the-shared-access-signature-is-authenticated-by-the-azure-storage-service"></a>Hur signatur för delad åtkomst autentiseras av Azure Storage-tjänsten
När lagringstjänsten tar emot begäran, använder indatafrågan parametrar och skapar en signatur med samma metod som det anropande programmet. Den jämför sedan två signaturer. Om användaren accepterar Kontrollera lagringstjänsten storage service-version för att kontrollera att den är giltig, kontrollera att den aktuella datum och tid är inom den angivna tidsperioden, kontrollera åtkomst begärd motsvarar begäran, osv.

Till exempel med våra URL misslyckas om URL: en pekar på en fil i stället för en blob denna begäran eftersom den anger att signatur för delad åtkomst är för en blob. Om kommandot REST anropas var att uppdatera en blob kan misslyckas det eftersom signatur för delad åtkomst anger att bara läsbehörighet tillåts.

#### <a name="types-of-shared-access-signatures"></a>Typer av signaturer för delad åtkomst
* En tjänstnivå SAS kan användas för åtkomst till specifika resurser i ett lagringskonto. Några exempel på detta hämtar en lista över blobbar i en behållare för att hämta en blob, uppdaterar en entitet i en tabell, lägga till meddelanden till en kö eller överför en fil till en filresurs.
* En kontonivå SAS kan användas för åtkomst till allt som en tjänstnivå SAS kan användas för. Dessutom kan den ge alternativ till resurser som inte tillåts med en tjänstnivå SAS, till exempel möjligheten att skapa behållare, tabeller, köer och filresurser. Du kan också ange åtkomst för flera tjänster på samma gång. Du kan till exempel ge någon åtkomst till både blobbar och filer på ditt lagringskonto.

#### <a name="creating-a-sas-uri"></a>Skapa en SAS-URI
1. Du kan skapa en URI på begäran, definiera frågeparametrar varje gång.

   Den här metoden är flexibla, men om du har en logisk uppsättning parametrar som liknar varje gång med hjälp av en åtkomstprincip för lagras är en bättre uppfattning.
2. Du kan skapa en åtkomstprincip lagras för en hela behållaren, filresurser, tabell eller kön. Du kan sedan använda den som underlag för SAS-URI som du skapar. Behörigheter som baseras på lagras åtkomstprinciper kan enkelt återkallas. Du kan ha upp till fem principer som definierats för varje behållare, kön, tabell eller filresurs.

   Om du tänker har många användare kan läsa blobbar i en specifik behållare kan du skapa en åtkomstprincip för lagras som säger ”ge läsbehörighet” och andra inställningar som ska vara samma varje gång. Du kan skapa ett SAS-URI med inställningarna för åtkomstprincip lagras och ange förfallodatum för datum/tid. Fördelen med detta är att du inte behöver ange alla frågeparametrar varje gång.

#### <a name="revocation"></a>Återkallade certifikat
Anta att din SAS har komprometterats eller om du vill ändra på grund av företagets säkerhet och regelefterlevnad krav. Hur du återkalla åtkomst till en resurs med hjälp av den SAS? Det beror på hur du skapar SAS-URI.

Om du använder ad hoc-URI: er har tre alternativ. Du kan utfärda SAS-token med kort giltighetsprinciper och vänta SAS ska upphöra att gälla. Du kan byta namn på eller ta bort resursen (förutsatt att token har begränsats till ett enda objekt). Du kan ändra lagringskontonycklar. Det här sista alternativet kan ha en betydande inverkan, beroende på hur många tjänster använder detta lagringskonto och förmodligen inte något du vill göra utan lite planering.

Om du använder en SAS som härletts från en lagrad åtkomstprincip, du kan ta bort åtkomst genom att återkalla åtkomstprincip lagras – du kan bara ändra den så att den har redan gått ut eller kan du ta bort det helt och hållet. Detta träder i kraft omedelbart och upphäver varje SAS som skapats med hjälp av den lagrade åtkomstprincip. Uppdatering eller ta bort åtkomstprincip lagras kan påverkan personer åtkomst till specifika behållaren, filresurs, tabell eller kön via SAS, men om klienterna skrivs så att de begär en ny SAS när den gamla servern blir ogiltig, den här metoden fungerar bra.

Eftersom med hjälp av en SAS som härletts från en lagrad åtkomstprincip ger dig möjlighet att återkalla den SAS omedelbart, är rekommenderad praxis att alltid använda lagras åtkomstprinciper när det är möjligt.

#### <a name="resources"></a>Resurser
Mer detaljerad information om hur du använder signaturer för delad åtkomst och lagras åtkomstprinciper, slutfördes med exempel, finns i följande artiklar:

* Dessa är referensartiklar.

  * [Service SAS](https://msdn.microsoft.com/library/dn140256.aspx)

    Den här artikeln innehåller exempel på användning av en tjänstnivå SAS med blobbar, Kömeddelanden, tabell intervall och filer.
  * [Hur du skapar en tjänst-SAS](https://msdn.microsoft.com/library/dn140255.aspx)
  * [Hur du skapar en konto-SAS](https://msdn.microsoft.com/library/mt584140.aspx)
* Dessa är kurserna om att använda .NET-klientbibliotek för att skapa signaturer för delad åtkomst och åtkomstregler lagras.

  * [Använda signaturer för delad åtkomst (SAS)](../storage-dotnet-shared-access-signature-part-1.md)
  * [Signaturer för delad åtkomst, del 2: Skapa och använda en SAS med Blob-tjänsten](../blobs/storage-dotnet-shared-access-signature-part-2.md)

    Den här artikeln innehåller en förklaring av SAS-modellen, exempel på signaturer för delad åtkomst och rekommendationer om bästa praxis och användning av SAS. Vi beskriver även återkallande av vilken behörighet.

* Autentisering

  * [Autentisering för Azure Storage-tjänster](https://msdn.microsoft.com/library/azure/dd179428.aspx)
* Signaturer för delad åtkomst komma igång Självstudier

  * [SAS komma igång-Självstudier](https://github.com/Azure-Samples/storage-dotnet-sas-getting-started)

## <a name="encryption-in-transit"></a>Kryptering under överföring
### <a name="transport-level-encryption--using-https"></a>Transportnivå kryptering – med hjälp av HTTPS
Ytterligare ett steg som du bör utföra för att säkerställa säkerheten för dina Azure Storage-data är att kryptera data mellan klienten och Azure Storage. Första rekommenderar vi att du alltid använda den [HTTPS](https://en.wikipedia.org/wiki/HTTPS) protokollet, vilket garanterar säker kommunikation över offentligt Internet.

Om du vill att en säker kommunikationskanal bör du alltid använda HTTPS när anropa REST-API: er eller få åtkomst till objekt i lagring. Dessutom **signaturer för delad åtkomst**, som kan användas för att delegera åtkomst till Azure Storage-objekt innehåller ett alternativ för att ange att endast HTTPS-protokollet kan användas när du använder signaturer för delad åtkomst, se till att vem som helst skicka länkar med SAS-token kommer att använda rätt protokoll.

Du kan framtvinga användningen av HTTPS när anropa REST-API: er att få åtkomst till objekt i storage-konton genom att aktivera [säker överföring krävs](../storage-require-secure-transfer.md) för lagringskontot. Anslutningar som använder HTTP ska avvisas när det här är aktiverat.

### <a name="using-encryption-during-transit-with-azure-file-shares"></a>Med hjälp av kryptering under överföring med Azure-filresurser
Azure Files stöder HTTPS när du använder REST-API, men är mer används ofta som en SMB filresurs kopplad till en virtuell dator. SMB 2.1 stöder inte kryptering, så anslutningar tillåts bara inom samma region i Azure. Dock SMB 3.0 stöder kryptering och den är tillgänglig i Windows Server 2012 R2, Windows 8, Windows 8.1 och Windows 10, så att både mellan region åtkomst och åtkomst till skrivbordet.

Medan Azure-filresurser kan användas med Unix, stöder Linux SMB-klienten ännu inte kryptering, så åtkomst tillåts endast i en Azure-region. Stöd för kryptering för Linux finns på Översikt av Linux-utvecklare som ansvarar för SMB-funktioner. När de lägger till kryptering har samma möjligheten för åtkomst till en Azure-filresurs på Linux som du gör för Windows.

Du kan tillämpa kryptering med tjänsten Azure-filer genom att aktivera [säker överföring krävs](../storage-require-secure-transfer.md) för lagringskontot. Om du använder REST-API: er, HTTPs krävs. För SMB, kan endast SMB-anslutningar som stöder kryptering ansluta.

#### <a name="resources"></a>Resurser
* [Azure Files introduktion](../files/storage-files-introduction.md)
* [Kom igång med Azure-filer i Windows](../files/storage-how-to-use-files-windows.md)

  Den här artikeln ger en översikt över Azure-filresurser och montera och använda dem i Windows.

* [Använda Azure Files med Linux](../files/storage-how-to-use-files-linux.md)

  Den här artikeln visar hur du monterar en Azure-filresurs på en Linux-system och överför/hämta filer.

### <a name="using-client-side-encryption-to-secure-data-that-you-send-to-storage"></a>Med kryptering på klientsidan för att skydda data som du skickar till lagring
Ett annat alternativ som hjälper dig att se till att dina data är säkra medan de överförs mellan ett klientprogram och lagring är kryptering på klientsidan. Data krypteras innan de överförs till Azure Storage. När du hämtar data från Azure Storage, dekrypteras data när den tas emot på klientsidan. Även om informationen är krypterad går över nätverket, rekommenderar vi att du också använda HTTPS, eftersom den har dataintegritetskontroller inbyggda som minimera nätverksfel som påverkar integriteten hos data.

Klientsidans kryptering är också en metod för att kryptera data i vila, eftersom data lagras i krypterad form. Lär dig om detta i detalj i avsnittet på [kryptering i vila](#encryption-at-rest).

## <a name="encryption-at-rest"></a>Kryptering i vila
Det finns tre Azure-funktioner som tillhandahåller kryptering i vila. Azure Disk Encryption används för att kryptera Operativsystemet och datadiskarna i IaaS-virtuella datorer. Både används kryptering och SSE på klientsidan att kryptera data i Azure Storage. 

Medan du kan använda kryptering på klientsidan för att kryptera data under överföring (som lagras också i krypterad form i lagring) måste överväga du att använda HTTPS under överföringen och har för data som ska krypteras automatiskt när den är lagrad. Det finns två sätt att göra detta--Azure Disk Encryption och SSE. En används för att kryptera data på Operativsystemet och datadiskarna som används av virtuella datorer direkt och den andra används för att kryptera data som skrivs till Azure Blob Storage.

### <a name="storage-service-encryption-sse"></a>Storage Service-kryptering (SSE)

SSE är aktiverat för alla lagringskonton och kan inte inaktiveras. SSE krypterar dina data automatiskt när du skriver till Azure Storage. När du läser data från Azure Storage, dekrypteras den innan de returneras av Azure Storage. SSE gör det möjligt för dig att skydda dina data utan att ändra koden och lägga till kod för program.

Nycklar som används för SSE hanteras av Microsoft. Microsoft genererar nycklarna ursprungligen och hanterar sina säker lagring samt rotationen reguljära som definieras av intern Microsoft-princip. Kundhanterad nycklar kommer så småningom finnas, tillsammans med en migreringsvägen från Microsoft-hanterad nycklar till kundhanterad nycklar.

SSE krypterar automatiskt data på alla prestandanivåer (Standard och Premium), alla distributionsmodeller (Azure Resource Manager och klassisk) och alla Azure Storage-tjänster (blob, kö, tabell och fil). 

### <a name="client-side-encryption"></a>Kryptering på klientsidan
Kryptering på klientsidan nämndes när det handlar om kryptering av data under överföringen. Den här funktionen kan du kryptera data i klientprogram innan den skickas över nätverket som ska skrivas till Azure Storage och programmässigt dekryptera data efter hämtning från Azure Storage via programmering.

Detta tillhandahåller kryptering under överföring, men det ger också funktionen kryptering i vila. Även om informationen är krypterad under överföringen, rekommenderar vi fortfarande använder HTTPS för att dra nytta av de inbyggda data integritetskontroller som hjälper till att minska nätverksfel som påverkar integriteten hos data.

Ett exempel på där du kan använda detta är om du har ett program som lagrar blobbar och hämtar BLOB och du vill att program och data att vara så säkert som möjligt. I så fall använder du kryptering på klientsidan. Trafiken mellan klienten och tjänsten Azure Blob innehåller krypterade resursen och ingen kan tolka data under överföringen och fylla i ditt privata blobbar.

Kryptering på klientsidan är inbyggd i Java och lagringsklientbiblioteken .NET, som i sin tur använder Azure nyckeln valvet API: erna, vilket gör det lätt att implementera. Processen för att kryptera och dekryptera data använder kuvert-teknik och lagrar metadata som används av kryptering i varje lagringsobjekt. Till exempel för BLOB, lagras den i blobmetadata, medan för köer, läggs till varje meddelande i kön.

Du kan skapa och hantera egna krypteringsnycklar för kryptering. Du kan också använda nycklar som genereras av Azure Storage-klientbiblioteket kan, eller så Azure Key Vault generera nycklar. Du kan lagra krypteringsnycklar i din lokala lagring eller du kan lagra dem i en Azure Key Vault. Azure Key Vault kan du bevilja åtkomst till hemligheter i Azure Key Vault till specifika användare med Azure Active Directory. Det innebär att inte bara alla kan läsa Azure Key Vault och hämta nycklar som du använder för kryptering på klientsidan.

#### <a name="resources"></a>Resurser
* [Kryptera och dekryptera blobbar i Microsoft Azure Storage med hjälp av Azure Key Vault](../blobs/storage-encrypt-decrypt-blobs-key-vault.md)

  Den här artikeln visar hur du använder kryptering på klientsidan med Azure Key Vault, inklusive hur du skapar KEK och lagra den i valvet med hjälp av PowerShell.
* [Kryptering på klientsidan och Azure Key Vault för Microsoft Azure Storage](../storage-client-side-encryption.md)

  Den här artikeln ger en förklaring av klientsidan kryptering och ger exempel på hur storage-klientbiblioteket för att kryptera och dekryptera resurser från fyra lagringstjänster. Det är också pratar om Azure Key Vault.

### <a name="using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines"></a>Använda Azure Disk Encryption för att kryptera diskar som används av virtuella datorer
Azure Disk Encryption är en ny funktion. Den här funktionen kan du kryptera OS-diskar och datadiskar som används av en virtuell IaaS-dator. För Windows krypteras enheterna med BitLocker-kryptering branschstandard. För Linux krypteras diskarna med DM-Crypt-teknik. Detta är integrerad med Azure Key Vault så att du kan styra och hantera krypteringsnycklar disk.

Lösningen stöder följande scenarion för virtuella IaaS-datorer när de är aktiverade i Microsoft Azure:

* Integrering med Azure Key Vault
* Standardnivån VMs: [A, D, DS, G, GS och så vidare serien IaaS-VM](https://azure.microsoft.com/pricing/details/virtual-machines/)
* Aktivera kryptering på Windows och Linux virtuella IaaS-datorer
* Om du inaktiverar kryptering på Operativsystemet och enheter för Windows IaaS-VM
* Om du inaktiverar kryptering på dataenheter för Linux virtuella IaaS-datorer
* Aktivera kryptering på virtuella IaaS-datorer som kör windowsklient-OS
* Aktivera kryptering på volymer med monteringssökvägar
* Aktivera kryptering på den virtuella Linux-datorer som är konfigurerade med disk-striping (RAID) med hjälp av mdadm
* Aktivera kryptering på den virtuella Linux-datorer med hjälp av LVM för datadiskar
* Aktivera kryptering på virtuella Windows-datorer som har konfigurerats med hjälp av lagringsutrymmen
* Alla offentliga Azure-regioner som stöds

Lösningen stöder inte följande scenarier, funktioner och tekniken i versionen:

* Grundnivån IaaS-VM
* Om du inaktiverar kryptering på en OS-enhet för Linux virtuella IaaS-datorer
* Virtuella IaaS-datorer som skapas med hjälp av klassiska metod för skapande av virtuell dator
* Integrering med din lokala nyckelhanteringstjänst
* Azure Files (delade filsystem), Network File System (NFS), dynamiska volymer och virtuella Windows-datorer som är konfigurerade med programvarubaserad RAID-system


> [!NOTE]
> Linux OS-diskkryptering stöds för närvarande på följande Linux-distributioner: RHEL 7.2, CentOS 7.2n och Ubuntu 16.04.
>
>

Den här funktionen garanterar att krypteras alla data på virtuella diskar i vila i Azure Storage.

#### <a name="resources"></a>Resurser
* [Azure Disk Encryption för Windows och Linux-IaaS-VM](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)

### <a name="comparison-of-azure-disk-encryption-sse-and-client-side-encryption"></a>Jämförelse mellan Azure Disk Encryption, SSE och kryptering på klientsidan

#### <a name="iaas-vms-and-their-vhd-files"></a>Virtuella IaaS-datorer och deras VHD-filer

För datadiskar som används av virtuella IaaS-datorer, rekommenderas Azure Disk Encryption. Om du skapar en virtuell dator med ohanterad diskar med hjälp av en avbildning från Azure Marketplace Azure utför en [ytlig kopiera](https://en.wikipedia.org/wiki/Object_copying) avbildningen och lagringen konto i Azure Storage, och det är inte krypterat även om du har aktiverat SSE. När den skapar den virtuella datorn och startar uppdatera bilden startar SSE kryptering av data. Därför är det bäst att använda Azure Disk Encryption på virtuella datorer med ohanterad diskar som skapats av avbildningar i Azure Marketplace om du vill att de fullständigt krypterade. Om du skapar en virtuell dator med hanterade diskar krypteras SSE alla data som standard med hjälp av plattform som hanterade nycklar. 

Om du tar en förkrypterade virtuell dator till Azure från lokala kommer du att kunna överföra krypteringsnycklarna till Azure Key Vault och fortsätta att använda kryptering för den virtuella datorn som du använde lokalt. Azure Disk Encryption är aktiverat för att hantera det här scenariot.

Om du har en icke-krypterade VHD från lokala kan du överföra den till gallery som en anpassad avbildning och etablera en virtuell dator från den. Om du gör detta med hjälp av Resource Manager-mallar, kan du be att aktivera Azure Disk Encryption när den startar den virtuella datorn.

När du lägger till en datadisk och montera den på den virtuella datorn, kan du aktivera Azure Disk Encryption på disken data. Den kommer att kryptera data disken lokalt först och sedan klassisk distribution-lager gör lazy skrivåtgärder mot storage så lagring innehållet krypteras.

#### <a name="client-side-encryption"></a>Kryptering av klientsidan
Kryptering på klientsidan är den säkraste metoden för att kryptera dina data eftersom den krypterar data före överföringen.  Det kräver dock att du lägger till kod för dina program med hjälp av lagring som du inte vill göra. I sådana fall kan använder du HTTPS för att skydda dina data under överföringen. När data når Azure Storage, krypteras den med SSE.

Du kan kryptera tabellentiteter, Kömeddelanden och blobbar med kryptering på klientsidan. 

Kryptering på klientsidan hanteras helt av programmet. Detta är den säkraste metoden, men kräver att du ändrar ditt program programmässiga och gjorda nyckelhantering processer. Du kan använda när du vill öka säkerheten under överföringen och du vill att lagrade data krypteras.

Kryptering på klientsidan är mer belastningen på klienten och du behöver konto för detta i planer för skalbarhet, särskilt om du krypterar och överför stora mängder data.

#### <a name="storage-service-encryption-sse"></a>Storage Service-kryptering (SSE)

SSE hanteras av Azure Storage. SSE ger inte för att skydda data under överföring, men den krypterar data som skrivs till Azure Storage. SSE påverkar inte Azure Storage-prestanda.

Du kan kryptera alla typer av data för storage-konto med hjälp av SSE (blockblobbar, tilläggsblobbar, sidblobar, tabelldata, kö-data och filer).

Om du har ett arkiv eller bibliotek av VHD-filer som du använder som grund för att skapa nya virtuella datorer kan du skapa ett nytt lagringskonto och sedan överföra VHD-filerna till det kontot. Dessa VHD-filer krypteras av Azure Storage.

Om du har aktiverat för diskarna i en virtuell dator för Azure Disk Encryption krypteras alla data som nyskrivna efter SSE och efter Azure Disk Encryption.

## <a name="storage-analytics"></a>Lagringsanalys
### <a name="using-storage-analytics-to-monitor-authorization-type"></a>Använda Storage Analytics för att övervaka tillstånd typ
Du kan aktivera Azure Storage Analytics att utföra loggning och lagra data för mått för varje storage-konto. Det här är ett bra verktyg för att använda när du vill kontrollera prestandamått för ett lagringskonto eller behöver felsöka ett storage-konto eftersom du har prestandaproblem med.

En annan typ av data som visas i loggarna storage analytics är autentiseringsmetoden som används av någon när de har åtkomst till lagring. Du kan till exempel se om de används en signatur för delad åtkomst eller lagringskontonycklarna, eller om blob nås var offentliga i med Blob Storage.

Det kan vara användbart om du nära skyddar åtkomst till lagring. I Blob Storage du exempelvis anger alla behållare som privat och implementerar en SAS-tjänst i dina program. Du kan kontrollera loggarna regelbundet för att se om dina blobbar används med lagringskontonycklar, vilket kan tyda på ett sekretessbrott, eller om blobbarna är offentliga men de får inte vara.

#### <a name="what-do-the-logs-look-like"></a>Hur ser loggar ut?
När du aktivera mätvärden för storage-konto och loggning via Azure portal analysdata kommer att börja samla snabbt. Loggning och mått för varje tjänst är separat; loggning skrivs endast när det är aktiviteten att storage-konto medan loggas mätvärden varje minut, varje timme eller varje dag, beroende på hur du konfigurerar den.

Loggfilerna lagras i blockblobbar i en behållare med namnet $logs i lagringskontot. Den här behållaren skapas automatiskt när Storage Analytics är aktiverad. När den här behållaren har skapats kan du ta bort det, även om du tar bort innehållet.

Det finns en mapp för varje tjänst under $logs-behållaren och det finns undermappar för år/månad/dag/timme. Timma numreras loggarna. Detta är hur katalogstrukturen ser ut:

![Visa loggfiler](./media/storage-security-guide/image1.png)

Alla begäranden till Azure Storage är inloggad. Här är en ögonblicksbild av en loggfil som visar de första några fälten.

![Ögonblicksbild av en loggfil](./media/storage-security-guide/image2.png)

Du kan se att du kan använda loggarna för att spåra alla slags anrop till ett lagringskonto.

#### <a name="what-are-all-of-those-fields-for"></a>Vad är alla dessa fält för?
Det finns en artikel som anges i resurserna nedan som innehåller en lista över många fält i loggarna och vad de används. Här är listan över fält i ordning:

![Ögonblicksbild av fälten i en loggfil](./media/storage-security-guide/image3.png)

Vi är intresserad av posterna för GetBlob och hur de autentiseras, så vi behöver leta efter poster med åtgärdstypen ”Get-Blob” och kontrollera begäran-status (fjärde</sup> kolumn) och auktorisering-typ (åttonde</sup> kolumn).

Till exempel i de första raderna i listan ovan, begäran-status är ”klar” och auktorisering-type ”autentiseras”. Det innebär att begäran har verifierats med hjälp av lagringskontots åtkomstnyckel.

#### <a name="how-are-my-blobs-being-authenticated"></a>Hur mitt blob som autentiseras?
Vi har tre fall som vi är intresserad av.

1. Blobben är offentlig och den kan nås med hjälp av en URL utan en signatur för delad åtkomst. I det här fallet begäran-status är ”AnonymousSuccess” och typen tillståndet är ”anonym”.

   1.0;2015-11-17T02:01:29.0488963Z;GetBlob;**AnonymousSuccess**;200;124;37;**anonymous**;;mystorage…
2. Blobben är privata och användes med en signatur för delad åtkomst. I det här fallet begäran-status är ”SASSuccess” och auktorisering-typen är ”sas”.

   1.0;2015-11-16T18:30:05.6556115Z;GetBlob;**SASSuccess**;200;416;64;**sas**;;mystorage…
3. Blob är privat nyckel för säkerhetslagring användes för att komma åt den. I det här fallet begäran-status är ”**lyckade**” och auktorisering-typen är ”**autentiserade**”.

   1.0; 2015-11-16T18:32:24.3174537Z; GetBlob; **Lyckade**; 206; 59; 22; **autentiserad**; mystorage...

Du kan använda Microsoft Message Analyzer för att visa och analysera dessa loggar. Den innehåller Sök och filtrera funktioner. Du kan till exempel vill söka efter förekomster av GetBlob att se om användningen är vad du förväntar dig, det vill säga för att se till att någon inte kommer åt ditt lagringskonto felaktigt.

#### <a name="resources"></a>Resurser
* [Lagringsanalys](../storage-analytics.md)

  Den här artikeln är en översikt över storage analytics och aktivera dem.
* [Storage Analytics loggformat](https://msdn.microsoft.com/library/azure/hh343259.aspx)

  Den här artikeln visar loggformatet Storage Analytics och detaljer tillgängliga fält, inklusive autentiseringstyp, som visar vilken typ av autentisering som används för begäran.
* [Övervaka ett Lagringskonto i Azure-portalen](../storage-monitor-storage-account.md)

  Den här artikeln visar hur du konfigurerar övervakning av mätvärden och loggning för ett lagringskonto.
* [Slutpunkt till slutpunkt-felsökning med hjälp av Azure Storage-mätvärden och loggning, AzCopy och Message Analyzer](../storage-e2e-troubleshooting.md)

  Den här artikeln handlar om felsökning med hjälp av Storage Analytics och visar hur du använder Microsoft Message Analyzer.
* [Microsoft Message Analyzer fungerar Guide](https://technet.microsoft.com/library/jj649776.aspx)

  Den här artikeln är referens för Microsoft Message Analyzer och innehåller länkar till en självstudiekurs och quickstart sammanfattning av funktioner.

## <a name="cross-origin-resource-sharing-cors"></a>Resursdelning för korsande ursprung (CORS)
### <a name="cross-domain-access-of-resources"></a>Korsdomänåtkomst för resurser
När en webbläsare som körs i en domän gör en HTTP-begäran för en resurs från en annan domän, kallas en cross-origin HTTP-begäran. Exempelvis begär en HTML-sida från contoso.com en JPEG-bild som finns på fabrikam.blob.core.windows.net. Av säkerhetsskäl begränsa cross-origin HTTP-begäranden som startas från skript, till exempel JavaScript i webbläsare. Det innebär att när JavaScript-kod på en webbsida på contoso.com begär att jpeg på fabrikam.blob.core.windows.net, webbläsaren inte tillåter att begäran.

Vad har detta för att göra med Azure Storage? Också, om du lagrar statiska resurser, t.ex JSON- eller XML-filer i Blob Storage med hjälp av ett lagringskonto kallas Fabrikam domänen för tillgångarna blir fabrikam.blob.core.windows.net och webbprogrammet contoso.com kommer inte kunna komma åt dem med hjälp av JavaScript eftersom domänerna som inte är samma. Detta gäller även om du försöker anropa en av Azure Storage-tjänster – till exempel Table Storage – som returnerar JSON-data som ska bearbetas av JavaScript-klienten.

#### <a name="possible-solutions"></a>Möjliga lösningar
Ett sätt att lösa problemet är att tilldela en anpassad domän som ”storage.contoso.com” till fabrikam.blob.core.windows.net. Problemet är att du kan endast tilldela den anpassa domänen ett lagringskonto. Vad händer om tillgångar lagras i flera storage-konton?

Ett annat sätt att lösa problemet är att webbprogrammet fungerar som proxy för storage-anrop. Det innebär att om du överför en fil till Blob Storage, webbprogrammet skulle antingen skriva det lokalt och sedan kopiera den till Blob Storage och den kommer att läsa in alla i minnet och skriver den till Blob Storage. Alternativt kan skriva du ett dedikerat webbprogram (till exempel en webb-API) som överför filer lokalt och skriver dem till Blob Storage. Oavsett hur du måste kontot för den funktionen när skalbarhet behöver.

#### <a name="how-can-cors-help"></a>CORS hur?
Azure Storage kan du aktivera CORS – Cross Origin Resource Sharing. Du kan ange domäner som har åtkomst till resurser i detta lagringskonto för varje storage-konto. I vårt fall som beskrivs ovan, vi till exempel aktivera CORS för fabrikam.blob.core.windows.net storage-konto och konfigurera den för att tillåta åtkomst till contoso.com. Web application contoso.com kan sedan direkt åtkomst till resurser i fabrikam.blob.core.windows.net.

Observera är att CORS tillåter åtkomst, men ger inte autentisering, vilket krävs för alla icke-offentlig åtkomst av lagringsresurser. Detta innebär att du har bara åtkomst till blobbar om de är offentliga eller om du inkluderar en signatur för delad åtkomst som ger behörighet. Tabeller, köer och filer har ingen offentlig åtkomst och kräver en SAS.

Som standard är CORS inaktiverad på alla tjänster. Du kan aktivera CORS med hjälp av REST API eller klientbibliotek för storage för att anropa en av metoderna för att ange principer för tjänsten. När du gör det kan inkludera du en regel för CORS som XML. Här är ett exempel på en CORS-regel som har angetts med hjälp av åtgärden Ange egenskaper för Blob-tjänsten för ett lagringskonto. Du kan utföra åtgärden med storage-klientbibliotek eller REST-API: er för Azure Storage.

```xml
<Cors>    
    <CorsRule>
        <AllowedOrigins>http://www.contoso.com, http://www.fabrikam.com</AllowedOrigins>
        <AllowedMethods>PUT,GET</AllowedMethods>
        <AllowedHeaders>x-ms-meta-data*,x-ms-meta-target*,x-ms-meta-abc</AllowedHeaders>
        <ExposedHeaders>x-ms-meta-*</ExposedHeaders>
        <MaxAgeInSeconds>200</MaxAgeInSeconds>
    </CorsRule>
<Cors>
```

Här är vad det innebär att varje rad:

* **AllowedOrigins** ser som icke-matchande domäner kan begära och ta emot data från storage-tjänst. Detta innebär att både contoso.com och fabrikam.com kan begära data från Blob Storage för ett visst lagringskonto. Du kan också ställa in det till ett jokertecken (\*) så att alla domäner att komma åt begäranden.
* **AllowedMethods** detta är listan över metoder (http-begäran verb) som kan användas när du gör en begäran. I det här exemplet tillåts bara skicka och hämta. Du kan ange detta till ett jokertecken (\*) så att alla metoder som ska användas.
* **AllowedHeaders** är huvuden för begäran som den ursprungliga domänen kan ange när du gör en begäran. I det här exemplet tillåts alla metadata huvuden som börjar med x-ms-metadata, x-ms-meta-mål- och x-ms-meta-abc. Jokertecknet (\*) anger att alla huvud som börjar med det angivna prefixet tillåts.
* **ExposedHeaders** detta talar om vilka svarshuvuden bör exponeras i webbläsaren att utfärdaren begäran. I det här exemplet, de huvuden som börjar med ”x-ms - meta-” visas.
* **MaxAgeInSeconds** detta är den maximala tid som en webbläsare cachelagras Preflight-alternativ för begäran. (Mer information om preflight-begäran att kontrollera den första artikeln nedan.)

#### <a name="resources"></a>Resurser
Mer information om CORS och hur du aktiverar det se dessa resurser.

* [Cross-Origin Resource Sharing (CORS) stöd för Azure Storage-tjänster på Azure.com](../storage-cors-support.md)

  Den här artikeln innehåller en översikt över CORS och hur du skapar regler för olika storage-tjänster.
* [Cross-Origin Resource Sharing (CORS) stöd för Azure Storage-tjänster på MSDN](https://msdn.microsoft.com/library/azure/dn535601.aspx)

  Detta är i referensdokumentationen för CORS-stöd för Azure Storage-tjänster. Det innehåller länkar till artiklar som gäller för varje storage-tjänst och visar ett exempel och förklarar varje element i filen CORS.
* [Microsoft Azure Storage: Introduktion till CORS](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/02/03/windows-azure-storage-introducing-cors.aspx)

  Detta är en länk till inledande bloggartikel presenterar CORS och visar hur du använder den.

## <a name="frequently-asked-questions-about-azure-storage-security"></a>Vanliga frågor och svar om Azure Storage-säkerhet
1. **Hur kan jag bekräfta integriteten för de blobbar som jag överföra till eller från Azure Storage om jag inte använda HTTPS-protokollet?**

   Om du av någon anledning som du måste använda HTTP i stället för HTTPS och du arbetar med blockblobbar, kan du använda MD5 kontroll för att kontrollera integriteten för de blobbar som överförs. Detta hjälper med skydd från nätverket/transport layer fel, men inte nödvändigtvis med mellanliggande attacker.

   Om du kan använda HTTPS, vilket ger säkerhet på radnivå transport är med hjälp av MD5 kontrollerar redundant och onödiga.

   Mer information finns i [översikt över Azure Blob-MD5](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/02/18/windows-azure-blob-md5-overview.aspx).
2. **Nyheter om FIPS-kompatibilitet i USA Government?**

   I USA FIPS Federal Information Processing Standard () definierar kryptografiska algoritmer som godkänts för användning av USA Federala myndigheter datorsystem för att skydda känsliga data. Aktivering av FIPS visar-läge på en Windows server eller fjärrskrivbord OS endast FIPS-validerade kryptografiska algoritmer som ska användas. Om ett program som använder icke-kompatibla algoritmer, bryts programmen. With.NET Framework version 4.5.2 eller senare och programmet automatiskt växlar krypteringsalgoritmer om du vill använda FIPS-kompatibla algoritmer när datorn är i FIPS-läge.

   Microsoft lämnar det upp till varje kund för att bestämma om du vill aktivera FIPS-läge. Vi tror att det finns ingen tvingande anledning för kunder som inte omfattas av government sina förordningar för att aktivera FIPS-läge som standard.

   **Resurser**

* [Varför inte rekommenderar vi ”FIPS-läge” längre](https://blogs.technet.microsoft.com/secguide/2014/04/07/why-were-not-recommending-fips-mode-anymore/)

  Den här bloggen artikeln ger en översikt över FIPS och förklarar varför de inte aktiverar FIPS-läge som standard.
* [FIPS 140 verifiering](https://technet.microsoft.com/library/cc750357.aspx)

  Den här artikeln innehåller information om hur Microsoft-produkter och kryptografimoduler överensstämmer med den FIPS-standarden för USA Federala myndigheter.
* [”Systemkryptografi: Använd FIPS-kompatibla algoritmer för kryptering, hashing och signering” inställningar effekterna på säkerheten i Windows XP och senare versioner av Windows](https://support.microsoft.com/kb/811833)

  Den här artikeln handlar om användning av FIPS-läge i äldre Windows-datorer.
