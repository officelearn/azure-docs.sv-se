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
ms.openlocfilehash: 86cc1a71bb09ea465621d65f84d2b838cb169a62
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2018
ms.locfileid: "42061099"
---
# <a name="access-control-in-azure-data-lake-storage-gen1"></a>Åtkomstkontroll i Azure Data Lake Storage Gen1

Azure Data Lake Storage Gen1 implementerar en modell för åtkomstkontroll som härstammar från HDFS och i sin tur från POSIX-modellen för åtkomstkontroll. Den här artikeln sammanfattar grunderna i modellen för åtkomstkontroll för Data Lake Storage Gen1. Läs mer om HDFS-modellen för åtkomstkontroll i [Guide för HDFS-behörigheter](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html).

## <a name="access-control-lists-on-files-and-folders"></a>Åtkomstkontrollistor för filer och mappar

Det finns två sorters åtkomstkontrollistor (ACL:er), **Åtkomst-ACL:er** och **Standard-ACL:er**.

* **Åtkomst-ACL:er**: De här kontrollerar åtkomst till ett objekt. Både filer och mappar har åtkomst-ACL:er.

* **Standard-ACL**: En "mall" av ACL:er som är associerad med en mapp som bestämmer åtkomst-ACL:er för underordnade objekt som skapats under mappen. Filer har inte standard-ACL:er.

![Data Lake Storage Gen1 ACL: er](./media/data-lake-store-access-control/data-lake-store-acls-1.png)

Både åtkomst-ACL:er och standard-ACL:er har samma struktur.

![Data Lake Storage Gen1 ACL: er](./media/data-lake-store-access-control/data-lake-store-acls-2.png)



> [!NOTE]
> Att ändra standard-ACL på ett överordnat objekt påverkar inte åtkomst-ACL eller standard-ACL för underordnade objekt som redan finns.
>
>

## <a name="users-and-identities"></a>Användare och identiteter

Alla filer och mappar har olika behörigheter för dessa identiteter:

* Ägande användare av filen
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
| 7            | RWX        | Läsa + skriva + köra |
| 5            | R-X        | Läsa + köra         |
| 4            | R--        | Läsa                   |
| 0            | ---        | Inga behörigheter         |


### <a name="permissions-do-not-inherit"></a>Behörigheter ärvs inte

I POSIX modellen som används av Data Lake Storage Gen1, förvaras behörigheter för ett objekt i själva objektet. Behörigheter för ett objekt kan med andra ord inte ärvas från de överordnade objekten.

## <a name="common-scenarios-related-to-permissions"></a>Vanliga scenarier som rör behörigheter

Nedan följer några vanliga scenarier för att förstå vilka behörigheter som krävs för att utföra vissa åtgärder på ett Data Lake Storage Gen1-konto.

### <a name="permissions-needed-to-read-a-file"></a>Behörigheter som krävs för att läsa en fil

![Data Lake Storage Gen1 ACL: er](./media/data-lake-store-access-control/data-lake-store-acls-3.png)

* För den fil som ska läsas behöver anroparen **Läs**-behörigheter.
* För alla mapparna i mappstrukturen som innehåller filen behöver anroparen **Kör**-behörigheter.

### <a name="permissions-needed-to-append-to-a-file"></a>Behörigheter som krävs för att lägga till i en fil

![Data Lake Storage Gen1 ACL: er](./media/data-lake-store-access-control/data-lake-store-acls-4.png)

* För filen som ska läggas till i behöver anroparen **Skriv**-behörigheter.
* För alla mappar som innehåller filen behöver anroparen **Kör**-behörigheter.

### <a name="permissions-needed-to-delete-a-file"></a>Behörigheter för att ta bort en fil

![Data Lake Storage Gen1 ACL: er](./media/data-lake-store-access-control/data-lake-store-acls-5.png)

* För den överordnade mappen behöver anroparen **Skriv + kör**-behörigheter.
* För alla andra mappar i sökvägen behöver anroparen **Kör**-behörigheter.



> [!NOTE]
> Skrivbehörigheterna för filen behövs inte att ta bort filen så länge de två villkoren ovan är sanna.
>
>

### <a name="permissions-needed-to-enumerate-a-folder"></a>Behörigheter som krävs för att iterera en mapp

![Data Lake Storage Gen1 ACL: er](./media/data-lake-store-access-control/data-lake-store-acls-6.png)

* För att mappen ska iterera behöver anroparen **Läs + kör**-behörigheter.
* För alla överordnade mappar behöver anroparen **Kör**-behörigheter.

## <a name="viewing-permissions-in-the-azure-portal"></a>Visa behörigheter i Azure-portalen

Från den **Datautforskaren** bladet i Data Lake Storage Gen1-kontot, klickar du på **åtkomst** att se ACL: er för filen eller mappen som visas i Datautforskaren. Klicka på **Åtkomst** för att se ACL:er för mappen **katalog** under kontot **mydatastore**.

![Data Lake Storage Gen1 ACL: er](./media/data-lake-store-access-control/data-lake-store-show-acls-1.png)

Överst på det här bladet visas ägarens behörigheter. (På skärmbilden är den ägande användaren Bob.) Efter det så visas de tilldelade åtkomst-ACL:erna. 

![Data Lake Storage Gen1 ACL: er](./media/data-lake-store-access-control/data-lake-store-show-acls-simple-view.png)

Klicka på **Avancerad vy** om du vill se en mer avancerad vy där standard-ACL:er, mask och en beskrivning av superanvändare visas.  Det här bladet ger också ett sätt att rekursivt ange åtkomst- och standard-ACL:er för underordnade filer och mappar baserat på behörigheterna i den aktuella mappen.

![Data Lake Storage Gen1 ACL: er](./media/data-lake-store-access-control/data-lake-store-show-acls-advance-view.png)

## <a name="the-super-user"></a>Superanvändaren

En superanvändare har mest behörighet av alla användare i Data Lake Store. En superanvändare:

* Har RWX-behörigheter till **alla** filer och mappar.
* Kan ändra behörigheterna för alla filer och mappar.
* Kan ändra ägande användare eller ägande grupp för alla filer och mappar.

I Azure har ett Data Lake Storage Gen1 konto flera Azure-roller, inklusive:

* Ägare
* Deltagare
* Läsare

Alla i den **ägare** roll för ett Data Lake Storage Gen1-konto är automatiskt en superanvändare för det kontot. Läs mer i [rollbaserad åtkomstkontroll](../role-based-access-control/role-assignments-portal.md).
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

I POSIX-ACL:er är varje användare associerad med en "primär grupp". Användaren "Alice" kan t.ex. tillhöra gruppen "Ekonomi". Alice kan också tillhöra flera grupper, men en grupp anges alltid som hennes primära grupp. När Alice skapar en fil i POSIX ställs den ägande gruppen för filen in som hennes primära grupp, som i det här fallet är "Ekonomi".

När ett nytt filsystemsobjekt skapats, tilldelar Data Lake Storage Gen1 ett värde till den ägande gruppen.

* **Fall 1**: Rotmappen "/". Den här mappen skapas när ett Data Lake Storage Gen1 konto skapas. I det här fallet har den ägande gruppen angetts till den användaren som skapade kontot.
* **Fall 2** (alla andra fall): När ett nytt objekt skapas, kopieras den ägande gruppen från den överordnade mappen.

Den ägande gruppen fungerar annars ungefär som tilldelade behörigheter för andra användare/grupper.

Den ägande gruppen kan ändras av:
* Alla superanvändare.
* Den ägande användaren, om den ägande användaren också är medlem i målgruppen.

> [!NOTE]
> Den ägande gruppen *kan inte* ändra ACL:er för en fil eller mapp.  Även om den ägande gruppen har angetts till användaren som skapade kontot för rotmappen i **Fall 1** ovan, är ett enda användarkonto inte giltigt för att ge behörighet via den ägande gruppen.  Du kan tilldela den här behörigheten till en giltig användargrupp, om det är lämpligt.

## <a name="access-check-algorithm"></a>Algoritm för åtkomstkontroll

Följande bild visar algoritmen för åtkomstkontroll för Data Lake Storage Gen1 konton.

![Data Lake Storage Gen1 ALC-algoritmer](./media/data-lake-store-access-control/data-lake-store-acls-algorithm.png)


## <a name="the-mask-and-effective-permissions"></a>Mask och "gällande behörigheter"

**Mask** är ett RWX-värde som används för att begränsa åtkomsten för **namngivna användare**, **ägande grupp** och **namngivna grupper** när du utför algoritmen för åtkomstkontroll. Här följer nyckelbegreppen för masken.

* Masken skapar "gällande behörigheter" D.v.s. den modifierar behörigheterna vid tidpunkten för åtkomstkontroll.
* Masken kan redigeras direkt av filens ägare och alla superanvändare.
* Masken kan ta bort behörigheter för att skapa den gällande behörigheten. Masken *kan inte* lägga till behörigheter till den gällande behörigheten.

Låt oss titta på några exempel. Nedan är masken inställd på **RWX**, vilket innebär att masken inte tar bort några behörigheter. De gällande behörigheterna för den namngivna användaren, ägande gruppen och namngivna gruppen ändras inte under åtkomstkontrollen.

![Data Lake Storage Gen1 ACL: er](./media/data-lake-store-access-control/data-lake-store-acls-mask-1.png)

I exemplet nedan anges masken till **R-X**. Så den **inaktiverar skrivbehörighet** för **namngiven användare**, **ägande grupp** och **namngiven grupp** vid tidpunkten för åtkomstkontroll.

![Data Lake Storage Gen1 ACL: er](./media/data-lake-store-access-control/data-lake-store-acls-mask-2.png)

Som referens är det här masken för en fil eller mapp visas i Azure-portalen.

![Data Lake Storage Gen1 ACL: er](./media/data-lake-store-access-control/data-lake-store-show-acls-mask-view.png)

> [!NOTE]
> För ett nytt Data Lake Storage Gen1-konto får masken för åtkomst-ACL för rotmappen (”/”) som standard RWX.
>
>

## <a name="permissions-on-new-files-and-folders"></a>Behörigheter för nya filer och mappar

När en ny fil eller mapp skapas under en befintlig mapp, anger standard-ACL:en på den överordnade mappen:

- En underordnad mapps standard-ACL och åtkomst-ACL.
- En underordnad fils åtkomst-ACL (filer har inte en standard-ACL).

### <a name="the-access-acl-of-a-child-file-or-folder"></a>En underordnad fils eller mapps åtkomst-ACL

När en underordnad fil eller mapp skapas, kopieras den överordnade filens standard-ACL till den underordnade filens eller mappens åtkomst-ACL. Även om **andra** användare har RWX-behörigheter i det överordnade objektets standard-ACL, tas de bort från det underordnade objektets åtkomst-ACL.

![Data Lake Storage Gen1 ACL: er](./media/data-lake-store-access-control/data-lake-store-acls-child-items-1.png)

I de flesta fall är ovanstående information allt du behöver känna till om hur ett underordnat objekts åtkomst-ACL bestäms. Om du är bekant med POSIX-system och vill förstå mer djupgående hur transformationen uppnås finns information i avsnittet [Umasks roll vid skapandet av åtkomst-ACL för nya filer och mappar](#umasks-role-in-creating-the-access-acl-for-new-files-and-folders) senare i den här artikeln.


### <a name="a-child-folders-default-acl"></a>En underordnad mapps standard-ACL

När en underordnad mapp skapas under en överordnad mapp, kopieras den överordnade mappens standard-ACL över, som den är, till den underordnade mappens standard-ACL.

![Data Lake Storage Gen1 ACL: er](./media/data-lake-store-access-control/data-lake-store-acls-child-items-2.png)

## <a name="advanced-topics-for-understanding-acls-in-data-lake-storage-gen1"></a>Avancerade ämnen för att förstå ACL: er i Data Lake Storage Gen1

Här följer lite avancerad information som hjälper dig att förstå hur ACL: er bestäms för Data Lake Storage Gen1 filer eller mappar.

### <a name="umasks-role-in-creating-the-access-acl-for-new-files-and-folders"></a>Umasks roll är att skapa åtkomst-ACL för nya filer och mappar

I ett POSIX-kompatibelt system är allmänna begrepp som umask ett 9-bitars värde för den överordnade mappen som används för att omvandla behörigheten för **ägande användare**, **ägande grupp** och **andra** på en ny underordnad fils eller mapps åtkomst-ACL. En umasks bitar identifierar vilka bitar som ska inaktiveras i det underordnade objektets åtkomst-ACL. Den används därför för att selektivt förhindra spridning av behörigheter för **ägande användare**, **ägande grupp** och **övriga**.

I ett HDFS-system är umask vanligtvis ett konfigurationsalternativ för hela platsen som styrs av administratörer. Data Lake Storage Gen1 använder en **umask för konto** som inte kan ändras. I följande tabell visas umask för Data Lake Storage Gen1.

| Användargrupp  | Inställning | Effekt av nytt underordnade objekts åtkomst-ACL |
|------------ |---------|---------------------------------------|
| Ägande användare | ---     | Ingen effekt                             |
| Ägande grupp| ---     | Ingen effekt                             |
| Annat       | RWX     | Ta bort läsa + skriva + köra         |

Följande bild visar denna umask när den är aktiv. Nettoeffekten är att ta bort **läsa + skriva + köra** för **andra** användare. Eftersom umask inte har angett bitar för **ägande användare** och **ägande grupp**, omvandlas inte dessa behörigheter.

![Data Lake Storage Gen1 ACL: er](./media/data-lake-store-access-control/data-lake-store-acls-umask.png)

### <a name="the-sticky-bit"></a>Sticky bit

Sticky bit är en mer avancerad funktion i ett POSIX-filsystem. I samband med Data Lake Storage Gen1 är det troligt att sticky bit kommer att behövas.

I följande tabell visar hur sticky bit fungerar i Data Lake Storage Gen1.

| Användargrupp         | Fil    | Mapp |
|--------------------|---------|-------------------------|
| Sticky bit **AV** | Ingen effekt   | Ingen effekt.           |
| Sticky bit **PÅ**  | Ingen effekt   | Förhindrar alla utom **superanvändare** och **ägande användare** av ett underordnat objekt från att ta bort eller byta namn på det underordnade objektet.               |

Sticky bit visas inte i Azure-portalen.

## <a name="common-questions-about-acls-in-data-lake-storage-gen1"></a>Vanliga frågor om ACL: er i Data Lake Storage Gen1

Här är några frågor som ofta kommer upp rörande ACL: er i Data Lake Storage Gen1.

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

### <a name="what-is-the-difference-between-mask-and-umask"></a>Vad är skillnaden mellan mask och umask?

| mask | umask|
|------|------|
| Egenskapen **mask** är tillgänglig på varje fil och mapp. | Den **umask** är en egenskap för Data Lake Storage Gen1-konto. Så finns det bara en enda umask i Data Lake Storage Gen1.    |
| Maskegenskapen på en fil eller mapp kan ändras av ägande användare eller ägande grupp för en fil eller en superanvändare. | Umask-egenskapen kan inte ändras av någon användare, inte ens en superanvändare. Det är ett konstant värde som inte ändras.|
| Maskegenskapen används under algoritmen för åtkomstkontroll vid körning för att avgöra om en användare har behörighet att utföra åtgärden på en fil eller mapp. Maskrollen är att skapa "gällande behörigheter" vid tidpunkten för åtkomstkontroll. | Umask används inte vid åtkomstkontroll alls. Umask används för att bestämma åtkomst-ACL för de nya underordnade objekten i en mapp. |
| Masken är ett 3-bitars RWX-värde som gäller för en namngiven användare, ägande grupp och namngiven grupp vid tidpunkten för åtkomstkontroll.| Umask är ett 9-bitarsvärde som gäller för ägande användare, ägande grupper och **övriga** för ett nytt underordnat objekt.|

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
