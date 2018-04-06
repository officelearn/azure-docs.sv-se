---
title: 'Azure AD Connect: Felsökning av fel vid synkronisering | Microsoft Docs'
description: Beskriver hur du felsöker fel påträffades vid synkronisering med Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: curtand
ms.assetid: 2209d5ce-0a64-447b-be3a-6f06d47995f8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: billmath
ms.openlocfilehash: aaa374d5a11ef5b5860f83a87386ff981319189f
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="troubleshooting-errors-during-synchronization"></a>Felsökning av fel under synkroniseringen
Fel kan inträffa när identitetsdata synkroniseras från Windows Server Active Directory (AD DS) till Azure Active Directory (AD Azure). Den här artikeln innehåller en översikt över olika typer av synkroniseringsfel några möjliga scenarier som orsakar dessa fel och möjliga sätt att åtgärda felen. Den här artikeln innehåller vanliga fel och kan inte omfatta alla möjliga fel.

 Den här artikeln förutsätter läsaren är bekant med den underliggande [designbegreppen av Azure AD och Azure AD Connect](active-directory-aadconnect-design-concepts.md).

Med den senaste versionen av Azure AD Connect \(augusti 2016 eller senare\), en rapport över synkroniseringsfel är tillgänglig i den [Azure Portal](https://aka.ms/aadconnecthealth) som en del av Azure AD Connect Health för synkronisering.

Från den 1 September 2016 [Azure Active Directory duplicera attribut återhämtning](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md) funktionen aktiveras som standard för alla de *nya* Azure Active Directory-klienter. Den här funktionen ska aktiveras automatiskt för befintliga klienter under de kommande månaderna.

Azure AD Connect utför 3 typer av åtgärder från kataloger den bevarar synkroniserade: Import, synkronisering och Export. Fel kan ske i alla åtgärder. Den här artikeln fokuserar huvudsakligen på fel under exporten till Azure AD.

## <a name="errors-during-export-to-azure-ad"></a>Fel under exporten till Azure AD
Följande avsnitt beskrivs olika typer av synkroniseringsfel som kan uppstå under exportåtgärden till Azure AD med hjälp av Azure AD-koppling. Den här anslutningen kan identifieras av namnformat som ”contoso. *onmicrosoft.com*”.
Fel under exporten till Azure AD anger att åtgärden \(lägga till, uppdatera, ta bort etc.\) gjordes ett försök av Azure AD Connect \(Synkroniseringsmotorn\) på Azure Active Directory misslyckades.

![Översikt över fel](./media/active-directory-aadconnect-troubleshoot-sync-errors/Export_Errors_Overview_01.png)

## <a name="data-mismatch-errors"></a>Felaktig matchning av datafel
### <a name="invalidsoftmatch"></a>InvalidSoftMatch
#### <a name="description"></a>Beskrivning
* När Azure AD Connect \(Synkroniseringsmotorn\) instruerar Azure Active Directory för att lägga till eller uppdatera objekt, Azure AD matchar inkommande objekt med den **sourceAnchor** attribut till den **immutableId**  attribut för objekt i Azure AD. Matchningen kallas en **hårda matchar**.
* När Azure AD **inte hittar** alla objekt som matchar den **immutableId** attributet med den **sourceAnchor** attribut för det inkommande objektet innan du etablerar en ny objektet använder för att använda attributen ProxyAddresses och UserPrincipalName för att hitta en matchande. Matchningen kallas en **mjuka matchar**. Mjuka matchar är utformat för att matcha objekt som redan finns i Azure AD (som tillhandahålls i Azure AD) med de nya objekt som lagts till/uppdateras under synkroniseringen som representerar samma entitet (användare, grupper) lokalt.
* **InvalidSoftMatch** fel uppstår när hårddisken matchar inte hitta något matchande objekt **och** mjuka matchar hittar en matchande objekt men objektet har ett annat värde för *immutableId* än den inkommande objektet *SourceAnchor*, tyder på att det matchande objektet synkroniserades med ett annat objekt från på lokala Active Directory.

Med andra ord, i ordning efter nedladdningsbara matchning ska fungera objektet ska vara mjuk matchas med ska inte ha ett värde för den *immutableId*. Om något objekt med *immutableId* med ett värde kan inte de hårda matchningen men uppfyller soft-matchningsvillkor åtgärden skulle leda till en InvalidSoftMatch synkroniseringsfel.

Azure Active Directory-schemat tillåter inte två eller flera objekt som ska ha samma värde för följande attribut. \(Detta är inte en fullständig förteckning.\)

* ProxyAddresses
* UserPrincipalName
* onPremisesSecurityIdentifier
* ObjectId

> [!NOTE]
> [Azure AD-attributet duplicera attribut återhämtning](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md) funktion är också på att släppas som standardbeteendet för Azure Active Directory.  Detta minskar antalet synkroniseringsfel ses av Azure AD Connect (samt andra sync-klienter) genom att Azure AD mer robust i hanteringen av den duplicerade ProxyAddresses UserPrincipalName attribut och på lokala AD-miljöer. Den här funktionen löser inte dubblettfel. Så behöver data fortfarande åtgärdas. Men det gör etablering av nya objekt som annars blockeras från tillhandahålls på grund av duplicerade värden i Azure AD. Detta minskar också antalet synkroniseringsfel som returneras till klienten synkronisering.
> Synkroniseringsfel InvalidSoftMatch visas vid etablering av nya objekt visas inte om den här funktionen är aktiverad för din klient.
>
>

#### <a name="example-scenarios-for-invalidsoftmatch"></a>Exempelscenarier för InvalidSoftMatch
1. Två eller flera objekt med samma värde för attributet ProxyAddresses finns i lokala Active Directory. Endast en etableras om i Azure AD.
2. Två eller flera objekt med samma värde för userPrincipalName finns i lokala Active Directory. Endast en etableras om i Azure AD.
3. Ett objekt har lagts till i lokalerna Active Directory med samma värde för attributet ProxyAddresses med ett befintligt objekt i Azure Active Directory. Objektet som läggs till på lokala komma inte etableras i Azure Active Directory.
4. Ett objekt har lagts till i lokala Active Directory med samma värde för attributet userPrincipalName som ett konto i Azure Active Directory. Objektet komma inte etableras i Azure Active Directory.
5. Synkroniserade konto har flyttats från skog A till skog B. Azure AD Connect (Synkroniseringsmotorn) med attributet ObjectGUID för att beräkna SourceAnchor. Värdet för SourceAnchor skiljer sig efter överflyttningen skog. Det nya objektet (från skog B) kan inte synkroniseras med det befintliga objektet i Azure AD.
6. Synkroniserade objekt har fått av misstag tas bort från lokala Active Directory och ett nytt objekt har skapats i Active Directory för samma entitet (till exempel användare) utan att ta bort kontot i Azure Active Directory. Det går inte att synkronisera med det befintliga objektet i Azure AD det nya kontot.
7. Azure AD Connect har avinstalleras och installeras igen. Ett annat attribut valdes under ominstallationen, som SourceAnchor. Alla objekt som tidigare har synkroniserats stoppats synkroniserar med InvalidSoftMatch fel.

#### <a name="example-case"></a>Exempel fall:
1. **Bob Smith** är en synkroniserade användare i Azure Active Directory från på lokala Active Directory för *contoso.com*
2. Bob Smith **UserPrincipalName** har angetts som **bobs@contoso.com**.
3. **”abcdefghijklmnopqrstuv ==”** är den **SourceAnchor** beräknas av Azure AD Connect med Bob Smith **objectGUID** från lokala Active Directory, vilket är den  **immutableId** för Bob Smith i Azure Active Directory.
4. Bob innehåller också följande värden för den **proxyAddresses** attribut:
   * smtp:bobs@contoso.com
   * smtp:bob.smith@contoso.com
   * **smtp:bob@contoso.com**
5. En ny användare **Bob Taylor**, har lagts till i lokalerna Active Directory.
6. Bob Taylor **UserPrincipalName** har angetts som **bobt@contoso.com**.
7. **”abcdefghijkl0123456789 ==” ”** är den **sourceAnchor** beräknas av Azure AD Connect med Bob Taylor **objectGUID** från på lokala Active Directory. Bob Taylor objektet har inte synkroniserats till Azure Active Directory ännu.
8. Bob Taylor har följande värden för attributet proxyAddresses
   * smtp:bobt@contoso.com
   * smtp:bob.taylor@contoso.com
   * **smtp:bob@contoso.com**
9. Azure AD Connect kommer under synkronisering känner igen för att lägga till Bob Taylor i lokala Active Directory och be Azure AD för att göra samma ändringar.
10. Azure AD först utför hårda matchning. Det vill säga söker om ett objekt med immutableId är lika med ”abcdefghijkl0123456789 ==”. Hårda matchar misslyckas eftersom inga objekt i Azure AD har den immutableId.
11. Azure AD försöker soft-matcha Bob Taylor. Det vill säga söker om ett objekt med proxyAddresses är lika med tre värden, inklusive smtp:bob@contoso.com
12. Azure AD hittar Bob Smith objekt som matchar villkor som soft-matchning. Men det här objektet har värdet för immutableId = ”abcdefghijklmnopqrstuv ==”. Anger det här objektet har synkroniserats från ett annat objekt från lokala Active Directory. Därför Azure AD kan inte soft-matcha de här objekten och resulterar i en **InvalidSoftMatch** synkroniseringsfel.

#### <a name="how-to-fix-invalidsoftmatch-error"></a>Hur du löser InvalidSoftMatch fel
Den vanligaste orsaken till felet InvalidSoftMatch är två objekt med olika SourceAnchor \(immutableId\) har samma värde för de ProxyAddresses och/eller UserPrincipalName attributen, som används under soft-matchning bearbeta på Azure AD. För att åtgärda ogiltig mjuka matchning

1. Identifiera duplicerade proxyAddresses, userPrincipalName eller andra attributvärde som orsakar felet. Också identifiera vilka två \(eller flera\) objekt ingår i konflikten. Rapporten som genereras av [Azure AD Connect Health för synkronisering](https://aka.ms/aadchsyncerrors) kan hjälpa dig att identifiera de båda objekten.
2. Identifiera vilka objekttyper som ska fortsätta att ha duplicerade värdet och vilket objekt bör inte.
3. Ta bort dubbletter värdet från det objekt som inte ska ha värdet. Observera att du ska göra ändringen i den katalog där objektet kommer från. I vissa fall kan behöva du ta bort ett objekt i konflikt.
4. Om du har gjort ändringen i lokalerna AD låta Azure AD Connect synkroniserar ändringen.

Observera att synkronisera felrapport i Azure AD Connect Health för synkronisering uppdateras var 30: e minut och innehåller fel från det senaste synkroniseringsförsöket.

> [!NOTE]
> ImmutableId, bör per definition inte ändra av objektet. Om Azure AD Connect inte har konfigurerats med några scenarier i åtanke i listan ovan, du kan hamna i en situation där Azure AD Connect beräknar ett annat värde för SourceAnchor för AD-objekt som representerar samma entitet (samma användargrupp / Kontakta osv.) som har en befintlig Azure AD-objekt som du vill fortsätta använda.
>
>

#### <a name="related-articles"></a>Relaterade artiklar
* [Dubblett eller ogiltigt attribut förhindra katalogsynkronisering i Office 365](https://support.microsoft.com/en-us/kb/2647098)

### <a name="objecttypemismatch"></a>ObjectTypeMismatch
#### <a name="description"></a>Beskrivning
När Azure AD försöker matcha mjuka två objekt, är det möjligt att två objekt i olika ”objekttypen” (till exempel användare, grupp, kontakta etc.) har samma värden för de attribut som används för att utföra mjuka matchningen. Som duplicering av dessa attribut inte är tillåten i Azure AD, kan åtgärden resultera i ”ObjectTypeMismatch” synkroniseringsfel.

#### <a name="example-scenarios-for-objecttypemismatch-error"></a>Exempelscenarier för ObjectTypeMismatch fel
* En e-aktiverade säkerhetsgruppen har skapats i Office 365. Administratören lägger till en ny användare eller en kontakt i lokala AD (som inte är synkroniserat till Azure AD ännu) med samma värde för attributet ProxyAddresses som Office 365-gruppen.

#### <a name="example-case"></a>Exempel fallet
1. Administratören skapar en ny e-post aktiverad säkerhetsgrupp i Office 365 för skatt avdelningen och ger en e-postadress som tax@contoso.com. Den här tilldelas ProxyAddresses attributet för den här gruppen med värdet för **smtp:tax@contoso.com**
2. En ny användare ansluter Contoso.com och ett konto skapas för användaren på lokal med proxyAddress som **smtp:tax@contoso.com**
3. När Azure AD Connect kommer att synkronisera det nya användarkontot, får den felet ”ObjectTypeMismatch”.

#### <a name="how-to-fix-objecttypemismatch-error"></a>Hur du löser ObjectTypeMismatch fel
Den vanligaste orsaken till felet ObjectTypeMismatch är två objekt av en annan typ (användare, grupp, kontakta etc.) har samma värde för attributet ProxyAddresses. För att åtgärda ObjectTypeMismatch:

1. Identifiera duplicerade proxyAddresses (eller andra attribut)-värde som orsakar felet. Också identifiera vilka två \(eller flera\) objekt ingår i konflikten. Rapporten som genereras av [Azure AD Connect Health för synkronisering](https://aka.ms/aadchsyncerrors) kan hjälpa dig att identifiera de båda objekten.
2. Identifiera vilka objekttyper som ska fortsätta att ha duplicerade värdet och vilket objekt bör inte.
3. Ta bort dubbletter värdet från det objekt som inte ska ha värdet. Observera att du ska göra ändringen i den katalog där objektet kommer från. I vissa fall kan behöva du ta bort ett objekt i konflikt.
4. Om du har gjort ändringen i lokalerna AD låta Azure AD Connect synkroniserar ändringen. Synkronisera felrapport i Azure AD Connect Health för synkronisering uppdateras var 30: e minut och innehåller fel från det senaste synkroniseringsförsöket.

## <a name="duplicate-attributes"></a>Duplicera attribut
### <a name="attributevaluemustbeunique"></a>AttributeValueMustBeUnique
#### <a name="description"></a>Beskrivning
Azure Active Directory-schemat tillåter inte två eller flera objekt som ska ha samma värde för följande attribut. Det är att varje objekt i Azure AD måste ha ett unikt värde av dessa attribut vid en viss instans.

* ProxyAddresses
* UserPrincipalName

Åtgärden resulterar i ”AttributeValueMustBeUnique” sync-fel om Azure AD Connect försöker lägga till ett nytt objekt eller uppdatera ett befintligt objekt med ett värde för ovanstående attribut som redan har tilldelats till ett annat objekt i Azure Active Directory.

#### <a name="possible-scenarios"></a>Möjliga scenarier:
1. Dubblettvärde har tilldelats ett redan synkroniserade objekt som står i konflikt med ett annat synkroniserade objekt.

#### <a name="example-case"></a>Exempel fall:
1. **Bob Smith** är en synkroniserade användare i Azure Active Directory från på lokala Active Directory för contoso.com
2. Bob Smith **UserPrincipalName** har angetts som lokala **bobs@contoso.com**.
3. Bob innehåller också följande värden för den **proxyAddresses** attribut:
   * smtp:bobs@contoso.com
   * smtp:bob.smith@contoso.com
   * **smtp:bob@contoso.com**
4. En ny användare **Bob Taylor**, har lagts till i lokalerna Active Directory.
5. Bob Taylor **UserPrincipalName** har angetts som **bobt@contoso.com**.
6. **Bob Taylor** har följande värden för den **ProxyAddresses** attribut i. smtp:bobt@contoso.com II. smtp:bob.taylor@contoso.com
7. Bob Taylor objektet synkroniseras med Azure AD har.
8. Administratören har valt att uppdatera Bob Taylor **ProxyAddresses** attribut med följande värde: jag. **smtp:bob@contoso.com**
9. Azure AD försöker uppdatera Bob Taylor objektet i Azure AD med ovanstående värde, men som misslyckas åtgärden som att ProxyAddresses värdet har redan tilldelats Bob Smith, vilket resulterade i felet ”AttributeValueMustBeUnique”.

#### <a name="how-to-fix-attributevaluemustbeunique-error"></a>Hur du löser AttributeValueMustBeUnique fel
Den vanligaste orsaken till felet AttributeValueMustBeUnique är två objekt med olika SourceAnchor \(immutableId\) har samma värde för attribut som ProxyAddresses och/eller UserPrincipalName. För att åtgärda felet AttributeValueMustBeUnique

1. Identifiera duplicerade proxyAddresses, userPrincipalName eller andra attributvärde som orsakar felet. Också identifiera vilka två \(eller flera\) objekt ingår i konflikten. Rapporten som genereras av [Azure AD Connect Health för synkronisering](https://aka.ms/aadchsyncerrors) kan hjälpa dig att identifiera de båda objekten.
2. Identifiera vilka objekttyper som ska fortsätta att ha duplicerade värdet och vilket objekt bör inte.
3. Ta bort dubbletter värdet från det objekt som inte ska ha värdet. Observera att du ska göra ändringen i den katalog där objektet kommer från. I vissa fall kan behöva du ta bort ett objekt i konflikt.
4. Om du har gjort ändringen i lokalerna AD låta Azure AD Connect synkroniserar ändringen för felet för att hämta fast.

#### <a name="related-articles"></a>Relaterade artiklar
-[Dubblett eller ogiltigt attribut förhindra katalogsynkronisering i Office 365](https://support.microsoft.com/en-us/kb/2647098)

## <a name="data-validation-failures"></a>Dataverifiering
### <a name="identitydatavalidationfailed"></a>IdentityDataValidationFailed
#### <a name="description"></a>Beskrivning
Azure Active Directory tillämpar olika begränsningar för själva informationen innan du tillåter att data skrivs till katalogen. Detta är så att slutanvändarna får bästa möjliga upplevelser när du använder program som är beroende av dessa data.

#### <a name="scenarios"></a>Scenarier
a. Värdet för attributet UserPrincipalName har tecken som inte ogiltig stöds.
b. Attributet UserPrincipalName följer inte formatet som krävs.

#### <a name="how-to-fix-identitydatavalidationfailed-error"></a>Hur du löser IdentityDataValidationFailed fel
a. Se till att attributet userPrincipalName har stöds tecken och format som krävs.

#### <a name="related-articles"></a>Relaterade artiklar
* [Förbereda för att etablera användare via katalogsynkronisering på Office 365](https://support.office.com/en-us/article/Prepare-to-provision-users-through-directory-synchronization-to-Office-365-01920974-9e6f-4331-a370-13aea4e82b3e)

### <a name="federateddomainchangeerror"></a>FederatedDomainChangeError
#### <a name="description"></a>Beskrivning
Detta är ett specialfall som resulterar i en **”FederatedDomainChangeError”** synkroniseringsfel när suffix för en användares UserPrincipalName ändras från en federerad domän till en annan federerad domän.

#### <a name="scenarios"></a>Scenarier
För en synkroniserad användare ändrades UserPrincipalName suffix från en federerad domän till en annan federerad domän lokalt. Till exempel *UserPrincipalName = bob@contoso.com*  ändrades till *UserPrincipalName = bob@fabrikam.com* .

#### <a name="example"></a>Exempel
1. Bob Smith, ett konto för Contoso.com, hämtar läggas till som en ny användare i Active Directory med UserPrincipalName bob@contoso.com
2. Bob flyttar till en annan avdelning contoso.com kallas Fabrikam.com och hans UserPrincipalName ändras till bob@fabrikam.com
3. Både contoso.com och fabrikam.com domäner är federerade med Azure Active Directory-domäner.
4. Bobs userPrincipalName uppdateras inte och resulterar i ett ”FederatedDomainChangeError” synkroniseringsfel.

#### <a name="how-to-fix"></a>Hur du löser
Om en användares UserPrincipalName suffix uppdaterades från bob @**contoso.com** till bob @**fabrikam.com**, där både **contoso.com** och **fabrikam.com** är **federerad domäner**, Följ stegen nedan för att åtgärda sync-fel

1. Uppdatera användarens UserPrincipalName i Azure AD från bob@contoso.com till bob@contoso.onmicrosoft.com. Du kan använda följande PowerShell-kommando med Azure AD PowerShell-modulen: `Set-MsolUserPrincipalName -UserPrincipalName bob@contoso.com -NewUserPrincipalName bob@contoso.onmicrosoft.com`
2. Tillåt synkronisering nästa cykel att försöka med synkronisering. Den här tidssynkronisering kommer att lyckas och den kommer att uppdatera UserPrincipalName Bob till bob@fabrikam.com som förväntat.

#### <a name="related-articles"></a>Relaterade artiklar
* [Ändringarna synkroniserats inte med verktyget Azure Active Directory-synkronisering när du ändrar UPN-namnet för ett användarkonto om du vill använda en annan federerad domän](https://support.microsoft.com/en-us/help/2669550/changes-aren-t-synced-by-the-azure-active-directory-sync-tool-after-you-change-the-upn-of-a-user-account-to-use-a-different-federated-domain)

## <a name="largeobject"></a>LargeObject
### <a name="description"></a>Beskrivning
När ett attribut som överskrider storleksgränsen, längdbegränsningen eller antal gränsvärdet i Azure Active Directory-schemat, resulterar synkroniseringsåtgärden i i **LargeObject** eller **ExceededAllowedLength**synkroniseringsfel. Det här felet uppstår vanligen för följande attribut

* userCertificate
* userSMIMECertificate
* thumbnailPhoto
* proxyAddresses

### <a name="possible-scenarios"></a>Möjliga scenarier
1. För många certifikat som har tilldelats Bob lagrar bObs userCertificate attribut. Dessa kan innehålla äldre, utgångna certifikat. Hård gräns är 15 certifikat. Mer information om hur du hanterar LargeObject fel med userCertificate attribut finns i artikeln [hantering LargeObject fel som orsakats av userCertificate attributet](active-directory-aadconnectsync-largeobjecterror-usercertificate.md).
2. För många certifikat som har tilldelats Bob lagrar bObs userSMIMECertificate attribut. Dessa kan innehålla äldre, utgångna certifikat. Hård gräns är 15 certifikat.
3. Bobs thumbnailPhoto i Active Directory är för stor för att synkroniseras i Azure AD.
4. Ett objekt har för många ProxyAddresses som tilldelas under automatisk ifyllning av ProxyAddresses attribut i Active Directory.

### <a name="how-to-fix"></a>Hur du löser
1. Kontrollera att attributet som orsakar felet ligger inom den tillåtna begränsningen.

## <a name="related-links"></a>Relaterade länkar
* [Hitta Active Directory-objekt i Active Directory Administrationscenter](https://technet.microsoft.com/library/dd560661.aspx)
* [Hur man frågar Azure Active Directory för ett objekt med Azure Active Directory PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx)
