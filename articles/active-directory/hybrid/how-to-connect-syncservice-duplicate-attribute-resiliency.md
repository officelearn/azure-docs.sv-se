---
title: Identitetssynkronisering och dubblettattributåterslutning | Microsoft-dokument
description: Nytt beteende för hur du hanterar objekt med UPN- eller ProxyAddress-konflikter under katalogsynkronisering med Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 537a92b7-7a84-4c89-88b0-9bce0eacd931
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/15/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5585f0cd04dca4145f0322db9d625e35372b24b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78298351"
---
# <a name="identity-synchronization-and-duplicate-attribute-resiliency"></a>Identitetssynkronisering och duplicerad attributåterhämtning
Duplicera attributåtersåterslag är en funktion i Azure Active Directory som eliminerar friktion som orsakas av Konflikter i **UserPrincipalName** och SMTP **ProxyAddress** när du kör något av Microsofts synkroniseringsverktyg.

Dessa två attribut måste i allmänhet vara unika i alla **användar-,** **grupp-** eller **kontaktobjekt** i en viss Azure Active Directory-klientorganisation.

> [!NOTE]
> Endast användare kan ha UPN.
> 
> 

Det nya beteendet som den här funktionen aktiverar finns i molndelen av synkroniseringspipelinen, därför är den klientoberoende och relevant för alla Microsoft-synkroniseringsprodukter, inklusive Azure AD Connect, DirSync och MIM + Connector. Den allmänna termen "synkroniseringsklient" används i det här dokumentet för att representera någon av dessa produkter.

## <a name="current-behavior"></a>Nuvarande beteende
Om det finns ett försök att etablera ett nytt objekt med ett UPN- eller ProxyAddress-värde som bryter mot den här unika begränsningen, blockerar Azure Active Directory objektet från att skapas. På samma sätt, om ett objekt uppdateras med en icke-unik UPN eller ProxyAddress, misslyckas uppdateringen. Etableringsförsöket eller uppdateringen görs på nytt av synkroniseringsklienten vid varje exportcykel och fortsätter att misslyckas tills konflikten har lösts. Ett e-postmeddelande med en felrapport genereras vid varje försök och ett fel loggas av synkroniseringsklienten.

## <a name="behavior-with-duplicate-attribute-resiliency"></a>Beteende med dubblettattributåtersåtershet
I stället för att helt misslyckas med att etablera eller uppdatera ett objekt med ett dubblettattribut, Azure Active Directory "karantän" dubblettattributet som skulle bryta mot unikhetsbegränsningen. Om det här attributet krävs för etablering, till exempel UserPrincipalName, tilldelar tjänsten ett platshållarvärde. Formatet på dessa temporära värden är  
_**OriginalPrefix\<>+ 4DigitNumber \@ \<>InitialTenantDomain>.onmicrosoft.com . \<**_

Attributåtersåterskompatibilitetsprocessen hanterar endast UPN- och SMTP **ProxyAddress-värden.**

Om attributet inte krävs, till exempel en **ProxyAddress,** sätter Azure Active Directory helt enkelt konfliktattributet och fortsätter med att skapa eller uppdatera objektet.

När attributet har kvarstå skickas information om konflikten i samma e-postmeddelande med felrapport som används i det gamla beteendet. Den här informationen visas dock bara i felrapporten en gång, när karantänen inträffar fortsätter den inte att loggas i framtida e-postmeddelanden. Eftersom exporten för det här objektet har lyckats loggar synkroniseringsklienten inte ett fel och försöker inte återskapa åtgärden skapa/uppdatera vid efterföljande synkroniseringscykler.

För att stödja detta har ett nytt attribut lagts till i objektklasserna Användare, Grupp och Kontakt:  
**DirSyncProvisioningErrors**

Detta är ett attribut med flera värden som används för att lagra de attribut som står i konflikt som skulle bryta mot unikhetsbegränsningen om de läggs till normalt. En bakgrundstimmaraktivitet har aktiverats i Azure Active Directory som körs varje timme för att leta efter dubblettattributkonflikter som har lösts och automatiskt tar bort attributen i fråga från karantänen.

### <a name="enabling-duplicate-attribute-resiliency"></a>Aktivera duplicerad attributåtersåtershet
Duplicera attributåtersåterhet är det nya standardbeteendet för alla Azure Active Directory-klienter. Den kommer att vara aktiverad som standard för alla klienter som aktiverade synkronisering för första gången den 22 augusti 2016 eller senare. Klienter som aktiverade synkronisering före detta datum har funktionen aktiverad i batchar. Den här distributionen börjar i september 2016 och ett e-postmeddelande skickas till varje klients tekniska meddelandekontakt med det specifika datum då funktionen aktiveras.

> [!NOTE]
> När duplicera attributåtersåtersåterslag har aktiverats kan den inte inaktiveras.

Om du vill kontrollera om funktionen är aktiverad för din klientorganisation kan du göra det genom att hämta den senaste versionen av Azure Active Directory PowerShell-modulen och köra:

`Get-MsolDirSyncFeatures -Feature DuplicateUPNResiliency`

`Get-MsolDirSyncFeatures -Feature DuplicateProxyAddressResiliency`

> [!NOTE]
> Du kan inte längre använda Cmdlet Set-MsolDirSyncFeature för att proaktivt aktivera funktionen Duplicera attributåterhämtning innan den är aktiverad för din klient. För att kunna testa funktionen måste du skapa en ny Azure Active Directory-klientorganisation.

## <a name="identifying-objects-with-dirsyncprovisioningerrors"></a>Identifiera objekt med DirSyncProvisioningErrors
Det finns för närvarande två metoder för att identifiera objekt som har dessa fel på grund av dubbla egenskapskonflikter, Azure Active Directory PowerShell och [Microsoft 365 admincenter](https://admin.microsoft.com). Det finns planer på att utöka till ytterligare portalbaserad rapportering i framtiden.

### <a name="azure-active-directory-powershell"></a>Azure Active Directory PowerShell
För PowerShell-cmdlets i det här avsnittet är följande sant:

* Alla följande cmdlets är skiftlägeskänsliga.
* **Egenskapskoneturen –ErrorCategory** måste alltid inkluderas. Det finns för närvarande inga andra typer av **ErrorCategory**, men detta kan förlängas i framtiden.

Börja med att komma igång genom att köra **Connect-MsolService** och ange autentiseringsuppgifter för en klientadministratör.

Använd sedan följande cmdlets och operatorer för att visa fel på olika sätt:

1. [Se alla](#see-all)
2. [Efter egenskapstyp](#by-property-type)
3. [Genom motstridiga värde](#by-conflicting-value)
4. [Använda en strängsökning](#using-a-string-search)
5. Sorterade
6. [I en begränsad mängd eller alla](#in-a-limited-quantity-or-all)

#### <a name="see-all"></a>Visa alla
När du har anslutit, om du vill se en allmän lista över attributetableringsfel i klientkörningen:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict`

Detta ger ett resultat som följande:  
 ![Get-MsolDirSyncProvisioningError](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/1.png "Get-MsolDirSyncProvisioningError")  

#### <a name="by-property-type"></a>Efter egenskapstyp
Om du vill visa fel efter egenskapstyp lägger du till flaggan **-PropertyName** med argumentet **UserPrincipalName** eller **ProxyAddresses:**

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName UserPrincipalName`

Eller

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName ProxyAddresses`

#### <a name="by-conflicting-value"></a>Genom motstridiga värde
Om du vill visa fel som rör en viss egenskap lägger du till flaggan **-PropertyValue** (**-PropertyName** måste också användas när du lägger till den här flaggan):

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyValue User@domain.com -PropertyName UserPrincipalName`

#### <a name="using-a-string-search"></a>Använda en strängsökning
Om du vill göra en bred strängsökning använder du flaggan **Söksträng.** Detta kan användas oberoende av alla ovanstående flaggor, med undantag för **-ErrorCategory PropertyConflict**, vilket alltid krävs:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -SearchString User`

#### <a name="in-a-limited-quantity-or-all"></a>I en begränsad mängd eller alla
1. **MaxResults \<Int>** kan användas för att begränsa frågan till ett visst antal värden.
2. **Alla** kan användas för att säkerställa att alla resultat hämtas om det finns ett stort antal fel.

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -MaxResults 5`

## <a name="microsoft-365-admin-center"></a>Administrationscenter för Microsoft 365
Du kan visa katalogsynkroniseringsfel i administrationscentret för Microsoft 365. Rapporten i administrationscentret för Microsoft 365 visar bara **användarobjekt** som har dessa fel. Information om konflikter mellan **grupper** och **kontakter**visas inte.

![Aktiva användare](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/1234.png "Aktiva användare")

Instruktioner om hur du visar synkroniseringsfel i katalogsynkronisering i administrationscentret för Microsoft 365 finns [i Identifiera synkroniseringsfel i katalogen i Office 365](https://support.office.com/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067).

### <a name="identity-synchronization-error-report"></a>Felrapport för identitetssynkronisering
När ett objekt med en dubblettattributkonflikt hanteras med det här nya beteendet inkluderas ett meddelande i standardmeddelandemeddelandet för identitetssynkroniseringsfel som skickas till den tekniska meddelandekontakten för klienten. Det finns dock en viktig förändring i detta beteende. Tidigare skulle information om en dubblettattributkonflikt inkluderas i varje efterföljande felrapport tills konflikten löstes. Med det här nya beteendet visas felmeddelandet för en viss konflikt bara en gång, när attributet motstridiga objekt sätts i karantän.

Här är ett exempel på hur e-postmeddelandet ser ut för en ProxyAddress konflikt:  
    ![Aktiva användare](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/6.png "Aktiva användare")  

## <a name="resolving-conflicts"></a>Lösa konflikter
Felsökningsstrategi och lösningstaktik för dessa fel bör inte skilja sig från hur dubblettattributfel hanterades tidigare. Den enda skillnaden är att timeraktiviteten sveper genom klienten på tjänstsidan för att automatiskt lägga till attributet i fråga till rätt objekt när konflikten har lösts.

I följande artikel beskrivs olika felsöknings- och [lösningsstrategier: Duplicera eller ogiltiga attribut förhindrar katalogsynkronisering i Office 365](https://support.microsoft.com/kb/2647098).

## <a name="known-issues"></a>Kända problem
Inget av dessa kända problem orsakar dataförlust eller tjänstförsämring. Flera av dem är estetiska, andra orsakar standard "*pre-resiliency*" dubblettattributfel som ska kastas i stället för att quarantining konfliktattributet, och en annan orsakar vissa fel att kräva extra manuell fix-up.

**Kärnbeteende:**

1. Objekt med specifika attributkonfigurationer fortsätter att ta emot exportfel i motsats till de dubblettattribut som sätts i karantän.  
   Ett exempel:
   
    a. Ny användare skapas i AD med en UPN av **\@Joe contoso.com** och ProxyAddress **smtp: Joe\@contoso.com**
   
    b. Egenskaperna för det här objektet står i konflikt med en befintlig grupp, där ProxyAddress är **\@SMTP:Joe contoso.com**.
   
    c. Vid export genereras ett **ProxyAddress-konfliktfel** i stället för att konfliktattributen sätts i karantän. Åtgärden görs på nytt vid varje efterföljande synkroniseringscykel, som den skulle ha varit innan återhämtningsfunktionen aktiverades.
2. Om två grupper skapas lokalt med samma SMTP-adress misslyckas man med att etablera det första försöket med ett standardfel **i proxyadressen.** Dubblettvärdet är dock korrekt i karantän vid nästa synkroniseringscykel.

**Office Portal-rapport**:

1. Det detaljerade felmeddelandet för två objekt i en UPN-konfliktuppsättning är detsamma. Detta tyder på att de båda har fått sin UPN ändrad / i karantän, när i själva verket bara en av dem hade några uppgifter ändras.
2. Det detaljerade felmeddelandet för en UPN-konflikt visar fel displayName för en användare som har fått sitt UPN ändrat/i karantän. Ett exempel:
   
    a. **Användare A** synkroniseras först med **UPN\@= Användare contoso.com**.
   
    b. **Användare B** försöker synkroniseras nästa med **UPN\@= Användare contoso.com**.
   
    c. **Användare B:s** UPN ändras till **\@User1234 contoso.onmicrosoft.com** och **\@contoso.com** läggs till i **DirSyncProvisioningErrors**.
   
    d. Felmeddelandet för **användare B** bör ange att användare **A** redan har **\@användare contoso.com** som upn, men det visar **användare B:s** eget displayName.

**Felrapport för identitetssynkronisering:**

Länken för *steg om hur du löser problemet* är felaktig:  
    ![Aktiva användare](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/6.png "Aktiva användare")  

Det bör [https://aka.ms/duplicateattributeresiliency](https://aka.ms/duplicateattributeresiliency)peka på .

## <a name="see-also"></a>Se även
* [Synkronisering av Azure AD Connect](how-to-connect-sync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)
* [Identifiera synkroniseringsfel i katalogen i Office 365](https://support.office.com/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067)

