---
title: Översikt över åtkomstkontroll i Azure Data Lake Storage Gen2 | Microsoft Docs
description: Förstå åtkomstkontroll i Azure Data Lake Storage Gen2
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 0b8139f11f937ddae30e25f4153e35287289a4d1
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233986"
---
# <a name="access-control-in-azure-data-lake-storage-gen2"></a>Åtkomstkontroll i Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 implementerar en modell för åtkomstkontroll som stöder både Azure rollbaserad åtkomstkontroll (RBAC) och POSIX-liknande åtkomstkontrollistor (ACL). Den här artikeln sammanfattar grunderna i modellen för åtkomstkontroll för Data Lake Storage Gen2.

<a id="azure-role-based-access-control-rbac" />

## <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll

RBAC används rolltilldelningar för att ge uppsättningar av behörigheter för att effektivt *säkerhetsobjekt*. En *säkerhetsobjekt* är ett objekt som representerar en användare, grupp, tjänstens huvudnamn eller hanterad identitet som har definierats i Azure Active Directory (AD) som begär åtkomst till Azure-resurser.

Vanligtvis dessa Azure-resurser är begränsade till översta resurser (till exempel: Azure Storage-konton). När det gäller Azure Storage, och därmed Azure Data Lake Storage Gen2, har den här mekanismen utökats till system-filresurs.

Läs hur du tilldelar roller till säkerhetsobjekt i omfånget för ditt lagringskonto i [autentisera åtkomst till Azure-blobbar och köer med hjälp av Azure Active Directory](https://docs.microsoft.com/azure/storage/common/storage-auth-aad?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="the-impact-of-role-assignments-on-file-and-directory-level-access-control-lists"></a>Effekten av rolltilldelningar på filer och kataloger på åtkomstkontrollistor

Med RBAC-rolltilldelningar är en kraftfull mekanism för att styra åtkomstbehörigheter, är en mycket ger detaljerade mekanism i förhållande till ACL: er. Den lägsta Granulariteten för RBAC har uppnått filsystemnivå och detta kommer att utvärderas med högre prioritet än ACL: er. Därför, om du tilldelar en roll till ett säkerhetsobjekt i omfånget för ett filsystem som säkerhetsobjektet har auktorisering som är associerad med rollen för alla kataloger och filer i det filsystemet, oavsett ACL tilldelningar.

När ett säkerhetsobjekt beviljas RBAC databehörigheter via en [inbyggd roll](https://docs.microsoft.com/azure/storage/common/storage-auth-aad?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#built-in-rbac-roles-for-blobs-and-queues), eller via en anpassad roll behörigheterna utvärderas först vid en begäran om godkännande. Om den begärda åtgärden har beviljats behörighet av säkerhetsobjektets RBAC-uppgifter och sedan auktorisering är omedelbart löst och inga fler utförs ACL-kontroller. Du kan också om säkerhetsobjektet har inte en RBAC-tilldelning, eller i förfrågan matchar inte tilldelad behörighet, utförs sedan ACL kontroller för att fastställa om säkerhetsobjektet har behörighet att utföra den begärda åtgärden.

> [!NOTE]
> Om säkerhetsobjektet har tilldelats inbyggda rolltilldelningen för Storage Blob Data ägare kommer säkerhetsobjektet anses vara en *superanvändare* och beviljas fullständig åtkomst till alla mutating åtgärder, inklusive inställning på ägare av en katalog eller fil samt ACL: er för kataloger och filer som de inte är ägaren. Superanvändare åtkomst är bara auktoriserad sätt att ändra ägaren till en resurs.

## <a name="shared-key-and-shared-access-signature-sas-authentication"></a>Autentisering med delad nyckel och signatur för delad åtkomst (SAS)

Azure Data Lake Storage Gen2 har stöd för delad nyckel och SAS-metoder för autentisering. En egenskap för dessa autentiseringsmetoder är att ingen identitet är kopplad till anroparen och därför säkerhet huvudnamn behörighet-baserad auktorisering kan inte utföras.

När det gäller delad nyckel får anroparen effektivt 'superanvändare' åtkomst, vilket innebär fullständig åtkomst till alla åtgärder på alla resurser, inklusive anger ägare och ändrar ACL: er.

SAS-token är tillåtna behörigheter som en del av token. De behörigheter som ingår i SAS-token är faktiskt tillämpas på alla beslut om auktorisering, men inga ytterligare kontroller för ACL utförs.

## <a name="access-control-lists-on-files-and-directories"></a>Åtkomstkontrollistor för filer och kataloger

Du kan associera ett säkerhetsobjekt med åtkomstnivå för filer och kataloger. Dessa associationer samlas i en *åtkomstkontrollistan (ACL)*. Varje fil och katalog i ditt storage-konto har en åtkomstkontrollista.

Du kan använda åtkomstkontrollistor för att bevilja det säkerhetsobjektet utökad åtkomst till specifika filer och kataloger om du har tilldelat en roll till en säkerhetsprincipal på storage-kontonivå.

Du kan inte använda åtkomstkontrollistor för att ange en åtkomstnivå som är lägre än en nivå som har beviljats av en rolltilldelning. Exempel: Om du tilldelar den [Storage Blob Data-deltagare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor-preview) rollen till säkerhetsobjekt, kan du inte använda åtkomstkontroll visar för att förhindra att säkerhetsobjekt från att skriva till en katalog.

### <a name="set-file-and-directory-level-permissions-by-using-access-control-lists"></a>Ange fil- och behörigheter på kolumnnivå med hjälp av åtkomstkontrollistor

Om du vill ange fil- och behörigheter på kolumnnivå, ser du något av följande artiklar:

|Om du vill använda det här verktyget:    |Se den här artikeln:    |
|--------|-----------|
|Azure Lagringsutforskaren    |[Ange fil- och behörigheter på kolumnnivå med Azure Storage Explorer med Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-how-to-set-permissions-storage-explorer)|
|REST API    |[Sökväg - uppdatering](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/path/update)|

> [!IMPORTANT]
> Om säkerhetsobjektet är en *service* huvudnamn är det viktigt att använda objekt-ID för tjänstens huvudnamn och inte objekt-ID för relaterade appregistreringen. Att hämta objekt-ID för tjänstens huvudnamn öppna Azure CLI och sedan använda det här kommandot: `az ad sp show --id <Your App ID> --query objectId`. Ersätt den `<Your App ID>` med App-ID för din appregistrering.

### <a name="types-of-access-control-lists"></a>Typer av åtkomstkontrollistor

Det finns två typer av åtkomstkontrollistor: *åtkomst-ACL* och *standard-ACL*.

Åtkomst-ACL:er kontrollerar åtkomst till ett objekt. Filer och kataloger har båda åtkomst-ACL:er.

Standard-ACL: er finns mallar av ACL: er som är associerade med en katalog som bestämmer åtkomst-ACL: er för underordnade objekt som skapats under den katalogen. Filer har inte standard-ACL:er.

Både åtkomst-ACL och standard-ACL har samma struktur.

Både åtkomst-ACL och standard-ACL har samma struktur.

> [!NOTE]
> Ändra serverns standardinställningar ACL på ett överordnat inte påverkar åtkomsten ACL och standard-ACL för underordnade objekt som redan finns.

### <a name="levels-of-permission"></a>Behörighetsnivåer

Behörigheter för ett objekt i filsystemet är **Läs**, **skriva**, och **kör**, och de kan användas för filer och kataloger som visas i följande tabell:

|            |    Fil     |   Katalog |
|------------|-------------|----------|
| **Läsa (R)** | Kan läsa innehållet i en fil | Kräver **Läs** och **kör** att visa innehållet i katalogen |
| **Skriva (W)** | Kan skriva eller lägg till i en fil | Kräver **skriva** och **kör** att skapa underordnade objekt i en katalog |
| **Köra (X)** | Innebär inte något i samband med Data Lake Storage Gen2 | Krävs för att bläddra bland de underordnade objekten i en katalog |

#### <a name="short-forms-for-permissions"></a>Kortformat för behörigheter

**RWX**används för att ange **läsa + skriva + köra**. Ett numeriskt mer komprimerat format finns där **Läsa = 4**, **skriva = 2** och **Köra = 1** och deras summa representerar behörigheterna. Här följer några exempel.

| Numeriskt format | Kortformat |      Vad det innebär     |
|--------------|------------|------------------------|
| 7            | `RWX`        | Läsa + skriva + köra |
| 5            | `R-X`        | Läsa + köra         |
| 4            | `R--`        | Läsa                   |
| 0            | `---`        | Ingen behörighet         |

#### <a name="permissions-inheritance"></a>Arv av behörigheter

I POSIX modellen som används av Data Lake Storage Gen2, förvaras behörigheter för ett objekt i själva objektet. Behörigheter för ett objekt kan inte med andra ord att ärvas från de överordnade objekten om behörigheterna som ställs in efter det underordnade objektet har redan skapats. Behörigheter ärvs endast om standardbehörigheterna har ställts in på de överordnade objekten innan de underordnade objekten har skapats.

### <a name="common-scenarios-related-to-permissions"></a>Vanliga scenarier som rör behörigheter

I följande tabell visas några vanliga scenarier för att hjälpa dig att förstå vilka behörigheter som krävs för att utföra vissa åtgärder på ett lagringskonto.

|    Operation             |    /    | Oregon / | Portland / | Data.txt     |
|--------------------------|---------|----------|-----------|--------------|
| Läs Data.txt            |   `--X`   |   `--X`    |  `--X`      | `R--`          |
| Lägga till i Data.txt       |   `--X`   |   `--X`    |  `--X`      | `RW-`          |
| Ta bort Data.txt          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Skapa Data.txt          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Lista /                   |   `R-X`   |   `---`    |  `---`      | `---`          |
| Lista /Oregon/           |   `--X`   |   `R-X`    |  `---`      | `---`          |
| Lista /Oregon/Portland /  |   `--X`   |   `--X`    |  `R-X`      | `---`          |

> [!NOTE]
> Skriva för filen inte krävs för att ta bort den, så länge de två villkoren ovan är sanna.

### <a name="users-and-identities"></a>Användare och identiteter

Alla filer och kataloger har olika behörigheter för dessa identiteter:

- Ägande användare
- Ägande grupp
- Namngivna användare
- Namngivna grupper
- Namngivna tjänstens huvudnamn
- Namngivna hanterade identiteter
- Alla andra användare

Identiteten för användare och grupper är Azure Active Directory (Azure AD)-identiteter. Så om inget annat anges en *användaren*, i samband med Data Lake Storage Gen2 kan referera till en Azure AD-användare, service principal, hanterad identitet eller säkerhetsgrupp.

#### <a name="the-owning-user"></a>Ägande användare

Användaren som skapade objektet är automatiskt ägande användare för objektet. En ägande användare kan:

* Ändra behörigheterna för en fil som ägs.
* Ändra ägande grupp för en fil som ägs, så länge den ägande användaren också är medlem i målgruppen.

> [!NOTE]
> Den ägande användaren *kan* ändra ägande användare av en fil eller katalog. Endast superanvändare kan ändra ägande användare av en fil eller katalog.

#### <a name="the-owning-group"></a>Ägande grupp

I POSIX ACL: er är varje användare som är associerad med en *primär grupp*. Användaren ”Alice” kan tillhöra gruppen ”Ekonomi”. Alice kan också tillhöra flera grupper, men en grupp anges alltid som hennes primära grupp. När Alice skapar en fil i POSIX ställs den ägande gruppen för filen in som hennes primära grupp, som i det här fallet är "Ekonomi". Den ägande gruppen fungerar annars ungefär som tilldelade behörigheter för andra användare/grupper.

##### <a name="assigning-the-owning-group-for-a-new-file-or-directory"></a>Tilldela den ägande gruppen för en ny fil eller katalog

* **Fall 1**: Rotkatalogen ”/”. Den här katalogen skapas när ett Data Lake Storage Gen2 filsystem skapas. I det här fallet den ägande gruppen angetts till användaren som skapade filsystemet om den har utförts med hjälp av OAuth. Om filsystemet har skapats med delad nyckel, en SAS-konto eller en SAS för tjänst så ägare och ägande grupp är inställda på **$superuser**.
* **Fall 2** (alla andra fall): När ett nytt objekt skapas, kopieras den ägande gruppen från den överordnade katalogen.

##### <a name="changing-the-owning-group"></a>Ändra den ägande gruppen

Den ägande gruppen kan ändras av:
* Alla superanvändare.
* Den ägande användaren, om den ägande användaren också är medlem i målgruppen.

> [!NOTE]
> Den ägande gruppen kan inte ändra ACL: er för en fil eller katalog.  Även om den ägande gruppen angetts till användaren som skapade kontot när det gäller rotkatalogen **fall 1** ovan, ett enda användarkonto är inte giltigt för att ge behörighet via den ägande gruppen. Du kan tilldela den här behörigheten till en giltig användargrupp, om det är lämpligt.

### <a name="access-check-algorithm"></a>Algoritm för åtkomstkontroll

Följande pseudocode representerar algoritmen för åtkomstkontroll för storage-konton.

```
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

Enligt beskrivningen i algoritmen Kontrollera åtkomst, begränsar masken åtkomst för namngiven användare, ägande gruppen och namngivna grupper.  

> [!NOTE]
> För ett nytt Data Lake Storage Gen2 filsystem masken för åtkomst-ACL i rotkatalogen (”/”) som standard 750 för kataloger och 640 för filer. Filer får inte X-bitars eftersom den är irrelevanta till filer i ett system som endast store.
>
> Masken kan anges på basis av per anrop. På så sätt kan olika konsumerande system, t.ex kluster, ha olika effektiva masker för sina filåtgärder. Om en mask anges på en viss begäran åsidosätter helt standard masken.

#### <a name="the-sticky-bit"></a>Sticky bit

Sticky bit är en mer avancerad funktion i ett POSIX-filsystem. I samband med Data Lake Storage Gen2 är det troligt att sticky bit kommer att behövas. Sammanfattningsvis om sticky bit är aktiverad på en katalog, kan ett underordnat objekt bara tas bort eller byta namn på av det underordnade objektets ägande användare.

Sticky bit visas inte i Azure-portalen.

### <a name="default-permissions-on-new-files-and-directories"></a>Standardbehörigheter för nya filer och kataloger

När en ny fil eller katalog som skapas under en befintlig katalog, standard ACL på den överordnade katalogen avgör:

- En underordnad katalog standard-ACL och åtkomst-ACL.
- En underordnad fils åtkomst-ACL (filer har inte en standard-ACL).

#### <a name="umask"></a>umask

När du skapar en fil eller katalog, används umask för att ändra hur standard-ACL: er är inställda på det underordnade objektet. umask är ett 9-bitars värde för överordnade kataloger som innehåller ett RWX-värde för **ägande användare**, **ägande grupp**, och **andra**.

Umask för Azure Data Lake Storage Gen2 en konstant värde som är inställt på 007. Det här värdet motsvarar:

| umask-komponent     | Numeriskt format | Kortformat | Betydelse |
|---------------------|--------------|------------|---------|
| umask.owning_user   |    0         |   `---`      | För ägande användare, kopierar du den överordnade filens standard-ACL till barnets åtkomst-ACL | 
| umask.owning_group  |    0         |   `---`      | Kopiera den överordnade filens standard-ACL till barnets åtkomst-ACL för ägande grupp | 
| umask.Other         |    7         |   `RWX`      | Ta bort alla behörigheter på barnets åtkomst-ACL för andra, |

Umask-värde som används av Azure Data Lake Storage Gen2 effektivt innebär att värdet för **andra** skickas aldrig som standard på nya barn, oavsett vad standardvärdet anger ACL. 

Följande pseudocode visar hur umask används när du skapar ACL: er för ett underordnat objekt.

```
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

Nej. Åtkomstkontroll via ACL: er har aktiverats för ett lagringskonto så länge den hierarkiska Namespace (HNS) funktionen är aktiverade.

Om HNS är avstängd, Azure RBAC-auktoriseringsregler fortfarande gäller.

### <a name="what-is-the-best-way-to-apply-acls"></a>Vad är det bästa sättet att tillämpa ACL: er?

Använd alltid Azure AD-säkerhetsgrupper som det tilldelade huvudnamnet i ACL: er. Stå emot möjlighet att direkt tilldela enskilda användare eller tjänstens huvudnamn. Den här strukturen kan du lägga till och ta bort användare eller tjänstens huvudnamn utan att behöva behålla ACL: er i en hel katalogstruktur. ) I stället, du behöver bara lägga till eller ta bort dem från den aktuella Azure AD-säkerhetsgruppen. Tänk på att ärvs inte ACL: er och ACL: er på nytt tillämpa så behöver uppdateras ACL på varje fil och underkatalog. 

### <a name="which-permissions-are-required-to-recursively-delete-a-directory-and-its-contents"></a>Vilka behörigheter krävs för att rekursivt ta bort en katalog och dess innehåll?

- Anroparen har '' superanvändarbehörigheter,

Eller

- Den överordnade katalogen måste ha Skriv + kör-behörighet.
- Katalogen som ska tas bort och alla kataloger inom den, behöver läsa + skriva + köra behörigheter.

> [!NOTE]
> Du behöver inte Skriv-behörigheter för att ta bort filer i kataloger. Dessutom rotkatalogen ”/” kan aldrig tas bort.

### <a name="who-is-the-owner-of-a-file-or-directory"></a>Vem är ägaren av en fil eller katalog?

Skaparen av en fil eller katalog blir ägaren. När det gäller rotkatalogen är detta identiteten för den användare som skapade filsystemet.

### <a name="which-group-is-set-as-the-owning-group-of-a-file-or-directory-at-creation"></a>Vilken grupp anges som den ägande gruppen för en fil eller katalog vid skapandet?

Den ägande gruppen kopieras från den ägande gruppen för den överordnade katalogen under vilken den nya filen eller katalogen skapas.

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>Jag är ägande användare av en fil, men jag har inte den RWX-behörighet jag behöver. Vad gör jag nu?

Den ägande användaren kan lätt ändra behörigheterna för filen för att ge sig själva de RWX-behörigheter de behöver.

### <a name="why-do-i-sometimes-see-guids-in-acls"></a>Varför ibland visas GUID i ACL: er?

En GUID visas om posten representerar en användare och att användaren finns inte i Azure AD längre. Detta inträffar vanligtvis när användaren har lämnat företaget eller om kontot har tagits bort i Azure AD. Dessutom tjänstens huvudnamn och säkerhetsgrupper har inte ett UPN User Principal Name () att identifiera dem och så de representeras av sina objektidentifierarattribut (en guid).

### <a name="how-do-i-set-acls-correctly-for-a-service-principal"></a>Hur ställer jag in ACL: er korrekt för en tjänst huvudnamn?

När du definierar ACL: er för tjänstens huvudnamn är det viktigt att använda det objekt-ID (OID) för den *tjänstens huvudnamn* för registreringen som du skapade. Det är viktigt att Observera att registrerade appar har en separat tjänstens huvudnamn i specifikt Azure AD-klient. Registrerade appar har en OID som visas i Azure-portalen, men *tjänstens huvudnamn* har en annan (olika) OID.

Du kan använda för att få OID för tjänstens huvudnamn som motsvarar en appregistrering kan den `az ad sp show` kommando. Ange program-ID som parameter. Här är ett exempel på hur du skaffar OID för tjänstens huvudnamn som motsvarar en appregistrering med App-ID = 18218b12-1895-43e9-ad80-6e8fc1ea88ce. Kör följande kommando i Azure CLI:

`az ad sp show --id 18218b12-1895-43e9-ad80-6e8fc1ea88ce --query objectId
<<OID will be displayed>>`

När du har rätt OID för tjänstens huvudnamn, går du till Lagringsutforskaren **hantera åtkomst** att lägga till OID och tilldela lämpliga behörigheter för OID. Kontrollera att du väljer **spara**.

### <a name="does-data-lake-storage-gen2-support-inheritance-of-acls"></a>Stöder Data Lake Storage Gen2 arv av ACL: er?

Azure RBAC-uppgifter ärver. Tilldelningar flödar från prenumeration, resursgrupp och lagringskontoresurserna ned filen Systemresursen.

ACL: er ärver inte. Standard-ACL: er kan dock användas för att ange ACL: er för underordnade undermappar och filer som skapas under den överordnade katalogen. 

### <a name="where-can-i-learn-more-about-posix-access-control-model"></a>Var hittar jag mer information om POSIX-modellen för åtkomstkontroll?

* [POSIX-åtkomstkontrollistor på Linux](https://www.linux.com/news/posix-acls-linux)
* [Guide för HDFS-behörighet](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)
* [Vanliga frågor och svar om POSIX](https://www.opengroup.org/austin/papers/posix_faq.html)
* [POSIX 1003.1 2008](https://standards.ieee.org/findstds/standard/1003.1-2008.html)
* [POSIX 1003.1 2013](https://pubs.opengroup.org/onlinepubs/9699919799.2013edition/)
* [POSIX 1003.1 2016](https://pubs.opengroup.org/onlinepubs/9699919799.2016edition/)
* [POSIX ACL på Ubuntu](https://help.ubuntu.com/community/FilePermissionsACLs)
* [ACL med åtkomstkontrollistor på Linux](https://bencane.com/2012/05/27/acl-using-access-control-lists-on-linux/)

## <a name="see-also"></a>Se också

* [Översikt över Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md)
