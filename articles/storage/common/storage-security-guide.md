---
title: Säkerhetsguiden för Azure Storage | Microsoft Docs
description: Detaljerad information om de många metoderna för att skydda Azure Storage, inklusive men inte begränsat till RBAC, kryptering av lagringstjänst, Client side Encryption, SMB 3.0 och Azure Disk Encryption.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 48ebbabca8d38db3a7c1344981f79991de29df80
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65154399"
---
# <a name="azure-storage-security-guide"></a>Säkerhetsguiden för Azure Storage

Azure Storage tillhandahåller en omfattande uppsättning funktioner för säkerhet som tillsammans att utvecklare kan skapa säkra program:

- Alla data som skrivs till Azure Storage krypteras automatiskt med [Storage Service Encryption (SSE)](storage-service-encryption.md). Mer information finns i [presenterar standard kryptering för Azure-Blobar, filer, tabeller och Queue Storage](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).
- Azure Active Directory (Azure AD) och rollbaserad åtkomstkontroll (RBAC) stöds för Azure Storage för både resource management-åtgärder och åtgärder, enligt följande:   
    - Du kan tilldela RBAC-roller som är begränsade till storage-konto till säkerhetsobjekt och Använd Azure AD för att auktorisera resource management-åtgärder, till exempel nyckelhantering.
    - Azure AD-integrering stöds för åtgärder för blob och kö. Du kan tilldela RBAC-roller som är begränsade till en prenumeration, resursgrupp, storage-konto, eller en enskild behållare eller kön på ett säkerhetsobjekt eller en hanterad identitet för Azure-resurser. Mer information finns i [autentisera åtkomsten till Azure Storage med Azure Active Directory](storage-auth-aad.md).   
- Data kan skyddas i rörelse mellan ett program och Azure med hjälp av [Client Side Encryption](../storage-client-side-encryption.md), HTTPS och SMB 3.0.  
- OS- och diskar som används av virtuella Azure-datorer kan krypteras med [Azure Disk Encryption](../../security/azure-security-disk-encryption.md). 
- Delegerad åtkomst till dataobjekt i Azure Storage kan tilldelas via [signaturer för delad åtkomst](../storage-dotnet-shared-access-signature-part-1.md).

Den här artikeln innehåller en översikt över var och en av dessa säkerhetsfunktioner som kan användas med Azure Storage. Länkar tillhandahålls till artiklar som ger information om varje funktion så kan du lätt göra ytterligare undersökningar på varje avsnitt.

Här följer ämnena i den här artikeln:

* [Hantering av plan Security](#management-plane-security) – skydda ditt Storage-konto

  Hanteringsplanet består av de resurser som används för att hantera ditt lagringskonto. Det här avsnittet beskriver distributionsmodellen Azure Resource Manager och hur du använder rollbaserad åtkomstkontroll (RBAC) för att styra åtkomsten till dina lagringskonton. Den behandlar också hantera dina lagringskontonycklar och hur du återskapar dem.
* [Data-dataplaner Security](#data-plane-security) – skydda åtkomst till dina Data

  I det här avsnittet tittar vi på att tillåta åtkomst till de faktiska data-objekt i ditt Storage-konto, t.ex blobar, filer, köer och tabeller, med hjälp av signaturer för delad åtkomst och lagrade åtkomstprinciper. Vi täcker både SAS på servicenivå och kontonivå SAS. Vi ska också se hur du begränsar åtkomsten till en specifik IP-adress (eller IP-adressintervall), hur du begränsar det protokoll som används till HTTPS och hur du återkalla en signatur för delad åtkomst utan att vänta tills det upphör att gälla.
* [Kryptering vid överföring](#encryption-in-transit)

  Det här avsnittet beskrivs hur du skyddar data när du överför den till eller från Azure Storage. Vi kommer att prata om rekommenderade användning av HTTPS och kryptering som används av SMB 3.0 för Azure-filresurser. Vi ska också ta en titt på klientsidan kryptering, som gör det möjligt att kryptera data innan den överförs till lagring i ett klientprogram och för att dekryptera data efter överföringen slut på minne.
* [Kryptering i vila](#encryption-at-rest)

  Vi ska prata om Storage Service Encryption (SSE), som nu aktiveras automatiskt för nya och befintliga lagringskonton. Vi ska också titta på hur du kan använda Azure Disk Encryption och utforska grundläggande skillnader och fall av diskkryptering jämfört med SSE jämfört med Client Side Encryption. Vi ska helt kort titta på FIPS-kompatibilitet för USA Government-datorer.
* Med hjälp av [Lagringsanalys](#storage-analytics) granska åtkomst till Azure Storage

  Det här avsnittet beskrivs hur du hittar information i storage analytics-loggar för en begäran. Vi ta en titt på verkliga lagringsanalys loggdata och se hur att avläsa om en begäran skickas med lagringskontonyckeln med en signatur för delad åtkomst eller anonymt och om den lyckades eller misslyckades.
* [Aktivera webbläsarbaserade klienter med CORS](#cross-origin-resource-sharing-cors)

  Det här avsnittet handlar om hur du tillåter cross-origin resource sharing (CORS). Vi kommer att tala om korsdomänåtkomst och hur du hanterar med CORS-funktioner som är inbyggda i Azure Storage.

## <a name="management-plane-security"></a>Management plan säkerhet
Hanteringsplanet består av åtgärder som påverkar själva lagringskontot. Du kan till exempel skapa eller ta bort ett lagringskonto, hämta en lista över storage-konton i en prenumeration, hämta lagringskontonycklarna eller återskapa lagringskontonycklarna.

När du skapar ett nytt lagringskonto kan välja du en distributionsmodell för klassisk eller Resource Manager. Den klassiska modellen för att skapa resurser i Azure tillåter endast fullständiga åtkomst till prenumerationen och i sin tur, storage-konto.

Den här guiden fokuserar på Resource Manager-modellen är det rekommendera sättet för att skapa storage-konton. Med den Resource Manager-konton i stället ger tillgång till hela prenumerationen, kan du styra åtkomsten på en mer begränsad nivå till Hanteringsplanet med hjälp av rollbaserad åtkomstkontroll (RBAC).

### <a name="how-to-secure-your-storage-account-with-role-based-access-control-rbac"></a>Hur du skyddar ditt lagringskonto med rollbaserad åtkomstkontroll (RBAC)
Nu ska vi prata om RBAC är och hur du kan använda den. Varje Azure-prenumerationen har en Azure Active Directory. Användare, grupper och program från katalogen kan beviljas åtkomst för att hantera resurser i Azure-prenumeration som använder Resource Manager-distributionsmodellen. Den här typen av security kallas rollbaserad åtkomstkontroll (RBAC). Du kan använda för att hantera den här åtkomsten, den [Azure-portalen](https://portal.azure.com/), [Azure CLI-verktygen](../../cli-install-nodejs.md), [PowerShell](/powershell/azureps-cmdlets-docs), eller [Azure Storage Resource Provider REST API: er](https://msdn.microsoft.com/library/azure/mt163683.aspx).

Med Resource Manager-modellen kan placera du storage-konto i en resurs och kontrollerar åtkomst till Hanteringsplanet för den specifika storage-konto med Azure Active Directory. Du kan till exempel ge särskilda användare möjlighet att få åtkomst till lagringskontonycklarna, medan andra användare kan visa information om storage-konto, men det går inte att få åtkomst till lagringskontonycklarna.

#### <a name="granting-access"></a>Bevilja åtkomst
Komma åt genom att tilldela lämpliga RBAC-roll till användare, grupper och program i det högra omfånget. Om du vill bevilja åtkomst till hela prenumerationen, kan du tilldela en roll på prenumerationsnivån. Du kan bevilja åtkomst till alla resurser i en resursgrupp genom att bevilja behörighet till själva resursgruppen. Du kan också tilldela specifika roller till specifika resurser, till exempel lagringskonton.

Här är de viktigaste aspekterna som du behöver veta om hur du använder RBAC för att få åtkomst till hanteringsåtgärder på ett Azure Storage-konto:

* När du tilldelar åtkomst, tilldela en roll i princip på det konto som du vill ha åtkomst. Du kan styra åtkomsten till de åtgärder som används för att hantera det lagringskontot, men inte till dataobjekt i kontot. Du kan till exempel ge behörighet att hämta egenskaperna för storage-konto (t.ex redundans), men inte till en behållare eller data i en behållare i Blob Storage.
* Du kan ge dem behörighet att läsa lagringskontonycklarna för någon behörighet att få åtkomst till dataobjekt i storage-konto och som användaren kan sedan använda dessa nycklar för att få åtkomst till blobbar, köer, tabeller och filer.
* Roller kan tilldelas till ett visst användarkonto, en grupp av användare eller till ett visst program.
* Varje roll har en lista över åtgärder och åtgärder som inte. Exempelvis kan rollen virtuell Datordeltagare har en åtgärd av ”Listnycklar” som gör det möjligt för storage-kontonycklar som ska läsas. Deltagaren har ”inte åtgärder”, till exempel uppdatering åtkomsten för användare i Active Directory.
* Roller för lagring omfattar (men är inte begränsade till) följande roller:

  * Ägare – de kan hantera allt, inklusive åtkomst.
  * Deltagare – de kan göra allt ägaren kan förutom tilldela åtkomst. Användare med den här rollen kan visa och återskapa lagringskontonycklarna. De kan använda storage-kontonycklar för att komma åt dataobjekt.
  * Läsare – de kan visa information om storage-konto, utom hemligheter. Till exempel om du tilldelar en roll med behörigheter för dataläsare för lagringskontot till någon, de kan visa egenskaperna för storage-konto, men de kan inte göra ändringar i egenskaperna eller visa lagringskontonycklarna.
  * Lagringskontodeltagare – de kan hantera storage-konto – de kan läsa prenumerationens resursgrupper och resurser, och skapa och hantera resursgrupper i prenumerationen. De kan också åtkomst till lagringskontonycklarna, vilket i sin tur innebär att de har åtkomst till dataplanet.
  * Administratör för användaråtkomst – de kan hantera användarnas åtkomst till lagringskontot. De kan till exempel ge läsåtkomst till en specifik användare.
  * Virtuell Datordeltagare – de kan hantera virtuella datorer men inte lagringskontot som de är anslutna. Den här rollen kan visa lagringskontonycklarna, vilket innebär att användaren som du tilldelar den här rollen kan uppdatera dataplanet.

    De måste kunna skapa motsvarande VHD-filen i ett lagringskonto för en användare att skapa en virtuell dator. Om du vill göra det behöver de för att kunna hämta lagringskontonyckeln och skickar den till API: et som skapade den virtuella datorn. Därför måste de ha denna behörighet så att de kan visa lagringskontonycklarna.
* Möjligheten att definiera anpassade roller är en funktion som gör det möjligt att skapa en uppsättning åtgärder från en lista över tillgängliga åtgärder som kan utföras på Azure-resurser.
* Användaren måste konfigureras i Azure Active Directory innan du kan tilldela en roll till dem.
* Du kan skapa en rapport över som beviljas/återkallas vilken typ av åtkomst till och från vilken och på vilka scope med PowerShell eller Azure CLI.

#### <a name="resources"></a>Resurser
* [Azure Active Directory rollbaserad åtkomstkontroll](../../role-based-access-control/role-assignments-portal.md)

  Den här artikeln förklarar Azure Active Directorys rollbaserade åtkomstkontroll och hur den fungerar.
* [RBAC: Inbyggda roller](../../role-based-access-control/built-in-roles.md)

  Den här artikeln beskriver alla inbyggda roller som är tillgängliga i RBAC.
* [Förstå Resource Manager-distribution och klassisk distribution](../../azure-resource-manager/resource-manager-deployment-model.md)

  Den här artikeln beskriver Resource Manager-distribution och klassiska distributionsmodeller och förklarar fördelarna med att använda Resource Manager och resursgrupper. Den förklarar hur Azure Compute, Network och Storage-Providers fungerar under Resource Manager-modellen.
* [Hantera rollbaserad åtkomstkontroll med REST API](../../role-based-access-control/role-assignments-rest.md)

  Den här artikeln visar hur du använder REST API för att hantera RBAC.
* [Referens för REST-API för Azure Storage Resource Provider](https://msdn.microsoft.com/library/azure/mt163683.aspx)

  Den här API-referens beskriver API: er som du kan använda för att hantera ditt lagringskonto via programmering.
* [Använda Resource Manager-autentisering-API för att få åtkomst till prenumerationer](../../azure-resource-manager/resource-manager-api-authentication.md)

  Den här artikeln visar hur du autentiserar med hjälp av Resource Manager-API: er.
* [Rollbaserad åtkomstkontroll i Microsoft Azure från Ignite](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

  Det här är en länk till en video på Channel 9 från 2015 MS Ignite-konferensen. I den här sessionen talar de om åtkomsthantering och rapporteringsfunktioner i Azure och utforskar bästa praxis när det gäller att säkra åtkomst till Azure-prenumerationer med Azure Active Directory.

### <a name="managing-your-storage-account-keys"></a>Hantera dina Lagringskontonycklar
Lagringskontonycklar är 512 bitar strängar som skapats av Azure som tillsammans med lagringskontonamn, kan användas för att få åtkomst till dataobjekt som lagras i lagringskontot, till exempel, blobbar, entiteter i en tabell, Kömeddelanden och filer på en Azure-filresurs. Kontrollera åtkomst till de storage-konto nycklar kontrollerar åtkomsten till dataplanet för det lagringskontot.

Varje lagringskonto har två nycklar som kallas ”nyckel 1” och ”nyckel 2” i den [Azure-portalen](https://portal.azure.com/) och i PowerShell-cmdlets. Dessa kan återskapas manuellt med hjälp av en eller flera metoder, inklusive men inte begränsat till med hjälp av den [Azure-portalen](https://portal.azure.com/), PowerShell, Azure CLI, eller programmässigt med .NET Storage Client Library eller Azure Storage-tjänster REST-API.

Det finns många anledningar till att återskapa nycklar för ditt lagringskonto.

* Du kan återskapa dem regelbundet av säkerhetsskäl.
* Om du vill återskapa dina lagringskontonycklar om någon hanteras hacka ett program och hämta den nyckel som har hårdkodad eller sparats i en konfigurationsfil, ge fullständig åtkomst till ditt lagringskonto.
* Ett annat fall för nycklar är om ditt team använder ett program i Storage Explorer som behåller lagringskontonyckeln och någon av gruppmedlemmarna lämnar. Programmet fortsätter att arbeta, ge dem åtkomst till ditt storage-konto när de är borta. Det här är faktiskt det främsta skälet som de skapat kontonivå signaturer för delad åtkomst – du kan använda en SAS på kontonivå istället för att lagra åtkomstnycklarna i en konfigurationsfil.

#### <a name="key-regeneration-plan"></a>Åtkomstnyckeln återskapades plan
Du vill inte bara återskapa nyckeln som du använder utan några planera. Om du gör det kan du beskärs all åtkomst till det lagringskontot, vilket kan orsaka avbrott i stora. Det här är anledningen till att det finns två nycklar. Du bör Generera en nyckel i taget.

Innan du återskapar dina nycklar måste du ha en lista över alla dina program som är beroende av lagringskontot, samt andra tjänster som du använder i Azure. Till exempel om du använder Azure Media Services som är beroende av ditt storage-konto, du måste synkroniserar du om åtkomstnycklarna med medietjänsten när du har återskapat nyckeln. Om du använder alla program, till exempel en Lagringsutforskaren behöver att tillhandahålla nya nycklar till dessa program också. Om du har virtuella datorer vars VHD-filer som lagras i lagringskontot kan påverkas de inte av återskapa lagringskontonycklarna.

Du kan återskapa dina nycklar i Azure-portalen. När nycklar genereras, kan de ta upp till 10 minuter kan synkroniseras mellan lagringstjänster.

När du är klar, är här den allmänna processen med information om hur du bör ändra din nyckel. I det här fallet antas att du använder nyckel 1 och du kommer att ändra allt för att använda nyckeln 2 i stället.

1. Återskapa nyckel 2 för att säkerställa att de är skyddade. Du kan göra detta i Azure-portalen.
2. Ändra lagringsnyckeln om du vill använda nyckel 2 nya värdet i alla program där lagringsnyckeln lagras. Testa och publicera programmet.
3. När alla program och tjänster som är igång och körs, återskapa nyckel 1. Detta säkerställer att vem som helst som du inte har uttryckligen gett den nya nyckeln inte längre har åtkomst till lagringskontot.

Om du använder nyckel 2 kan du använda samma process, men omvänd nyckelnamnen.

Du kan migrera över ett par dagar, ändra varje program för att använda den nya nyckeln och publicera den. När alla är klar bör du gå tillbaka och återskapa den gamla nyckeln så att den inte längre fungerar.

Ett annat alternativ är att placera lagringskontonyckeln i en [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) som en hemlighet och ha dina program hämta nyckeln därifrån. När du återskapa nyckel och uppdatera Azure Key Vault behöver sedan program som inte distribueras eftersom de ska hämta den nya nyckeln från Azure Key Vault automatiskt. Observera att du kan få programmet att läsa nyckeln varje gång som du behöver den, eller så kan cachelagra i minnet och om det misslyckas när du använder den, hämta nyckeln igen från Azure Key Vault.

Med Azure Key Vault lägger också till en annan nivå av säkerhet för dina lagringsnycklar. Om du använder den här metoden kan har du aldrig lagring viktiga hårdkodad i en konfigurationsfil, vilket tar bort den minimering av någon får åtkomst till nycklarna utan uttryckligt tillstånd.

En annan fördel med att använda Azure Key Vault är att du kan också kontrollera åtkomsten till dina nycklar med hjälp av Azure Active Directory. Det innebär att du kan bevilja åtkomst till program som behöver hämta nycklarna från Azure Key Vault och säker på att andra program inte kan komma åt nycklarna utan att ge dem behörighet specifikt handfull.

> [!NOTE]
> Microsoft rekommenderar att du använder bara en av nycklarna i alla dina program på samma gång. Om du använder nyckel 1 på vissa platser och nyckel 2 i andra kan kunna du inte rotera nycklarna utan att ett program att förlora åtkomst.

#### <a name="resources"></a>Resurser

* [Hantera inställningar för lagringskonto i Azure portal](storage-account-manage.md)
* [Referens för REST-API för Azure Storage Resource Provider](https://msdn.microsoft.com/library/mt163683.aspx)

## <a name="data-plane-security"></a>Plan för datasäkerhet
Plan för datasäkerhet refererar till de metoder som används för att skydda dataobjekt som lagras i Azure-lagring – blobar, köer, tabeller och filer. Vi har sett metoder för att kryptera data och security under överföring av data, men hur skaffar du styr åtkomst till objekten?

Det finns tre alternativ för att auktorisera åtkomst till dataobjekt i Azure Storage, inklusive:

- Använda Azure AD för att bevilja åtkomst till behållare och köer. Azure AD tillhandahåller fördelar jämfört med andra metoder för auktorisering, inklusive ta bort behovet av att lagra hemligheter i din kod. Mer information finns i [autentisera åtkomsten till Azure Storage med Azure Active Directory](storage-auth-aad.md). 
- Med storage-kontonycklar för att godkänna åtkomst via delad nyckel. Auktorisera via delad nyckel kräver att lagra dina lagringskontonycklar i ditt program, så Microsoft rekommenderar att du använder Azure AD i stället där det är möjligt.
- Använda signaturer för delad åtkomst för att ge kontrollerad behörighet till specifika dataobjekt för en viss tidsperiod.

Dessutom för Blob Storage, kan du tillåta offentlig åtkomst till dina blobar genom att ange åtkomstnivån för den behållare som innehåller blobar i enlighet med detta. Om du ställer in åtkomst för en behållare för Blob eller behållare kommer du att kunna offentlig läsbehörighet för blobar i behållaren. Det innebär att vem som helst med en URL som pekar till en blobb i behållaren kan du öppna den i en webbläsare utan med hjälp av en signatur för delad åtkomst eller lagringskontonycklarna.

Förutom att begränsa åtkomst via auktorisering, du kan också använda [brandväggar och virtuella nätverk](storage-network-security.md) att begränsa åtkomsten till lagringskontot baserat på regler.  Den här metoden kan du neka åtkomst till offentliga internet-trafik och att bevilja åtkomst till vissa specifika Azure-nätverk eller offentliga internet IP-adressintervall.

### <a name="storage-account-keys"></a>Lagringskontonycklar
Lagringskontonycklar är 512 bitar strängar som skapats av Azure som tillsammans med lagringskontonamn, kan användas för att få åtkomst till dataobjekt som lagras i lagringskontot.

Du kan till exempel läser blobbar, skriva till köer, skapa tabeller och ändra filer. Många av de här åtgärderna kan utföras via Azure-portalen eller med hjälp av ett av många program i Storage Explorer. Du kan också skriva kod för att använda REST API eller en av Storage-klientbibliotek för att utföra dessa åtgärder.

Enligt beskrivningen i avsnittet på den [Management plan Security](#management-plane-security), åtkomsten till storage-nycklar för ett klassiskt lagringskonto kan beviljas genom att ge fullständig åtkomst till Azure-prenumerationen. Åtkomst till storage-nycklar för ett lagringskonto med hjälp av Azure Resource Manager-modellen kan styras via rollbaserad åtkomstkontroll (RBAC).

### <a name="how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies"></a>Hur du delegerar åtkomst till objekt i ditt konto med signaturer för delad åtkomst och lagrade åtkomstprinciper
En signatur för delad åtkomst är en sträng som innehåller en säkerhetstoken som kan kopplas till en URI som gör det möjligt att delegera åtkomst till lagringsobjekt och ange begränsningar, till exempel behörigheter och datum-/ tidsintervall för åtkomst.

Du kan bevilja åtkomst till blobar, behållare, Kömeddelanden, filer och tabeller. Med tabeller, kan du faktiskt få åtkomstbehörighet till ett intervall med enheter i tabellen genom att ange de partition och radnycklar nyckelintervall som du vill att användaren ska ha åtkomst. Till exempel om du har data som lagras med en partitionsnyckel för geografiska tillstånd, du kan ge någon åtkomst till helt enkelt data för Kalifornien.

Ett annat exempel är kan du ge ett webbprogram en SAS-token som gör det möjligt att skriva poster till en kö och ge en worker rollen programmet en SAS-token för att hämta meddelanden från kön och bearbeta dem. Eller du kan ge en kund en SAS-token som de kan använda för att ladda upp bilder till en behållare i Blob Storage och ge en web program behörighet att läsa dessa bilder. Det finns en inkapsling av problem i båda fallen – varje program kan få just den tillgång som de behöver för att kunna utföra sina uppgifter. Detta är möjligt genom att använda signaturer för delad åtkomst.

#### <a name="why-you-want-to-use-shared-access-signatures"></a>Varför du vill använda signaturer för delad åtkomst
Varför skulle du vill använda en SAS istället för att bara ge ut din lagringskontonyckel som är mycket enklare? Lämna ut din lagringskontonyckel är som att dela nycklarna för storage-kungariket. Den ger fullständig åtkomst. Någon kan använda dina nycklar och ladda upp sina hela musikbibliotek till ditt lagringskonto. De kan även ersätta dina filer med virusinfekterade versioner eller stjäla dina data. Ge bort obegränsad tillgång till ditt lagringskonto är något som inte bör noga.

Du kan ge bara de behörigheter som krävs för en begränsad tidsperiod på en klient med signaturer för delad åtkomst. Till exempel om någon överfört en blob till ditt konto kan ge du dem skrivåtkomst tillräckligt länge att ladda upp blob (beroende på storleken på blobben, naturligtvis). Och om du ändrar dig kan du återkalla som har åtkomst till.

Dessutom kan du ange att begäranden som görs med hjälp av en SAS är begränsade till en viss IP-adress eller IP-adressintervall utanför Azure. Du kan också kräva att begäranden som görs med ett visst protokoll (HTTPS eller HTTP/HTTPS). Det innebär att om du bara vill tillåta HTTPS-trafik, du kan ange protokollet krävs till HTTPS endast och HTTP-trafik blockeras.

#### <a name="definition-of-a-shared-access-signature"></a>Definitionen av en signatur för delad åtkomst
En signatur för delad åtkomst är en uppsättning frågeparametrar som bifogats till den URL som pekar på resursen

som innehåller information om åtkomst tillåts och hur lång tid som åtkomst tillåts. Här är ett exempel. URI: N ger läsåtkomst till en blob i fem minuter. Observera att SAS-frågeparametrar måste vara URL-kodade, till exempel % 3A för kolon (:) eller % 20 för ett blanksteg.

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

#### <a name="how-the-shared-access-signature-is-authorized-by-the-azure-storage-service"></a>Hur signatur för delad åtkomst har beviljats behörighet av Azure Storage-tjänsten
När storage-tjänsten tar emot begäran, tar de inkommande frågeparametrarna och skapar en signatur med samma metod som det anropande programmet. Sedan jämförs två signaturer. Om användaren accepterar kan lagringstjänsten Kontrollera storage service-versionen för att kontrollera att den är giltig, kontrollera att aktuellt datum och tid är inom den angivna tidsperioden, se till att åtkomsten begärt motsvarar den begäran som görs, osv.

Till exempel med våra URL: en ovan misslyckas om du URL: en pekar på en fil i stället för en blob denna begäran eftersom den anger att signatur för delad åtkomst är för en blob. Om kommandot REST som anropas var att uppdatera en blob kan misslyckas det eftersom signatur för delad åtkomst som anger att bara läsbehörighet tillåts.

#### <a name="types-of-shared-access-signatures"></a>Typer av signaturer för delad åtkomst
* En SAS på servicenivå kan användas för åtkomst till specifika resurser i ett lagringskonto. Några exempel på detta hämtar en lista över blobar i en behållare, ladda ned en blob, uppdaterar en entitet i en tabell, att lägga till meddelanden i en kö eller ladda upp en fil till en filresurs.
* En SAS på kontonivå kan användas för åtkomst till allt som en tjänstnivå SAS kan användas för. Dessutom kan det ge alternativ till resurser som inte tillåts med en SAS på servicenivå, till exempel möjligheten att skapa behållare, tabeller, köer och filresurser. Du kan också ange åtkomst till flera tjänster på samma gång. Du kan till exempel ge någon kommer åt både blobar och filer i ditt storage-konto.

#### <a name="creating-a-sas-uri"></a>Skapa en SAS-URI
1. Du kan skapa en URI för på begäran, definierar frågeparametrarna varje gång.

   Den här metoden är flexibel, men om du har en logisk uppsättning parametrar som liknar varje gång, med hjälp av en lagrad åtkomstprincip är en bättre uppfattning.
2. Du kan skapa en lagrad åtkomstprincip för en hela behållaren, filresurs, tabell eller kö. Du kan sedan använda detta som grund för SAS-URI som du skapar. Behörigheter till följd av lagrade åtkomstprinciper kan återkallas enkelt. Du kan ha upp till fem principer som definierats för varje behållare, kö, tabell eller filresurs.

   Om du tänker har många personer som läser blobbar i en särskild behållare kan du till exempel skapa en lagrad åtkomstprincip som säger ”ger läsbehörighet” och eventuella andra inställningar som ska vara samma varje gång. Du kan skapa en SAS-URI med inställningarna för åtkomstprincip lagras och ange förfallodatum datum/tid. Fördelen med detta är att du inte behöver ange alla frågeparametrar varje gång.

#### <a name="revocation"></a>Återkallade certifikat
Anta att dina SAS har komprometterats eller om du vill ändra den på grund av företagets säkerhets- eller efterlevnadskrav. Hur du för att återkalla åtkomst till en resurs med hjälp av den SAS? Det beror på hur du har skapat SAS-URI.

Om du använder ad hoc-URI: er, har du tre alternativ. Du kan utfärda SAS-token med principer för kort förfallodatum och vänta tills Signaturen på att gå ut. Du kan byta namn på eller ta bort resursen (förutsatt att token har begränsade till ett enda objekt). Du kan ändra lagringskontonycklarna. Det här sista alternativet kan ha en betydande inverkan, beroende på hur många tjänster använder det lagringskontot och inte är förmodligen något du vill göra utan några planera.

Om du använder en SAS som härletts från en lagrad åtkomstprincip, du kan ta bort åtkomst genom att återkalla åtkomstprincip lagras – du kan bara ändra den så att den har redan upphört att gälla eller du kan ta bort det helt och hållet. Detta träder i kraft omedelbart och upphäver varje SAS som skapats med hjälp av den lagrade åtkomstprincip. Uppdatera eller ta bort åtkomstprincip lagras kan påverka besökarna åtkomst till specifika behållaren filresurs, tabell eller kö via SAS, men om klienterna skrivs så att de begär en ny SAS när den gamla servern blir ogiltig, den här metoden fungerar bra.

Eftersom en SAS som härletts från en lagrad åtkomstprincip ger dig möjlighet att återkalla den SAS direkt, är det rutin att alltid använda lagrade åtkomstprinciper när det är möjligt.

#### <a name="resources"></a>Resurser
Mer detaljerad information om hur du använder signaturer för delad åtkomst och lagrade åtkomstprinciper, med exempel, finns i följande artiklar:

* Det här är referensartiklar.

  * [Service SAS](https://msdn.microsoft.com/library/dn140256.aspx)

    Den här artikeln innehåller exempel på användning av en tjänstnivå SAS med BLOB-objekt, Kömeddelanden, tabell-intervall och filer.
  * [Skapa en tjänst-SAS](https://msdn.microsoft.com/library/dn140255.aspx)
  * [Skapa en konto-SAS](https://msdn.microsoft.com/library/mt584140.aspx)
* Det här är självstudier för att använda .NET-klientbiblioteket för att skapa signaturer för delad åtkomst och lagrade åtkomstprinciper.

  * [Använda signaturer för delad åtkomst (SAS)](../storage-dotnet-shared-access-signature-part-1.md)
  * [Signaturer för delad åtkomst, del 2: Skapa och använda en SAS med Blob Service](../blobs/storage-dotnet-shared-access-signature-part-2.md)

    Den här artikeln innehåller en förklaring av den SAS-modellen, exempel på signaturer för delad åtkomst och rekommendationer för bästa praxis använder SAS. Vi beskriver även återkallande av vilken behörighet.

* Autentisering

  * [Autentisering för Azure Storage-tjänster](https://msdn.microsoft.com/library/azure/dd179428.aspx)
* Signaturer för delad åtkomst har börjat självstudie

  * [SAS komma igång-Självstudier](https://github.com/Azure-Samples/storage-dotnet-sas-getting-started)

## <a name="encryption-in-transit"></a>Kryptering under överföring
### <a name="transport-level-encryption--using-https"></a>Kryptering på transportnivå – med hjälp av HTTPS
Ytterligare ett steg som du bör vidta för att säkerställa säkerheten för dina Azure Storage-data är att kryptera data mellan klienten och Azure Storage. Den första rekommendationen är att alltid använda den [HTTPS](https://en.wikipedia.org/wiki/HTTPS) -protokollet som säkerställer säker kommunikation via det offentliga Internet.

För att få en säker kommunikationskanal ska använda du alltid HTTPS när du anropar REST-API: er eller komma åt objekt i lagring. Dessutom **signaturer för delad åtkomst**, som kan användas för att delegera åtkomst till Azure Storage-objekt, innehåller ett alternativ för att ange endast HTTPS-protokollet kan användas när du använder signaturer för delad åtkomst, vilket säkerställer att vem som helst skicka länkar med SAS använder token rätt protokoll.

Du kan framtvinga användningen av HTTPS när du anropar REST-API: er att få åtkomst till objekt i storage-konton genom att aktivera [säker överföring krävs](../storage-require-secure-transfer.md) för lagringskontot. Anslutningar som använder HTTP ska avvisas när detta har aktiverats.

### <a name="using-encryption-during-transit-with-azure-file-shares"></a>Med hjälp av kryptering under överföring med Azure-filresurser
[Azure Files](../files/storage-files-introduction.md) stöder kryptering via SMB 3.0 och med HTTPS när du använder File REST API. När montera Azure-filresurs utanför Azure-regionen finns i, till exempel lokalt eller i en annan Azure-region, SMB 3.0 med kryptering krävs alltid. SMB 2.1 stöder inte kryptering, så att som standard tillåts enbart anslutningar inom samma region i Azure, men SMB 3.0 med kryptering kan aktiveras av [kräver säker överföring](../storage-require-secure-transfer.md) för lagringskontot.

SMB 3.0 med kryptering finns i [alla Windows och Windows Server-operativsystem som stöds](../files/storage-how-to-use-files-windows.md) utom Windows 7 och Windows Server 2008 R2, som endast stöd för SMB 2.1. SMB 3.0 stöds även på [macOS](../files/storage-how-to-use-files-mac.md) och på distributioner av [Linux](../files/storage-how-to-use-files-linux.md) med hjälp av Linux-kernel 4.11 och senare. Stöd för kryptering för SMB 3.0 har också anpassats för äldre versioner av Linux-kernel genom flera Linux-distributioner, läser [förstå SMB klientkrav](../files/storage-how-to-use-files-linux.md#smb-client-reqs).

### <a name="using-client-side-encryption-to-secure-data-that-you-send-to-storage"></a>Använder Client side encryption för att skydda data som du skickar till storage
Ett annat alternativ som hjälper dig att säkerställa att dina data är säkra medan de överförs mellan ett klientprogram och lagring är Client side Encryption. Data krypteras innan de överförs till Azure Storage. När du hämtar data från Azure Storage, dekrypteras data när den tas emot på klientsidan. Även om datan krypteras över nätverket, rekommenderar vi att du även använda HTTPS, eftersom den har dataintegritetskontroller inbyggda som hjälper till att minska nätverksfel som påverkar integriteten hos data.

Client side encryption är också en metod för att kryptera dina data i vila, eftersom data lagras i krypterat format. Vi kommer att tala om detta i detalj i avsnittet på [kryptering i vila](#encryption-at-rest).

## <a name="encryption-at-rest"></a>Kryptering i vila
Det finns tre Azure-funktioner som tillhandahåller kryptering i vila. Azure Disk Encryption används för att kryptera OS och datadiskar i IaaS-datorer. Både används kryptering och SSE på klientsidan att kryptera data i Azure Storage. 

Du kan använda Client side Encryption för att kryptera data under överföring (som lagras också i krypterat format i Storage), kan du föredrar att använda HTTPS under överföringen och har vissa metod för data krypteras automatiskt när den är lagrad. Det finns två sätt att göra detta – Azure Disk Encryption och SSE. En används för att kryptera data på Operativsystemet och datadiskarna som används av virtuella datorer direkt och den andra används för att kryptera data som skrivs till Azure Blob Storage.

### <a name="storage-service-encryption-sse"></a>Storage Service Encryption (SSE)

SSE är aktiverat för alla lagringskonton och kan inte inaktiveras. SSE krypterar automatiskt data när du skriver den till Azure Storage. När du läser data från Azure Storage kan dekrypteras av Azure Storage innan de returneras. SSE gör det möjligt för dig att skydda dina data utan att behöva ändra kod och lägga till kod för program.

Du kan använda Microsoft-hanterade nycklar eller dina egna anpassade nycklar. Microsoft genererar hanterade nycklar och hanterar sina säker lagring samt deras regelbundna rotation, som den definieras av intern Microsoft-princip. Mer information om hur du använder anpassade nycklar finns i [kryptering av lagringstjänst med Kundhanterade nycklar i Azure Key Vault](storage-service-encryption-customer-managed-keys.md).

SSE krypterar automatiskt data på alla prestandanivåer (Standard och Premium), alla distributionsmodeller (Azure Resource Manager och klassisk) och alla Azure Storage-tjänster (blob, kö, tabell och fil). 

### <a name="client-side-encryption"></a>Client side Encryption
Vi nämns client side encryption när diskutera kryptering av data under överföring. Den här funktionen kan du programmässigt kryptera data i ett klientprogram innan de skickas via kabeln som ska skrivas till Azure Storage och att programmässigt dekryptera data efter hämtning från Azure Storage.

Detta har kryptering under överföring, men det ger också en funktion till kryptering i vila. Även om informationen är krypterad under överföringen, rekommenderar vi fortfarande använda HTTPS för att dra nytta av de inbyggda integritetskontroller som hjälper till att minska nätverksfel som påverkar integriteten hos data.

Ett exempel på där du kan använda det här är om du har ett webbprogram som lagrar blobar och hämtar BLOB-objekt och de program och data ska vara precis lika säkert som möjligt. I så fall använder du client side encryption. Trafiken mellan klienten och Azure Blob-tjänsten innehåller krypterade resursen och ingen kan tolka data under överföring och fylla i dina privata blobar.

Client side encryption är inbyggt i Java och .NET lagringsklientbiblioteken, som i sin tur använder Azure Key Vault API: er, vilket gör det enkelt för dig att implementera. Processen för att kryptera och dekryptera data använder kuvert-teknik och lagrar metadata som används av kryptering i varje lagringsobjekt. Till exempel för blobbar, lagras den i blobmetadata och för köer, läggs till varje kömeddelande.

För krypteringen, kan du generera och hantera egna krypteringsnycklar. Du kan också använda nycklarna som genereras av Azure Storage-klientbibliotek eller du kan ha Azure Key Vault genererar nycklarna. Du kan lagra krypteringsnycklarna i din lokala lagring eller du kan lagra dem i ett Azure Key Vault. Azure Key Vault kan du bevilja åtkomst till hemligheter i Azure Key Vault till specifika användare med Azure Active Directory. Det innebär att inte bara vem som helst kan läsa Azure Key Vault och hämta de nycklar som du använder för kryptering av klientsidan.

#### <a name="resources"></a>Resurser
* [Kryptera och dekryptera blobbar i Microsoft Azure Storage med Azure Key Vault](../blobs/storage-encrypt-decrypt-blobs-key-vault.md)

  Den här artikeln visar hur du använder client side encryption med Azure Key Vault, inklusive hur du skapar KEK och lagra den i valvet med hjälp av PowerShell.
* [Client Side Encryption och Azure Key Vault för Microsoft Azure Storage](../storage-client-side-encryption.md)

  Den här artikeln ger en förklaring av client side encryption och innehåller exempel på hur du använder storage-klientbiblioteket för att kryptera och dekryptera resurser från de fyra storage-tjänsterna. Den berättar även om Azure Key Vault.

### <a name="using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines"></a>Med Azure Disk Encryption för att kryptera diskar som används av dina virtuella datorer
Azure Disk Encryption kan du kryptera OS-diskar och datadiskar som används av en IaaS-dator. För Windows krypteras enheterna som med BitLocker-kryptering branschstandard. För Linux krypteras diskar med hjälp av teknik för DM-Crypt. Detta är integrerat med Azure Key Vault så att du kan styra och hantera krypteringsnycklar för disken.

Lösningen stöder följande scenarier för virtuella IaaS-datorer när de är aktiverade i Microsoft Azure:

* Integrering med Azure Key Vault
* Standard-nivån virtuella datorer: [A, D, DS, G, GS och så vidare serien virtuella IaaS-datorer](https://azure.microsoft.com/pricing/details/virtual-machines/)
* Aktivera kryptering på Windows och Linux IaaS-datorer
* Inaktivera kryptering på operativsystem och enheter för Windows virtuella IaaS-datorer
* Inaktivering av kryptering på enheter för virtuella Linux IaaS-datorer
* Aktivera kryptering på den virtuella IaaS-datorer som kör Windows-klientoperativsystem
* Aktivera kryptering på volymer med monteringssökvägar
* Aktivera kryptering på virtuella Linux-datorer som är konfigurerade med disk-striping (RAID) med hjälp av mdadm
* Aktivera kryptering på virtuella Linux-datorer med hjälp av LVM för datadiskar
* Aktivera kryptering på Windows virtuella datorer som konfigureras med hjälp av lagringsutrymmen
* Alla offentliga Azure-regioner som stöds

Lösningen stöder inte följande scenarier, funktioner och -teknik i version:

* Basic-nivån virtuella IaaS-datorer
* Inaktivering av kryptering på en OS-enhet för virtuella Linux IaaS-datorer
* Virtuella IaaS-datorer som skapas med hjälp av klassiska metod för skapande av virtuell dator
* Integrering med din lokala nyckelhanteringstjänst
* Azure Files (delade filsystem), Network File System (NFS), dynamiska volymer och Windows-datorer som är konfigurerade med programvarubaserade RAID-system


> [!NOTE]
> Linux OS-diskkryptering stöds för närvarande på följande Linux-distributioner: RHEL 7.2 CentOS 7.2n och Ubuntu 16.04.
>
>

Den här funktionen garanterar att alla data på virtuella datordiskar krypteras i vila i Azure Storage.

#### <a name="resources"></a>Resurser
* [Azure Disk Encryption för Windows och Linux IaaS-datorer](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)

### <a name="comparison-of-azure-disk-encryption-sse-and-client-side-encryption"></a>Jämförelse av Azure Disk Encryption, SSE och Client Side Encryption

#### <a name="iaas-vms-and-their-vhd-files"></a>Virtuella IaaS-datorer och deras VHD-filer

Azure Disk Encryption rekommenderas för datadiskar som används av virtuella IaaS-datorer. Om du skapar en virtuell dator med ohanterade diskar med hjälp av en avbildning från Azure Marketplace, Azure utför en [ytlig kopia](https://en.wikipedia.org/wiki/Object_copying) av avbildningen till ditt storage-konto i Azure Storage och det är inte krypterad även om du har aktiverat SSE. När den skapar den virtuella datorn och startar uppdatering av avbildningen, startar SSE krypterar data. Därför är det bäst att använda Azure Disk Encryption på virtuella datorer med ohanterade diskar som skapas från avbildningar på Azure Marketplace om du vill att de helt krypterad. Om du skapar en virtuell dator med Managed Disks krypterar SSE alla data som standard med hjälp av plattform som hanterade nycklar. 

Om du använder en förkrypterade virtuell dator till Azure från lokala kommer du att kunna överföra dem till Azure Key Vault och fortsätt att använda kryptering för den virtuella datorn som du använde lokala. Azure Disk Encryption är aktiverat kan hantera detta scenario.

Om du har icke-krypterade VHD från en lokal plats kan du överföra den till galleriet som en anpassad avbildning och etablera en virtuell dator från den. Om du gör detta med hjälp av Resource Manager-mallar, kan du be den för att aktivera Azure Disk Encryption när den startas den virtuella datorn.

När du lägger till en datadisk och montera den på den virtuella datorn, kan du aktivera Azure Disk Encryption på disken data. Det kommer att kryptera data disken lokalt först och sedan modellager klassisk distribution gör en lazy skrivning mot storage så storage innehållet krypteras.

#### <a name="client-side-encryption"></a>Kryptering av klientsidan
Client side encryption är den säkraste metoden för att kryptera dina data, eftersom krypteras data före överföringen.  Det kräver dock att du lägger till kod till dina program med hjälp av lagringsutrymmen, som du inte vill göra. I sådana fall kan använda du HTTPS för att skydda dina data under överföring. När data når Azure Storage, krypteras den med SSE.

Du kan kryptera tabellenheter, Kömeddelanden och blobar med client side encryption. 

Client side encryption hanteras helt av programmet. Detta är den säkraste metoden, men kräver att du gör programmässiga ändringar i programmet och placera viktiga processer på plats. Du skulle använda när du vill ha extra säkerhet under överföringen och du vill att lagrade data krypteras.

Client side encryption är högre belastning på klienten och du behöver det här i beräkningen planerna skalbarhet, särskilt om du krypterar och överför en stor mängd data.

#### <a name="storage-service-encryption-sse"></a>Storage Service Encryption (SSE)

SSE hanteras av Azure Storage. SSE ger inte för att skydda data under överföring, men den krypterar data som skrivs till Azure Storage. SSE påverkar inte Azure Storage-prestanda.

Du kan kryptera alla slags data på lagringskontot med SSE (blockblobbar, tilläggsblobbar, sidblobar, tabell, kö-data och filer).

Om du har ett arkiv eller bibliotek med VHD-filer som du använder som bas för att skapa nya virtuella datorer kan du skapa ett nytt lagringskonto och sedan ladda upp VHD-filerna till det kontot. Dessa VHD-filer krypteras av Azure Storage.

Om du har aktiverat för diskarna i en virtuell dator för Azure Disk Encryption, krypteras alla data som nyskrivna både genom SSE och Azure Disk Encryption.

## <a name="storage-analytics"></a>Lagringsanalys
### <a name="using-storage-analytics-to-monitor-authorization-type"></a>Använda Storage Analytics för att övervaka auktoriseringstyp
Du kan aktivera Azure Storage Analytics att utföra loggning och lagra måttdata för varje storage-konto. Det här är ett bra verktyg för att använda när du vill kontrollera prestandavärden för ett lagringskonto eller behöver felsöka ett storage-konto eftersom du har problem med prestanda.

En annan typ av data som du ser i storage analytics-loggar är den autentiseringsmetod som används av någon när de har åtkomst till lagring. Du kan exempelvis se om de används en signatur för delad åtkomst eller lagringskontonycklarna, eller om den blob som nås var offentliga i med Blob Storage.

Detta kan vara användbart om du nära skyddar åtkomst till lagring. I Blob Storage kan du exempelvis anger alla behållare som privat och implementera användningen av en SAS-tjänst i dina program. Du kan kontrollera loggarna regelbundet för att se om dina blobar ska hämtas med hjälp av lagringskontonycklar, vilket kan tyda på ett sekretessbrott, eller om att blobarna är offentliga men de får inte vara.

#### <a name="what-do-the-logs-look-like"></a>Hur ser loggar ut?
När du aktiverar mått för lagringskonto och loggning via Azure portal, analysdata börjar ackumuleras snabbt. Loggning och mått för varje tjänst är separat; loggning skrivs endast när det är aktiviteten i det lagringskontot medan mått som loggas varje minut, varje timme eller varje dag, beroende på hur du konfigurerar.

Loggfilerna lagras i blockblob-objekt i en behållare med namnet $logs i lagringskontot. Den här behållaren skapas automatiskt när Lagringsanalys är aktiverat. När den här behållaren har skapats kan du ta bort den, även om du kan ta bort innehållet.

Det finns en mapp för varje tjänst under behållaren $logs och det finns undermappar för år/månad/dag/timme. Under timmen, är loggarna numrerade. Detta är hur katalogstrukturen ser ut:

![Visa loggfiler](./media/storage-security-guide/image1.png)

Varje begäran till Azure Storage loggas. Här är en ögonblicksbild av en loggfil som visar de första några fält.

![Ögonblicksbild av en loggfil](./media/storage-security-guide/image2.png)

Du kan se att du kan använda loggarna för att spåra alla slags anrop till ett lagringskonto.

#### <a name="what-are-all-of-those-fields-for"></a>Vad är alla dessa fält för?
Det finns en artikel som anges i resurserna nedan som innehåller en lista över många fält i loggarna och hur de används. Här är listan över fält i ordning:

![Ögonblicksbild av fält i en loggfil](./media/storage-security-guide/image3.png)

Vi är intresserade av posterna för GetBlob och hur de har behörighet, så vi behöver titta efter poster med åtgärdstypen ”Get-Blob” och kontrollera begäran-status (fjärde</sup> kolumn) och auktorisering-typ (åttonde</sup> kolumn).

Till exempel i de första raderna i listan ovan, begäran-status är ”lyckades” och auktorisering-type ”autentiseras”. Det innebär att förfrågan auktoriserades med hjälp av lagringskontots åtkomstnyckel.

#### <a name="how-is-access-to-my-blobs-being-authorized"></a>Hur är åtkomst till mitt BLOB-kunde auktoriseras?
Vi har tre fall som vi är intresserade.

1. Bloben är offentliga och den kan nås med hjälp av en URL utan en signatur för delad åtkomst. I det här fallet begäran-status är ”AnonymousSuccess” och auktorisering-typ är ”anonym”.

   1.0;2015-11-17T02:01:29.0488963Z;GetBlob;**AnonymousSuccess**;200;124;37;**anonymous**;;mystorage…
2. Bloben är privat och användes med en signatur för delad åtkomst. I det här fallet begäran-status är ”SASSuccess” och auktorisering-typ är ”sas”.

   1.0; 2015-11-16T18:30:05.6556115Z; GetBlob; **SASSuccess**, 200, 416, 64; **SAS**; mystorage...
3. Bloben är privat och lagringsnyckeln har använts för att komma åt den. I det här fallet begäran-status är ”**lyckades**” och auktorisering-typen är ”**autentiserad**”.

   1.0; 2015-11-16T18:32:24.3174537Z; GetBlob; **Lyckades**; 206; 59; 22; **autentiserad**; mystorage...

Du kan använda Microsoft Message Analyzer för att visa och analysera dessa loggar. Den innehåller funktioner för sökning och filter. Du kan till exempel vill söka efter instanser av GetBlob för att se om användningen är vad du förväntar dig, det vill säga för att kontrollera att någon inte har åtkomst till ditt storage-konto på ett olämpligt sätt.

#### <a name="resources"></a>Resurser
* [Lagringsanalys](../storage-analytics.md)

  Den här artikeln är en översikt över storage analytics och hur du aktiverar dem.
* [Loggformatet för Storage Analytics](https://msdn.microsoft.com/library/azure/hh343259.aspx)

  Den här artikeln visar loggformatet Storage Analytics och lagringsmedia, information fält som är tillgängliga, inklusive autentisering-typ, som visar vilken typ av autentisering som används för begäran.
* [Övervaka ett Lagringskonto i Azure portal](../storage-monitor-storage-account.md)

  Den här artikeln visar hur du konfigurerar övervakning av mätvärden och loggning för ett lagringskonto.
* [Felsökning för slutpunkt till slutpunkt med hjälp av Azure Storage-mätvärden och loggning, AzCopy och Message Analyzer](../storage-e2e-troubleshooting.md)

  Den här artikeln pratar om felsökning med hjälp av Azure-Lagringsanalys och visar hur du använder Microsoft Message Analyzer.
* [Microsoft Message Analyzer fungerar Guide](https://technet.microsoft.com/library/jj649776.aspx)

  Den här artikeln är referensen för Microsoft Message Analyzer och innehåller länkar till en självstudie, Snabbstart och funktionssammanfattningen.

## <a name="cross-origin-resource-sharing-cors"></a>Resursdelning för korsande ursprung (CORS)
### <a name="cross-domain-access-of-resources"></a>Korsdomänåtkomst för resurser
När en webbläsare som körs i en domän gör en HTTP-begäran för en resurs från en annan domän, kallas detta en resursdelning för korsande ursprung HTTP-begäran. Exempelvis begär en HTML-sida som hämtats från contoso.com jpeg fabrikam.blob.core.windows.net som värdplattform. Av säkerhetsskäl begränsa resursdelning för korsande ursprung HTTP-begäranden som initieras från inom skript, till exempel JavaScript i webbläsare. Det innebär att när JavaScript-kod på en webbsida på contoso.com begär som jpeg på fabrikam.blob.core.windows.net, webbläsaren inte tillåter att begäran.

Vad har detta för att göra med Azure Storage? Om du lagrar statiska tillgångar som till exempel JSON eller XML-filer i Blob Storage med hjälp av ett lagringskonto kallas Fabrikam domänen för resurserna som kommer att fabrikam.blob.core.windows.net och webbprogrammet contoso.com kommer inte att kunna komma åt dem med hjälp av JavaScript eftersom domänerna är olika. Detta gäller även om du försöker anropa en Azure Storage-tjänster – till exempel tabellagring – som returnerar JSON-data som ska bearbetas av JavaScript-klient.

#### <a name="possible-solutions"></a>Möjliga lösningar
Ett sätt att lösa problemet är att tilldela en anpassad domän som ”storage.contoso.com” till fabrikam.blob.core.windows.net. Problemet är att du endast tilldela den anpassa domänen till ett lagringskonto. Vad händer om resurserna som lagras i flera storage-konton?

Ett annat sätt att lösa problemet är att ha webbprogrammet fungerar som proxy för storage-anrop. Det innebär att om du överför en fil till Blob Storage, webbprogrammet kan antingen skriva det lokalt och sedan kopiera den till Blob Storage eller den skulle läsa in dem i minnet och sedan skriva den till Blob Storage. Du kan också skriva ett dedikerat webbprogram (till exempel ett webb-API) som överför filer lokalt och skriver dem till Blob Storage. I båda fallen som du har att kompensera för den funktionen när skalbarheten behöver du använda.

#### <a name="how-can-cors-help"></a>Hur kan CORS hjälpa?
Azure Storage kan du aktivera CORS – Cross Origin Resource Sharing. Du kan ange domäner som har åtkomst till resurser i det lagringskontot för varje storage-konto. I vårt fall som beskrivs ovan, vi till exempel aktivera CORS för fabrikam.blob.core.windows.net storage-konto och konfigurera den för att tillåta åtkomst till contoso.com. Web application contoso.com kan sedan direkt åtkomst till resurser i fabrikam.blob.core.windows.net.

En sak att tänka på är att CORS tillåter åtkomst, men den ger inte autentisering, vilket krävs för alla icke-offentlig åtkomst för lagringsresurser. Det innebär att du kan bara komma åt blobar om de är offentlig eller du inkludera en signatur för delad åtkomst som ger dig rätt behörighet. Tabeller, köer och filer har ingen offentlig åtkomst och kräver en SAS.

CORS är inaktiverad på alla tjänster som standard. Du kan aktivera CORS med hjälp av REST-API: et eller storage-klientbiblioteket för att anropa en av metoder för att ange principer för tjänsten. När du gör det kan inkludera du en CORS-regel som XML. Här är ett exempel på en CORS-regel som har ställts in med hjälp av åtgärden Ange egenskaper för Blob-tjänsten för ett lagringskonto. Du kan utföra åtgärden med hjälp av storage-klientbibliotek eller REST-API: er för Azure Storage.

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

Här är vad varje rad innebär:

* **AllowedOrigins** anger vilka icke-matchande domäner kan begära och ta emot data från storage-tjänsten. Det säger att både contoso.com och fabrikam.com kan begära data från Blob Storage för ett specifikt lagringskonto. Du kan också ange det till ett jokertecken (\*) så att alla domäner får åtkomst till begäranden.
* **AllowedMethods** det här är listan över metoder (HTTP-förfrågningsverb) som kan användas när du gör begäran. I det här exemplet tillåts endast PUT- och GET. Du kan ange detta till ett jokertecken (\*) så att alla metoder som ska användas.
* **AllowedHeaders** det här är begärandehuvuden som ursprungsdomänen kan ange när den skickar förfrågan. I det här exemplet tillåts alla rubriker för arbetsflödesmetadata som börjar med x-ms-metadata, x-ms-meta-mål och x-ms-meta-abc. Jokertecknet (\*) anger att alla rubrik som börjar med det angivna prefixet tillåts.
* **ExposedHeaders** anger vilka svarshuvuden som ska visas i webbläsaren för utfärdaren av förfrågan. I det här exemplet, alla rubriker som börjar med ”x-ms - meta-” visas.
* **MaxAgeInSeconds** det här är den maximala tidsperiod som webbläsaren cachelagrar den preliminära OPTIONS-begäran. (Mer information om preliminära begäran finns i den första artikeln nedan.)

#### <a name="resources"></a>Resurser
Mer information om CORS och hur du aktiverar det finns dessa resurser.

* [Cross-Origin Resource Sharing (CORS) Support för Azure Storage-tjänsterna på Azure.com](../storage-cors-support.md)

  Den här artikeln innehåller en översikt över CORS och hur du ställer in regler för olika lagringstjänster.
* [Cross-Origin Resource Sharing (CORS) Support för Azure Storage-tjänsterna på MSDN](https://msdn.microsoft.com/library/azure/dn535601.aspx)

  Det här är referensdokumentation för CORS-stöd för Azure Storage-tjänster. Det finns länkar till artiklar som gäller för varje lagringstjänst och visar ett exempel och förklarar varje element i CORS-filen.
* [Microsoft Azure Storage: Introduktion till CORS](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/02/03/windows-azure-storage-introducing-cors.aspx)

  Det här är en länk till inledande bloggartikel presenterar CORS och visar hur du använder den.

## <a name="frequently-asked-questions-about-azure-storage-security"></a>Vanliga frågor och svar om Azure Storage-säkerhet
1. **Hur kan jag verifiera integriteten hos de blobar som jag överföra till och från Azure-lagring om jag inte kan använda HTTPS-protokollet?**

   Om du av någon anledning måste du använda HTTP i stället för HTTPS och du arbetar med blockblobar kan använda du MD5 kontrollerar för att kontrollera integriteten för de blobar som överförs. På så sätt med skydd mot fel för nätverk/transport layer, men inte nödvändigtvis med mellanliggande attacker.

   Om du kan använda HTTPS, som ger transportsäkerhet, är sedan med hjälp av MD5 kontrollerar redundant och onödiga.

   Mer information finns på den [översikt över Azure Blob-MD5](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/02/18/windows-azure-blob-md5-overview.aspx).
2. **Vad är FIPS-kompatibilitet för USA Myndigheter?**

   I USA FIPS Federal Information Processing Standard () definierar kryptografiska algoritmer som godkänts för användning av USA Federala myndigheter datorsystem för skydd av känsliga data. För att aktivera FIPS-läge på en Windows server eller desktop talar om Operativsystemet endast FIPS-validerade kryptografiska algoritmer som ska användas. Om ett program använder icke-kompatibla algoritmer, bryts programmen. With.NET ramverksversioner 4.5.2 eller senare programmet automatiskt växlar krypteringsalgoritmer för att använda FIPS-kompatibla algoritmer när datorn är i FIPS-läge.

   Microsoft lämnar det upp till varje kund för att bestämma om du vill aktivera FIPS-läge. Vi tror att det finns ingen övertygande anledning för kunder som inte omfattas av statliga regleringar att aktivera FIPS-läge som standard.

### <a name="resources"></a>Resurser
* [Varför inte rekommenderar vi ”FIPS-läge” längre](https://blogs.technet.microsoft.com/secguide/2014/04/07/why-were-not-recommending-fips-mode-anymore/)

  Den här bloggen artikeln ger en översikt över FIPS och som förklarar varför de inte aktivera FIPS-läge som standard.
* [FIPS 140 verifiering](https://technet.microsoft.com/library/cc750357.aspx)

  Den här artikeln innehåller information om hur Microsoft-produkter och kryptografiska moduler som överensstämmer med den FIPS-standarden för USA Federala myndigheter.
* [”Systemkryptografi: Använd FIPS-kompatibla algoritmer för kryptering, hashing och signering ”inställningar effekterna på säkerheten i Windows XP och senare versioner av Windows](https://support.microsoft.com/kb/811833)

  Den här artikeln handlar om användning av FIPS-läge i äldre Windows-datorer.
