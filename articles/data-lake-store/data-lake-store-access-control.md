---
title: Översikt över åtkomstkontroll i Data Lake Storage Gen1 | Microsoft Docs
description: Förstå åtkomstkontroll i Azure Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: d16f8c09-c954-40d3-afab-c86ffa8c353d
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: nitinme
ms.openlocfilehash: 72bc0408ed1eba2d959d246a55677ee9964ef106
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "44718822"
---
# <a name="access-control-in-azure-data-lake-storage-gen1"></a>Åtkomstkontroll i Azure Data Lake Storage Gen1

Azure Data Lake Storage Gen1 implementerar en modell för åtkomstkontroll som härstammar från HDFS och i sin tur från POSIX-modellen för åtkomstkontroll. Den här artikeln sammanfattar grunderna i modellen för åtkomstkontroll för Data Lake Storage Gen1. 

## <a name="access-control-lists-on-files-and-folders"></a>Åtkomstkontrollistor för filer och mappar

Det finns två sorters åtkomstkontrollistor (ACL:er), **Åtkomst-ACL:er** och **Standard-ACL:er**.

* **Åtkomst-ACL:er**: De här kontrollerar åtkomst till ett objekt. Både filer och mappar har åtkomst-ACL:er.

* **Standard-ACL**: En "mall" av ACL:er som är associerad med en mapp som bestämmer åtkomst-ACL:er för underordnade objekt som skapats under mappen. Filer har inte standard-ACL:er.


Både åtkomst-ACL:er och standard-ACL:er har samma struktur.



> [!NOTE]
> Att ändra standard-ACL på ett överordnat objekt påverkar inte åtkomst-ACL eller standard-ACL för underordnade objekt som redan finns.
>
>

## <a name="users-and-identities"></a>Användare och identiteter

Alla filer och mappar har olika behörigheter för dessa identiteter:

* Ägande användare
* Ägande grupp
* Namngivna användare
* Namngivna grupper
* Alla andra användare

Identiteten för användare och grupper är Azure Active Directory (Azure AD)-identiteter. Så om inget annat anges kan en ”användare” i samband med Data Lake Storage Gen1: antingen Azure AD-användare eller en Azure AD-säkerhetsgrupp.

## <a name="permissions"></a>Behörigheter

Behörigheter för ett objekt i filsystemet är **Läsa**, **Skriva** och **Köra** och de kan användas för filer och mappar som visas i tabellen nedan:

|            |    Fil     |   Mapp |
|------------|-------------|----------|
| **Läsa (R)** | Kan läsa innehållet i en fil | Kräver **Läsa** och **Köra** för att visa innehållet i mappen|
| **Skriva (W)** | Kan skriva eller lägg till i en fil | Kräver **Skriva** och **Köra** för att skapa underordnade objekt i en mapp |
| **Köra (X)** | Innebär inte något i samband med Data Lake Storage Gen1 | Krävs för att bläddra bland de underordnade objekten i en mapp |

### <a name="short-forms-for-permissions"></a>Kortformat för behörigheter

**RWX**används för att ange **läsa + skriva + köra**. Ett numeriskt mer komprimerat format finns där **Läsa = 4**, **skriva = 2** och **Köra = 1** och deras summa representerar behörigheterna. Här följer några exempel.

| Numeriskt format | Kortformat |      Vad det innebär     |
|--------------|------------|------------------------|
| 7            | `RWX`        | Läsa + skriva + köra |
| 5            | `R-X`        | Läsa + köra         |
| 4            | `R--`        | Läsa                   |
| 0            | `---`        | Inga behörigheter         |


### <a name="permissions-do-not-inherit"></a>Behörigheter ärvs inte

I POSIX modellen som används av Data Lake Storage Gen1, förvaras behörigheter för ett objekt i själva objektet. Behörigheter för ett objekt kan med andra ord inte ärvas från de överordnade objekten.

## <a name="common-scenarios-related-to-permissions"></a>Vanliga scenarier som rör behörigheter

Nedan följer några vanliga scenarier för att förstå vilka behörigheter som krävs för att utföra vissa åtgärder på ett Data Lake Storage Gen1-konto.

|    Åtgärd             |    /    | Seattle / | Portland / | Data.txt     |
|--------------------------|---------|----------|-----------|--------------|
| Läs Data.txt            |   `--X`   |   `--X`    |  `--X`      | `R--`          |
| Lägga till i Data.txt       |   `--X`   |   `--X`    |  `--X`      | `RW-`          |
| Ta bort Data.txt          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Skapa Data.txt          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Lista /                   |   `R-X`   |   `---`    |  `---`      | `---`          |
| Lista /Seattle/           |   `--X`   |   `R-X`    |  `---`      | `---`          |
| Lista /Seattle/Portland /  |   `--X`   |   `--X`    |  `R-X`      | `---`          |


> [!NOTE]
> Skrivbehörigheterna för filen behövs inte att ta bort filen så länge de två villkoren ovan är sanna.
>
>


## <a name="the-super-user"></a>Superanvändaren

En superanvändare har mest behörighet av alla användare i Data Lake Storage Gen1-konto. En superanvändare:

* Har RWX-behörigheter till **alla** filer och mappar.
* Kan ändra behörigheterna för alla filer och mappar.
* Kan ändra ägande användare eller ägande grupp för alla filer och mappar.

Alla användare som är en del av den **ägare** för ett Data Lake Storage Gen1 konto blir automatiskt en superanvändare.

Om du vill skapa en anpassad rollbaserad åtkomstkontroll(RBAC)-roll som har superanvändarbehörigheter så måste den ha följande behörigheter:
- Microsoft.DataLakeStore/accounts/Superuser/action
- Microsoft.Authorization/roleAssignments/write


## <a name="the-owning-user"></a>Ägande användare

Användaren som skapade objektet är automatiskt ägande användare för objektet. En ägande användare kan:

* Ändra behörigheterna för en fil som ägs.
* Ändra ägande grupp för en fil som ägs, så länge den ägande användaren också är medlem i målgruppen.

> [!NOTE]
> Den ägande användaren *kan inte* ändra ägande användare för en fil eller mapp. Endast superanvändare kan ändra ägande användare av en fil eller mapp.
>
>

## <a name="the-owning-group"></a>Ägande grupp

**Bakgrund**

I POSIX-ACL:er är varje användare associerad med en "primär grupp". Användaren "Alice" kan t.ex. tillhöra gruppen "Ekonomi". Alice kan också tillhöra flera grupper, men en grupp anges alltid som hennes primära grupp. När Alice skapar en fil i POSIX ställs den ägande gruppen för filen in som hennes primära grupp, som i det här fallet är "Ekonomi". Den ägande gruppen fungerar annars ungefär som tilldelade behörigheter för andra användare/grupper.

**Koppla den ägande gruppen för en ny fil eller mapp**

* **Fall 1**: Rotmappen "/". Den här mappen skapas när ett Data Lake Storage Gen1 konto skapas. I det här fallet har den ägande gruppen angetts till den användaren som skapade kontot.
* **Fall 2** (alla andra fall): När ett nytt objekt skapas, kopieras den ägande gruppen från den överordnade mappen.

**Ändra den ägande gruppen**

Den ägande gruppen kan ändras av:
* Alla superanvändare.
* Den ägande användaren, om den ägande användaren också är medlem i målgruppen.

> [!NOTE]
> Den ägande gruppen *kan inte* ändra ACL:er för en fil eller mapp.  Även om den ägande gruppen har angetts till användaren som skapade kontot för rotmappen i **Fall 1** ovan, är ett enda användarkonto inte giltigt för att ge behörighet via den ägande gruppen.  Du kan tilldela den här behörigheten till en giltig användargrupp, om det är lämpligt.


## <a name="access-check-algorithm"></a>Algoritm för åtkomstkontroll

Följande pseudocode representerar algoritmen för åtkomstkontroll för Data Lake Storage Gen1 konton.

```
def access_check( user, desired_perms, path ) : 
  # access_check returns true if user has the desired permissions on the path, false otherwise
  # user is the identity that wants to perform an operation on path
  # desired_perms is a simple integer with values from 0 to 7 ( R=4, W=2, X=1). User desires these permissions
  # path is the file or folder
  # Note: the "sticky bit" is not illustrated in this algorithm
  
# Handle super users.
  if (is_superuser(user)) :
    return True

  # Handle the owning user. Note that mask IS NOT used.
  entry = get_acl_entry( path, OWNER )
  if (user == entry.identity)
      return ( (desired_perms & e.permissions) == desired_perms )

  # Handle the named users. Note that mask IS used.
  entries = get_acl_entries( path, NAMED_USERS )
  for entry in entries:
      if (user == entry.identity ) :
          mask = get_mask( path )
          return ( (desired_perms & entry.permmissions & mask) == desired_perms)

  # Handle groups (named groups and owning group)
  member_count = 0
  perms = 0
  for g in get_groups(path) :
    if (user_is_member_of_group(user, g)) :
      member_count += 1
      perms | =  get_perms_for_group(path,g)
  if (member_count>0) :
    return ((desired_perms & perms & mask ) == desired_perms)
 
  # Handle other
  perms = get_perms_for_other(path)
  mask = get_mask( path )
  return ( (desired_perms & perms & mask ) == desired_perms)
```

### <a name="the-mask"></a>Masken

Enligt beskrivningen i algoritmen Kontrollera åtkomst, masken begränsar åtkomst för **namngivna användare**, **ägande grupp**, och **namngivna grupper**.  

> [!NOTE]
> För ett nytt Data Lake Storage Gen1-konto får masken för åtkomst-ACL för rotmappen (”/”) som standard RWX.
>
>

### <a name="the-sticky-bit"></a>Sticky bit

Sticky bit är en mer avancerad funktion i ett POSIX-filsystem. I samband med Data Lake Storage Gen1 är det troligt att sticky bit kommer att behövas. Sammanfattningsvis om sticky bit är aktiverad på en mapp, kan ett underordnat objekt bara tas bort eller byta namn på av det underordnade objektets ägande användare.

Sticky bit visas inte i Azure-portalen.

## <a name="default-permissions-on-new-files-and-folders"></a>Standardbehörigheter för nya filer och mappar

När en ny fil eller mapp skapas under en befintlig mapp, anger standard-ACL:en på den överordnade mappen:

- En underordnad mapps standard-ACL och åtkomst-ACL.
- En underordnad fils åtkomst-ACL (filer har inte en standard-ACL).

### <a name="umask"></a>umask

När du skapar en fil eller mapp, används umask för att ändra hur standard-ACL: er är inställda på det underordnade objektet. umask är ett 9-bitars ett 9-bitars värde för överordnade mappar som innehåller ett RWX-värde för **ägande användare**, **ägande grupp**, och **andra**.

Umask för Azure Data Lake Storage Gen1 en konstant värde som är inställt på 007. Det här värdet motsvarar

| umask-komponent     | Numeriskt format | Kortformat | Betydelse |
|---------------------|--------------|------------|---------|
| umask.owning_user   |    0         |   `---`      | För ägande användare, kopierar du överordnat filens standard-ACL till barnets åtkomst-ACL | 
| umask.owning_group  |    0         |   `---`      | Kopiera överordnat filens standard-ACL till barnets åtkomst-ACL för ägande grupp | 
| umask.Other         |    7         |   `RWX`      | Ta bort alla behörigheter för barnets åtkomst-ACL för andra, |

Umask-värde som används av Azure Data Lake Storage Gen1 effektivt innebär att värdet för andra aldrig skickas som standard på nya underordnade - oavsett vad som anger standard-ACL. 

Följande pseudocode visar hur umask används när du skapar ACL: er för ett underordnat objekt.

```
def set_default_acls_for_new_child(parent, child):
    child.acls = []
    foreach entry in parent.acls :
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

## <a name="common-questions-about-acls-in-data-lake-storage-gen1"></a>Vanliga frågor om ACL: er i Data Lake Storage Gen1

### <a name="do-i-have-to-enable-support-for-acls"></a>Måste jag aktivera stöd för ACL:er?

Nej. Åtkomstkontroll via ACL: er är alltid på ett Data Lake Storage Gen1-konto.

### <a name="which-permissions-are-required-to-recursively-delete-a-folder-and-its-contents"></a>Vilka behörigheter krävs för att rekursivt ta bort en mapp och dess innehåll?

* Den överordnade mappen måste ha behörigheterna **skriva + köra**.
* Mappen som ska tas bort och alla mappar inom den, behöver behörigheterna **läsa + skriva + köra**.

> [!NOTE]
> Du behöver inte Skriv-behörigheter för att ta bort filer i mappar. Dessutom kan rotmappen "/" **aldrig** tas bort.
>
>

### <a name="who-is-the-owner-of-a-file-or-folder"></a>Vem äger en fil eller mapp?

Skaparen av en fil eller mapp blir ägaren.

### <a name="which-group-is-set-as-the-owning-group-of-a-file-or-folder-at-creation"></a>Vilken grupp anges som den ägande gruppen för en fil eller mapp vid skapandet?

Det kopieras från den ägande gruppen för den överordnade mappen under vilken den nya filen eller mappen skapas.

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>Jag är ägande användare av en fil, men jag har inte den RWX-behörighet jag behöver. Vad gör jag nu?

Den ägande användaren kan lätt ändra behörigheterna för filen för att ge sig själva de RWX-behörigheter de behöver.

### <a name="when-i-look-at-acls-in-the-azure-portal-i-see-user-names-but-through-apis-i-see-guids-why-is-that"></a>När jag tittar på ACL:er i Azure-portalen, visas användarnamn men via API:er visas GUID:er. Varför?

Posterna i ACL lagras som GUID:er som motsvarar användare i Azure AD. API:erna returnerar GUID:erna i befintligt skick. Azure-portalen försöker göra det enklare att använda ACL:er genom att översätta GUID:er till bekanta namn när det är möjligt.

### <a name="why-do-i-sometimes-see-guids-in-the-acls-when-im-using-the-azure-portal"></a>Varför visas ibland GUID:er i ACL:er när jag använder Azure-portalen?

En GUID visas när användaren inte finns i Azure AD längre. Detta inträffar vanligtvis när användaren har lämnat företaget eller om kontot har tagits bort i Azure AD.

### <a name="does-data-lake-storage-gen1-support-inheritance-of-acls"></a>Stöder Data Lake Storage Gen1 arv av ACL: er?

Nej, men standard-ACL:er kan användas för att ange ACL:er för underordnade filer och mappar som nyligen skapats under den överordnade mappen.  

### <a name="where-can-i-learn-more-about-posix-access-control-model"></a>Var hittar jag mer information om POSIX-modellen för åtkomstkontroll?

* [POSIX-åtkomstkontrollistor på Linux](https://www.linux.com/news/posix-acls-linux)
* [Guide för HDFS-behörighet](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)
* [Vanliga frågor och svar om POSIX](http://www.opengroup.org/austin/papers/posix_faq.html)
* [POSIX 1003.1 2008](http://standards.ieee.org/findstds/standard/1003.1-2008.html)
* [POSIX 1003.1 2013](http://pubs.opengroup.org/onlinepubs/9699919799.2013edition/)
* [POSIX 1003.1 2016](http://pubs.opengroup.org/onlinepubs/9699919799.2016edition/)
* [POSIX ACL på Ubuntu](https://help.ubuntu.com/community/FilePermissionsACLs)
* [ACL med åtkomstkontrollistor på Linux](http://bencane.com/2012/05/27/acl-using-access-control-lists-on-linux/)

## <a name="see-also"></a>Se också

* [Översikt över Azure Data Lake Storage Gen1](data-lake-store-overview.md)
