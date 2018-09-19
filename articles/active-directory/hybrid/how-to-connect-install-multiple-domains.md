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
ms.openlocfilehash: 8b5abe252ab9b3389680508537ea1d6f3823f910
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46311866"
---
# <a name="multiple-domain-support-for-federating-with-azure-ad"></a>Stöd för flera domäner för federering med Azure AD
Följande dokumentation innehåller råd om hur du använder flera översta domäner och underdomäner när federera med Office 365 eller Azure AD-domäner.

## <a name="multiple-top-level-domain-support"></a>Stöd för flera toppnivådomän
Federera flera domäner på toppnivå med Azure AD kräver ytterligare konfiguration som inte krävs när federera med en toppnivådomän.

När en domän är federerad med Azure AD, ställs flera egenskaper på domänen i Azure.  En viktig fråga är IssuerUri.  Den här egenskapen är en URI som används av Azure AD för att identifiera den domän som token som är associerad med.  URI: N behöver inte matcha till något annat än det måste vara en giltig URI.  Som standard Azure AD anger URI: N till värdet för federationstjänstens identifierare i din lokala AD FS konfiguration.

> [!NOTE]
> Federationstjänstens identifierare är en URI som unikt identifierar en federationstjänst.  Federationstjänsten är en instans av AD FS som fungerar som säkerhetstokentjänsten.
>
>

Du kan visa IssuerUri med hjälp av PowerShell-kommando `Get-MsolDomainFederationSettings -DomainName <your domain>`.

![Get-MsolDomainFederationSettings](./media/how-to-connect-install-multiple-domains/MsolDomainFederationSettings.png)

Ett problem uppstår när du lägger till fler än en toppnivådomän.  Exempel: Anta att du har konfigurerat federation mellan Azure AD och din lokala miljö.  I det här dokumentet domänen, används bmcontoso.com.  Nu en andra, översta domän, bmfabrikam.com har lagts till.

![Domäner](./media/how-to-connect-install-multiple-domains/domains.png)

Ett fel uppstår när du försöker konvertera bmfabrikam.com domänen som ska federeras.  Orsaken är Azure AD har en begränsning som inte tillåter egenskapen IssuerUri ha samma värde för fler än en domän.  

![Federation-fel](./media/how-to-connect-install-multiple-domains/error.png)

### <a name="supportmultipledomain-parameter"></a>SupportMultipleDomain-parametern
Du kan undvika den här begränsningen genom du behöver lägga till en annan IssuerUri som kan göras med hjälp av den `-SupportMultipleDomain` parametern.  Den här parametern används med följande cmdletar:

* `New-MsolFederatedDomain`
* `Convert-MsolDomaintoFederated`
* `Update-MsolFederatedDomain`

Den här parametern gör Azure AD, konfigurera IssuerUri så att den är baserad på namnet på domänen.  IssuerUri ska vara unikt inom kataloger i Azure AD.  Med hjälp av parametern kan PowerShell-kommandot ska slutföras.

![Federation-fel](./media/how-to-connect-install-multiple-domains/convert.png)

Titta på inställningarna för bmfabrikam.com domänen som du kan se följande:

![Federation-fel](./media/how-to-connect-install-multiple-domains/settings.png)

`-SupportMultipleDomain` ändrar inte de andra slutpunkterna som fortfarande har konfigurerats så att den pekar till federationstjänsten på adfs.bmcontoso.com.

En annan sak som `-SupportMultipleDomain` gör är att det säkerställer att AD FS-system innehåller rätt utfärdarvärdet i token som utfärdas för Azure AD. Det här värdet anges genom att ta domändelen i användare UPN och ange den som domänen i IssuerUri, d.v.s. https://{upn suffix} / adfs/services/trust.

Därför under autentiseringen till Azure AD eller Office 365, IssuerUri-elementet i användarens token används för att leta upp domänen i Azure AD.  Om en matchning hittas, misslyckas autentiseringen.

Till exempel om en användares UPN är bsimon@bmcontoso.com, IssuerUri-elementet i problem med AD FS-token upphör att http://bmcontoso.com/adfs/services/trust. Det här elementet matchar Azure AD-konfigurationen och autentiseringen lyckas.

Följande är det anpassade anspråksregeln som implementerar den här logiken:

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));


> [!IMPORTANT]
> För att kunna använda växeln - SupportMultipleDomain vid försök att lägga till nya eller konvertera befintliga domäner, måste din federerat förtroende har redan ställts in för att stödja dem.
>
>

## <a name="how-to-update-the-trust-between-ad-fs-and-azure-ad"></a>Så här uppdaterar du förtroende mellan AD FS och Azure AD
Om du inte har konfigurerats federerat förtroende mellan AD FS och din instans av Azure AD, kan du behöva återskapa det här förtroendet.  Anledningen är att när det ursprungligen har konfigurerats utan den `-SupportMultipleDomain` parametern, IssuerUri anges med standardvärdet.  I skärmbilden nedan ser du IssuerUri är inställd på https://adfs.bmcontoso.com/adfs/services/trust.

Om du har lagt till en ny domän i Azure AD-portalen och sedan försöker konvertera den med hjälp av `Convert-MsolDomaintoFederated -DomainName <your domain>`, du får följande fel.

![Federation-fel](./media/how-to-connect-install-multiple-domains/trust1.png)

Om du försöker lägga till den `-SupportMultipleDomain` växla, du får följande felmeddelande:

![Federation-fel](./media/how-to-connect-install-multiple-domains/trust2.png)

Helt enkelt försöker köra `Update-MsolFederatedDomain -DomainName <your domain> -SupportMultipleDomain` på den ursprungliga domänen resulterar också i ett fel.

![Federation-fel](./media/how-to-connect-install-multiple-domains/trust3.png)

Följ anvisningarna nedan om du vill lägga till en ytterligare toppnivådomän.  Om du har lagt till en domän och inte använde den `-SupportMultipleDomain` parameter, börja med stegen för att ta bort och uppdatera din ursprungliga domän.  Om du inte har lagt till en toppnivådomän ännu kan börja du med steg för att lägga till en domän med hjälp av PowerShell för Azure AD Connect.

Använd följande steg för att ta bort Microsoft Online-förtroende och uppdatera din ursprungliga domän.

1. På din AD FS-federationsserver öppna **AD FS-hantering.**
2. Till vänster expanderar **förtroenden** och **förtroende för förlitande part**
3. Till höger, ta bort den **Microsoft Office 365-Identitetsplattform** posten.
   ![Ta bort Microsoft Online](./media/how-to-connect-install-multiple-domains/trust4.png)
4. På en dator som har [Azure Active Directory-modulen för Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx) installerat kör du följande: `$cred=Get-Credential`.  
5. Ange användarnamnet och lösenordet för en global administratör för Azure AD-domänen som du federerar med.
6. I PowerShell, ange `Connect-MsolService -Credential $cred`
7. I PowerShell, ange `Update-MSOLFederatedDomain -DomainName <Federated Domain Name> -SupportMultipleDomain`.  Den här uppdateringen är avsedd för den ursprungliga domänen.  Det med hjälp av de ovanstående domäner som det skulle vara:  `Update-MsolFederatedDomain -DomainName bmcontoso.com -SupportMultipleDomain`

Använd följande steg för att lägga till den nya översta domänen med hjälp av PowerShell

1. På en dator som har [Azure Active Directory-modulen för Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx) installerat kör du följande: `$cred=Get-Credential`.  
2. Ange användarnamnet och lösenordet för en global administratör för Azure AD-domänen som du federerar med
3. I PowerShell, ange `Connect-MsolService -Credential $cred`
4. I PowerShell, ange `New-MsolFederatedDomain –SupportMultipleDomain –DomainName`

Använd följande steg för att lägga till den nya översta domänen med Azure AD Connect.

1. Starta Azure AD Connect från skrivbordet eller start-menyn
2. Välj ”Lägg till en ytterligare Azure AD-domän” ![lägga till ytterligare Azure AD-domän](./media/how-to-connect-install-multiple-domains/add1.png)
3. Ange din Azure AD och Active Directory-autentiseringsuppgifter
4. Välj den andra domänen som du vill konfigurera för federation.
   ![Lägg till ytterligare Azure AD-domän](./media/how-to-connect-install-multiple-domains/add2.png)
5. Klicka på Installera

### <a name="verify-the-new-top-level-domain"></a>Kontrollera den nya översta domänen
Med hjälp av PowerShell-kommando `Get-MsolDomainFederationSettings -DomainName <your domain>`du kan visa den uppdaterade IssuerUri.  Skärmbilden nedan visar federationen inställningarna har uppdaterats på den ursprungliga domänen http://bmcontoso.com/adfs/services/trust

![Get-MsolDomainFederationSettings](./media/how-to-connect-install-multiple-domains/MsolDomainFederationSettings.png)

Och IssuerUri på den nya domänen har ställts in på https://bmfabrikam.com/adfs/services/trust

![Get-MsolDomainFederationSettings](./media/how-to-connect-install-multiple-domains/settings2.png)

## <a name="support-for-subdomains"></a>Stöd för underdomäner
När du lägger till en underdomän på grund av sättet Azure AD hanteras domäner ärver den inställningarna för överordnat.  Därför måste IssuerUri, matcha överordnade.

Så kan anta exempelvis att jag har bmcontoso.com och Lägg sedan till corp.bmcontoso.com.  IssuerUri för en användare från corp.bmcontoso.com måste vara  **http://bmcontoso.com/adfs/services/trust.**  Men standard regeln implementeras ovan för Azure AD, skapar en token med en utfärdare som  **http://corp.bmcontoso.com/adfs/services/trust.** som inte matchar domänens krävs värdet och misslyckas autentiseringen.

### <a name="how-to-enable-support-for-subdomains"></a>Så här aktiverar du stöd för underdomäner
AD FS-förlitande för Microsoft Online måste uppdateras för att undvika problemet.  Om du vill göra detta måste konfigurera du en anpassad anspråksregel så att den tar bort datoridentiteten från av underdomäner från användarens UPN-suffixet när anpassade utfärdarvärdet.

Följande anspråk kommer att göra detta:

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^.*@([^.]+\.)*?(?<domain>([^.]+\.?){2})$", "http://${domain}/adfs/services/trust/"));

[!NOTE]
Det sista numret i uppsättningen reguljärt uttryck är hur många överordnade domänerna det finns i rotdomänen. Här används bmcontoso.com, så att två överordnade domäner är nödvändiga. Om tre överordnade domäner har behålls (d.v.s.: corp.bmcontoso.com), och sedan hur många skulle ha varit tre. Slutligen kan ett intervall anges matchningen görs alltid att matcha maximalt antalet domäner. ”{2,3}” matchar två till tre domäner (d.v.s.: bmfabrikam.com och corp.bmcontoso.com).

Använd följande steg för att lägga till ett anpassat anspråk för underdomäner.

1. Öppna AD FS-hantering
2. Högerklicka på Microsoft Online RP-förtroendet och välj Redigera anspråksregler
3. Välj den tredje anspråksregeln och Ersätt ![redigera anspråk](./media/how-to-connect-install-multiple-domains/sub1.png)
4. Ersätt det aktuella anspråket:

        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)","http://${domain}/adfs/services/trust/"));

       with

        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^.*@([^.]+\.)*?(?<domain>([^.]+\.?){2})$", "http://${domain}/adfs/services/trust/"));

    ![Ersätt anspråk](./media/how-to-connect-install-multiple-domains/sub2.png)

5. Klicka på Ok.  Klicka på tillämpa.  Klicka på Ok.  Stäng AD FS-hantering.

## <a name="next-steps"></a>Nästa steg
Nu när Azure AD Connect är installerat kan du [verifiera installationen och tilldela licenser](how-to-connect-post-installation.md).

Lär dig mer om dessa funktioner, som aktiverades med installationen: [Automatisk uppgradering](how-to-connect-install-automatic-upgrade.md), [Förhindra oavsiktliga borttagningar](how-to-connect-sync-feature-prevent-accidental-deletes.md) och [Azure AD Connect Health](how-to-connect-health-sync.md).

Läs mer om [schemaläggaren och hur du utlöser synkronisering](how-to-connect-sync-feature-scheduler.md).

Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).