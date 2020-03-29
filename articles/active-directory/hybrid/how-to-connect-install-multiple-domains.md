---
title: Azure AD Connect flera domäner
description: Det här dokumentet beskriver hur du konfigurerar och konfigurerar flera toppdomäner med O365 och Azure AD.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 5595fb2f-2131-4304-8a31-c52559128ea4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/31/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 18b5f19e3e994aa05fa99caf360d0c1be69ec7a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049777"
---
# <a name="multiple-domain-support-for-federating-with-azure-ad"></a>Stöd för flera domäner för federering med Azure AD
Följande dokumentation innehåller vägledning om hur du använder flera toppdomäner och underdomäner när du matar med Office 365- eller Azure AD-domäner.

## <a name="multiple-top-level-domain-support"></a>Flera domänstöd på toppnivå
Federerning av flera toppdomäner med Azure AD kräver ytterligare konfiguration som inte krävs när du federerar med en toppdomän.

När en domän federeras med Azure AD anges flera egenskaper på domänen i Azure.  En viktig är IssuerUri.  Den här egenskapen är en URI som används av Azure AD för att identifiera den domän som token är associerad med.  URI behöver inte lösa något men det måste vara en giltig URI.  Som standard anger Azure AD URI till värdet för federationstjänstidentifieraren i din lokala AD FS-konfiguration.

> [!NOTE]
> Federationstjänstidentifieraren är en URI som unikt identifierar en federationstjänst.  Federationstjänsten är en instans av AD FS som fungerar som säkerhetstokentjänst.
>
>

Du kan visa IssuerUri med kommandot `Get-MsolDomainFederationSettings -DomainName <your domain>`PowerShell .

![Get-MsolDomainFederationSettings](./media/how-to-connect-install-multiple-domains/MsolDomainFederationSettings.png)

Ett problem uppstår när du lägger till mer än en toppdomän.  Anta till exempel att du har konfigurerat federation mellan Azure AD och din lokala miljö.  För det här dokumentet används domänen, bmcontoso.com.  Nu har en andra domän på den översta nivån bmfabrikam.com lagts till.

![Domäner](./media/how-to-connect-install-multiple-domains/domains.png)

När du försöker konvertera den bmfabrikam.com domän som ska federeras uppstår ett fel.  Anledningen är att Azure AD har en begränsning som inte tillåter att egenskapen IssuerUri har samma värde för mer än en domän.  

![Federationsfel](./media/how-to-connect-install-multiple-domains/error.png)

### <a name="supportmultipledomain-parameter"></a>StödMultipleDomain Parameter
För att komma runt den här begränsningen måste du lägga till `-SupportMultipleDomain` en annan IssuerUri, vilket kan göras med hjälp av parametern.  Den här parametern används med följande cmdlets:

* `New-MsolFederatedDomain`
* `Convert-MsolDomaintoFederated`
* `Update-MsolFederatedDomain`

Den här parametern gör att Azure AD konfigurerar IssuerUri så att den baseras på namnet på domänen.  IssuerUri kommer att vara unikt över kataloger i Azure AD.  Med hjälp av parametern kan PowerShell-kommandot slutföras.

![Federationsfel](./media/how-to-connect-install-multiple-domains/convert.png)

Om du tittar på inställningarna för bmfabrikam.com domänen kan du se följande:

![Federationsfel](./media/how-to-connect-install-multiple-domains/settings.png)

`-SupportMultipleDomain`ändrar inte de andra slutpunkterna, som fortfarande är konfigurerade för att peka på federationstjänsten på adfs.bmcontoso.com.

En annan `-SupportMultipleDomain` sak som gör det är att det säkerställer att AD FS-systemet innehåller rätt Utfärdarvärde i token som utfärdats för Azure AD. Det här värdet anges genom att domändelen av användarna UPN och ange den som domän i UtfärdarUri, dvs https://{upn suffix}/adfs/services/trust.

Under autentisering till Azure AD eller Office 365 används därför IssuerUri-elementet i användarens token för att hitta domänen i Azure AD.  Om det inte går att hitta en matchning misslyckas autentiseringen.

Om till exempel en användares bsimon@bmcontoso.comUPN är , kommer IssuerUri-elementet i token, AD FS-problem, att `http://bmcontoso.com/adfs/services/trust`ställas in på . Det här elementet matchar Azure AD-konfigurationen och autentiseringen kommer att lyckas.

Följande är den anpassade anspråksregeln som implementerar den här logiken:

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));


> [!IMPORTANT]
> För att använda växeln -SupportMultipleDomain när du försöker lägga till nya eller konvertera redan befintliga domäner måste ditt federerade förtroende redan ha konfigurerats för att stödja dem.
>
>

## <a name="how-to-update-the-trust-between-ad-fs-and-azure-ad"></a>Så här uppdaterar du förtroendet mellan AD FS och Azure AD
Om du inte har konfigurerat det federerade förtroendet mellan AD FS och din instans av Azure AD kan du behöva återskapa det här förtroendet.  Anledningen är att när den ursprungligen `-SupportMultipleDomain` ställs in utan parametern anges IssuerUri med standardvärdet.  I skärmbilden nedan kan du se Att `https://adfs.bmcontoso.com/adfs/services/trust`IssuerUri är inställd på .

Om du har lagt till en ny domän i Azure AD-portalen och sedan försöker konvertera den med `Convert-MsolDomaintoFederated -DomainName <your domain>`får du följande fel.

![Federationsfel](./media/how-to-connect-install-multiple-domains/trust1.png)

Om du försöker `-SupportMultipleDomain` lägga till växeln visas följande felmeddelande:

![Federationsfel](./media/how-to-connect-install-multiple-domains/trust2.png)

Att bara `Update-MsolFederatedDomain -DomainName <your domain> -SupportMultipleDomain` försöka köra på den ursprungliga domänen kommer också att resultera i ett fel.

![Federationsfel](./media/how-to-connect-install-multiple-domains/trust3.png)

Följ stegen nedan för att lägga till ytterligare en toppdomän.  Om du redan har lagt till en `-SupportMultipleDomain` domän och inte har använt parametern börjar du med stegen för att ta bort och uppdatera den ursprungliga domänen.  Om du inte har lagt till en domän på den högsta nivån ännu kan du börja med stegen för att lägga till en domän med PowerShell i Azure AD Connect.

Följ följande steg för att ta bort Microsoft Online-förtroendet och uppdatera den ursprungliga domänen.

1. Öppna **AD FS-hantering** på AD FS-federationsservern.
2. Till vänster expanderar du **förtroenderelationer** och **förtroenden för förlitande part**
3. Till höger tar du bort posten **Microsoft Office 365 Identity Platform.**
   ![Ta bort Microsoft Online](./media/how-to-connect-install-multiple-domains/trust4.png)
4. På en dator som har [Azure Active Directory Module för Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx) installerat på den körs följande: `$cred=Get-Credential`.  
5. Ange användarnamn och lösenord för en global administratör för Azure AD-domänen som du matar med.
6. Ange`Connect-MsolService -Credential $cred`
7. Ange i `Update-MSOLFederatedDomain -DomainName <Federated Domain Name> -SupportMultipleDomain`PowerShell .  Den här uppdateringen är för den ursprungliga domänen.  Så med hjälp av ovanstående domäner skulle det vara:`Update-MsolFederatedDomain -DomainName bmcontoso.com -SupportMultipleDomain`

Följ följande steg för att lägga till den nya toppdomänen med PowerShell

1. På en dator som har [Azure Active Directory Module för Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx) installerat på den körs följande: `$cred=Get-Credential`.  
2. Ange användarnamn och lösenord för en global administratör för Azure AD-domänen som du matar med
3. Ange`Connect-MsolService -Credential $cred`
4. Ange`New-MsolFederatedDomain –SupportMultipleDomain –DomainName`

Följ följande steg för att lägga till den nya toppdomänen med Azure AD Connect.

1. Starta Azure AD Connect från skrivbordet eller startmenyn
2. Välj Lägg till ytterligare en ![Azure AD-domän, Lägg till ytterligare en Azure AD-domän](./media/how-to-connect-install-multiple-domains/add1.png)
3. Ange dina Azure AD- och Active Directory-autentiseringsuppgifter
4. Välj den andra domän som du vill konfigurera för federationen.
   ![Lägga till ytterligare en Azure AD-domän](./media/how-to-connect-install-multiple-domains/add2.png)
5. Klicka på Installera

### <a name="verify-the-new-top-level-domain"></a>Verifiera den nya domänen på den översta nivån
Med kommandot PowerShell `Get-MsolDomainFederationSettings -DomainName <your domain>`kan du visa den uppdaterade IssuerUri.  Skärmbilden nedan visar att federationsinställningarna har uppdaterats på den ursprungliga domänen`http://bmcontoso.com/adfs/services/trust`

![Get-MsolDomainFederationSettings](./media/how-to-connect-install-multiple-domains/MsolDomainFederationSettings.png)

Och IssuerUri på den nya domänen har ställts in för att`https://bmfabrikam.com/adfs/services/trust`

![Get-MsolDomainFederationSettings](./media/how-to-connect-install-multiple-domains/settings2.png)

## <a name="support-for-subdomains"></a>Stöd för underdomäner
När du lägger till en underdomän, på grund av hur Azure AD hanterade domäner, ärver den den överordnade.  Så, IssuerUri, måste matcha föräldrarna.

Så låt oss till exempel säga att jag har bmcontoso.com och sedan lägga till corp.bmcontoso.com.  IssuerUri för en användare från corp.bmcontoso.com måste ** http://bmcontoso.com/adfs/services/trustvara .**  Standardregeln som implementeras ovan för Azure AD genererar dock en token med en utfärdare som ** http://corp.bmcontoso.com/adfs/services/trust.** som inte matchar domänens önskade värde och autentisering misslyckas.

### <a name="how-to-enable-support-for-subdomains"></a>Så här aktiverar du stöd för underdomäner
För att undvika detta måste AD FS-förlitande partens förtroende för Microsoft Online uppdateras.  För att göra detta måste du konfigurera en anpassad anspråksregel så att den tar bort alla underdomäner från användarens UPN-suffix när du skapar det anpassade utfärdarvärdet.

Följande anspråk kommer att göra detta:

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^.*@([^.]+\.)*?(?<domain>([^.]+\.?){2})$", "http://${domain}/adfs/services/trust/"));

[!NOTE]
Det sista numret i uppsättningen med reguljära uttryck är hur många överordnade domäner det finns i rotdomänen. Här används bmcontoso.com, så två överordnade domäner är nödvändiga. Om tre överordnade domäner skulle behållas (dvs. corp.bmcontoso.com) skulle antalet ha varit tre. Så småningom kan ett intervall anges, kommer matchningen alltid att göras för att matcha maximalt antal domäner. "{2,3}" kommer att matcha två till tre domäner (dvs. bmfabrikam.com och corp.bmcontoso.com).

Följ följande steg för att lägga till ett anpassat anspråk för att stödja underdomäner.

1. Öppna AD FS-hantering
2. Högerklicka på Microsoft Online RP-förtroendet och välj Redigera anspråksregler
3. Markera den tredje anspråksregeln och ersätt ![Redigera anspråk](./media/how-to-connect-install-multiple-domains/sub1.png)
4. Ersätt det aktuella anspråket:

        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)","http://${domain}/adfs/services/trust/"));

       with

        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^.*@([^.]+\.)*?(?<domain>([^.]+\.?){2})$", "http://${domain}/adfs/services/trust/"));

    ![Ersätt anspråk](./media/how-to-connect-install-multiple-domains/sub2.png)

5. Klicka på Ok.  Klicka på Använd.  Klicka på Ok.  Stäng AD FS-hantering.

## <a name="next-steps"></a>Nästa steg
Nu när Azure AD Connect är installerat kan du [verifiera installationen och tilldela licenser](how-to-connect-post-installation.md).

Lär dig mer om dessa funktioner, som aktiverades med installationen: [Automatisk uppgradering](how-to-connect-install-automatic-upgrade.md), [Förhindra oavsiktliga borttagningar](how-to-connect-sync-feature-prevent-accidental-deletes.md) och [Azure AD Connect Health](how-to-connect-health-sync.md).

Läs mer om [schemaläggaren och hur du utlöser synkronisering](how-to-connect-sync-feature-scheduler.md).

Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
