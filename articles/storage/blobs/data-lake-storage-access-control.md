---
title: Översikt över åtkomst kontroll i Azure Data Lake Storage Gen2 | Microsoft Docs
description: Förstå hur åtkomst kontroll fungerar i Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 93c21656a768ae458572e0b4917412c8103b2f2d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80992223"
---
# <a name="access-control-in-azure-data-lake-storage-gen2"></a>Åtkomstkontroll i Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 implementerar en åtkomst kontroll modell som stöder både RBAC (Azure rollbaserad åtkomst kontroll) och POSIX-liknande åtkomst kontrol listor (ACL: er). I den här artikeln sammanfattas grunderna för åtkomst kontroll modellen för Data Lake Storage Gen2.

<a id="azure-role-based-access-control-rbac" />

## <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll

RBAC använder roll tilldelningar för att effektivt tillämpa uppsättningar av behörigheter för *säkerhets objekt*. Ett *säkerhets objekt* är ett objekt som representerar en användare, grupp, tjänstens huvud namn eller en hanterad identitet som definieras i Azure Active Directory (AD) som begär åtkomst till Azure-resurser.

Normalt är dessa Azure-resurser begränsade till resurser på den översta nivån (till exempel: Azure Storage konton). Om Azure Storage, och därför Azure Data Lake Storage Gen2, har den här mekanismen utökats till behållarens (fil system) resurs.

Information om hur du tilldelar roller till säkerhets objekt i omfånget för ditt lagrings konto finns i [bevilja åtkomst till Azure blob och Queue data med RBAC i Azure Portal](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> En gäst användare kan inte skapa en roll tilldelning.

### <a name="the-impact-of-role-assignments-on-file-and-directory-level-access-control-lists"></a>Effekten av roll tilldelningar på fil-och katalog nivå listor för åtkomst kontroll

När du använder RBAC-roll tilldelningar är en kraftfull mekanism för att kontrol lera åtkomst behörighet, men det är en mycket kornig mekanism i förhållande till ACL: er. Den minsta granularitet för RBAC är på behållar nivån och detta kommer att utvärderas med högre prioritet än ACL: er. Om du tilldelar en roll till ett säkerhets objekt i omfånget för en behållare, är det därför säkerhets objektets autentiseringsnivå som associeras med rollen för alla kataloger och filer i behållaren, oavsett ACL-tilldelningar.

När ett säkerhets objekt beviljas RBAC-databehörighet via en [inbyggd roll](https://docs.microsoft.com/azure/storage/common/storage-auth-aad?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#built-in-rbac-roles-for-blobs-and-queues)eller via en anpassad roll, utvärderas dessa behörigheter först när en begäran auktoriseras. Om den begärda åtgärden auktoriseras av säkerhets objektets RBAC-tilldelningar, löses auktoriseringen omedelbart och inga ytterligare ACL-kontroller utförs. Alternativt, om säkerhetsobjektet inte har en RBAC-tilldelning eller om begärans åtgärd inte matchar den tilldelade behörigheten, utförs ACL-kontroller för att avgöra om säkerhets objekt har behörighet att utföra den begärda åtgärden.

> [!NOTE]
> Om säkerhets objekt har tilldelats den inbyggda roll tilldelningen Storage BLOB data-ägare, betraktas säkerhetsobjektet som en *superanvändare* och beviljas fullständig åtkomst till alla relevanta åtgärder, inklusive att ange ägare till en katalog eller fil, samt ACL: er för kataloger och filer som de inte är ägare till. Super-User Access är det enda godkända sättet att ändra ägaren till en resurs.

## <a name="shared-key-and-shared-access-signature-sas-authentication"></a>Autentisering med delad nyckel och signatur för delad åtkomst (SAS)

Azure Data Lake Storage Gen2 stöder delade nycklar och SAS-metoder för autentisering. En egenskap hos dessa autentiseringsmetoder är att ingen identitet är kopplad till anroparen och därför kan inte behörigheten för säkerhets objekts behörighet utföras.

I händelse av en delad nyckel får anroparen en effektiv åtkomst till "Super-User", vilket innebär fullständig åtkomst till alla åtgärder på alla resurser, inklusive inställning av ägare och ändring av ACL: er.

SAS-token inkluderar tillåtna behörigheter som en del av token. Behörigheterna som ingår i SAS-token tillämpas effektivt på alla auktoriseringsbeslut, men inga ytterligare ACL-kontroller utförs.

## <a name="access-control-lists-on-files-and-directories"></a>Åtkomst kontrol listor på filer och kataloger

Du kan associera ett säkerhets objekt med en åtkomst nivå för filer och kataloger. Dessa associationer samlas in i en *åtkomst kontrol lista (ACL)*. Varje fil och katalog i ditt lagrings konto har en åtkomst kontrol lista.

> [!NOTE]
> ACL: er gäller endast för säkerhets objekt i samma klient organisation. 

Om du har tilldelat en roll till ett säkerhets objekt på lagrings konto nivå, kan du använda åtkomst kontrol listor för att ge säkerhetsobjektet utökad åtkomst till särskilda filer och kataloger.

Du kan inte använda åtkomst kontrol listor för att ge en åtkomst nivå som är lägre än en nivå som har beviljats av en roll tilldelning. Om du till exempel tilldelar rollen [Storage BLOB data Contributor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) till ett säkerhets objekt kan du inte använda åtkomst kontrol listor för att förhindra att säkerhets objekt skriver till en katalog.


### <a name="set-file-and-directory-level-permissions-by-using-access-control-lists"></a>Ange behörigheter för fil-och katalog nivå med hjälp av åtkomst kontrol listor

Om du vill ange behörigheter för fil-och katalog nivå kan du läsa följande artiklar:

|||
|--------|-----------|
|Azure Lagringsutforskaren |[Använd Azure Storage Explorer för att hantera kataloger, filer och ACL: er i Azure Data Lake Storage Gen2](data-lake-storage-explorer.md#managing-access)|
|.NET |[Använd .NET för att hantera kataloger, filer och ACL: er i Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-dotnet.md)|
|Java|[Använd Java för att hantera kataloger, filer och ACL: er i Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-java.md)|
|Python|[Använd python för att hantera kataloger, filer och ACL: er i Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-python.md)|
|PowerShell|[Använd PowerShell för att hantera kataloger, filer och ACL: er i Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-powershell.md)|
|Azure CLI|[Använd Azure CLI för att hantera kataloger, filer och ACL: er i Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-cli.md)|
|REST-API |[Sökväg – uppdatera](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/path/update)|

> [!IMPORTANT]
> Om säkerhetsobjektet är ett huvud namn för *tjänsten* är det viktigt att använda objekt-ID: t för tjänstens huvud namn och inte objekt-ID: t för den relaterade appens registrering. För att hämta objekt-ID: t för tjänstens huvud namn öppnar du Azure CLI och använder sedan `az ad sp show --id <Your App ID> --query objectId`det här kommandot:. Se till att ersätta `<Your App ID>` plats hållaren med app-ID: t för din app Registration.

### <a name="types-of-access-control-lists"></a>Typer av åtkomst kontrol listor

Det finns två typer av åtkomst kontrol listor: *åtkomst-ACL* : er och *standard-ACL: er*.

Åtkomst-ACL:er kontrollerar åtkomst till ett objekt. Filer och kataloger har båda åtkomst-ACL:er.

Standard-ACL: er är mallar för ACL: er som är kopplade till en katalog som fastställer åtkomst-ACL: er för underordnade objekt som skapas under den katalogen. Filer har inte standard-ACL:er.

Både åtkomst-ACL: er och standard-ACL: er har samma struktur.

> [!NOTE]
> Att ändra standard-ACL: en på en överordnad påverkar inte åtkomst-ACL: en eller standard-ACL för underordnade objekt som redan finns.

### <a name="levels-of-permission"></a>Behörighets nivåer

Behörigheterna för ett behållar objekt är **läsa**, **skriva**och **köra**och de kan användas på filer och kataloger som visas i följande tabell:

|            |    Fil     |   Katalog |
|------------|-------------|----------|
| **Läsa (R)** | Kan läsa innehållet i en fil | Kräver **läsa** och **köra** för att visa innehållet i katalogen |
| **Skriva (W)** | Kan skriva eller lägg till i en fil | Kräver **skriva** och **köra** för att skapa underordnade objekt i en katalog |
| **Köra (X)** | Betyder inte något i samband med Data Lake Storage Gen2 | Krävs för att bläddra bland de underordnade objekten i en katalog |

> [!NOTE]
> Om du beviljar behörigheter genom att endast använda ACL: er (ingen RBAC), så måste du ge säkerhets objektets behörighet att **köra** behörigheter till behållaren och till varje mapp i hierarkin för mappar som leder till filen.

#### <a name="short-forms-for-permissions"></a>Kortformat för behörigheter

**RWX**används för att ange **läsa + skriva + köra**. Ett numeriskt mer komprimerat format finns där **Läsa = 4**, **skriva = 2** och **Köra = 1** och deras summa representerar behörigheterna. Här följer några exempel.

| Numeriskt format | Kortformat |      Vad det innebär     |
|--------------|------------|------------------------|
| 7            | `RWX`        | Läsa + skriva + köra |
| 5            | `R-X`        | Läsa + köra         |
| 4            | `R--`        | Läsa                   |
| 0            | `---`        | Inga behörigheter         |

#### <a name="permissions-inheritance"></a>Arv av behörigheter

I POSIX-format modellen som används av Data Lake Storage Gen2 lagras behörigheter för ett objekt i själva objektet. Med andra ord kan inte behörigheter för ett objekt ärvas från de överordnade objekten om behörigheterna anges efter att det underordnade objektet redan har skapats. Behörigheter ärvs bara om standard behörigheter har angetts för de överordnade objekten innan de underordnade objekten har skapats.

### <a name="common-scenarios-related-to-permissions"></a>Vanliga scenarier som rör behörigheter

I följande tabell visas några vanliga scenarier som hjälper dig att förstå vilka behörigheter som krävs för att utföra vissa åtgärder på ett lagrings konto.

|    Åtgärd             |    /    | Oregon | Portland | Data. txt     |
|--------------------------|---------|----------|-----------|--------------|
| Läsa data. txt            |   `--X`   |   `--X`    |  `--X`      | `R--`          |
| Lägg till i data. txt       |   `--X`   |   `--X`    |  `--X`      | `RW-`          |
| Ta bort data. txt          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Skapa data. txt          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Lista                   |   `R-X`   |   `---`    |  `---`      | `---`          |
| Visa lista/Oregon/           |   `--X`   |   `R-X`    |  `---`      | `---`          |
| Visa lista/Oregon/Portland/  |   `--X`   |   `--X`    |  `R-X`      | `---`          |

> [!NOTE]
> Skriv behörighet för filen krävs inte för att ta bort den, så länge som de föregående två villkoren är sanna.

### <a name="users-and-identities"></a>Användare och identiteter

Varje fil och katalog har distinkta behörigheter för dessa identiteter:

- Ägande användare
- Ägande grupp
- Namngivna användare
- Namngivna grupper
- Namngivna tjänstens huvud namn
- Namngivna hanterade identiteter
- Alla andra användare

Identiteten för användare och grupper är Azure Active Directory (Azure AD)-identiteter. Så om inget annat anges kan en *användare*, i kontexten för data Lake Storage Gen2, referera till en Azure AD-användare, tjänstens huvud namn, en hanterad identitet eller säkerhets grupp.

#### <a name="the-owning-user"></a>Ägande användare

Användaren som skapade objektet är automatiskt ägande användare för objektet. En ägande användare kan:

* Ändra behörigheterna för en fil som ägs.
* Ändra ägande grupp för en fil som ägs, så länge den ägande användaren också är medlem i målgruppen.

> [!NOTE]
> Den ägande användaren *kan inte* ändra ägande användare av en fil eller katalog. Endast Super-användare kan ändra ägande användare av en fil eller katalog.

#### <a name="the-owning-group"></a>Ägande grupp

I POSIX-ACL: erna är alla användare kopplade till en *primär grupp*. Användaren "Alice" kan t. ex. tillhöra gruppen "ekonomi". Alice kan också tillhöra flera grupper, men en grupp anges alltid som sin primära grupp. När Alice skapar en fil i POSIX ställs den ägande gruppen för filen in som hennes primära grupp, som i det här fallet är "Ekonomi". Den ägande gruppen fungerar annars ungefär som tilldelade behörigheter för andra användare/grupper.

##### <a name="assigning-the-owning-group-for-a-new-file-or-directory"></a>Tilldela ägande grupp för en ny fil eller katalog

* **Fall 1**: rot katalogen "/". Den här katalogen skapas när en Data Lake Storage Gen2-behållare skapas. I det här fallet anges den ägande gruppen till den användare som skapade behållaren om den gjordes med OAuth. Om behållaren har skapats med hjälp av delad nyckel, en konto säkerhets Association eller en tjänst-SAS, är ägaren och ägande gruppen inställt på **$superuser**.
* **Fall 2** (alla andra fall): när ett nytt objekt skapas, kopieras den ägande gruppen från den överordnade katalogen.

##### <a name="changing-the-owning-group"></a>Ändra den ägande gruppen

Den ägande gruppen kan ändras av:
* Alla superanvändare.
* Den ägande användaren, om den ägande användaren också är medlem i målgruppen.

> [!NOTE]
> Den ägande gruppen kan inte ändra ACL: er för en fil eller katalog.  Även om den ägande gruppen har angetts till den användare som skapade kontot i fallet för rot katalogen, **fall 1** ovan, är ett enskilt användar konto inte giltigt för att ge behörighet via den ägande gruppen. Du kan tilldela den här behörigheten till en giltig användargrupp, om det är lämpligt.

### <a name="access-check-algorithm"></a>Algoritm för åtkomstkontroll

Följande pseudocode representerar algoritmen för åtkomst kontroll för lagrings konton.

```console
def access_check( user, desired_perms, path ) : 
  # access_check returns true if user has the desired permissions on the path, false otherwise
  # user is the identity that wants to perform an operation on path
  # desired_perms is a simple integer with values from 0 to 7 ( R=4, W=2, X=1). User desires these permissions
  # path is the file or directory
  # Note: the "sticky bit" isn't illustrated in this algorithm
  
# Handle super users.
  if (is_superuser(user)) :
    return True

# Handle the owning user. Note that mask isn't used.
entry = get_acl_entry( path, OWNER )
if (user == entry.identity)
    return ( (desired_perms & entry.permissions) == desired_perms )

# Handle the named users. Note that mask IS used.
entries = get_acl_entries( path, NAMED_USER )
for entry in entries:
    if (user == entry.identity ) :
        mask = get_mask( path )
        return ( (desired_perms & entry.permissions & mask) == desired_perms)

# Handle named groups and owning group
member_count = 0
perms = 0
entries = get_acl_entries( path, NAMED_GROUP | OWNING_GROUP )
for entry in entries:
if (user_is_member_of_group(user, entry.identity)) :
    member_count += 1
    perms | =  entry.permissions
if (member_count>0) :
return ((desired_perms & perms & mask ) == desired_perms)

# Handle other
perms = get_perms_for_other(path)
mask = get_mask( path )
return ( (desired_perms & perms & mask ) == desired_perms)
```

#### <a name="the-mask"></a>Masken

Som illustreras i algoritmen för åtkomst kontroll begränsar masken åtkomsten för namngivna användare, ägande grupp och namngivna grupper.  

> [!NOTE]
> För en ny Data Lake Storage Gen2-behållare är masken för åtkomst-ACL: en för rot katalogen ("/") standardvärdet 750 för kataloger och 640 för filer. Filerna tar inte emot X-biten eftersom det är irrelevant för filer i ett system för endast lagring.
>
> Masken kan anges per anrop. Detta gör att olika användnings system, till exempel kluster, har olika effektiva masker för deras fil åtgärder. Om en mask anges för en viss begäran, åsidosätter den fullständigt standard masken.

#### <a name="the-sticky-bit"></a>Sticky bit

Trögheten bit är en mer avancerad funktion i en POSIX-behållare. I samband med Data Lake Storage Gen2 är det osannolikt att det behövs. I sammanfattning, om trögheten är aktive rad i en katalog, kan ett underordnat objekt bara tas bort eller byta namn av det underordnade objektets ägande användare.

Trögheten bit visas inte i Azure Portal.

### <a name="default-permissions-on-new-files-and-directories"></a>Standard behörigheter för nya filer och kataloger

När en ny fil eller katalog skapas under en befintlig katalog, fastställer standard-ACL: en för den överordnade katalogen:

- En underordnad katalogs standard-ACL och åtkomst-ACL.
- En underordnad fils åtkomst-ACL (filer har inte en standard-ACL).

#### <a name="umask"></a>umask

När du skapar en fil eller katalog, används umask för att ändra hur standard-ACL: erna anges för det underordnade objektet. umask är ett 9-bitars värde i överordnade kataloger som innehåller ett RWX-värde för **ägande användare**, **ägande grupp**och **annat**.

Umask för Azure Data Lake Storage Gen2 ett konstant värde som är inställt på 007. Det här värdet översätts till:

| umask-komponent     | Numeriskt format | Kortformat | Betydelse |
|---------------------|--------------|------------|---------|
| umask. owning_user   |    0         |   `---`      | För ägande användare kopierar du den överordnade standard-ACL: en till barnets åtkomst-ACL | 
| umask. owning_group  |    0         |   `---`      | För ägande grupp kopierar du den överordnade standard-ACL: en till barnets åtkomst-ACL | 
| umask. other         |    7         |   `RWX`      | För övrigt tar du bort alla behörigheter för barnets åtkomst-ACL |

Det umask-värde som används av Azure Data Lake Storage Gen2 effektivt innebär att värdet för **other** aldrig skickas som standard på nya underordnade, oavsett vad standard-ACL: en visar. 

Följande pseudocode visar hur umask används när du skapar ACL: er för ett underordnat objekt.

```console
def set_default_acls_for_new_child(parent, child):
    child.acls = []
    for entry in parent.acls :
        new_entry = None
        if (entry.type == OWNING_USER) :
            new_entry = entry.clone(perms = entry.perms & (~umask.owning_user))
        elif (entry.type == OWNING_GROUP) :
            new_entry = entry.clone(perms = entry.perms & (~umask.owning_group))
        elif (entry.type == OTHER) :
            new_entry = entry.clone(perms = entry.perms & (~umask.other))
        else :
            new_entry = entry.clone(perms = entry.perms )
        child_acls.add( new_entry )
```

## <a name="common-questions-about-acls-in-data-lake-storage-gen2"></a>Vanliga frågor om ACL: er i Data Lake Storage Gen2

### <a name="do-i-have-to-enable-support-for-acls"></a>Måste jag aktivera stöd för ACL:er?

Nej. Åtkomst kontroll via ACL: er aktive ras för ett lagrings konto så länge funktionen för hierarkiskt namn område (HNS) är aktive rad.

Om HNS är inaktiverat används fortfarande Azure RBAC-auktoriseringsregler.

### <a name="what-is-the-best-way-to-apply-acls"></a>Vad är det bästa sättet att tillämpa ACL: er?

Använd alltid Azure AD-säkerhetsgrupper som tilldelat objekt i ACL: er. Motstå möjligheten att direkt tilldela enskilda användare eller tjänst huvud namn. Med den här strukturen kan du lägga till och ta bort användare eller tjänstens huvud namn utan att behöva tillämpa ACL: er på en hel katalog struktur. ) I stället behöver du bara lägga till eller ta bort dem från rätt Azure AD-säkerhetsgrupp. Tänk på att ACL: er inte ärvs och att återanvända ACL: er måste uppdatera ACL: en på alla filer och under kataloger. 

### <a name="which-permissions-are-required-to-recursively-delete-a-directory-and-its-contents"></a>Vilka behörigheter krävs för att rekursivt ta bort en katalog och dess innehåll?

- Anroparen har behörigheten "Super-User",

Eller

- Den överordnade katalogen måste ha Skriv-och körnings behörighet.
- Katalogen som ska tas bort och alla kataloger i den, kräver Läs-och skriv + kör-behörigheter.

> [!NOTE]
> Du behöver inte Skriv behörighet för att ta bort filer i kataloger. Dessutom kan rot katalogen "/" aldrig tas bort.

### <a name="who-is-the-owner-of-a-file-or-directory"></a>Vem är ägaren till en fil eller katalog?

Skaparen av en fil eller katalog blir ägare. Om det gäller rot katalogen är detta identiteten för den användare som skapade behållaren.

### <a name="which-group-is-set-as-the-owning-group-of-a-file-or-directory-at-creation"></a>Vilken grupp anges som den ägande gruppen för en fil eller katalog när den skapas?

Den ägande gruppen kopieras från den ägande gruppen i den överordnade katalogen under vilken den nya filen eller katalogen skapas.

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>Jag är ägande användare av en fil men jag har inte de RWX-behörigheter jag behöver. Vad gör jag nu?

Den ägande användaren kan lätt ändra behörigheterna för filen för att ge sig själva de RWX-behörigheter de behöver.

### <a name="why-do-i-sometimes-see-guids-in-acls"></a>Varför visas ibland GUID i ACL: er?

Ett GUID visas om posten representerar en användare och den användaren inte finns längre i Azure AD. Detta inträffar vanligtvis när användaren har lämnat företaget eller om kontot har tagits bort i Azure AD. Dessutom har tjänstens huvud namn och säkerhets grupper inget UPN (User Principal Name) för att identifiera dem och så de representeras av deras OID-attribut (en GUID).

### <a name="how-do-i-set-acls-correctly-for-a-service-principal"></a>Hur gör jag för att ange ACL: er korrekt för ett huvud namn för tjänsten?

När du definierar ACL: er för tjänstens huvud namn är det viktigt att använda objekt-ID: t (OID) för *tjänstens huvud namn* för den app-registrering som du skapade. Det är viktigt att notera att registrerade appar har ett separat tjänst objekt i den aktuella Azure AD-klienten. Registrerade appar har ett OID som är synligt i Azure Portal, men *tjänstens huvud namn* har en annan (annan) OID.

Om du vill hämta OID för det tjänst huvud namn som motsvarar en registrerad app kan du använda `az ad sp show` kommandot. Ange program-ID som parameter. Här är ett exempel på hur du hämtar OID för tjänstens huvud namn som motsvarar en app-registrering med app-ID = 18218b12-1895-43e9-ad80-6e8fc1ea88ce. Kör följande kommando i Azure CLI:

```azurecli
az ad sp show --id 18218b12-1895-43e9-ad80-6e8fc1ea88ce --query objectId
```

OID kommer att visas.

När du har rätt OID för tjänstens huvud namn går du till sidan Storage Explorer **Hantera åtkomst** för att lägga till OID och tilldela lämpliga behörigheter för OID. Se till att du väljer **Spara**.

### <a name="does-data-lake-storage-gen2-support-inheritance-of-acls"></a>Stöder Data Lake Storage Gen2 arv av ACL: er?

Azure RBAC-tilldelningar ärver. Tilldelnings flödet från prenumerations-, resurs grupps-och lagrings konto resurser till behållar resursen.

ACL: er ärver inte. Standard-ACL: er kan dock användas för att ange ACL: er för underordnade under kataloger och filer som skapats under den överordnade katalogen. 

### <a name="where-can-i-learn-more-about-posix-access-control-model"></a>Var hittar jag mer information om POSIX-modellen för åtkomstkontroll?

* [POSIX-åtkomstkontrollistor på Linux](https://www.linux.com/news/posix-acls-linux)
* [Guide för HDFS-behörighet](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)
* [Vanliga frågor och svar om POSIX](https://www.opengroup.org/austin/papers/posix_faq.html)
* [POSIX 1003.1 2008](https://standards.ieee.org/findstds/standard/1003.1-2008.html)
* [POSIX 1003.1 2013](https://pubs.opengroup.org/onlinepubs/9699919799.2013edition/)
* [POSIX 1003.1 2016](https://pubs.opengroup.org/onlinepubs/9699919799.2016edition/)
* [POSIX ACL på Ubuntu](https://help.ubuntu.com/community/FilePermissionsACLs)
* [ACL med åtkomstkontrollistor på Linux](https://bencane.com/2012/05/27/acl-using-access-control-lists-on-linux/)

## <a name="see-also"></a>Se även

* [Översikt över Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md)
