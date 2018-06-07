---
title: Identitet synkronisering och dubblett attributet återhämtning | Microsoft Docs
description: Nya beteendet för hur du hanterar objekt med UPN- eller ProxyAddress konflikter under katalogsynkronisering med Azure AD Connect.
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
ms.author: markvi
ms.openlocfilehash: cfed9d32e919cc3c1b7b9c2a6ea5ddb31f2a8fb9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34593216"
---
# <a name="identity-synchronization-and-duplicate-attribute-resiliency"></a>Identitetssynkronisering och duplicerad attributåterhämtning
Dubbla attribut återhämtning är en funktion i Azure Active Directory som eliminerar friktion som orsakas av **UserPrincipalName** och **ProxyAddress** när du kör någon av Microsofts synkroniseringsverktyg i konflikt.

Dessa två attribut vanligtvis måste vara unikt för alla **användare**, **grupp**, eller **Kontakta** objekt i en viss Azure Active Directory-klient.

> [!NOTE]
> Endast användare kan ha UPN-namn.
> 
> 

Den nya funktionen som gör att den här funktionen är i molndelen av sync-pipeline, är det därför klienten storleksoberoende och relevant för varje produkt för synkronisering av Microsoft inklusive Azure AD Connect, DirSync och MIM + Connector. Den allmänna termen ”synkroniseringsklient” används i det här dokumentet för att representera någon av dessa produkter.

## <a name="current-behavior"></a>Aktuella beteende
Om det finns ett försök att etablera ett nytt objekt med ett UPN eller ProxyAddress värde som bryter mot denna unikhetsbegränsningen, blockerar objektet skapas i Azure Active Directory. På liknande sätt, om ett objekt uppdateras med en icke-unikt UPN- eller ProxyAddress uppdateringen misslyckas. Etablering försök eller uppdatering igen av synkroniseringsklient vid varje cykel för export och fortsätter att inte förrän konflikten har lösts. Ett e-postmeddelande med fel rapporten genereras vid varje försök och ett fel loggas av sync-klienten.

## <a name="behavior-with-duplicate-attribute-resiliency"></a>Problemet med Duplicerat attribut återhämtning
I stället för att helt misslyckas att etablera eller uppdatera ett objekt med ett dubblettattribut, Azure Active Directory ”sätts i karantän” dubblettattribut som skulle strider mot unikhetsbegränsningen. Om det här attributet är obligatoriskt för etablering som UserPrincipalName, tilldelas tjänsten en platshållarvärde. Formatet på dessa tillfälliga värden är  
”***<OriginalPrefix>+ < 4DigitNumber > @<InitialTenantDomain>. onmicrosoft.com***”.  
Om attributet inte är obligatoriskt, som en **ProxyAddress**, Azure Active Directory bara placerar i karantän attributet konflikt och fortsätter med att skapa eller uppdatera.

När attributet sätta i karantän, skickas information om konflikten i samma fel rapporten e-postmeddelandet används i den gamla funktionen. Men den här informationen visas bara i felrapporten en gång när karantän sker det fortsätter inte ska loggas i framtida e-postmeddelanden. Dessutom eftersom exporten för det här objektet har slutförts sync-klienten inte logga in med ett fel och inte försök skapa / uppdatera igen vid nästa synkroniseringscykler.

Ett nytt attribut har lagts till objektklasser användare, grupp och kontakta för att stödja det här problemet:  
**DirSyncProvisioningErrors**

Detta är ett flervärdesattribut som används för att lagra de motstridiga attribut som skulle strider mot unikhetsbegränsningen ska de läggas till normalt. En bakgrundsaktivitet timern har aktiverats i Azure Active Directory som körs varje timme för att leta efter dubblettattribut konflikter som har lösts och tar automatiskt bort attribut i fråga från karantän.

### <a name="enabling-duplicate-attribute-resiliency"></a>Aktiverar återhämtning Duplicerat attribut
Dubbla attribut återhämtning blir den nya standardfunktionen över alla Azure Active Directory-klienter. Den kommer att som standard för alla klienter som aktiverad synkronisering för första gången på 22 augusti 2016 eller senare. Klienter som aktiverad synkronisering innan detta datum har funktionen är aktiverad i batchar. Den här distributionen börjar i September 2016 och ett e-postmeddelande skickas till varje klient tekniskt meddelande kontakt med ett visst datum när funktionen aktiveras.

> [!NOTE]
> När återhämtning Duplicerat attribut har aktiverats kan inte inaktiveras.

Om du vill kontrollera om funktionen är aktiverad för din klient, kan du göra det genom att hämta den senaste versionen av Azure Active Directory PowerShell-modulen och kör:

`Get-MsolDirSyncFeatures -Feature DuplicateUPNResiliency`

`Get-MsolDirSyncFeatures -Feature DuplicateProxyAddressResiliency`

> [!NOTE]
> Du kan inte längre använda cmdlet Set-MsolDirSyncFeature för att aktivera funktionen duplicera attribut återhämtning proaktivt innan det är aktiverat för din klient. Om du vill testa funktionen, behöver du skapa en ny Azure Active Directory-klient.

## <a name="identifying-objects-with-dirsyncprovisioningerrors"></a>Identifiera objekt med DirSyncProvisioningErrors
Det finns för närvarande två metoder för att identifiera objekt som har de här felen på grund av duplicerade konflikter, Azure Active Directory PowerShell och administrationsportalen för Office 365. Det finns planer för att utöka till ytterligare portal baseras reporting i framtiden.

### <a name="azure-active-directory-powershell"></a>Azure Active Directory PowerShell
För PowerShell-cmdlets i det här avsnittet är följande sant:

* Alla följande cmdlets är skiftlägeskänsliga.
* Den **– ErrorCategory PropertyConflict** måste alltid finnas. Det finns för närvarande inga andra typer av **ErrorCategory**, men detta kan utökas i framtiden.

Kom igång genom att köra först **Anslut MsolService** och ange autentiseringsuppgifter för en klientadministratör.

Använd sedan följande cmdlets och operatörer för att visa fel på olika sätt:

1. [Visa alla](#see-all)
2. [Av egenskapstyp](#by-property-type)
3. [Med motstridiga värde](#by-conflicting-value)
4. [Med hjälp av en sträng-sökning](#using-a-string-search)
5. [Sortera](#sorted)
6. [I ett begränsat antal eller alla](#in-a-limited-quantity-or-all)

#### <a name="see-all"></a>Se alla
När du är ansluten, om du vill se en allmän lista över attribut etablering kör fel i klienten:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict`

Detta ger ett resultat som liknar följande:  
 ![Get-MsolDirSyncProvisioningError](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/1.png "Get-MsolDirSyncProvisioningError")  

#### <a name="by-property-type"></a>Av egenskapstyp
Om du vill se felen genom egenskapstyp lägger du till den **- PropertyName** flagga med den **UserPrincipalName** eller **ProxyAddresses** argument:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName UserPrincipalName`

Eller

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName ProxyAddresses`

#### <a name="by-conflicting-value"></a>Med motstridiga värde
Se fel relaterade till en specifik egenskap lägga till den **- PropertyValue** flagga (**- PropertyName** måste användas samt när du lägger till den här flaggan):

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyValue User@domain.com -PropertyName UserPrincipalName`

#### <a name="using-a-string-search"></a>Med hjälp av en sträng-sökning
Att göra en bred sträng Sök använder den **- söksträng** flaggan. Detta kan användas oberoende från alla ovanstående flaggor, med undantag av **- ErrorCategory PropertyConflict**, vilket krävs alltid:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -SearchString User`

#### <a name="in-a-limited-quantity-or-all"></a>I ett begränsat antal eller alla
1. **MaxResults <Int>**  kan användas för att begränsa frågan till ett visst antal värden.
2. **Alla** kan användas för att se till att alla resultat hämtas i de fall som ett stort antal fel finns.

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -MaxResults 5`

## <a name="office-365-admin-portal"></a>Administrationsportalen för Office 365
Du kan visa directory synkroniseringsfel i administrationscentret för Office 365. Rapporten i Office 365-portalen visas bara **användaren** objekt som har dessa fel. Visar inte information om konflikter mellan **grupper** och **kontakter**.

![Aktiva användare](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/1234.png "aktiva användare")

Instruktioner om hur du visar katalogen synkroniseringsfel i administrationscentret för Office 365 finns i [identifiera directory synkroniseringsfel i Office 365](https://support.office.com/en-us/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067).

### <a name="identity-synchronization-error-report"></a>Identitet felrapport för synkronisering
När ett objekt med en dubblettattribut konflikt hanteras med den här nya funktionen ett meddelande som ingår i standard identitet felrapport för synkronisering av e-postmeddelandet som skickas till tekniska meddelandet kontakta för klienten. Det finns dock en viktig förändring i det här beteendet. Tidigare skulle information om en konflikt dubblettattribut tas med i varje efterföljande felrapport tills konflikten har lösts. Med den här nya funktionen visas endast felmeddelanden för en given konflikt när - när attributet som står i konflikt har satts i karantän.

Här är ett exempel på e-postmeddelande ser ut för en ProxyAddress konflikt:  
    ![Aktiva användare](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/6.png "aktiva användare")  

## <a name="resolving-conflicts"></a>Lösa konflikter
Felsökning av metoder för strategi och upplösning för de här felen bör inte skilja sig från hanteringen fel med Duplicerat attribut har passerats. Den enda skillnaden är att timer-uppgift sveps via klient på tjänstsidan-att automatiskt lägga till attributet i fråga till rätt objekt när konflikten har lösts.

I följande artikel beskrivs olika strategier för felsökning och upplösning: [dubblett eller ogiltigt attribut förhindra katalogsynkronisering i Office 365](https://support.microsoft.com/kb/2647098).

## <a name="known-issues"></a>Kända problem
Ingen av de här problemen gör att data går förlorade eller tjänst försämras. Flera av dem är tycke, andra orsaka standard ”*före återhämtning*” dubblettattribut fel uppstod i stället för att sätta i karantän attributet konflikt och en annan gör vissa fel kräver extra Manuell korrigering av.

**Core beteende:**

1. Objekt med ett specifikt attribut konfigurationer fortsätter att få exportfel och dubbla attribut i karantän.  
   Exempel:
   
    a. Ny användare skapas i AD med UPN för **Joe@contoso.com** och ProxyAddress **smtp:Joe@contoso.com**
   
    b. Egenskaperna för det här objektet är i konflikt med en befintlig grupp, där ProxyAddress är **SMTP:Joe@contoso.com**.
   
    c. Vid export en **ProxyAddress konflikt** felmeddelande i stället för att konflikten attribut i karantän. Åtgärden försöks vid varje efterföljande synkronisering cykel som skulle ha varit innan funktionen återhämtning aktiverades.
2. Om två grupper skapas lokalt med samma SMTP-adress, en misslyckas att etablera vid första försöket med en standard dubblett **ProxyAddress** fel. Dock dubblettvärden är korrekt i karantän vid nästa synkronisering cykel.

**Office-portalen rapporten**:

1. Detaljerat felmeddelande för två objekt i en konflikt UPN-mängd är samma. Detta anger att de har både haft deras UPN ändras / sätts i karantän, när i själva verket bara en av dem har alla data som har ändrats.
2. Detaljerat felmeddelande för en UPN-konflikt visar fel displayName för en användare som har haft deras UPN ändras/i karantän. Exempel:
   
    a. **Användare A** synkroniseras först med **UPN = User@contoso.com** .
   
    b. **Användare B** försökte synkroniseras nästa med **UPN = User@contoso.com** .
   
    c. **Användare B** UPN ändras till **User1234@contoso.onmicrosoft.com** och **User@contoso.com** har lagts till i **DirSyncProvisioningErrors**.
   
    d. Felmeddelandet för **användare B** ska visa att **användare A** har redan **User@contoso.com** som ett UPN, men den visar **användare B** egna displayName.

**Identitet synkronisering felrapporten**:

Länken för *instruktioner för hur du löser problemet* är felaktig:  
    ![Aktiva användare](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/6.png "aktiva användare")  

Den ska peka på [ https://aka.ms/duplicateattributeresiliency ](https://aka.ms/duplicateattributeresiliency).

## <a name="see-also"></a>Se också
* [Azure AD Connect-synkronisering](active-directory-aadconnectsync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md)
* [Identifiera directory synkroniseringsfel i Office 365](https://support.office.com/en-us/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067)

