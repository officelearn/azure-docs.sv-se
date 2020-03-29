---
title: 'Azure AD Connect: Felsöka fel under synkronisering | Microsoft-dokument'
description: I artikeln beskrivs felsÃ¶kning av fel som uppstått vid synkronisering med Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 2209d5ce-0a64-447b-be3a-6f06d47995f8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 745ddcc95bb91e61478307265aec1ac8a7ebba54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609204"
---
# <a name="troubleshooting-errors-during-synchronization"></a>Felsöka fel under synkroniseringen
Fel kan uppstå när identitetsdata synkroniseras från Windows Server Active Directory (AD DS) till Azure Active Directory (Azure AD). Den här artikeln innehåller en översikt över olika typer av synkroniseringsfel, några av de möjliga scenarier som orsakar dessa fel och potentiella sätt att åtgärda felen. Den här artikeln innehåller de vanliga feltyperna och täcker kanske inte alla möjliga fel.

 Den här artikeln förutsätter att läsaren är bekant med de underliggande [designkoncepten för Azure AD och Azure AD Connect](plan-connect-design-concepts.md).

Med den senaste versionen \(av Azure AD Connect\)augusti 2016 eller senare är en rapport över synkroniseringsfel tillgänglig i [Azure-portalen](https://aka.ms/aadconnecthealth) som en del av Azure AD Connect Health för synkronisering.

Från och med den 1 september 2016 azure [Active Directory Duplicate Attribute Resiliency-funktionen](how-to-connect-syncservice-duplicate-attribute-resiliency.md) aktiveras som standard för alla *nya* Azure Active Directory-klienter. Den här funktionen aktiveras automatiskt för befintliga klienter under de kommande månaderna.

Azure AD Connect utför tre typer av åtgärder från de kataloger som den håller synkroniserade: Importera, synkronisera och exportera. Fel kan uppstå i alla åtgärder. Den här artikeln fokuserar främst på fel under Export till Azure AD.

## <a name="errors-during-export-to-azure-ad"></a>Fel under export till Azure AD
I följande avsnitt beskrivs olika typer av synkroniseringsfel som kan uppstå under exportåtgärden till Azure AD med Azure AD-kopplingen. Den här kopplingen kan identifieras med namnformatet som "contoso. *onmicrosoft.com*".
Fel under Export till Azure AD \(indikerar att åtgärden\) lägger till, \(uppdaterar,\) tar bort etc. som försöktes av Azure AD Connect Sync Engine på Azure Active Directory misslyckades.

![Översikt över exportfel](./media/tshoot-connect-sync-errors/Export_Errors_Overview_01.png)

## <a name="data-mismatch-errors"></a>Fel i datamatchning
### <a name="invalidsoftmatch"></a>OgiltigaSoftMatch
#### <a name="description"></a>Beskrivning
* När Azure \(AD\) Connect-synkroniseringsmotor instruerar Azure Active Directory att lägga till eller uppdatera objekt matchar Azure AD det inkommande objektet med **attributet sourceAnchor** till **attributet oföränderligaId** för objekt i Azure AD. Denna match kallas en **hård match**.
* När Azure AD **inte hittar** något objekt som matchar **attributet oföränderligId** med **attributet sourceAnchor** för det inkommande objektet, innan du etablerar ett nytt objekt, går det tillbaka för att använda proxyadresser och UserPrincipalName-attribut för att hitta en matchning. Den här matchningen kallas **mjuk matchning**. Soft Match är utformad för att matcha objekt som redan finns i Azure AD (som kommer från Azure AD) med de nya objekten som läggs till/uppdateras under synkroniseringen som representerar samma entitet (användare, grupper) lokalt.
* **InvalidSoftMatch-fel** uppstår när den hårda matchningen inte hittar något matchande objekt **och** mjuk matchning hittar ett matchande objekt men det objektet har ett annat värde av *oföränderlig information* än det inkommande objektets *SourceAnchor*, vilket tyder på att det matchande objektet synkroniserades med ett annat objekt från lokal Active Directory.

Med andra ord, för att den mjuka matchningen ska fungera, bör objektet som ska vara mjukmatchat med inte ha något värde för *immutableId*. Om ett objekt med *oföränderligId-uppsättning* med ett värde misslyckas med den hårda matchningen men uppfyller kriterierna för mjukmatchning, skulle åtgärden resultera i ett InvalidSoftMatch-synkroniseringsfel.

Azure Active Directory-schema tillåter inte att två eller flera objekt har samma värde för följande attribut. \(Detta är inte en uttömmande lista.\)

* ProxyAddresses
* UserPrincipalName
* påPremisesSecurityIdentifier
* ObjectId

> [!NOTE]
> [Azure AD-attribut duplicera attribut återhämtning](how-to-connect-syncservice-duplicate-attribute-resiliency.md) funktionen är också rullas ut som standardbeteende för Azure Active Directory.  Detta minskar antalet synkroniseringsfel som ses av Azure AD Connect (liksom andra synkroniseringsklienter) genom att göra Azure AD mer flexibelt på det sätt som hanteras dubblerade ProxyAddresses och UserPrincipalName-attribut som finns i lokala AD-miljöer. Den här funktionen åtgärdar inte dupliceringsfelen. Så uppgifterna måste fortfarande fastställas. Men det tillåter etablering av nya objekt som annars blockeras från att etableras på grund av dubblerade värden i Azure AD. Detta minskar också antalet synkroniseringsfel som returneras till synkroniseringsklienten.
> Om den här funktionen är aktiverad för din klientorganisation visas inte synkroniseringsfelen invalidsoftmatch som visas vid etablering av nya objekt.
>
>

#### <a name="example-scenarios-for-invalidsoftmatch"></a>Exempel på scenarier för InvalidSoftMatch
1. Det finns två eller flera objekt med samma värde för attributet ProxyAddresses i lokal Active Directory. Endast en etableras i Azure AD.
2. Två eller flera objekt med samma värde för attributet userPrincipalName finns i lokal Active Directory. Endast en etableras i Azure AD.
3. Ett objekt har lagts till i den lokala Active Directory med samma värde proxyadresser attribut som för ett befintligt objekt i Azure Active Directory. Objektet som läggs till lokalt etableras inte i Azure Active Directory.
4. Ett objekt har lagts till i lokal Active Directory med samma värde som attributet userPrincipalName som för ett konto i Azure Active Directory. Objektet etableras inte i Azure Active Directory.
5. Ett synkroniserat konto flyttades från skog A till skog B. Azure AD Connect (synkroniseringsmotor) använde ObjectGUID-attributet för att beräkna SourceAnchor. När skogen har flyttats är värdet för SourceAnchor annorlunda. Det nya objektet (från skog B) synkroniseras inte med det befintliga objektet i Azure AD.
6. Ett synkroniserat objekt togs bort av misstag från lokal Active Directory och ett nytt objekt skapades i Active Directory för samma entitet (till exempel användare) utan att ta bort kontot i Azure Active Directory. Det nya kontot synkroniseras inte med det befintliga Azure AD-objektet.
7. Azure AD Connect avinstallerades och installerades om. Under ominstallationen valdes ett annat attribut som SourceAnchor. Alla objekt som tidigare har synkroniserats slutade synkroniseras med InvalidSoftMatch-fel.

#### <a name="example-case"></a>Exempel fall:
1. **Bob Smith** är synkroniserad användare i Azure Active Directory från lokal Active Directory *i contoso.com*
2. Bob Smith's **UserPrincipalName** anges som **\@bobs contoso.com**.
3. **"abcdefghijklmnopqrstuv=="** är **SourceAnchor** som beräknas av Azure AD Connect med Bob Smiths **objectGUID** från lokal Active Directory, som är **immutableId** för Bob Smith i Azure Active Directory.
4. Bob har också följande värden för attributet **proxyAddresses:**
   * Smtp:bobs@contoso.com
   * Smtp:bob.smith@contoso.com
   * **smtp:\@bob contoso.com**
5. En ny användare, **Bob Taylor,** läggs till i den lokala Active Directory.
6. Bob Taylor's **UserPrincipalName** anges som **\@bobt contoso.com**.
7. **"abcdefghijkl0123456789==""** är **källanAnchor** som beräknas av Azure AD Connect med Bob Taylors **objectGUID** från lokal Active Directory. Bob Taylors objekt har INTE synkroniserats till Azure Active Directory ännu.
8. Bob Taylor har följande värden för attributet proxyAddresses
   * Smtp:bobt@contoso.com
   * Smtp:bob.taylor@contoso.com
   * **smtp:\@bob contoso.com**
9. Under synkroniseringen kommer Azure AD Connect att känna igen tillägget av Bob Taylor i lokal Active Directory och be Azure AD att göra samma ändring.
10. Azure AD kommer först att utföra hård matchning. Det vill än, det kommer att söka om det finns något objekt med oföränderligaId lika med "abcdefghijkl0123456789==". Hård matchning misslyckas eftersom inget annat objekt i Azure AD kommer att ha det oföränderligaId.
11. Azure AD försöker sedan att mjukmatcha Bob Taylor. Det vill än, det kommer att söka om det finns något objekt med proxyAddresses lika med de tre värdena, inklusive smtp:bob@contoso.com
12. Azure AD hittar Bob Smiths objekt som matchar kriterierna för mjukmatchning. Men detta objekt har värdet av oföränderligId = "abcdefghijklmnopqrstuv==". vilket indikerar att objektet synkroniserades från ett annat objekt från lokal Active Directory. Azure AD kan därför inte mjukmatcha dessa objekt och resulterar i ett **InvalidSoftMatch-synkroniseringsfel.**

#### <a name="how-to-fix-invalidsoftmatch-error"></a>Så här åtgärdar du InvalidSoftMatch-fel
Den vanligaste orsaken till InvalidSoftMatch-felet är två objekt \(med olika\) SourceAnchor immutableId har samma värde för ProxyAddresses och/eller UserPrincipalName-attributen, som används under soft-match-processen på Azure AD. För att åtgärda den ogiltiga mjuka matchningen

1. Identifiera de duplicerade proxyadresserna, userPrincipalName eller något annat attributvärde som orsakar felet. Identifiera också \(vilka\) två eller flera objekt som är inblandade i konflikten. Rapporten som genereras av [Azure AD Connect Health för synkronisering](https://aka.ms/aadchsyncerrors) kan hjälpa dig att identifiera de två objekten.
2. Identifiera vilket objekt som ska fortsätta att ha det duplicerade värdet och vilket objekt som inte ska.
3. Ta bort det duplicerade värdet från objektet som INTE ska ha det värdet. Du bör göra ändringen i katalogen där objektet kommer från. I vissa fall kan du behöva ta bort ett av objekten i konflikt.
4. Om du har gjort ändringen i den lokala AD:en låter du Azure AD Connect synkronisera ändringen.

Synkroniseringsfelrapporter i Azure AD Connect Health för synkronisering uppdateras var 30:e minut och innehåller felen från det senaste synkroniseringsförsöket.

> [!NOTE]
> ImmutableId, per definition, bör inte ändras under objektets livstid. Om Azure AD Connect inte har konfigurerats med några av scenarierna i åtanke från listan ovan kan du hamna i en situation där Azure AD Connect beräknar ett annat värde för SourceAnchor för AD-objektet som representerar samma entitet (samma användare/grupp/kontakt etc. som har ett befintligt Azure AD-objekt som du vill fortsätta använda.
>
>

#### <a name="related-articles"></a>Relaterade artiklar
* [Duplicerade eller ogiltiga attribut förhindrar katalogsynkronisering i Office 365](https://support.microsoft.com/kb/2647098)

### <a name="objecttypemismatch"></a>ObjectTypeMismatch
#### <a name="description"></a>Beskrivning
När Azure AD försöker matcha två objekt mjukt är det möjligt att två objekt av olika "objekttyp" (till exempel Användare, Grupp, Kontakt etc.) har samma värden för de attribut som används för att utföra den mjuka matchningen. Eftersom duplicering av dessa attribut inte är tillåtet i Azure AD, kan åtgärden resultera i synkroniseringsfel för "ObjectTypeMismatch".

#### <a name="example-scenarios-for-objecttypemismatch-error"></a>Exempelscenarier för ObjectTypeMismatch-fel
* En e-postaktiverad säkerhetsgrupp skapas i Office 365. Administratör lägger till en ny användare eller kontakt i lokal AD (som inte är synkroniserad med Azure AD ännu) med samma värde för attributet ProxyAddresses som för Office 365-gruppen.

#### <a name="example-case"></a>Exempelfall
1. Administratör skapar en ny e-postaktiverad säkerhetsgrupp i Office 365 tax@contoso.comför skatteavdelningen och tillhandahåller en e-postadress som . Den här gruppen tilldelas attributvärdet ProxyAddresses för **smtp:\@tax contoso.com**
2. En ny användare ansluter Contoso.com och ett konto skapas för användaren lokalt med proxyAddress som **smtp:\@tax contoso.com**
3. När Azure AD Connect synkroniserar det nya användarkontot får det felet "ObjectTypeMismatch".

#### <a name="how-to-fix-objecttypemismatch-error"></a>Åtgärda ObjectTypeMismatch-fel
Den vanligaste orsaken till ObjectTypeMismatch-felet är två objekt av olika typ (Användare, Grupp, Kontakt etc.) har samma värde för attributet ProxyAddresses. För att åtgärda ObjectTypeMismatch:

1. Identifiera det duplicerade proxyAdresser (eller annat attribut) värde som orsakar felet. Identifiera också \(vilka\) två eller flera objekt som är inblandade i konflikten. Rapporten som genereras av [Azure AD Connect Health för synkronisering](https://aka.ms/aadchsyncerrors) kan hjälpa dig att identifiera de två objekten.
2. Identifiera vilket objekt som ska fortsätta att ha det duplicerade värdet och vilket objekt som inte ska.
3. Ta bort det duplicerade värdet från objektet som INTE ska ha det värdet. Observera att du bör göra ändringen i katalogen där objektet kommer från. I vissa fall kan du behöva ta bort ett av objekten i konflikt.
4. Om du har gjort ändringen i den lokala AD:en låter du Azure AD Connect synkronisera ändringen. Synkroniseringsfelrapporten i Azure AD Connect Health för synkronisering uppdateras var 30:e minut och innehåller felen från det senaste synkroniseringsförsöket.

## <a name="duplicate-attributes"></a>Duplicera attribut
### <a name="attributevaluemustbeunique"></a>AttributVärdeMustBeUnique
#### <a name="description"></a>Beskrivning
Azure Active Directory-schema tillåter inte att två eller flera objekt har samma värde för följande attribut. Det är varje objekt i Azure AD tvingas ha ett unikt värde för dessa attribut vid en viss instans.

* ProxyAddresses
* UserPrincipalName

Om Azure AD Connect försöker lägga till ett nytt objekt eller uppdatera ett befintligt objekt med ett värde för ovanstående attribut som redan har tilldelats ett annat objekt i Azure Active Directory, resulterar åtgärden i synkroniseringsfelet "AttributeValueMustBeUnique".

#### <a name="possible-scenarios"></a>Möjliga scenarier:
1. Dubblettvärdet tilldelas ett redan synkroniserat objekt som står i konflikt med ett annat synkroniserat objekt.

#### <a name="example-case"></a>Exempel fall:
1. **Bob Smith** är synkroniserad användare i Azure Active Directory från lokal Active Directory i contoso.com
2. Bob Smith's **UserPrincipalName** lokalt anges som **\@bobs contoso.com**.
3. Bob har också följande värden för attributet **proxyAddresses:**
   * Smtp:bobs@contoso.com
   * Smtp:bob.smith@contoso.com
   * **smtp:\@bob contoso.com**
4. En ny användare, **Bob Taylor,** läggs till i den lokala Active Directory.
5. Bob Taylor's **UserPrincipalName** anges som **\@bobt contoso.com**.
6. **Bob Taylor** har följande värden för **attributet ProxyAddresses** i. smtp: bobt@contoso.com ii. Smtp:bob.taylor@contoso.com
7. Bob Taylors objekt synkroniseras med Azure AD.
8. Admin beslutat att uppdatera Bob Taylor's **ProxyAddresses** attribut med följande värde: i. **smtp:\@bob contoso.com**
9. Azure AD försöker uppdatera Bob Taylors objekt i Azure AD med ovanstående värde, men den åtgärden misslyckas eftersom det ProxyAddresses-värdet redan har tilldelats Bob Smith, vilket resulterar i felet "AttributeValueMustBeUnique".

#### <a name="how-to-fix-attributevaluemustbeunique-error"></a>Så här åtgärdar du attributevalueMustBeUnique-fel
Den vanligaste orsaken till attributevalueMustBeUnique-felet är två objekt \(med olika\) SourceAnchor immutableId har samma värde för ProxyAddresses och/eller UserPrincipalName attribut. För att åtgärda AttributeValueMustBeUnique-felet

1. Identifiera de duplicerade proxyadresserna, userPrincipalName eller något annat attributvärde som orsakar felet. Identifiera också \(vilka\) två eller flera objekt som är inblandade i konflikten. Rapporten som genereras av [Azure AD Connect Health för synkronisering](https://aka.ms/aadchsyncerrors) kan hjälpa dig att identifiera de två objekten.
2. Identifiera vilket objekt som ska fortsätta att ha det duplicerade värdet och vilket objekt som inte ska.
3. Ta bort det duplicerade värdet från objektet som INTE ska ha det värdet. Observera att du bör göra ändringen i katalogen där objektet kommer från. I vissa fall kan du behöva ta bort ett av objekten i konflikt.
4. Om du har gjort ändringen i den lokala AD:en låter du Azure AD Connect synkronisera ändringen för att felet ska åtgärdas.

#### <a name="related-articles"></a>Relaterade artiklar
-[Duplicerade eller ogiltiga attribut förhindrar katalogsynkronisering i Office 365](https://support.microsoft.com/kb/2647098)

## <a name="data-validation-failures"></a>Dataverifieringsfel
### <a name="identitydatavalidationfailed"></a>IdentityDataValidationMissilerad
#### <a name="description"></a>Beskrivning
Azure Active Directory tillämpar olika begränsningar för själva data innan du tillåter att data skrivs in i katalogen. Dessa begränsningar är att säkerställa att slutanvändarna får bästa möjliga upplevelser när du använder de program som är beroende av dessa data.

#### <a name="scenarios"></a>Scenarier
a. Attributet UserPrincipalName har ogiltiga/ej stödda tecken.
b. Attributet UserPrincipalName följer inte det format som krävs.

#### <a name="how-to-fix-identitydatavalidationfailed-error"></a>Åtgärdar IdentityDataValidationFailed fel
a. Kontrollera att attributet userPrincipalName har tecken och format som krävs.

#### <a name="related-articles"></a>Relaterade artiklar
* [Förbereda etablering av användare via katalogsynkronisering till Office 365](https://support.office.com/article/Prepare-to-provision-users-through-directory-synchronization-to-Office-365-01920974-9e6f-4331-a370-13aea4e82b3e)

### <a name="federateddomainchangeerror"></a>FedereradeDomainChangeError
#### <a name="description"></a>Beskrivning
Det här fallet resulterar i ett **synkroniseringsfel i "FederatedDomainChangeError"** när suffixet för en användares UserPrincipalName ändras från en federerad domän till en annan federerad domän.

#### <a name="scenarios"></a>Scenarier
För en synkroniserad användare har suffixet UserPrincipalName ändrats från en federerad domän till en annan federerad domän lokalt. *UserPrincipalName = bob\@contoso.com* ändrades till *UserPrincipalName\@= bob fabrikam.com*.

#### <a name="example"></a>Exempel
1. Bob Smith, ett konto för Contoso.com, läggs till som en ny användare i Active Directory med UserPrincipalNamebob@contoso.com
2. Bob flyttar till en annan avdelning av Contoso.com som kallas Fabrikam.com och deras UserPrincipalName ändras tillbob@fabrikam.com
3. Både contoso.com och fabrikam.com domäner är federerade domäner med Azure Active Directory.
4. Bobs userPrincipalName uppdateras inte och resulterar i ett synkroniseringsfel i "FederatedDomainChangeError".

#### <a name="how-to-fix"></a>Så här löser du det
Om en användares UserPrincipalName-suffix uppdaterades**contoso.com** från bob@ contoso.com\@till bob**fabrikam.com**, där både **contoso.com** och **fabrikam.com** är **federerade domäner,** följer du dessa steg för att åtgärda synkroniseringsfelet

1. Uppdatera användarens UserPrincipalName i Azure bob@contoso.com bob@contoso.onmicrosoft.comAD från till . Du kan använda följande PowerShell-kommando med Azure AD PowerShell-modulen:`Set-MsolUserPrincipalName -UserPrincipalName bob@contoso.com -NewUserPrincipalName bob@contoso.onmicrosoft.com`
2. Tillåt att nästa synkroniseringscykel försöker synkronisera. Den här gången kommer synkroniseringen att lyckas och det bob@fabrikam.com kommer att uppdatera UserPrincipalName för Bob till som förväntat.

#### <a name="related-articles"></a>Relaterade artiklar
* [Ändringarna synkroniseras inte med Azure Active Directory Sync-verktyget när du har ändrat UPN för ett användarkonto så att det använder en annan federerad domän](https://support.microsoft.com/help/2669550/changes-aren-t-synced-by-the-azure-active-directory-sync-tool-after-you-change-the-upn-of-a-user-account-to-use-a-different-federated-domain)

## <a name="largeobject"></a>LargeObject (largeobject)
### <a name="description"></a>Beskrivning
När ett attribut överskrider den tillåtna storleksgränsen, längdgränsen eller räknegränsen som angetts av Azure Active Directory-schemat, resulterar synkroniseringsåtgärden i synkroniseringsfelet **LargeObject** eller **ExceededAllowedLength.** Normalt uppstår det här felet för följande attribut

* userCertificate
* userSMIMECertificate
* thumbnailPhoto
* proxyAddresses

### <a name="possible-scenarios"></a>Möjliga scenarier
1. Bobs userCertificate-attribut lagrar för många certifikat som tilldelats Bob. Dessa kan omfatta äldre certifikat som har upphört att gälla. Den hårda gränsen är 15 certifikat. Mer information om hur du hanterar LargeObject-fel med attributet userCertificate finns i artikeln [Hantering av storaobjektfel som orsakas av attributet userCertificate](tshoot-connect-largeobjecterror-usercertificate.md).
2. Bobs attribute UserSMIMECertificate lagrar för många certifikat som tilldelats Bob. Dessa kan omfatta äldre certifikat som har upphört att gälla. Den hårda gränsen är 15 certifikat.
3. Bobs thumbnailPhoto-uppsättning i Active Directory är för stor för att synkroniseras i Azure AD.
4. Under automatisk population av attributet ProxyAddresses i Active Directory har ett objekt för många ProxyAddresses tilldelat.

### <a name="how-to-fix"></a>Så här löser du det
1. Kontrollera att attributet som orsakar felet ligger inom den tillåtna begränsningen.

## <a name="existing-admin-role-conflict"></a>Befintlig administratörsrollkonflikt

### <a name="description"></a>Beskrivning
En **befintlig administratörsrollkonflikt** uppstår på ett användarobjekt under synkroniseringen när det användarobjektet har:

- administrativa behörigheter och
- samma UserPrincipalName som ett befintligt Azure AD-objekt

Azure AD Connect tillåts inte att mjukmatcha ett användarobjekt från lokalt AD med ett användarobjekt i Azure AD som har en administrativ roll tilldelad.  Mer information finns i [Azure AD UserPrincipalName-populationen](plan-connect-userprincipalname.md)

![Befintlig administratör](media/tshoot-connect-sync-errors/existingadmin.png)


### <a name="how-to-fix"></a>Så här löser du det
Så här löser du problemet:

1. Ta bort Azure AD-kontot (ägaren) från alla administratörsroller. 
2. **Hård ta bort** karantänobjektet i molnet. 
3. Nästa synkroniseringscykel tar hand om mjukmatchning av den lokala användaren till molnkontot (eftersom molnanvändaren nu inte längre är en global GA). 
4. Återställ rollmedlemskapen för ägaren. 

>[!NOTE]
>Du kan tilldela den administrativa rollen till det befintliga användarobjektet igen efter att den mjuka matchningen mellan det lokala användarobjektet och Azure AD-användarobjektet har slutförts.

## <a name="related-links"></a>Relaterade länkar
* [Leta reda på Active Directory-objekt i Active Directory Administrationscenter](https://technet.microsoft.com/library/dd560661.aspx)
* [Fråga Azure Active Directory för ett objekt med Azure Active Directory PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx)
