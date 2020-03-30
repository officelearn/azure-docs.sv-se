---
title: Översikt över åtkomstkontroll i Azure Data Lake Storage Gen2 | Microsoft-dokument
description: Förstå hur åtkomstkontroll fungerar i Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 192e46fd7f86b6053eaf658fa65e3c6cdfa3a4e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528616"
---
# <a name="access-control-in-azure-data-lake-storage-gen2"></a>Åtkomstkontroll i Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 implements an access control model that supports both Azure role-based access control (RBAC) and POSIX-like access control lists (ACLs). Den här artikeln sammanfattar grunderna i åtkomstkontrollmodellen för Data Lake Storage Gen2.

<a id="azure-role-based-access-control-rbac" />

## <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll

RBAC använder rolltilldelningar för att effektivt tillämpa behörighetsgrupper på *säkerhetsobjekt*. Ett *säkerhetsobjekt* är ett objekt som representerar ett användar-, grupp-, tjänsthuvudnamn eller hanterad identitet som definieras i Azure Active Directory (AD) som begär åtkomst till Azure-resurser.

Vanligtvis är dessa Azure-resurser begränsade till resurser på den högsta nivån (till exempel Azure Storage-konton). När det gäller Azure Storage och azure data lake storage gen2, har den här mekanismen utökats till behållarresursen (filsystem).

Mer information om hur du tilldelar roller till säkerhetsobjekt i omfattningen av ditt lagringskonto finns i [Bevilja åtkomst till Azure-blob och ködata med RBAC i Azure-portalen](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> En gästanvändare kan inte skapa en rolltilldelning.

### <a name="the-impact-of-role-assignments-on-file-and-directory-level-access-control-lists"></a>Rolltilldelningarnas inverkan på åtkomstkontrolllistor på fil- och katalognivå

När du använder RBAC-rolltilldelningar är en kraftfull mekanism för att styra åtkomstbehörigheter, är det en mycket grovkornig mekanism i förhållande till ACL: er. Den minsta granulariteten för RBAC är på behållarnivå och detta kommer att utvärderas med högre prioritet än ACL. Om du tilldelar en roll till ett säkerhetsobjekt i en behållares omfattning har säkerhetsobjektet därför auktoriseringsnivån som är associerad med den rollen för ALLA kataloger och filer i behållaren, oavsett ACL-tilldelningar.

När ett säkerhetsobjekt beviljas RBAC-databehörigheter via en [inbyggd roll](https://docs.microsoft.com/azure/storage/common/storage-auth-aad?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#built-in-rbac-roles-for-blobs-and-queues)eller via en anpassad roll utvärderas dessa behörigheter först vid auktorisering av en begäran. Om den begärda åtgärden godkänns av säkerhetsobjektets RBAC-tilldelningar löses auktoriseringen omedelbart och inga ytterligare ACL-kontroller utförs. Alternativt, om säkerhetsobjektet inte har en RBAC-tilldelning, eller om begärans åtgärd inte matchar den tilldelade behörigheten, utförs ACL-kontroller för att avgöra om säkerhetsobjektet har behörighet att utföra den begärda åtgärden.

> [!NOTE]
> Om säkerhetsobjektet har tilldelats den inbyggda rolltilldelningen för lagringsblobbdatas ägare betraktas säkerhetsobjektet som en *superanvändare* och beviljas fullständig åtkomst till alla muterande åtgärder, inklusive att ange ägaren till en katalog eller fil samt ACL:er för kataloger och filer som de inte är ägare till. Superanvändaråtkomst är det enda behöriga sättet att ändra ägaren till en resurs.

## <a name="shared-key-and-shared-access-signature-sas-authentication"></a>SAS-autentisering (Shared Key and Shared Access Signature)

Azure Data Lake Storage Gen2 stöder shared key- och SAS-metoder för autentisering. En egenskap hos dessa autentiseringsmetoder är att ingen identitet är associerad med anroparen och därför kan inte behörighetsbaserad auktorisering för säkerhetstillstånd utföras.

När det gäller delad nyckel får den som ringer effektivt "superanvändaråtkomst", vilket innebär full åtkomst till alla åtgärder på alla resurser, inklusive inställning av ägare och ändring av ACL:er.

SAS-token innehåller tillåtna behörigheter som en del av token. Behörigheterna som ingår i SAS-token tillämpas effektivt på alla auktoriseringsbeslut, men inga ytterligare ACL-kontroller utförs.

## <a name="access-control-lists-on-files-and-directories"></a>Komma åt kontrolllistor för filer och kataloger

Du kan associera ett säkerhetsobjekt med en åtkomstnivå för filer och kataloger. Dessa associationer fångas in i en *åtkomstkontrollista (ACL)*. Varje fil och katalog i ditt lagringskonto har en åtkomstkontrolllista.

> [!NOTE]
> ACL:er gäller endast för säkerhetsobjekt i samma klient. Du kan inte associera en gästanvändare med en åtkomstnivå.  

Om du har tilldelat en roll till ett säkerhetsobjekt på lagringskontonivå kan du använda åtkomstkontrollistor för att bevilja säkerhetsobjektet förhöjd åtkomst till specifika filer och kataloger.

Du kan inte använda åtkomstkontrollistor för att tillhandahålla en åtkomstnivå som är lägre än en nivå som beviljas av en rolltilldelning. Om du till exempel tilldelar rollen [Storage Blob Data Contributor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) till ett säkerhetsobjekt kan du inte använda åtkomstkontrollistor för att förhindra att säkerhetsobjektet skriver till en katalog.


### <a name="set-file-and-directory-level-permissions-by-using-access-control-lists"></a>Ange behörigheter på fil- och katalognivå med hjälp av åtkomstkontrollistor

Information om hur du anger behörigheter på fil- och katalognivå finns i någon av följande artiklar:

|||
|--------|-----------|
|Azure Lagringsutforskaren |[Använda Azure Storage Explorer för att hantera kataloger, filer och ACL:er i Azure Data Lake Storage Gen2](data-lake-storage-explorer.md#managing-access)|
|.NET |[Använda .NET för att hantera kataloger, filer och ACL:er i Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-dotnet.md)|
|Java|[Använda Java för att hantera kataloger, filer och ACL:er i Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-java.md)|
|Python|[Använda Python för att hantera kataloger, filer och ACL:er i Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-python.md)|
|PowerShell|[Använda PowerShell för att hantera kataloger, filer och ACL:er i Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-powershell.md)|
|Azure CLI|[Använda Azure CLI för att hantera kataloger, filer och ACL:er i Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-cli.md)|
|REST API |[Sökväg - Uppdatering](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/path/update)|

> [!IMPORTANT]
> Om säkerhetsobjektet är ett *tjänsthuvudnamn* är det viktigt att använda objekt-ID:t för tjänstens huvudnamn och inte objekt-ID för den relaterade appregistreringen. Om du vill hämta objekt-ID:et för tjänstens `az ad sp show --id <Your App ID> --query objectId`huvudnamn öppnar du Azure CLI och använder sedan det här kommandot: . se till att `<Your App ID>` ersätta platshållaren med app-ID för din appregistrering.

### <a name="types-of-access-control-lists"></a>Typer av åtkomstkontrolllistor

Det finns två typer av åtkomstkontrollistor: *åtkomstkontrollistor* och *standard-ACL:er*.

Åtkomst-ACL:er kontrollerar åtkomst till ett objekt. Filer och kataloger har båda åtkomst-ACL:er.

Standard-ACL:er är mallar för ACL:er som är associerade med en katalog som bestämmer åtkomstkontrollistorna för underordnade objekt som skapas under den katalogen. Filer har inte standard-ACL:er.

Både åtkomst-ÅTKOMSTkontrollistor och standard-ACL:er har samma struktur.

> [!NOTE]
> Om du ändrar standardförkortningsförordnarna för en överordnad påverkas inte åtkomsten till åtkomstkontrollistan eller standard-åtkomstkontrollistan för underordnade objekt som redan finns.

### <a name="levels-of-permission"></a>Behörighetsnivåer

Behörigheterna för ett behållarobjekt är **Läs,** **Skriv**och **Kör**, och de kan användas på filer och kataloger som visas i följande tabell:

|            |    Fil     |   Katalog |
|------------|-------------|----------|
| **Läsa (R)** | Kan läsa innehållet i en fil | Kräver **läsning** **och körning** för att lista innehållet i katalogen |
| **Skriva (W)** | Kan skriva eller lägg till i en fil | Kräver **skriv och** **kör** för att skapa underordnade objekt i en katalog |
| **Köra (X)** | Betyder inte något i samband med Data Lake Storage Gen2 | Krävs för att gå igenom underordnade objekt i en katalog |

> [!NOTE]
> Om du beviljar behörigheter med endast ACL:er (ingen RBAC) måste du ge säkerhetsobjektet läs- **Execute** eller skrivbehörighet till en fil, ge säkerhetsobjektet Körbehörighet till behållaren och till varje mapp i hierarkin av mappar som leder till filen.

#### <a name="short-forms-for-permissions"></a>Kortformat för behörigheter

**RWX**används för att ange **läsa + skriva + köra**. Ett numeriskt mer komprimerat format finns där **Läsa = 4**, **skriva = 2** och **Köra = 1** och deras summa representerar behörigheterna. Här följer några exempel.

| Numeriskt format | Kortformat |      Vad det innebär     |
|--------------|------------|------------------------|
| 7            | `RWX`        | Läsa + skriva + köra |
| 5            | `R-X`        | Läsa + köra         |
| 4            | `R--`        | Läsa                   |
| 0            | `---`        | Inga behörigheter         |

#### <a name="permissions-inheritance"></a>Arv av behörigheter

I den POSIX-formatmodell som används av Data Lake Storage Gen2 lagras behörigheter för ett objekt på själva objektet. Med andra ord kan behörigheter för ett objekt inte ärvas från de överordnade objekten om behörigheterna har angetts när det underordnade objektet redan har skapats. Behörigheter ärvs bara om standardbehörigheter har angetts för de överordnade objekten innan de underordnade objekten har skapats.

### <a name="common-scenarios-related-to-permissions"></a>Vanliga scenarier som rör behörigheter

I följande tabell visas några vanliga scenarier som hjälper dig att förstå vilka behörigheter som behövs för att utföra vissa åtgärder på ett lagringskonto.

|    Åtgärd             |    /    | Oregon/ | Portland/ | Data.txt     |
|--------------------------|---------|----------|-----------|--------------|
| Läs Data.txt            |   `--X`   |   `--X`    |  `--X`      | `R--`          |
| Lägg till i Data.txt       |   `--X`   |   `--X`    |  `--X`      | `RW-`          |
| Ta bort data.txt          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Skapa data.txt          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Lista /                   |   `R-X`   |   `---`    |  `---`      | `---`          |
| Lista /Oregon/           |   `--X`   |   `R-X`    |  `---`      | `---`          |
| Lista /Oregon/Portland/  |   `--X`   |   `--X`    |  `R-X`      | `---`          |

> [!NOTE]
> Skrivbehörigheter för filen krävs inte för att ta bort den, så länge de två föregående villkoren är uppfyllda.

### <a name="users-and-identities"></a>Användare och identiteter

Varje fil och katalog har olika behörigheter för dessa identiteter:

- Ägande användare
- Ägande grupp
- Namngivna användare
- Namngivna grupper
- Namngivna tjänsthuvudnamn
- Namngivna hanterade identiteter
- Alla andra användare

Identiteten för användare och grupper är Azure Active Directory (Azure AD)-identiteter. Så om inget annat anges kan en *användare*, i samband med Data Lake Storage Gen2, referera till en Azure AD-användare, tjänsthuvudnamn, hanterad identitet eller säkerhetsgrupp.

#### <a name="the-owning-user"></a>Ägande användare

Användaren som skapade objektet är automatiskt ägande användare för objektet. En ägande användare kan:

* Ändra behörigheterna för en fil som ägs.
* Ändra ägande grupp för en fil som ägs, så länge den ägande användaren också är medlem i målgruppen.

> [!NOTE]
> Den ägande användaren *kan inte* ändra den ägande användaren av en fil eller katalog. Endast superanvändare kan ändra den ägande användaren av en fil eller katalog.

#### <a name="the-owning-group"></a>Ägande grupp

I POSIX-ACL:erna associeras varje användare med en *primär grupp*. Användaren "Alice" kan till exempel tillhöra gruppen "ekonomi". Alice kan också tillhöra flera grupper, men en grupp betecknas alltid som sin primära grupp. När Alice skapar en fil i POSIX ställs den ägande gruppen för filen in som hennes primära grupp, som i det här fallet är "Ekonomi". Den ägande gruppen fungerar annars ungefär som tilldelade behörigheter för andra användare/grupper.

##### <a name="assigning-the-owning-group-for-a-new-file-or-directory"></a>Tilldela den ägande gruppen för en ny fil eller katalog

* **Fall 1**: Rotkatalogen "/". Den här katalogen skapas när en Data Lake Storage Gen2-behållare skapas. I det här fallet är den ägande gruppen inställd på den användare som skapade behållaren om den gjordes med OAuth. Om behållaren skapas med delad nyckel, ett konto SAS eller en Service SAS är ägaren och ägargruppen inställda på **$superuser**.
* **Fall 2** (Vartannat ärende): När ett nytt objekt skapas kopieras ägargruppen från den överordnade katalogen.

##### <a name="changing-the-owning-group"></a>Ändra ägandegruppen

Den ägande gruppen kan ändras av:
* Alla superanvändare.
* Den ägande användaren, om den ägande användaren också är medlem i målgruppen.

> [!NOTE]
> Den ägande gruppen kan inte ändra ACL:erna för en fil eller katalog.  Medan den ägande gruppen är inställd på den användare som skapade kontot i fallet med rotkatalogen, **mål 1** ovan, är ett enda användarkonto inte giltigt för att ge behörighet via den ägande gruppen. Du kan tilldela den här behörigheten till en giltig användargrupp, om det är lämpligt.

### <a name="access-check-algorithm"></a>Algoritm för åtkomstkontroll

Följande pseudokod representerar algoritmen för åtkomstkontroll för lagringskonton.

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

Som illustreras i algoritmen för åtkomstkontroll begränsar masken åtkomst för namngivna användare, den ägande gruppen och namngivna grupper.  

> [!NOTE]
> För en ny Data Lake Storage Gen2-behållare används 750 standard 750 för kataloger och 640 för filer. Filer får inte X-biten eftersom det är irrelevant för filer i ett system med endast store.
>
> Masken kan anges per samtal. Detta gör att olika tidskrävande system, till exempel kluster, har olika effektiva masker för sina filåtgärder. Om en mask anges på en viss begäran åsidosätter den helt standardmasken.

#### <a name="the-sticky-bit"></a>Sticky bit

Den klibbiga biten är en mer avancerad funktion i en POSIX-behållare. I samband med Data Lake Storage Gen2 är det osannolikt att den klibbiga biten kommer att behövas. Sammanfattningsvis, om den klibbiga biten är aktiverad på en katalog, kan ett underordnat objekt bara tas bort eller byta namn på av det underordnade objektets ägandeanvändare.

Den klibbiga biten visas inte i Azure-portalen.

### <a name="default-permissions-on-new-files-and-directories"></a>Standardbehörigheter för nya filer och kataloger

När en ny fil eller katalog skapas under en befintlig katalog avgör standard-åtkomstkontrollistan i den överordnade katalogen:

- En underordnad katalogs standardförstörjd och åtkomst till ACL.
- En underordnad fils åtkomst till ACL (filer har ingen standardkontroll av ACL).

#### <a name="umask"></a>umask

När du skapar en fil eller katalog används umask för att ändra hur standard-ACL:erna anges för det underordnade objektet. umask är ett 9-bitarsvärde på överordnade kataloger som innehåller ett RWX-värde för **att äga användare,** **äga grupp**och **andra**.

Umask för Azure Data Lake Storage Gen2 ett konstant värde som är inställt på 007. Det här värdet översätts till:

| umask-komponent     | Numeriskt format | Kortformat | Betydelse |
|---------------------|--------------|------------|---------|
| umask.owning_user   |    0         |   `---`      | För att äga användaren, kopiera den överordnades standard-åtkomstkontrollant till barnets åtkomst-åtkomst | 
| umask.owning_group  |    0         |   `---`      | För att äga gruppen kopierar du den överordnades standardförordnare till barnets åtkomstkontrollista | 
| umask.other         |    7         |   `RWX`      | För andra, ta bort alla behörigheter för barnets åtkomst ACL |

Det umask-värde som används av Azure Data Lake Storage Gen2 innebär effektivt att värdet för **andra** aldrig överförs som standard på nya underordnade, oavsett vad standard-åtkomstkontrollistan anger. 

Följande pseudokod visar hur umask används när du skapar ACL:erna för ett underordnat objekt.

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

## <a name="common-questions-about-acls-in-data-lake-storage-gen2"></a>Vanliga frågor om ACL:er i Gen2 för lagring av datasjö

### <a name="do-i-have-to-enable-support-for-acls"></a>Måste jag aktivera stöd för ACL:er?

Nej. Åtkomstkontroll via ACL:er är aktiverad för ett lagringskonto så länge hns-funktionen (Hierarchical Namespace) är aktiverad.

Om HNS är inaktiverat gäller fortfarande Azure RBAC-auktoriseringsreglerna.

### <a name="what-is-the-best-way-to-apply-acls"></a>Vilket är det bästa sättet att tillämpa ACL: er?

Använd alltid Azure AD-säkerhetsgrupper som tilldelat huvudnamn i ACL:er. Motstå möjligheten att direkt tilldela enskilda användare eller tjänsthuvudnamn. Med hjälp av den här strukturen kan du lägga till och ta bort användare eller tjänsthuvudnamn utan att behöva använda ACL:er på nytt i en hel katalogstruktur. ) I stället behöver du bara lägga till eller ta bort dem från lämplig Azure AD-säkerhetsgrupp. Tänk på att ACL inte ärvs och därför återanvända ACL kräver uppdatering av åtkomstkontrollistan för varje fil och underkatalog. 

### <a name="which-permissions-are-required-to-recursively-delete-a-directory-and-its-contents"></a>Vilka behörigheter krävs för att rekursivt ta bort en katalog och dess innehåll?

- Anroparen har "superanvändarbehörighet",

Eller

- Den överordnade katalogen måste ha behörigheten Skriv + Kör.
- Katalogen som ska tas bort, och varje katalog i den, kräver läs + skriv + kör behörigheter.

> [!NOTE]
> Du behöver inte skrivbehörighet för att ta bort filer i kataloger. Dessutom kan rotkatalogen "/" aldrig tas bort.

### <a name="who-is-the-owner-of-a-file-or-directory"></a>Vem är ägare till en fil eller katalog?

Skaparen av en fil eller katalog blir ägare. När det gäller rotkatalogen är detta identiteten för den användare som skapade behållaren.

### <a name="which-group-is-set-as-the-owning-group-of-a-file-or-directory-at-creation"></a>Vilken grupp anges som den ägande gruppen för en fil eller katalog när den skapas?

Den ägande gruppen kopieras från den ägande grupp i den överordnade katalogen under vilken den nya filen eller katalogen skapas.

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>Jag är den ägande förbrukaren av en arkivera utom JAG dont' har den RWX tillåtelse jag nöd. Vad gör jag nu?

Den ägande användaren kan lätt ändra behörigheterna för filen för att ge sig själva de RWX-behörigheter de behöver.

### <a name="why-do-i-sometimes-see-guids-in-acls"></a>Varför ser jag ibland GUID:er i ACL:er?

Ett GUID visas om posten representerar en användare och den användaren inte längre finns i Azure AD. Detta inträffar vanligtvis när användaren har lämnat företaget eller om kontot har tagits bort i Azure AD. Dessutom har tjänsthuvudnamn och säkerhetsgrupper inte ett UPN (User Principal Name) för att identifiera dem och därför representeras de av sitt OID-attribut (en guid).

### <a name="how-do-i-set-acls-correctly-for-a-service-principal"></a>Hur ställer jag in ACL:er korrekt för ett tjänsthuvudnamn?

När du definierar ACL:er för tjänsthuvudnamn är det viktigt att använda objekt-ID (OID) för *tjänstens huvudnamn* för appregistreringen som du skapade. Det är viktigt att notera att registrerade appar har ett separat tjänsthuvudnamn i den specifika Azure AD-klienten. Registrerade appar har en OID som är synlig i Azure-portalen, men *tjänstens huvudnamn* har en annan (annan) OID.

Om du vill hämta OID för tjänstens huvudnamn som `az ad sp show` motsvarar en appregistrering kan du använda kommandot. Ange program-ID som parameter. Här är ett exempel på att få OID för tjänstens huvudnamn som motsvarar en appregistrering med App-ID = 18218b12-1895-43e9-ad80-6e8fc1ea88ce. Kör följande kommando i Azure CLI:

```azurecli
az ad sp show --id 18218b12-1895-43e9-ad80-6e8fc1ea88ce --query objectId
```

OID visas.

När du har rätt OID för tjänstens huvudnamn går du till sidan Hantera åtkomst för **lagringsutforskaren** för att lägga till OID och tilldela lämpliga behörigheter för OID. Se till att du väljer **Spara**.

### <a name="does-data-lake-storage-gen2-support-inheritance-of-acls"></a>Stöder Data Lake Storage Gen2 arv av ACL:er?

Azure RBAC-tilldelningar ärver. Tilldelningar flödar från prenumerations-, resursgrupps- och lagringskontoresurser ner till behållarresursen.

ACL:er ärver inte. Standard-ACL:er kan dock användas för att ange ACL:er för underordnade underkataloger och filer som skapats under den överordnade katalogen. 

### <a name="where-can-i-learn-more-about-posix-access-control-model"></a>Var hittar jag mer information om POSIX-modellen för åtkomstkontroll?

* [POSIX-åtkomstkontrollistor på Linux](https://www.linux.com/news/posix-acls-linux)
* [Behörighetsguide för HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)
* [Vanliga frågor och svar om POSIX](https://www.opengroup.org/austin/papers/posix_faq.html)
* [POSIX 1003.1 2008](https://standards.ieee.org/findstds/standard/1003.1-2008.html)
* [POSIX 1003.1 2013](https://pubs.opengroup.org/onlinepubs/9699919799.2013edition/)
* [POSIX 1003.1 2016](https://pubs.opengroup.org/onlinepubs/9699919799.2016edition/)
* [POSIX ACL på Ubuntu](https://help.ubuntu.com/community/FilePermissionsACLs)
* [ACL med åtkomstkontrollistor på Linux](https://bencane.com/2012/05/27/acl-using-access-control-lists-on-linux/)

## <a name="see-also"></a>Se även

* [Översikt över Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md)
