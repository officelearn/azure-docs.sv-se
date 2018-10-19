---
title: 'Azure AD Connect: Felsök fel under synkronisering | Microsoft Docs'
description: Beskriver hur du felsöker fel påträffades vid synkronisering med Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 2209d5ce-0a64-447b-be3a-6f06d47995f8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: cb2b4bdee445587b32516c8db869170ab067b8d3
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2018
ms.locfileid: "49406865"
---
# <a name="troubleshooting-errors-during-synchronization"></a>Felsök fel under synkronisering
Fel kan uppstå när identitetsdata synkroniseras från Windows Server Active Directory (AD DS) till Azure Active Directory (AD Azure). Den här artikeln innehåller en översikt över olika typer av synkroniseringsfel några möjliga scenarier som orsakar dessa fel och potentiella sätt att åtgärda felen. Den här artikeln innehåller vanliga fel och kan inte omfatta alla eventuella fel.

 Den här artikeln förutsätter att läsaren är bekant med den underliggande [designbegrepp för Azure AD och Azure AD Connect](plan-connect-design-concepts.md).

Med den senaste versionen av Azure AD Connect \(augusti 2016 eller högre\), en rapport om synkroniseringsfel är tillgängliga i den [Azure-portalen](https://aka.ms/aadconnecthealth) som en del av Azure AD Connect Health för synkronisering.

Från och med 1 September 2016 [Azure Active Directory duplicerad Attributåterhämtning](how-to-connect-syncservice-duplicate-attribute-resiliency.md) funktionen aktiveras som standard för alla de *nya* Azure Active Directory-klienter. Den här funktionen ska aktiveras automatiskt för befintliga klienter under de kommande månaderna.

Azure AD Connect utför tre typer av åtgärder från de kataloger som den bevarar synkroniserade: Import, synkronisering och Export. Fel kan ske i alla åtgärder. Den här artikeln fokuserar främst på fel vid Export till Azure AD.

## <a name="errors-during-export-to-azure-ad"></a>Fel vid Export till Azure AD
Följande avsnitt beskrivs olika typer av synkroniseringsfel som kan uppstå under exportåtgärden till Azure AD med Azure AD-anslutningsappen. Den här anslutningen kan identifieras av namnformat som ”contoso. *onmicrosoft.com*”.
Fel vid Export till Azure AD anger att åtgärden \(lägga till, uppdatera, ta bort osv\) gjordes ett försök av Azure AD Connect \(Synkroniseringsmotorn\) på Azure Active Directory misslyckades.

![Översikt över fel](./media/tshoot-connect-sync-errors/Export_Errors_Overview_01.png)

## <a name="data-mismatch-errors"></a>Matchningsfel datafel
### <a name="invalidsoftmatch"></a>InvalidSoftMatch
#### <a name="description"></a>Beskrivning
* När Azure AD Connect \(Synkroniseringsmotorn\) instruerar Azure Active Directory för att lägga till eller uppdatera objekt, Azure AD matchar en inkommande objekt med hjälp av den **sourceAnchor** attributet den **immutableId**  attribut för objekt i Azure AD. Den här matchning kallas en **hårda matchar**.
* När Azure AD **inte hittar** objekt som matchar den **immutableId** attributet med den **sourceAnchor** attribut för det inkommande objektet innan du etablerar en ny objekt att det faller tillbaka om du vill använda ProxyAddresses och UserPrincipalName-attribut för att hitta en matchning. Den här matchning kallas en **mjuk matchar**. Mjuk matchar har utformats för att matcha objekt som redan finns i Azure AD (som kommer i Azure AD) med de nya objekten att lägga till/uppdatera under synkroniseringen som representerar samma entitet (användare, grupper) lokalt.
* **InvalidSoftMatch** felet uppstår när hårda matchar inte hitta något matchande objekt **AND** mjuk matchning hittar en matchande objekt men objektet har ett annat värde för *immutableId* än den inkommande objektets *SourceAnchor*, föreslå att matchande objekt har synkroniserats med ett annat objekt från på lokala Active Directory.

Med andra ord för mjuk matchningen för att fungera, objektet ska vara mjuk matchas med bör inte ha något värde för den *immutableId*. Om något objekt med *immutableId* set med ett värde misslyckas hård-matchningen men som uppfyller kriterierna för ungefärlig matchning åtgärden skulle resultera i en InvalidSoftMatch synkroniseringsfel.

Azure Active Directory-schemat tillåter inte två eller flera objekt som ska ha samma värde för följande attribut. \(Detta är inte en fullständig förteckning.\)

* proxyAddresses
* userPrincipalName
* onPremisesSecurityIdentifier
* ObjectId

> [!NOTE]
> [Azure AD-attributet duplicerad Attributåterhämtning](how-to-connect-syncservice-duplicate-attribute-resiliency.md) funktionen också distribueras som standardbeteendet för Azure Active Directory.  Detta minskar antalet synkroniseringsfel som setts av Azure AD Connect (samt andra sync-klienter) genom att göra Azure AD mer motståndskraftig i hanteringen av den duplicerade ProxyAddresses och UserPrincipalName-attribut finns i lokala AD-miljö. Den här funktionen löser inte dubblettfel. Därför måste data fortfarande kan åtgärdas. Men den ger etablering av nya objekt som annars blockeras från håller på att etableras på grund av duplicerade värden i Azure AD. Detta minskar också antalet synkroniseringsfel som returneras till klienten synkronisering.
> Om den här funktionen är aktiverad för din klient, visas inte InvalidSoftMatch synkroniseringsfel som visas under etableringen av nya objekt.
>
>

#### <a name="example-scenarios-for-invalidsoftmatch"></a>Exempelscenarier för InvalidSoftMatch
1. Det finns två eller flera objekt med samma värde för attributet ProxyAddresses i den lokala Active Directory. Endast en etableras om i Azure AD.
2. Det finns två eller flera objekt med samma värde för attributet userPrincipalName i den lokala Active Directory. Endast en etableras om i Azure AD.
3. Ett objekt har lagts till i lokalt Active Directory med samma värde för attributet ProxyAddresses som för ett befintligt objekt i Azure Active Directory. Objektet har lagts till i lokala komma inte etableras i Azure Active Directory.
4. Ett objekt har lagts till i lokala Active Directory med samma värdet för userPrincipalName-attribut med ett konto i Azure Active Directory. Objektet komma inte etableras i Azure Active Directory.
5. Ett synkroniserat konto har flyttats från skog A till skog B. Azure AD Connect (Synkroniseringsmotorn) har använt ObjectGUID-attributet för att beräkna SourceAnchor. Värdet för SourceAnchor skiljer sig efter överflyttningen skog. Det nya objektet (från skog B) kan inte synkroniseras med det befintliga objektet i Azure AD.
6. Ett synkroniserat objekt har av misstag tagits bort från lokala Active Directory och ett nytt objekt har skapats i Active Directory för samma entitet (till exempel användare) utan att ta bort kontot i Azure Active Directory. Det nya kontot inte kan synkronisera med det befintliga Azure AD-objektet.
7. Azure AD Connect har avinstallerats och installerats om. Under ominstallationen, har ett annat attribut valts som SourceAnchor. Alla objekt som tidigare har synkroniserats stoppats synkroniseras med InvalidSoftMatch fel.

#### <a name="example-case"></a>Exempel fall:
1. **Bob Smith** är en synkroniserade användare i Azure Active Directory från på lokala Active Directory *contoso.com*
2. Bob Smith **UserPrincipalName** har angetts som **bobs@contoso.com**.
3. **”abcdefghijklmnopqrstuv ==”** är den **SourceAnchor** beräknas genom att Azure AD Connect med Bob Smith **objectGUID** från lokala Active Directory, vilket är den  **immutableId** för Bob Smith i Azure Active Directory.
4. Bob innehåller också följande värden för den **proxyAddresses** attribut:
   * SMTP: bobs@contoso.com
   * SMTP: bob.smith@contoso.com
   * **SMTP: bob@contoso.com**
5. En ny användare **Bob Taylor**, har lagts till i lokalerna Active Directory.
6. Bob Taylor **UserPrincipalName** har angetts som **bobt@contoso.com**.
7. **”abcdefghijkl0123456789 ==” ”** är den **sourceAnchor** beräknas genom att Azure AD Connect med Bob Taylor **objectGUID** från på lokala Active Directory. Bob Taylor objektet har inte synkroniserats till Azure Active Directory ännu.
8. Bob Taylor har följande värden för attributet proxyAddresses
   * SMTP: bobt@contoso.com
   * SMTP: bob.taylor@contoso.com
   * **SMTP: bob@contoso.com**
9. Under synkronisering, Azure AD Connect identifierar att lägga till Bob Taylor i lokala Active Directory och be Azure AD för att göra samma ändring.
10. Azure AD först utför hårda matchning. Det vill säga söks om ett objekt med immutableId är lika med ”abcdefghijkl0123456789 ==”. Hårda matchning fungerar inte eftersom inga andra objekt i Azure AD har den immutableId.
11. Azure AD försöker ungefärlig matchning Bob Taylor. Det vill säga söks om ett objekt med proxyAddresses motsvarar värdena, inklusive smtp: bob@contoso.com
12. Azure AD hittar Bob Smith objekt som matchar villkor som ungefärlig matchning. Men det här objektet har värdet för immutableId = ”abcdefghijklmnopqrstuv ==”. vilket betyder att det här objektet har synkroniserats från ett annat objekt från lokala Active Directory. Därför Azure AD kan inte ungefärlig matchning dessa objekt och resulterar i en **InvalidSoftMatch** fel.

#### <a name="how-to-fix-invalidsoftmatch-error"></a>Hur du löser InvalidSoftMatch fel
Den vanligaste orsaken till felet InvalidSoftMatch är två objekt med olika SourceAnchor \(immutableId\) har samma värde för ProxyAddresses och/eller UserPrincipalName-attribut, som används under en ungefärlig matchning bearbeta på Azure AD. För att åtgärda ogiltig mjuk matchning

1. Identifiera duplicerade proxyAddresses, userPrincipalName eller andra attribut-värde som orsakar felet. Även identifiera vilka två \(eller flera\) objekt som är inblandade i konflikten. Rapporten som genereras av [Azure AD Connect Health för synkronisering](https://aka.ms/aadchsyncerrors) kan hjälpa dig identifiera de två objekten.
2. Identifiera vilka objekt ska fortsätta att få det duplicerade värdet och vilket objekt bör inte.
3. Ta bort duplicerade värdet från det objekt som inte ska ha värdet. Du bör göra ändringen i katalogen där objektet kommer från. I vissa fall kan behöva du ta bort ett objekt i konflikt.
4. Om du gjort ändringen i lokalt AD, kan Azure AD Connect synkroniserar ändringen.

Synkronisera felrapporter i Azure AD Connect Health för synkronisering uppdateras var 30: e minut och inkludera fel från det senaste synkroniseringsförsöket.

> [!NOTE]
> ImmutableId, bör per definition inte ändra av objektet. Om Azure AD Connect inte har konfigurerats med några av scenarierna i åtanke från listan ovan, du kan hamna i en situation där Azure AD Connect beräknar ett annat värde för SourceAnchor för AD-objekt som representerar samma entitet (samma användare/grupp / Kontakta osv) som har en befintlig Azure AD-objekt som du vill fortsätta att använda.
>
>

#### <a name="related-articles"></a>Relaterade artiklar
* [Dubblett eller ogiltigt attribut förhindra katalogsynkronisering i Office 365](https://support.microsoft.com/kb/2647098)

### <a name="objecttypemismatch"></a>ObjectTypeMismatch
#### <a name="description"></a>Beskrivning
När Azure AD försöker matcha mjuk två objekt, är det möjligt att två objekt i olika ”objekttyp” (till exempel användare, grupp, kontakt och så vidare) har samma värden för de attribut som används för att utföra mjuk matchningen. Eftersom duplicering av dessa attribut inte tillåts i Azure AD, kan åtgärden resultera i ”ObjectTypeMismatch” synkroniseringsfel.

#### <a name="example-scenarios-for-objecttypemismatch-error"></a>Exempelscenarier för ObjectTypeMismatch fel
* En e-postaktiverad säkerhetsgrupp skapas i Office 365. Administratören lägger till en ny användare eller kontakt i lokalt AD (som inte är synkroniserad med Azure AD ännu) med samma värde för attributet ProxyAddresses som Office 365-grupp.

#### <a name="example-case"></a>Exempel fallet
1. Administratören skapar en ny e-postaktiverad säkerhetsgrupp i Office 365 för Skatteverket och ger en e-postadress som tax@contoso.com. Den här gruppen har tilldelats värdet på attributet ProxyAddresses för **smtp: tax@contoso.com**
2. En ny användare ansluter Contoso.com och ett konto har skapats för användaren på plats med proxyAddress som **smtp: tax@contoso.com**
3. När Azure AD Connect kommer att synkronisera det nya användarkontot, får den felet ”ObjectTypeMismatch”.

#### <a name="how-to-fix-objecttypemismatch-error"></a>Hur du löser ObjectTypeMismatch fel
Den vanligaste orsaken till felet ObjectTypeMismatch är två objekt av annan typ (användare, grupp, kontakt och så vidare) har samma värde för attributet ProxyAddresses. För att kunna åtgärda ObjectTypeMismatch:

1. Identifiera duplicerade proxyAddresses (eller andra attribut) värde som orsakar felet. Även identifiera vilka två \(eller flera\) objekt som är inblandade i konflikten. Rapporten som genereras av [Azure AD Connect Health för synkronisering](https://aka.ms/aadchsyncerrors) kan hjälpa dig identifiera de två objekten.
2. Identifiera vilka objekt ska fortsätta att få det duplicerade värdet och vilket objekt bör inte.
3. Ta bort duplicerade värdet från det objekt som inte ska ha värdet. Observera att du ska göra ändringen i katalogen där objektet kommer från. I vissa fall kan behöva du ta bort ett objekt i konflikt.
4. Om du gjort ändringen i lokalt AD, kan Azure AD Connect synkroniserar ändringen. Felrapport för synkronisering i Azure AD Connect Health för synkronisering uppdateras var 30: e minut och innehåller fel från det senaste synkroniseringsförsöket.

## <a name="duplicate-attributes"></a>Duplicera attribut
### <a name="attributevaluemustbeunique"></a>AttributeValueMustBeUnique
#### <a name="description"></a>Beskrivning
Azure Active Directory-schemat tillåter inte två eller flera objekt som ska ha samma värde för följande attribut. Det är att varje objekt i Azure AD måste ha ett unikt värde av dessa attribut för en viss instans.

* proxyAddresses
* userPrincipalName

Om Azure AD Connect försöker lägga till ett nytt objekt eller uppdatera ett befintligt objekt med ett värde för ovanstående attribut som redan har tilldelats till ett annat objekt i Azure Active Directory, resulterar åtgärden i ”AttributeValueMustBeUnique” sync-fel.

#### <a name="possible-scenarios"></a>Möjliga scenarier:
1. Dubblettvärde har tilldelats ett redan synkroniserade objekt som står i konflikt med ett annat synkroniserat objekt.

#### <a name="example-case"></a>Exempel fall:
1. **Bob Smith** är en synkroniserade användare i Azure Active Directory från på lokala Active Directory contoso.com
2. Bob Smith **UserPrincipalName** lokalt har angetts som **bobs@contoso.com**.
3. Bob innehåller också följande värden för den **proxyAddresses** attribut:
   * SMTP: bobs@contoso.com
   * SMTP: bob.smith@contoso.com
   * **SMTP: bob@contoso.com**
4. En ny användare **Bob Taylor**, har lagts till i lokalerna Active Directory.
5. Bob Taylor **UserPrincipalName** har angetts som **bobt@contoso.com**.
6. **Bob Taylor** har följande värden för den **ProxyAddresses** attribut i. SMTP: bobt@contoso.com ii. SMTP: bob.taylor@contoso.com
7. Bob Taylor objekt synkroniseras med Azure AD har.
8. Administratören har valt att uppdatera Bob Taylor **ProxyAddresses** attributet med följande värde: jag. **SMTP: bob@contoso.com**
9. Azure AD försöker uppdatera Bob Taylor objektet i Azure AD med ovanstående värde, men som misslyckas åtgärden som att ProxyAddresses värdet har redan tilldelats Bob Smith, vilket resulterar i ”AttributeValueMustBeUnique”-fel.

#### <a name="how-to-fix-attributevaluemustbeunique-error"></a>Hur du löser AttributeValueMustBeUnique fel
Den vanligaste orsaken till felet AttributeValueMustBeUnique är två objekt med olika SourceAnchor \(immutableId\) har samma värde för ProxyAddresses och/eller UserPrincipalName-attribut. För att åtgärda felet för AttributeValueMustBeUnique

1. Identifiera duplicerade proxyAddresses, userPrincipalName eller andra attribut-värde som orsakar felet. Även identifiera vilka två \(eller flera\) objekt som är inblandade i konflikten. Rapporten som genereras av [Azure AD Connect Health för synkronisering](https://aka.ms/aadchsyncerrors) kan hjälpa dig identifiera de två objekten.
2. Identifiera vilka objekt ska fortsätta att få det duplicerade värdet och vilket objekt bör inte.
3. Ta bort duplicerade värdet från det objekt som inte ska ha värdet. Observera att du ska göra ändringen i katalogen där objektet kommer från. I vissa fall kan behöva du ta bort ett objekt i konflikt.
4. Om du gjort ändringen i lokalt AD, kan Azure AD Connect synkroniseras ändringen för felet för att hämta har åtgärdats.

#### <a name="related-articles"></a>Relaterade artiklar
-[Dubblett eller ogiltigt attribut förhindra katalogsynkronisering i Office 365](https://support.microsoft.com/kb/2647098)

## <a name="data-validation-failures"></a>Dataverifiering
### <a name="identitydatavalidationfailed"></a>IdentityDataValidationFailed
#### <a name="description"></a>Beskrivning
Azure Active Directory tillämpar olika begränsningar för själva informationen innan dessa data ska skrivas till katalogen. Det här är så att slutanvändarna får bästa möjliga upplevelse när du använder de program som är beroende av den här informationen.

#### <a name="scenarios"></a>Scenarier
a. Värdet för attributet UserPrincipalName har ogiltigt/stöds inte tecken.
b. Attributet UserPrincipalName följer inte formatet som krävs.

#### <a name="how-to-fix-identitydatavalidationfailed-error"></a>Hur du löser IdentityDataValidationFailed fel
a. Se till att attributet userPrincipalName har stöds tecken och format som krävs.

#### <a name="related-articles"></a>Relaterade artiklar
* [Förbereda för att etablera användare via katalogsynkronisering på Office 365](https://support.office.com/article/Prepare-to-provision-users-through-directory-synchronization-to-Office-365-01920974-9e6f-4331-a370-13aea4e82b3e)

### <a name="federateddomainchangeerror"></a>FederatedDomainChangeError
#### <a name="description"></a>Beskrivning
Det här fallet resulterar i en **”FederatedDomainChangeError”** synkronisera fel när du suffix för en användares UserPrincipalName ändras från en federerad domän till en annan federerad domän.

#### <a name="scenarios"></a>Scenarier
För en synkroniserad användare ändrades UserPrincipalName-suffix från en federerad domän till en annan federerad domän lokalt. Till exempel *UserPrincipalName = bob@contoso.com*  har ändrats till *UserPrincipalName = bob@fabrikam.com* .

#### <a name="example"></a>Exempel
1. Bob Smith, ett konto för Contoso.com, hämtar läggas till som en ny användare i Active Directory med UserPrincipalName bob@contoso.com
2. Bob flyttas till en annan avdelning contoso.com som kallas Fabrikam.com och hans UserPrincipalName ändras till bob@fabrikam.com
3. Både contoso.com och fabrikam.com domäner är federerade domäner med Azure Active Directory.
4. Bobs userPrincipalName uppdateras inte och resulterar i ett ”FederatedDomainChangeError” synkroniseringsfel.

#### <a name="how-to-fix"></a>Hur du åtgärdar
Om en användares UserPrincipalName suffix har uppdaterats från bob @**contoso.com** till bob @**fabrikam.com**, där båda **contoso.com** och **fabrikam.com** är **federerade domäner**, Följ stegen nedan för att åtgärda sync-fel

1. Uppdatera användarens UserPrincipalName i Azure AD från bob@contoso.com till bob@contoso.onmicrosoft.com. Du kan använda följande PowerShell-kommando med Azure AD PowerShell-modulen: `Set-MsolUserPrincipalName -UserPrincipalName bob@contoso.com -NewUserPrincipalName bob@contoso.onmicrosoft.com`
2. Tillåt kommer nästa synkroniseringscykel att försöka synkronisering. Den här tidssynkronisering kommer att lyckas och den kommer att uppdatera UserPrincipalName Bob till bob@fabrikam.com som förväntat.

#### <a name="related-articles"></a>Relaterade artiklar
* [Ändringarna synkroniserats inte med Azure Active Directory Sync-verktyget när du har ändrat UPN-namnet för ett användarkonto om du vill använda en annan federerad domän](https://support.microsoft.com/help/2669550/changes-aren-t-synced-by-the-azure-active-directory-sync-tool-after-you-change-the-upn-of-a-user-account-to-use-a-different-federated-domain)

## <a name="largeobject"></a>LargeObject
### <a name="description"></a>Beskrivning
När ett attribut överskrider den tillåtna storleksgränsen, längdbegränsningen eller gränsvärdet som anges av Azure Active Directory-schemat, synkroniseringsåtgärden resulterar i den **LargeObject** eller **ExceededAllowedLength**fel. Det här felet uppstår vanligen för följande attribut

* userCertificate
* userSMIMECertificate
* thumbnailPhoto
* proxyAddresses

### <a name="possible-scenarios"></a>Möjliga scenarier
1. Bobs userCertificate-attributet lagrar för många certifikat som har tilldelats Bob. Dessa kan innehålla äldre, utgångna certifikat. Hård gräns är 15 certifikat. Mer information om hur du hanterar LargeObject-fel med userCertificate-attributet finns i artikeln [hantera LargeObject-fel som orsakats av userCertificate-attributet](tshoot-connect-largeobjecterror-usercertificate.md).
2. Bobs userSMIMECertificate attributet lagrar för många certifikat som har tilldelats Bob. Dessa kan innehålla äldre, utgångna certifikat. Hård gräns är 15 certifikat.
3. Bobs thumbnailPhoto i Active Directory är för stor för att synkroniseras i Azure AD.
4. Ett objekt har för många ProxyAddresses tilldelad vid automatisk ifyllning av attributet ProxyAddresses i Active Directory.

### <a name="how-to-fix"></a>Hur du åtgärdar
1. Kontrollera att attributet som orsakar felet ligger inom den tillåtna begränsningen.

## <a name="related-links"></a>Relaterade länkar
* [Hitta Active Directory-objekt i Active Directory Administrationscenter](https://technet.microsoft.com/library/dd560661.aspx)
* [Hur du frågar Azure Active Directory för ett objekt med Azure Active Directory PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx)
