---
title: Identitet identitetssynkronisering och duplicerad attributåterhämtning | Microsoft Docs
description: Nya beteendet för hur du hanterar objekt med UPN eller ProxyAddress konflikter under katalogsynkronisering med Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 537a92b7-7a84-4c89-88b0-9bce0eacd931
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: f8fe86712b59a896d1f431ef677f592ced382db8
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46312076"
---
# <a name="identity-synchronization-and-duplicate-attribute-resiliency"></a>Identitetssynkronisering och duplicerad attributåterhämtning
Duplicerad Attributåterhämtning är en funktion i Azure Active Directory åtgärdar problem som orsakas av **UserPrincipalName** och **ProxyAddress** står i konflikt när du kör något av Microsofts synkroniseringsverktyg för.

Dessa två attribut Allmänt måste vara unikt för alla **användaren**, **grupp**, eller **Kontakta** objekt i en viss Azure Active Directory-klient.

> [!NOTE]
> Endast användare kan ha UPN-namn.
> 
> 

Den nya funktionen som gör att den här funktionen ingår i molndelen av sync-pipeline, därför är det klienten oberoende och relevanta för en produkt för synkronisering av Microsoft, inklusive Azure AD Connect, DirSync och MIM + Connector. Den allmänna termen ”Synkroniseringsklienten” används i det här dokumentet för att representera någon av dessa produkter.

## <a name="current-behavior"></a>Aktuella beteendet
Om det finns ett försök att etablera ett nytt objekt med ett UPN eller ProxyAddress-värde som bryter mot den här unikhetsbegränsningen, blockerar objektet skapas i Azure Active Directory. På samma sätt, om ett objekt uppdateras med en icke-unikt UPN eller ProxyAddress, misslyckas uppdateringen. Etableringsförsök eller uppdatering görs av Synkroniseringsklienten vid varje cykel för export och fortsätter att utföras förrän konflikten har lösts. Ett e-postmeddelande med fel rapporten genereras vid varje försök och ett fel loggas av Synkroniseringsklienten.

## <a name="behavior-with-duplicate-attribute-resiliency"></a>Beteende med duplicerad Attributåterhämtning
Istället för att helt föra att etablera eller uppdatera ett objekt med Duplicerat attribut, Azure Active Directory ”placerar i karantän” dubblettattribut som skulle strida mot unikhetsbegränsningen. Om det här attributet är obligatoriskt för etablering som UserPrincipalName, tilldelar ett platshållarvärde i tjänsten. Formatet för dessa tillfälliga värden är  
”***<OriginalPrefix>+ < 4DigitNumber > @<InitialTenantDomain>. onmicrosoft.com***”.  
Om attributet inte är obligatoriskt, som en **ProxyAddress**, Azure Active Directory bara placerar i karantän attributet konflikt och fortsätter med objektskapande eller update.

Vid sätta attributet skickas information om konflikten i samma fel rapporten e-postmeddelandet används i den gamla funktionen. Men den här informationen visas bara i felrapporten en gång om karantänen inträffar kan det inte fortsätter att loggas i framtida e-postmeddelanden. Dessutom eftersom exporten av det här objektet har slutförts Synkroniseringsklienten loggar inte ett fel och gör inget nytt försök att skapa / uppdatera igen vid nästa synkroniseringscykler.

Ett nytt attribut har lagts till objektklasser användare, grupp och kontakta för att stödja det här beteendet:  
**DirSyncProvisioningErrors**

Det här är ett attribut med flera värden som används för att lagra de motstridiga attribut som skulle strida mot unikhetsbegränsningen ska de läggas normalt. En bakgrundsaktivitet timern har aktiverats i Azure Active Directory som körs varje timme efter dubblettattribut konflikter som har blivit lösta och automatiskt att ta bort attributen i fråga från karantänen.

### <a name="enabling-duplicate-attribute-resiliency"></a>Aktiverar återhämtning av Duplicerat attribut
Duplicerad Attributåterhämtning blir den nya standardfunktionen för alla Azure Active Directory-klienter. Den ska visas på som standard för alla klienter som aktiverats synkronisering för första gången den 22 augusti 2016 eller senare. Klienter som aktiverat synkronisering före detta datum har funktionen aktiverad i batchar. Den här distributionen börjar i September 2016 och ett e-postmeddelande skickas till varje klient tekniska meddelanden kontakten med det datum när funktionen aktiveras.

> [!NOTE]
> När duplicerad Attributåterhämtning har aktiverats kan inte inaktiveras.

Om du vill kontrollera om funktionen är aktiverad för din klient, kan du göra det genom att hämta den senaste versionen av Azure Active Directory PowerShell-modulen och kör:

`Get-MsolDirSyncFeatures -Feature DuplicateUPNResiliency`

`Get-MsolDirSyncFeatures -Feature DuplicateProxyAddressResiliency`

> [!NOTE]
> Du kan inte längre använda cmdlet Set-MsolDirSyncFeature för att aktivera funktionen duplicerad Attributåterhämtning proaktivt innan den är aktiverad för din klient. Om du vill kunna testa funktionen behöver du skapa en ny Azure Active Directory-klient.

## <a name="identifying-objects-with-dirsyncprovisioningerrors"></a>Identifierar objekt med DirSyncProvisioningErrors
Det finns för närvarande två sätt att identifiera objekt som har de här felen på grund av duplicerade egenskapsvärden konflikter, Azure Active Directory PowerShell och administrationsportalen för Office 365. Det finns planer på att utöka till ytterligare portal baserat rapportering i framtiden.

### <a name="azure-active-directory-powershell"></a>Azure Active Directory PowerShell
För PowerShell-cmdletar i det här avsnittet stämmer följande:

* Alla av följande cmdletar är skiftlägeskänsliga.
* Den **– ErrorCategory PropertyConflict** måste alltid tas. Det finns för närvarande inga andra typer av **ErrorCategory**, men detta kan utökas i framtiden.

Först kom igång genom att köra **Connect-MsolService** och ange autentiseringsuppgifter för en klientadministratör.

Sedan använder du följande cmdlet: ar och operatörer för att visa fel på olika sätt:

1. [Visa alla](#see-all)
2. [Genom att egenskapstyp](#by-property-type)
3. [Med motstridiga värde](#by-conflicting-value)
4. [Med hjälp av en sträng-sökning](#using-a-string-search)
5. [Sorterade](#sorted)
6. [I ett begränsat antal eller alla](#in-a-limited-quantity-or-all)

#### <a name="see-all"></a>Se alla
När du är ansluten, om du vill se en allmän lista över attribut etablering kör fel i klienten:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict`

Detta ger ett resultat som liknar följande:  
 ![Get-MsolDirSyncProvisioningError](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/1.png "Get-MsolDirSyncProvisioningError")  

#### <a name="by-property-type"></a>Genom att egenskapstyp
Om du vill se fel av typ vlastnosti, lägger du till den **- PropertyName** flaggan med den **UserPrincipalName** eller **ProxyAddresses** argument:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName UserPrincipalName`

Eller

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName ProxyAddresses`

#### <a name="by-conflicting-value"></a>Med motstridiga värde
Du får felmeddelanden som rör en viss egenskap lägger du till den **- egenskapsvärde** flaggan (**- PropertyName** måste också användas när du lägger till den här flaggan):

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyValue User@domain.com -PropertyName UserPrincipalName`

#### <a name="using-a-string-search"></a>Med hjälp av en sträng-sökning
Att göra en bred sträng search använder den **- SearchString** flaggan. Detta kan användas fristående från alla ovanstående flaggor, med undantag för **- ErrorCategory PropertyConflict**, vilket krävs alltid:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -SearchString User`

#### <a name="in-a-limited-quantity-or-all"></a>I ett begränsat antal eller alla
1. **MaxResults <Int>**  kan användas för att begränsa frågan till ett visst antal värden.
2. **Alla** kan användas för att se till att alla resultat hämtas om det finns ett stort antal fel.

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -MaxResults 5`

## <a name="office-365-admin-portal"></a>Administrationsportalen för Office 365
Du kan visa directory synkroniseringsfel i administrationscentret för Office 365. Rapporten i Office 365-portalen visar endast **användaren** objekt som har de här felen. Den visar inte information om konflikter mellan **grupper** och **kontakter**.

![Aktiva användare](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/1234.png "aktiva användare")

Anvisningar om hur du visar katalogen synkroniseringsfel i Office 365 Administrationscenter finns i [identifiera directory synkroniseringsfel i Office 365](https://support.office.com/en-us/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067).

### <a name="identity-synchronization-error-report"></a>Felrapport för synkronisering av identitet
När ett objekt med en dubblettattribut konflikt hanteras med den här nya funktionen ett meddelande som ingår i standard identitet felrapport för synkronisering av e-postmeddelandet som skickas till tekniska meddelanden kontakta för klienten. Det finns dock en viktig förändring i det här beteendet. Tidigare skulle information om en konflikt dubblettattribut inkluderas i varje efterföljande felrapport tills konflikten har lösts. Med den här nya funktionen visas endast felmeddelanden för en viss konflikt en gång – då attributet som står i konflikt har placerats i karantän.

Här är ett exempel på hur e-postmeddelande som ser ut för en ProxyAddress-konflikt:  
    ![Aktiva användare](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/6.png "aktiva användare")  

## <a name="resolving-conflicts"></a>Lösa konflikter
Felsöka strategi och upplösning taktik för dessa fel bör inte skiljer sig från hanteringen av fel med duplicerade attribut har i förflutna. Den enda skillnaden är att timer-uppgift rensar via klient på tjänstsidan att automatiskt lägga till attributet i fråga till rätt objekt när konflikten har lösts.

I följande artikel beskriver olika strategier för felsökning och lösningar: [dubblett eller ogiltigt attribut hindrar katalogsynkronisering i Office 365](https://support.microsoft.com/kb/2647098).

## <a name="known-issues"></a>Kända problem
Ingen av de här problemen orsakar förluster eller tjänsten försämring av data. Flera av dem är aesthetic, andra orsaka standard ”*före återhämtning*” fel med duplicerade attribut undantagsfel i stället för att sätta i karantän attributet konflikt och en annan gör vissa fel att kräva extra manuell åtgärds-.

**Core beteende:**

1. Objekt med specifika attributkonfigurationer fortfarande får exportfel till skillnad från det duplicerade attribut karantän.  
   Exempel:
   
    a. Ny användare skapas i AD med en UPN för **Joe@contoso.com** och ProxyAddress **smtp:Joe@contoso.com**
   
    b. Egenskaperna för det här objektet är i konflikt med en befintlig grupp, där ProxyAddress är **SMTP:Joe@contoso.com**.
   
    c. Vid export en **ProxyAddress konflikt** fel inträffar i stället för att konflikten attribut i karantän. Åtgärden görs vid varje efterföljande synkroniseringscykel eftersom det skulle ha varit innan funktionen återhämtning aktiverades.
2. Om två grupper skapas lokalt med samma SMTP-adress, ett inte går att etablera på det första försöket med en standard dubblett **ProxyAddress** fel. Dock dubblettvärden är korrekt i karantän vid nästa synkroniseringscykel.

**Office-portalen rapporten**:

1. Det detaljerade felmeddelandet för två objekt i en UPN-konflikt är samma. Detta anger att de har både haft sina UPN ändrats / i karantän när i själva verket bara en av dem hade några data ändras.
2. Det detaljerade felmeddelandet för en UPN-konflikt visar fel displayName för en användare som har haft sina UPN ändrats/i karantän. Exempel:
   
    a. **Användare A** synkroniseras först med **UPN = User@contoso.com** .
   
    b. **Användare B** försökte synkroniseras nästa med **UPN = User@contoso.com** .
   
    c. **Användare B: s** UPN ändras till **User1234@contoso.onmicrosoft.com** och **User@contoso.com** läggs till i **DirSyncProvisioningErrors**.
   
    d. Felmeddelandet för **användare B** ska indikera som **användare A** har redan **User@contoso.com** så som visas i ett UPN, men det **användare B** egna displayName.

**Felrapport för synkronisering av identitet**:

Länken för *anvisningar om hur du löser problemet* är felaktig:  
    ![Aktiva användare](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/6.png "aktiva användare")  

Det måste peka på [ https://aka.ms/duplicateattributeresiliency ](https://aka.ms/duplicateattributeresiliency).

## <a name="see-also"></a>Se också
* [Azure AD Connect-synkronisering](how-to-connect-sync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)
* [Identifiera directory synkroniseringsfel i Office 365](https://support.office.com/en-us/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067)

