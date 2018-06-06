---
title: Azure AD Connect flera domäner
description: Det här dokumentet beskriver hur du konfigurerar och konfigurera flera domäner på toppnivå med O365 och Azure AD.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: curtand
ms.assetid: 5595fb2f-2131-4304-8a31-c52559128ea4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 2f596f64041b3d429b99db482cd635ab441bf468
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "34801515"
---
# <a name="multiple-domain-support-for-federating-with-azure-ad"></a>Stöd för flera domäner för federering med Azure AD
Följande dokumentation innehåller information om hur du använder flera domäner på toppnivå och underdomäner när federering med Office 365 eller Azure AD-domäner.

## <a name="multiple-top-level-domain-support"></a>Stöd för flera toppdomäner
Federering flera domäner på toppnivå med Azure AD kräver ytterligare konfiguration som inte krävs när federerar med en toppnivådomän.

När en domän är federerat med Azure AD, ange flera egenskaper på domänen i Azure.  Ett är viktigt IssuerUri.  Den här egenskapen är en URI som används av Azure AD för att identifiera den domän som token som är associerad med.  URI: N behöver inte matcha till något annat än det måste vara en giltig URI.  Standard Azure AD anger URI: N till värdet för federationstjänstidentifieraren i din lokala AD FS konfiguration.

> [!NOTE]
> Identifierare för federation service är en URI som unikt identifierar en federationstjänst.  Federationstjänsten är en instans av AD FS som fungerar som säkerhetstokentjänsten.
>
>

Du kan visa IssuerUri med hjälp av PowerShell-kommando `Get-MsolDomainFederationSettings -DomainName <your domain>`.

![Get-MsolDomainFederationSettings](./media/active-directory-multiple-domains/MsolDomainFederationSettings.png)

Ett problem uppstår när du lägger till fler än en toppnivådomän.  Till exempel anta att du har konfigurerat federation mellan Azure AD och din lokala miljö.  För det här dokumentet, domän, används bmcontoso.com.  Nu en andra, på den översta nivån domän, bmfabrikam.com har lagts till.

![Domäner](./media/active-directory-multiple-domains/domains.png)

Ett fel uppstår när du försöker konvertera bmfabrikam.com domänen att bli federerad.  Orsaken är Azure AD har en begränsning som inte tillåter att egenskapen IssuerUri ska ha samma värde för fler än en domän.  

![Federation fel](./media/active-directory-multiple-domains/error.png)

### <a name="supportmultipledomain-parameter"></a>SupportMultipleDomain Parameter
Du kan undvika den här begränsningen måste du lägga till en annan IssuerUri som kan göras med hjälp av den `-SupportMultipleDomain` parameter.  Den här parametern används tillsammans med följande cmdlets:

* `New-MsolFederatedDomain`
* `Convert-MsolDomaintoFederated`
* `Update-MsolFederatedDomain`

Denna parameter gör Azure AD som konfigurerar IssuerUri så att den är baserad på namnet på domänen.  IssuerUri är unik i kataloger i Azure AD.  Med hjälp av parametern kan PowerShell-kommandot ska slutföras.

![Federation fel](./media/active-directory-multiple-domains/convert.png)

Tittar på inställningarna för bmfabrikam.com-domän som du kan se följande:

![Federation fel](./media/active-directory-multiple-domains/settings.png)

`-SupportMultipleDomain` de övriga slutpunkterna som fortfarande är konfigurerade för att peka till federationstjänsten på adfs.bmcontoso.com ändras inte.

En annan sak som `-SupportMultipleDomain` har är det garanterar att AD FS-system innehåller värdet för rätt utfärdaren i token som utfärdats för Azure AD. Det här värdet anges genom att använda den som domändel av användarna UPN och ange domänen i IssuerUri, d.v.s. https://{upn suffix} / adfs-services-förtroendet.

Därmed under autentiseringen till Azure AD eller Office 365, elementet IssuerUri i användarens token som används för att leta upp domänen i Azure AD.  Om en matchning hittas, misslyckas autentiseringen.

Till exempel om en användares UPN är bsimon@bmcontoso.com, elementet IssuerUri i token, AD FS-problem, kommer att anges som http://bmcontoso.com/adfs/services/trust. Det här elementet matchar Azure AD-konfiguration och autentiseringen lyckas.

Följande är anpassade anspråksregeln som implementerar denna logik:

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));


> [!IMPORTANT]
> För att kunna använda växeln - SupportMultipleDomain vid försök att lägga till nya eller konvertera redan befintliga domäner, måste din federerat förtroende har redan ställts som stöder dessa.
>
>

## <a name="how-to-update-the-trust-between-ad-fs-and-azure-ad"></a>Så här uppdaterar du förtroende mellan AD FS och Azure AD
Om du inte har konfigurerats federerat förtroende mellan AD FS och din instans av Azure AD, kan du behöva återskapa förtroendet.  Det beror på, när den ursprungligen har angetts utan den `-SupportMultipleDomain` parameter, IssuerUri anges med standardvärdet.  I skärmbilden nedan ser du IssuerUri är inställd på https://adfs.bmcontoso.com/adfs/services/trust.

Om du har lagt till en ny domän i Azure AD-portalen och sedan försöker konvertera den med hjälp av `Convert-MsolDomaintoFederated -DomainName <your domain>`, du får följande fel.

![Federation fel](./media/active-directory-multiple-domains/trust1.png)

Om du försöker lägga till den `-SupportMultipleDomain` växla, får du följande felmeddelande:

![Federation fel](./media/active-directory-multiple-domains/trust2.png)

Bara försöker köra `Update-MsolFederatedDomain -DomainName <your domain> -SupportMultipleDomain` på den ursprungliga domänen också resulterar i ett fel.

![Federation fel](./media/active-directory-multiple-domains/trust3.png)

Följ anvisningarna nedan för att lägga till en ytterligare toppnivådomän.  Om du redan har lagt till en domän och inte har använt den `-SupportMultipleDomain` parameter, börjar med stegen för att ta bort och uppdatera din ursprungliga domän.  Om du inte har lagt till en toppnivådomän ännu kan starta du med stegen för att lägga till en domän med hjälp av PowerShell för Azure AD Connect.

Använd följande steg för att ta bort Microsoft Online-förtroende och uppdatera din ursprungliga domän.

1. På din AD FS-federationsserver öppna **AD FS-hantering.**
2. Till vänster, expandera **förtroenden** och **förtroende för förlitande part**
3. Ta bort till höger i **Identitetsplattformen för Microsoft Office 365** post.
   ![Ta bort Microsoft Online](./media/active-directory-multiple-domains/trust4.png)
4. På en dator som har [Azure Active Directory-modulen för Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx) installerad kör du följande: `$cred=Get-Credential`.  
5. Ange användarnamn och lösenord för en global administratör för Azure AD-domänen du federerar med.
6. Ange i PowerShell `Connect-MsolService -Credential $cred`
7. Ange i PowerShell `Update-MSOLFederatedDomain -DomainName <Federated Domain Name> -SupportMultipleDomain`.  Denna uppdatering är för den ursprungliga domänen.  Det med hjälp av ovanstående domäner som den skulle ha:  `Update-MsolFederatedDomain -DomainName bmcontoso.com -SupportMultipleDomain`

Använd följande steg för att lägga till den nya översta domänen med hjälp av PowerShell

1. På en dator som har [Azure Active Directory-modulen för Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx) installerad kör du följande: `$cred=Get-Credential`.  
2. Ange användarnamn och lösenord för en global administratör för Azure AD-domänen du federerar med
3. Ange i PowerShell `Connect-MsolService -Credential $cred`
4. Ange i PowerShell `New-MsolFederatedDomain –SupportMultipleDomain –DomainName`

Använd följande steg för att lägga till den nya översta domänen med Azure AD Connect.

1. Starta Azure AD Connect från skrivbordet eller start-menyn
2. Välj ”Lägg till en ytterligare Azure AD-domän” ![Lägg till en Azure AD-domän](./media/active-directory-multiple-domains/add1.png)
3. Ange din Azure AD och Active Directory-autentiseringsuppgifter
4. Välj den andra domänen som du vill konfigurera för federation.
   ![Lägg till en Azure AD-domän](./media/active-directory-multiple-domains/add2.png)
5. Klicka på Installera

### <a name="verify-the-new-top-level-domain"></a>Kontrollera den nya översta domänen
Med hjälp av PowerShell-kommando `Get-MsolDomainFederationSettings -DomainName <your domain>`du kan visa den uppdaterade IssuerUri.  Skärmbilden nedan visar federationen inställningarna har uppdaterats i den ursprungliga domänen http://bmcontoso.com/adfs/services/trust

![Get-MsolDomainFederationSettings](./media/active-directory-multiple-domains/MsolDomainFederationSettings.png)

Och IssuerUri på den nya domänen har ställts in på https://bmfabrikam.com/adfs/services/trust

![Get-MsolDomainFederationSettings](./media/active-directory-multiple-domains/settings2.png)

## <a name="support-for-subdomains"></a>Stöd för underdomäner
När du lägger till en underdomän på grund av hur Azure AD hanterade domäner ärver den inställningarna för överordnat.  Därför måste IssuerUri, matcha överordnade.

Så kan anta exempelvis att jag har bmcontoso.com och Lägg sedan till corp.bmcontoso.com.  IssuerUri för en användare från corp.bmcontoso.com måste vara  **http://bmcontoso.com/adfs/services/trust.**  Men standard regeln implementerats ovan för Azure AD, genererar en token med en utfärdare som  **http://corp.bmcontoso.com/adfs/services/trust.** som inte matchar domänens krävs värdet och autentiseringen misslyckas.

### <a name="how-to-enable-support-for-subdomains"></a>Aktivera stöd för underdomäner
AD FS förtroende för förlitande part för Microsoft Online måste uppdateras för att undvika problemet.  Om du vill göra detta måste konfigurera du en anpassad anspråksregel så att den tar ut underdomäner från användarens UPN-suffixet när man skapar anpassade utfärdaren värdet.

Följande anspråk kommer att göra det:

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^.*@([^.]+\.)*?(?<domain>([^.]+\.?){2})$", "http://${domain}/adfs/services/trust/"));

[!NOTE]
Det sista numret i uppsättningen av reguljärt uttryck är hur många överordnade domäner det är i rotdomänen. Här används bmcontoso.com, så att två överordnade domäner krävs. Om tre överordnade domäner har hållas (d.v.s.: corp.bmcontoso.com), och sedan antalet skulle ha varit tre. Slutligen kan en intervallet anges matchningen görs alltid att matcha maximalt antalet domäner. ”{2,3}” matchar två till tre domäner (d.v.s.: bmfabrikam.com och corp.bmcontoso.com).

Använd följande steg om du vill lägga till ett anpassat anspråk för att stödja underdomäner.

1. Öppna AD FS-hantering
2. Högerklicka på Microsoft Online RP-förtroende och väljer Redigera anspråksregler
3. Välj den tredje anspråksregeln och Ersätt ![redigera anspråk](./media/active-directory-multiple-domains/sub1.png)
4. Ersätt det aktuella anspråket:

        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)","http://${domain}/adfs/services/trust/"));

       with

        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^.*@([^.]+\.)*?(?<domain>([^.]+\.?){2})$", "http://${domain}/adfs/services/trust/"));

    ![Ersätt anspråk](./media/active-directory-multiple-domains/sub2.png)

5. Klicka på Ok.  Klicka på Använd.  Klicka på Ok.  Stäng AD FS-hantering.

## <a name="next-steps"></a>Nästa steg
Nu när Azure AD Connect är installerat kan du [verifiera installationen och tilldela licenser](active-directory-aadconnect-whats-next.md).

Lär dig mer om dessa funktioner, som aktiverades med installationen: [Automatisk uppgradering](active-directory-aadconnect-feature-automatic-upgrade.md), [Förhindra oavsiktliga borttagningar](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) och [Azure AD Connect Health](../connect-health/active-directory-aadconnect-health-sync.md).

Läs mer om [schemaläggaren och hur du utlöser synkronisering](active-directory-aadconnectsync-feature-scheduler.md).

Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md).