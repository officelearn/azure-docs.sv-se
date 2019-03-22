---
title: Översikt över åtkomstkontroll i Azure Data Lake Storage Gen2 | Microsoft Docs
description: Förstå åtkomstkontroll i Azure Data Lake Storage Gen2
services: storage
author: jamesbak
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: jamesbak
ms.openlocfilehash: 906b1dde3d145268df4fb1ff5c243c7daa8396ec
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57992433"
---
# <a name="access-control-in-azure-data-lake-storage-gen2"></a>Åtkomstkontroll i Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 implementerar en modell för åtkomstkontroll som stöder både Azure rollbaserad åtkomstkontroll (RBAC) och POSIX-liknande åtkomstkontrollistor (ACL). Den här artikeln sammanfattar grunderna i modellen för åtkomstkontroll för Data Lake Storage Gen2. 

## <a name="azure-role-based-access-control-rbac"></a>Azure rollbaserad åtkomstkontroll (RBAC)

Azure rollbaserad åtkomstkontroll (RBAC) använder rolltilldelningar för att effektivt gäller uppsättningar av behörigheter för användare, grupper och tjänstens huvudnamn för Azure-resurser. Vanligtvis dessa Azure-resurser är begränsade till översta resurser (*t.ex.*, Azure Storage-konton). När det gäller Azure Storage, och därmed Azure Data Lake Storage Gen2, har den här mekanismen utökats till system-filresurs.

Med RBAC-rolltilldelningar är en kraftfull mekanism för att kontrollera användarbehörighet, är en mycket ger detaljerade mekanism i förhållande till ACL: er. Den lägsta Granulariteten för RBAC har uppnått filsystemnivå och detta kommer att utvärderas med högre prioritet än ACL: er. Därför om du tilldelar RBAC-behörigheter i ett filsystem, har användaren eller tjänstens huvudnamn den auktoriseringen för alla kataloger och filer på det filsystemet, oavsett ACL tilldelningar.

Azure Storage tillhandahåller tre inbyggda RBAC-roller för Blob storage: 

- [Storage Blob Data-ägare](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner-preview)
- [Storage Blob Data-deltagare](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor-preview)
- [Storage Blob Data-läsare](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader-preview)

När en användare eller tjänstens huvudnamn beviljas RBAC databehörigheter via någon av dessa inbyggda roller eller via en anpassad roll, utvärderas dessa behörigheter först vid en begäran om godkännande. Om den begärda åtgärden har beviljats behörighet av anroparens RBAC-uppgifter och sedan auktorisering är omedelbart löst och inga fler utförs ACL-kontroller. Du kan också om anroparen har inte en RBAC-tilldelning eller i förfrågan matchar inte tilldelad behörighet, utförs sedan ACL kontroller för att fastställa om anroparen har behörighet att utföra den begärda åtgärden.

En särskild anmärkning ska göras av den inbyggda rollen som ägare för Storage Blob-Data. Om anroparen har tilldelningen RBAC kommer användaren betraktas som en *superanvändare* och är beviljas fullständig åtkomst till alla mutera åtgärder, inklusive att ställa in ägaren av en katalog eller fil samt ACL: er för kataloger och filer som de är inte ägare. Superanvändare åtkomst är bara auktoriserad sätt att ändra ägaren till en resurs.

## <a name="shared-key-and-shared-access-signature-authentication"></a>Autentisering med signatur för delad nyckel och delad åtkomst

Azure Data Lake Storage Gen2 stöder delad nyckel och signatur för delad åtkomst för autentisering. En egenskap för dessa autentiseringsmetoder är att ingen identitet är kopplad till anroparen och därför behörighet-baserade användarautentisering kan inte utföras.

När det gäller delad nyckel får anroparen effektivt 'superanvändare' åtkomst, vilket innebär fullständig åtkomst till alla åtgärder på alla resurser, inklusive anger ägare och ändrar ACL: er.

SAS-token är tillåtna behörigheter som en del av token. De behörigheter som ingår i SAS-token är faktiskt tillämpas på alla beslut om auktorisering, men inga ytterligare kontroller för ACL utförs.

## <a name="access-control-lists-on-files-and-directories"></a>Åtkomstkontrollistor för filer och kataloger

Det finns två sorters åtkomstkontrollistor (ACL): åtkomst-ACL: er och standard-ACL: er.

* **Åtkomst-ACL**: Åtkomst-ACL:er kontrollerar åtkomst till ett objekt. Filer och kataloger har båda åtkomst-ACL:er.

* **Standard-ACL**: En mall av ACL: er som är associerade med en katalog som bestämmer åtkomst-ACL: er för underordnade objekt som skapats under den katalogen. Filer har inte standard-ACL:er.

Både åtkomst-ACL och standard-ACL har samma struktur.

> [!NOTE]
> Ändra serverns standardinställningar ACL på ett överordnat inte påverkar åtkomsten ACL och standard-ACL för underordnade objekt som redan finns.

## <a name="permissions"></a>Behörigheter

Behörigheter för ett objekt i filsystemet är **Läs**, **skriva**, och **kör**, och de kan användas för filer och kataloger som visas i följande tabell:

|            |    Fil     |   Katalog |
|------------|-------------|----------|
| **Läsa (R)** | Kan läsa innehållet i en fil | Kräver **Läs** och **kör** att visa innehållet i katalogen |
| **Skriva (W)** | Kan skriva eller lägg till i en fil | Kräver **skriva** och **kör** att skapa underordnade objekt i en katalog |
| **Köra (X)** | Innebär inte något i samband med Data Lake Storage Gen2 | Krävs för att bläddra bland de underordnade objekten i en katalog |

### <a name="short-forms-for-permissions"></a>Kortformat för behörigheter

**RWX**används för att ange **läsa + skriva + köra**. Ett numeriskt mer komprimerat format finns där **Läsa = 4**, **skriva = 2** och **Köra = 1** och deras summa representerar behörigheterna. Här följer några exempel.

| Numeriskt format | Kortformat |      Vad det innebär     |
|--------------|------------|------------------------|
| 7            | `RWX`        | Läsa + skriva + köra |
| 5            | `R-X`        | Läsa + köra         |
| 4            | `R--`        | Läsa                   |
| 0            | `---`        | Inga behörigheter         |

### <a name="permissions-inheritance"></a>Arv av behörigheter

I POSIX modellen som används av Data Lake Storage Gen2, förvaras behörigheter för ett objekt i själva objektet. Behörigheter för ett objekt kan inte med andra ord att ärvas från de överordnade objekten om behörigheterna som ställs in efter det underordnade objektet har redan skapats. Behörigheter ärvs endast om standardbehörigheterna har ställts in på de överordnade objekten innan de underordnade objekten har skapats.

## <a name="common-scenarios-related-to-permissions"></a>Vanliga scenarier som rör behörigheter

I följande tabell visas några vanliga scenarier för att hjälpa dig att förstå vilka behörigheter som krävs för att utföra vissa åtgärder på ett Data Lake Storage Gen2-konto.

|    Åtgärd             |    /    | Oregon / | Portland / | Data.txt     |
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
>
>

## <a name="users-and-identities"></a>Användare och identiteter

Alla filer och kataloger har olika behörigheter för dessa identiteter:

- Ägande användare
- Ägande grupp
- Namngivna användare
- Namngivna grupper
- Namngivna tjänstens huvudnamn
- Alla andra användare

Identiteten för användare och grupper är Azure Active Directory (Azure AD)-identiteter. Så om inget annat anges en *användaren*, i samband med Data Lake Storage Gen2 kan referera till en Azure AD-användare, service principal eller säkerhetsgrupp.

### <a name="the-owning-user"></a>Ägande användare

Användaren som skapade objektet är automatiskt ägande användare för objektet. En ägande användare kan:

* Ändra behörigheterna för en fil som ägs.
* Ändra ägande grupp för en fil som ägs, så länge den ägande användaren också är medlem i målgruppen.

> [!NOTE]
> Den ägande användaren *kan* ändra ägande användare av en fil eller katalog. Endast superanvändare kan ändra ägande användare av en fil eller katalog.

### <a name="the-owning-group"></a>Ägande grupp

I POSIX ACL: er är varje användare som är associerad med en *primär grupp*. Användaren "Alice" kan t.ex. tillhöra gruppen "Ekonomi". Alice kan också tillhöra flera grupper, men en grupp anges alltid som hennes primära grupp. När Alice skapar en fil i POSIX ställs den ägande gruppen för filen in som hennes primära grupp, som i det här fallet är "Ekonomi". Den ägande gruppen fungerar annars ungefär som tilldelade behörigheter för andra användare/grupper.

#### <a name="assigning-the-owning-group-for-a-new-file-or-directory"></a>Tilldela den ägande gruppen för en ny fil eller katalog

* **Fall 1**: Rotkatalogen ”/”. Den här katalogen skapas när ett Data Lake Storage Gen2 filsystem skapas. I det här fallet den ägande gruppen angetts till användaren som skapade filsystemet om den har utförts med hjälp av OAuth. Om filsystemet har skapats med delad nyckel, en SAS-konto eller en SAS för tjänst så ägare och ägande grupp är inställda på **$superuser**.
* **Fall 2** (alla andra fall): När ett nytt objekt skapas, kopieras den ägande gruppen från den överordnade katalogen.

#### <a name="changing-the-owning-group"></a>Ändra den ägande gruppen

Den ägande gruppen kan ändras av:
* Alla superanvändare.
* Den ägande användaren, om den ägande användaren också är medlem i målgruppen.

> [!NOTE]
> Den ägande gruppen kan inte ändra ACL: er för en fil eller katalog.  Även om den ägande gruppen angetts till användaren som skapade kontot när det gäller rotkatalogen **fall 1** ovan, ett enda användarkonto är inte giltig för att ge behörighet via den ägande gruppen. Du kan tilldela den här behörigheten till en giltig användargrupp, om det är lämpligt.

## <a name="access-check-algorithm"></a>Algoritm för åtkomstkontroll

Följande pseudocode representerar algoritmen för åtkomstkontroll för Data Lake Storage Gen2 konton.

```
def access_check( user, desired_perms, path ) : 
  # access_check returns true if user has the desired permissions on the path, false otherwise
  # user is the identity that wants to perform an operation on path
  # desired_perms is a simple integer with values from 0 to 7 ( R=4, W=2, X=1). User desires these permissions
  # path is the file or directory
  # Note: the "sticky bit" is not illustrated in this algorithm
  
# Handle super users.
  if (is_superuser(user)) :
    return True

# Handle the owning user. Note that mask IS NOT used.
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

### <a name="the-mask"></a>Masken

Enligt beskrivningen i algoritmen Kontrollera åtkomst, begränsar masken åtkomst för namngiven användare, ägande gruppen och namngivna grupper.  

> [!NOTE]
> För ett nytt Data Lake Storage Gen2 filsystem masken för åtkomst-ACL i rotkatalogen (”/”) som standard 750 för kataloger och 640 för filer. Filer får inte X-bitars eftersom den är irrelevanta till filer i ett system som endast store.
>
> Masken kan anges på basis av per anrop. På så sätt kan olika konsumerande system, t.ex kluster, ha olika effektiva masker för sina filåtgärder. Om en mask anges på en viss begäran åsidosätter helt standard masken.

### <a name="the-sticky-bit"></a>Sticky bit

Sticky bit är en mer avancerad funktion i ett POSIX-filsystem. I samband med Data Lake Storage Gen2 är det troligt att sticky bit kommer att behövas. Sammanfattningsvis om sticky bit är aktiverad på en katalog, kan ett underordnat objekt bara tas bort eller byta namn på av det underordnade objektets ägande användare.

Sticky bit visas inte i Azure-portalen.

## <a name="default-permissions-on-new-files-and-directories"></a>Standardbehörigheter för nya filer och kataloger

När en ny fil eller katalog som skapas under en befintlig katalog, standard ACL på den överordnade katalogen avgör:

- En underordnad katalog standard-ACL och åtkomst-ACL.
- En underordnad fils åtkomst-ACL (filer har inte en standard-ACL).

### <a name="umask"></a>umask

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

Nej. Åtkomstkontroll via ACL: er har aktiverats för ett Data Lake Storage Gen2 konto så länge den hierarkiska Namespace (HNS) funktionen är aktiverade.

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
