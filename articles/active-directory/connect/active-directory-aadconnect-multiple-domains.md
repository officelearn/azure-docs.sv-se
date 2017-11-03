---
title: "Azure AD Connect flera domäner"
description: "Det här dokumentet beskriver hur du konfigurerar och konfigurera flera domäner på toppnivå med O365 och Azure AD."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: curtand
ms.assetid: 5595fb2f-2131-4304-8a31-c52559128ea4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 8e3f496c2868cc3430e0efd47805aec2205168aa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="multiple-domain-support-for-federating-with-azure-ad"></a>Stöd för flera domäner för federering med Azure AD
Följande dokumentation innehåller information om hur du använder flera domäner på toppnivå och underdomäner när federering med Office 365 eller Azure AD-domäner.

## <a name="multiple-top-level-domain-support"></a>Stöd för flera toppdomäner
Federering flera domäner på toppnivå med Azure AD kräver ytterligare konfiguration som inte krävs när federerar med en toppnivådomän.

När en domän är federerat med Azure AD, ange flera egenskaper på domänen i Azure.  Ett är viktigt IssuerUri.  Det här är en URI som används av Azure AD för att identifiera den domän som token som är associerad med.  URI: N behöver inte matcha till något annat än det måste vara en giltig URI.  Standard Azure AD anger detta till värdet för federationstjänstidentifieraren i din lokala AD FS konfiguration.

> [!NOTE]
> Identifierare för federation service är en URI som unikt identifierar en federationstjänst.  Federationstjänsten är en instans av AD FS som fungerar som säkerhetstokentjänsten. 
> 
> 

Du kan visa IssuerUri med hjälp av PowerShell-kommando `Get-MsolDomainFederationSettings -DomainName <your domain>`.

![Get-MsolDomainFederationSettings](./media/active-directory-multiple-domains/MsolDomainFederationSettings.png)

Ett problem uppstår när vi vill lägga till fler än en toppnivådomän.  Till exempel anta att du har installationen federation mellan Azure AD och din lokala miljö.  Jag använder bmcontoso.com för det här dokumentet.  Nu jag har lagt till en andra, på den översta nivån domän, bmfabrikam.com.

![Domäner](./media/active-directory-multiple-domains/domains.png)

När vi försöker konvertera vår bmfabrikam.com domän ska vara federerad får vi ett felmeddelande.  Anledningen är Azure AD har en begränsning som inte tillåter att egenskapen IssuerUri ska ha samma värde för fler än en domän.  

![Federation fel](./media/active-directory-multiple-domains/error.png)

### <a name="supportmultipledomain-parameter"></a>SupportMultipleDomain Parameter
För att lösa detta, behöver vi lägga till en annan IssuerUri som kan göras med hjälp av den `-SupportMultipleDomain` parameter.  Den här parametern används tillsammans med följande cmdlets:

* `New-MsolFederatedDomain`
* `Convert-MsolDomaintoFederated`
* `Update-MsolFederatedDomain`

Denna parameter gör Azure AD som konfigurerar IssuerUri så att den är baserad på namnet på domänen.  Detta är unik i kataloger i Azure AD.  Med hjälp av parametern kan PowerShell-kommandot ska slutföras.

![Federation fel](./media/active-directory-multiple-domains/convert.png)

Tittar på inställningarna på vår nya bmfabrikam.com domänen som du kan se följande:

![Federation fel](./media/active-directory-multiple-domains/settings.png)

Observera att `-SupportMultipleDomain` ändras inte de andra slutpunkterna som fortfarande är konfigurerade för att peka till vår federationstjänsten på adfs.bmcontoso.com.

En annan sak som `-SupportMultipleDomain` har är det garanterar att AD FS-system innehåller värdet för rätt utfärdaren i token som utfärdats för Azure AD. Detta åstadkoms genom att använda den som domändel av användarna UPN och inställningar som domänen i IssuerUri, d.v.s. https://{upn suffix} / adfs-services-förtroendet. 

Därmed under autentiseringen till Azure AD eller Office 365, elementet IssuerUri i användarens token som används för att leta upp domänen i Azure AD.  Om en matchning inte hittas autentiseringen misslyckas. 

Till exempel om en användares UPN är bsimon@bmcontoso.com, elementet IssuerUri i token AD FS problem kommer att ställas in http://bmcontoso.com/adfs/services/trust. Detta motsvarar Azure AD-konfigurationen och autentiseringen lyckas.

Följande är anpassade anspråksregeln som implementerar denna logik:

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));


> [!IMPORTANT]
> För att kunna använda växeln - SupportMultipleDomain när du försöker lägga till nya eller konvertera redan lagts till domäner, som du behöver ha installationsprogrammet din federerat förtroende som ursprungligen stöder dessa.  
> 
> 

## <a name="how-to-update-the-trust-between-ad-fs-and-azure-ad"></a>Så här uppdaterar du förtroende mellan AD FS och Azure AD
Om du inte konfigurera federerat förtroende mellan AD FS och din instans av Azure AD, kan du behöva återskapa förtroendet.  Detta beror på att när den är ursprungligen installationen utan den `-SupportMultipleDomain` parameter, IssuerUri anges med standardvärdet.  I skärmbilden nedan du ser IssuerUri är inställd på https://adfs.bmcontoso.com/adfs/services/trust.

Så nu, om vi har lagt till en ny domän i Azure AD-portalen och försök sedan att konvertera den med hjälp av `Convert-MsolDomaintoFederated -DomainName <your domain>`, vi använder följande felmeddelande.

![Federation fel](./media/active-directory-multiple-domains/trust1.png)

Om du försöker lägga till den `-SupportMultipleDomain` växel som vi får du följande felmeddelande:

![Federation fel](./media/active-directory-multiple-domains/trust2.png)

Bara försöker köra `Update-MsolFederatedDomain -DomainName <your domain> -SupportMultipleDomain` på den ursprungliga domänen också resulterar i ett fel.

![Federation fel](./media/active-directory-multiple-domains/trust3.png)

Följ anvisningarna nedan för att lägga till en ytterligare toppnivådomän.  Om du redan har lagt till en domän och inte har använt den `-SupportMultipleDomain` parametern börjar med stegen för att ta bort och uppdatera din ursprungliga domän.  Om du inte har lagt till en toppnivådomän kan ännu du med steg för att lägga till en domän med hjälp av PowerShell för Azure AD Connect.

Använd följande steg för att ta bort Microsoft Online-förtroende och uppdatera din ursprungliga domän.

1. På din AD FS-federationsserver öppna **AD FS-hantering.** 
2. Till vänster, expandera **förtroenden** och **förtroende för förlitande part**
3. Ta bort till höger i **Identitetsplattformen för Microsoft Office 365** post.
   ![Ta bort Microsoft Online](./media/active-directory-multiple-domains/trust4.png)
4. På en dator som har [Azure Active Directory-modulen för Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx) installerad kör du följande: `$cred=Get-Credential`.  
5. Ange användarnamn och lösenord för en global administratör för Azure AD-domänen du federerar med
6. Ange i PowerShell`Connect-MsolService -Credential $cred`
7. Ange i PowerShell `Update-MSOLFederatedDomain -DomainName <Federated Domain Name> -SupportMultipleDomain`.  Detta är den ursprungliga domänen.  Det med hjälp av ovanstående domäner som den skulle ha:`Update-MsolFederatedDomain -DomainName bmcontoso.com -SupportMultipleDomain`

Använd följande steg för att lägga till den nya översta domänen med hjälp av PowerShell

1. På en dator som har [Azure Active Directory-modulen för Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx) installerad kör du följande: `$cred=Get-Credential`.  
2. Ange användarnamn och lösenord för en global administratör för Azure AD-domänen du federerar med
3. Ange i PowerShell`Connect-MsolService -Credential $cred`
4. Ange i PowerShell`New-MsolFederatedDomain –SupportMultipleDomain –DomainName`

Använd följande steg för att lägga till den nya översta domänen med Azure AD Connect.

1. Starta Azure AD Connect från skrivbordet eller start-menyn
2. Välj ”Lägg till en ytterligare Azure AD-domän” ![Lägg till en Azure AD-domän](./media/active-directory-multiple-domains/add1.png)
3. Ange din Azure AD och Active Directory-autentiseringsuppgifter
4. Välj den andra domänen som du vill konfigurera för federation.
   ![Lägg till en Azure AD-domän](./media/active-directory-multiple-domains/add2.png)
5. Klicka på Installera

### <a name="verify-the-new-top-level-domain"></a>Kontrollera den nya översta domänen
Med hjälp av PowerShell-kommando `Get-MsolDomainFederationSettings -DomainName <your domain>`du kan visa den uppdaterade IssuerUri.  Skärmbilden nedan visar federationen inställningarna har uppdaterats i vår ursprungliga domän http://bmcontoso.com/adfs/services/trust

![Get-MsolDomainFederationSettings](./media/active-directory-multiple-domains/MsolDomainFederationSettings.png)

Och IssuerUri på vår nya domänen har ställts in på https://bmfabrikam.com/adfs/services/trust

![Get-MsolDomainFederationSettings](./media/active-directory-multiple-domains/settings2.png)

## <a name="support-for-sub-domains"></a>Stöd för underordnade domäner
När du lägger till en underordnad domän, på grund av hur Azure AD hanterade domäner ärver den inställningarna för överordnat.  Det innebär att IssuerUri måste matcha överordnade.

Så kan anta till exempel att jag har bmcontoso.com och Lägg sedan till corp.bmcontoso.com.  Det innebär att IssuerUri för en användare från corp.bmcontoso.com måste vara **http://bmcontoso.com/adfs/services/trust.**  Men standard regeln implementerats ovan för Azure AD, genererar en token med en utfärdare som **http://corp.bmcontoso.com/adfs/services/trust.** som inte matchar domänens krävs värdet och autentiseringen misslyckas.

### <a name="how-to-enable-support-for-sub-domains"></a>Så här aktiverar du stöd för underordnade domäner
Förlitande part för Microsoft Online måste uppdateras för att komma runt detta AD FS.  Om du vill göra detta måste konfigurera du en anpassad anspråksregel så att den tar ut alla underordnade domäner från användarens UPN-suffixet när man skapar anpassade utfärdaren värdet. 

Följande anspråk kommer att göra det:

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^.*@([^.]+\.)*?(?<domain>([^.]+\.?){2})$", "http://${domain}/adfs/services/trust/"));

[!NOTE]
Det sista numret i det reguljära uttrycket ange hur många överordnade domäner som finns i rotdomänen. Här finns i bmcontoso.com så att två överordnade domäner krävs. Om tre överordnade domäner har hållas (d.v.s.: corp.bmcontoso.com), och sedan antalet skulle ha varit tre. Eventualy kan ett intervall anges matchningen görs alltid att matcha maximalt antalet domäner. {2,3}-matchar två till tre domäner (d.v.s.: bmfabrikam.com och corp.bmcontoso.com).

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

