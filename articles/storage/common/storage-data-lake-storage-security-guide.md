---
title: Azure Data Lake Storage Gen2 lagringssäkerhetsguide | Microsoft Docs
description: Information om de många metoderna för att skydda Azure Storage, inklusive men inte begränsat till RBAC och kryptering av lagringstjänst
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 12/04/2018
ms.author: rogarana
ms.component: common
ms.openlocfilehash: d2182942b8d1ce78fd4a72ff387c7a6a1cfead5a
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52975090"
---
# <a name="azure-data-lake-storage-gen2-security-guide"></a>Säkerhetsguiden för Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 förhandsversion, är en uppsättning funktioner som bygger på Azure Storage-konton. Därför måste är alla referenser i den här artikeln avsedda för ett Azure Storage-konto med hierarkiskt namnområde aktiverat (Data Lake Storage Gen2 funktioner).

- Alla data som skrivs till Azure Storage krypteras automatiskt med [Storage Service Encryption (SSE)](storage-service-encryption.md). Mer information finns i [presenterar standard kryptering för Azure-Blobar, filer, tabeller och Queue Storage](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).
- Azure Active Directory (Azure AD) och rollbaserad åtkomstkontroll (RBAC) stöds för Azure Storage för både resource management-åtgärder och åtgärder, enligt följande:
    - Du kan tilldela RBAC-roller som är begränsade till storage-konto till säkerhetsobjekt och Använd Azure AD för att auktorisera resource management-åtgärder, till exempel nyckelhantering.
    - Azure AD-integrering stöds i förhandsversionen för åtgärder på Azure Storage. Du kan tilldela RBAC-roller som är begränsade till en prenumeration, resursgrupp, storage-konto eller en enskild filsystemet till ett säkerhetsobjekt eller en hanterad identitet för Azure-resurser. Mer information finns i [autentisera åtkomsten till Azure Storage med Azure Active Directory (förhandsversion)](storage-auth-aad.md).
- Delegerad åtkomst till dataobjekt i Azure Storage kan tilldelas via [signaturer för delad åtkomst](../storage-dotnet-shared-access-signature-part-1.md).

Den här artikeln innehåller en översikt över var och en av dessa säkerhetsfunktioner som kan användas med Azure Storage. Länkar tillhandahålls till artiklar som ger information om varje funktion så kan du lätt göra ytterligare undersökningar på varje avsnitt.

Här följer ämnena i den här artikeln:

* [Hantering av plan Security](#management-plane-security) – skydda ditt Storage-konto

  Hanteringsplanet består av de resurser som används för att hantera ditt lagringskonto. Det här avsnittet beskriver distributionsmodellen Azure Resource Manager och hur du använder rollbaserad åtkomstkontroll (RBAC) för att styra åtkomsten till dina lagringskonton. Den behandlar också hantera dina lagringskontonycklar och hur du återskapar dem.
* [Data-dataplaner Security](#data-plane-security) – skydda åtkomst till dina Data

  I det här avsnittet tittar vi på att tillåta åtkomst till de faktiska data-objekt i ditt Storage-konto, t.ex. filer och kataloger, med hjälp av signaturer för delad åtkomst och lagrade åtkomstprinciper. Vi täcker både SAS på servicenivå och kontonivå SAS. Vi ska också se hur du begränsar åtkomsten till en specifik IP-adress (eller IP-adressintervall), hur du begränsar det protokoll som används till HTTPS och hur du återkalla en signatur för delad åtkomst utan att vänta tills det upphör att gälla.
* [Kryptering vid överföring](#encryption-in-transit)

Det här avsnittet beskrivs hur du skyddar data när du överför den till eller från ett lagringskonto med Data Lake Storage Gen2 aktiverat. Vi kommer att prata om rekommenderade användning av HTTPS.

## <a name="management-plane-security"></a>Management plan säkerhet

Hanteringsplanet består av åtgärder som påverkar själva lagringskontot. Du kan till exempel skapa eller ta bort ett lagringskonto, hämta en lista över storage-konton i en prenumeration, hämta lagringskontonycklarna eller återskapa lagringskontonycklarna.

Den här guiden fokuserar på Resource Manager-modellen för distribution, vilket är ett sätt för att skapa storage-konton med Data Lake Storage Gen2 funktioner. Med den Resource Manager-konton i stället ger tillgång till hela prenumerationen, kan du styra åtkomsten på en mer begränsad nivå till Hanteringsplanet med hjälp av rollbaserad åtkomstkontroll (RBAC).

### <a name="how-to-secure-your-storage-account-with-role-based-access-control-rbac"></a>Hur du skyddar ditt lagringskonto med rollbaserad åtkomstkontroll (RBAC)

Nu ska vi prata om RBAC är och hur du kan använda den. Varje Azure-prenumerationen har en Azure Active Directory. Användare, grupper och program från katalogen kan beviljas åtkomst för att hantera resurser i Azure-prenumeration som använder Resource Manager-distributionsmodellen. Den här typen av security kallas rollbaserad åtkomstkontroll (RBAC). Hantera åtkomst med **åtkomstkontroll (IAM)** i Azure-portalen.

Med Resource Manager-modellen kan placera du storage-konto i en resurs och kontrollerar åtkomst till Hanteringsplanet för den specifika storage-konto med Azure Active Directory. Du kan till exempel ge särskilda användare möjlighet att få åtkomst till lagringskontonycklarna, medan andra användare kan visa information om storage-konto, men det går inte att få åtkomst till lagringskontonycklarna.

#### <a name="granting-access"></a>Bevilja åtkomst

Komma åt genom att tilldela lämpliga RBAC-roll till användare, grupper och program i det högra omfånget. Om du vill bevilja åtkomst till hela prenumerationen, kan du tilldela en roll på prenumerationsnivån. Du kan bevilja åtkomst till alla resurser i en resursgrupp genom att bevilja behörighet till själva resursgruppen. Du kan också tilldela specifika roller till specifika resurser, till exempel lagringskonton.

Här är de viktigaste aspekterna som du behöver veta om hur du använder RBAC för att få åtkomst till hanteringsåtgärder på ett Azure Storage-konto:

* Du kan ge dem behörighet att läsa lagringskontonycklarna för någon behörighet att få åtkomst till dataobjekt i storage-konto och som användaren kan sedan använda dessa nycklar för att få åtkomst till filer och kataloger.
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
* [RBAC: inbyggda roller](../../role-based-access-control/built-in-roles.md)

  Den här artikeln beskriver alla inbyggda roller som är tillgängliga i RBAC.
* [Förstå Resource Manager-distribution och klassisk distribution](../../azure-resource-manager/resource-manager-deployment-model.md)

### <a name="managing-your-storage-account-keys"></a>Hantera dina Lagringskontonycklar

Lagringskontonycklar är 512 bitar strängar som skapats av Azure som tillsammans med lagringskontonamn, kan användas för att få åtkomst till dataobjekt som lagras i lagringskontot, till exempel, blobbar, entiteter i en tabell, Kömeddelanden och filer på en Azure-filresurs. Kontrollera åtkomst till de storage-konto nycklar kontrollerar åtkomsten till dataplanet för det lagringskontot.

Varje lagringskonto har två nycklar som kallas ”nyckel 1” och ”nyckel 2” i den [Azure-portalen](http://portal.azure.com/) och i PowerShell-cmdlets. Dessa kan återskapas manuellt med hjälp av en eller flera metoder, inklusive men inte begränsat till med hjälp av den [Azure-portalen](https://portal.azure.com/), PowerShell, Azure CLI, eller programmässigt med .NET Storage Client Library eller Azure Storage-tjänster REST-API.

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

Ett annat alternativ är att placera lagringskontonyckeln i en [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) som en hemlighet och ha dina program hämta nyckeln därifrån. När du återskapa nyckel och uppdatera Azure Key Vault behöver sedan program som inte distribueras eftersom de ska hämta den nya nyckeln från Azure Key Vault automatiskt. Du kan få programmet att läsa nyckeln varje gång som du behöver den, eller så kan cachelagra i minnet och om det misslyckas när du använder den, hämta nyckeln igen från Azure Key Vault.

Med Azure Key Vault lägger också till en annan nivå av säkerhet för dina lagringsnycklar. Om du använder den här metoden kan har du aldrig lagring viktiga hårdkodad i en konfigurationsfil, vilket tar bort den minimering av någon får åtkomst till nycklarna utan uttryckligt tillstånd.

En annan fördel med att använda Azure Key Vault är att du kan också kontrollera åtkomsten till dina nycklar med hjälp av Azure Active Directory. Det innebär att du kan bevilja åtkomst till program som behöver hämta nycklarna från Azure Key Vault och säker på att andra program inte kan komma åt nycklarna utan att ge dem behörighet specifikt handfull.

> [!NOTE]
> Microsoft rekommenderar att du använder bara en av nycklarna i alla dina program på samma gång. Om du använder nyckel 1 på vissa platser och nyckel 2 i andra kan kunna du inte rotera nycklarna utan att ett program att förlora åtkomst.

#### <a name="resources"></a>Resurser

* [Hantera inställningar för lagringskonto i Azure portal](storage-account-manage.md)
* [Referens för REST-API för Azure Storage Resource Provider](https://msdn.microsoft.com/library/mt163683.aspx)

## <a name="data-plane-security"></a>Plan för datasäkerhet
Plan för datasäkerhet refererar till de metoder som används för att skydda dataobjekt som lagras i Azure Storage. Vi har sett metoder för att kryptera data och security under överföring av data, men hur skaffar du styr åtkomst till objekten?

Det finns tre alternativ för att auktorisera åtkomst till dataobjekt i Azure Storage, inklusive:

- Använda Azure AD för att bevilja åtkomst till filsystem och köer (förhandsversion). Azure AD tillhandahåller fördelar jämfört med andra metoder för auktorisering, inklusive ta bort behovet av att lagra hemligheter i din kod. Mer information finns i [autentisera åtkomsten till Azure Storage med Azure Active Directory (förhandsversion)](storage-auth-aad.md). 
- Med storage-kontonycklar för att godkänna åtkomst via delad nyckel. Auktorisera via delad nyckel kräver att lagra dina lagringskontonycklar i ditt program, så Microsoft rekommenderar att du använder Azure AD i stället där det är möjligt. Program i produktion eller för att auktorisera åtkomst till Azure-tabeller och filer kan fortsätta att använda delad nyckel medan Azure AD-integrering är en förhandsversion.
- Använda signaturer för delad åtkomst för att ge kontrollerad behörighet till specifika dataobjekt för en viss tidsperiod.

Förutom att begränsa åtkomst via auktorisering, du kan också använda [brandväggar och virtuella nätverk](storage-network-security.md) att begränsa åtkomsten till lagringskontot baserat på regler.  Den här metoden kan du neka åtkomst till offentliga internet-trafik och att bevilja åtkomst till vissa specifika Azure-nätverk eller offentliga internet IP-adressintervall.

### <a name="storage-account-keys"></a>Lagringskontonycklar

Lagringskontonycklar är 512 bitar strängar som skapats av Azure som tillsammans med lagringskontonamn, kan användas för att få åtkomst till dataobjekt som lagras i lagringskontot.

Du kan till exempel läsa filer. Många av de här åtgärderna kan utföras via Azure-portalen eller med hjälp av ett av många program i Storage Explorer. Du kan också skriva kod för att använda REST API för att utföra dessa åtgärder.

Enligt beskrivningen i avsnittet på den [Management plan Security](#management-plane-security), åtkomsten till storage-nycklar för ett lagringskonto med Azure Resource Manager-modellen kan styras via rollbaserad åtkomstkontroll (RBAC).

### <a name="how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies"></a>Hur du delegerar åtkomst till objekt i ditt konto med signaturer för delad åtkomst och lagrade åtkomstprinciper

En signatur för delad åtkomst är en sträng som innehåller en säkerhetstoken som kan kopplas till en URI som gör det möjligt att delegera åtkomst till lagringsobjekt och ange begränsningar, till exempel behörigheter och datum-/ tidsintervall för åtkomst.

Du kan bevilja åtkomst till filer eller kataloger.

Du kan ge en kund en SAS-token som de kan använda för att ladda upp bilder till ett filsystem i Blob Storage och ge en web program behörighet att läsa dessa bilder. Det finns en inkapsling av problem i båda fallen – varje program kan få just den tillgång som de behöver för att kunna utföra sina uppgifter. Detta är möjligt genom att använda signaturer för delad åtkomst.

#### <a name="why-you-want-to-use-shared-access-signatures"></a>Varför du vill använda signaturer för delad åtkomst

Varför skulle du vill använda en SAS istället för att bara ge ut din lagringskontonyckel som är mycket enklare? Lämna ut din lagringskontonyckel är som att dela nycklarna för storage-kungariket. Den ger fullständig åtkomst. Någon kan använda dina nycklar och ladda upp sina hela musikbibliotek till ditt lagringskonto. De kan även ersätta dina filer med virusinfekterade versioner eller stjäla dina data. Ge bort obegränsad tillgång till ditt lagringskonto är något som inte bör noga.

Du kan ge bara de behörigheter som krävs för en begränsad tidsperiod på en klient med signaturer för delad åtkomst. Till exempel om någon överför en fil till ditt konto, ge du dem skrivåtkomst tillräckligt med tid för att överföra filen (beroende på storleken på filen, naturligtvis). Och om du ändrar dig kan du återkalla som har åtkomst till.

Dessutom kan du ange att begäranden som görs med hjälp av en SAS är begränsade till en viss IP-adress eller IP-adressintervall utanför Azure. Du kan också kräva att begäranden som görs med ett visst protokoll (HTTPS eller HTTP/HTTPS). Det innebär att om du bara vill tillåta HTTPS-trafik, du kan ange protokollet krävs till HTTPS endast och HTTP-trafik blockeras.

#### <a name="definition-of-a-shared-access-signature"></a>Definitionen av en signatur för delad åtkomst

En signatur för delad åtkomst är en uppsättning frågeparametrar som bifogats till den URL som pekar på resursen

som innehåller information om åtkomst tillåts och hur lång tid som åtkomst tillåts. Här är ett exempel. URI: N ger läsåtkomst till en blob i fem minuter. SAS-frågeparametrar måste vara URL-kodade, till exempel % 3A för kolon (:) eller % 20 för ett blanksteg.

```
http://mystorage.dfs.core.windows.net/myfilesystem/myfile.txt (URL to the file)
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

* En SAS på servicenivå kan användas för åtkomst till specifika resurser i ett lagringskonto. Några exempel på detta hämtar en lista över filer i ett filsystem eller hämta en fil.
* En SAS på kontonivå kan användas för åtkomst till allt som en tjänstnivå SAS kan användas för. Dessutom kan det ge alternativ till resurser som inte tillåts med en SAS på servicenivå, till exempel möjligheten att skapa filsystem.

#### <a name="creating-a-sas-uri"></a>Skapa en SAS-URI

1. Du kan skapa en URI för på begäran, definierar frågeparametrarna varje gång.

   Den här metoden är flexibel, men om du har en logisk uppsättning parametrar som liknar varje gång, med hjälp av en lagrad åtkomstprincip är en bättre uppfattning.
2. Du kan skapa en lagrad åtkomstprincip för en hela filsystem, filresurs, tabell eller kö. Du kan sedan använda detta som grund för SAS-URI som du skapar. Behörigheter till följd av lagrade åtkomstprinciper kan återkallas enkelt. Du kan ha upp till fem principer som definierats på varje filsystem, kö, tabell eller filresurs.

   Du kan till exempel skapa en lagrad åtkomstprincip som säger ”ger läsbehörighet” och eventuella andra inställningar som ska vara samma varje gång om du tänker har många användare kan läsa blobarna i en specifik filsystem. Du kan skapa en SAS-URI med inställningarna för åtkomstprincip lagras och ange förfallodatum datum/tid. Fördelen med detta är att du inte behöver ange alla frågeparametrar varje gång.

#### <a name="revocation"></a>Återkallade certifikat

Anta att dina SAS har komprometterats eller om du vill ändra den på grund av företagets säkerhets- eller efterlevnadskrav. Hur du för att återkalla åtkomst till en resurs med hjälp av den SAS? Det beror på hur du har skapat SAS-URI.

Om du använder ad hoc-URI: er, har du tre alternativ. Du kan utfärda SAS-token med principer för kort förfallodatum och vänta tills Signaturen på att gå ut. Du kan byta namn på eller ta bort resursen (förutsatt att token har begränsade till ett enda objekt). Du kan ändra lagringskontonycklarna. Det här sista alternativet kan ha en betydande inverkan, beroende på hur många tjänster använder det lagringskontot och inte är förmodligen något du vill göra utan några planera.

Om du använder en SAS som härletts från en lagrad åtkomstprincip, du kan ta bort åtkomst genom att återkalla åtkomstprincip lagras – du kan bara ändra den så att den har redan upphört att gälla eller du kan ta bort det helt och hållet. Detta träder i kraft omedelbart och upphäver varje SAS som skapats med hjälp av den lagrade åtkomstprincip. Uppdatera eller ta bort åtkomstprincip lagras kan påverkan personer åtkomst till det specifika filsystemet, filresurs, tabell eller kö via SAS, men om klienterna skrivs så att de begär en ny SAS när den gamla servern blir ogiltig, den här metoden fungerar bra.

Eftersom en SAS som härletts från en lagrad åtkomstprincip ger dig möjlighet att återkalla den SAS direkt, är det rutin att alltid använda lagrade åtkomstprinciper när det är möjligt.

#### <a name="resources"></a>Resurser

Mer detaljerad information om hur du använder signaturer för delad åtkomst och lagrade åtkomstprinciper, med exempel, finns i följande artiklar:

* Det här är referensartiklar.

  * [Tjänst-SAS](https://msdn.microsoft.com/library/dn140256.aspx)

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

## <a name="encryption-at-rest"></a>Kryptering i vila

### <a name="storage-service-encryption-sse"></a>Storage Service Encryption (SSE)

SSE är aktiverat för alla lagringskonton och kan inte inaktiveras. SSE krypterar automatiskt data när du skriver den till Azure Storage. När du läser data från Azure Storage kan dekrypteras av Azure Storage innan de returneras. SSE gör det möjligt för dig att skydda dina data utan att behöva ändra kod och lägga till kod för program.

Du kan använda Microsoft-hanterade nycklar eller dina egna anpassade nycklar. Microsoft genererar hanterade nycklar och hanterar sina säker lagring samt deras regelbundna rotation, som den definieras av intern Microsoft-princip. Mer information om hur du använder anpassade nycklar finns i [kryptering av lagringstjänst med Kundhanterade nycklar i Azure Key Vault](storage-service-encryption-customer-managed-keys.md).