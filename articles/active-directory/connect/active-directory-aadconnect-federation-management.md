---
title: Azure AD Connect - AD FS-hantering och anpassning | Microsoft Docs
description: AD FS-hantering med Azure AD Connect och anpassning av AD FS-inloggning användarupplevelsen med Azure AD Connect och PowerShell.
keywords: ADFS, ADFS, AD FS management, AAD Connect Connect, inloggning, AD FS anpassning, reparera förtroendet, O365, federation, förlitande part
services: active-directory
documentationcenter: ''
author: anandyadavmsft
manager: mtillman
editor: ''
ms.assetid: 2593b6c6-dc3f-46ef-8e02-a8e2dc4e9fb9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.component: hybrid
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: 276e53784b30c2196ad7455cf9fd801a103fdc30
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34590862"
---
# <a name="manage-and-customize-active-directory-federation-services-by-using-azure-ad-connect"></a>Hantera och anpassa Active Directory Federation Services med hjälp av Azure AD Connect
Den här artikeln beskriver hur du hanterar och anpassa Active Directory Federation Services (AD FS) med hjälp av Azure Active Directory (AD Azure) Anslut. Den omfattar också andra vanliga AD FS-uppgifter som du kan behöva göra för att slutföra konfigurationen av en AD FS-servergrupp.

| Avsnitt | Det täcker |
|:--- |:--- |
| **Hantera AD FS** | |
| [Reparera förtroendet](#repairthetrust) |Så här reparerar federationsförtroende med Office 365. |
| [Federera med Azure AD med hjälp av alternativa inloggnings-ID ](#alternateid) | Konfigurera federation med hjälp av alternativa inloggnings-ID  |
| [Lägga till en AD FS-server](#addadfsserver) |Hur du expanderar en AD FS-servergrupp med en ytterligare AD FS-servern. |
| [Lägga till en AD FS Web Application Proxy-server](#addwapserver) |Hur du expanderar en AD FS-servergrupp med ytterligare en Webbprogramproxy (WAP) server. |
| [Lägga till en federerad domän](#addfeddomain) |Hur du lägger till en federerad domän. |
| [Uppdatera SSL-certifikatet](active-directory-aadconnectfed-ssl-update.md)| Så här uppdaterar SSL-certifikatet för en AD FS-servergrupp. |
| **Anpassa AD FS** | |
| [Lägga till en anpassad logotyp eller bild](#customlogo) |Hur du anpassar en AD FS-inloggningssida med företagets logotyp och illustration. |
| [Lägg till en beskrivning för inloggning](#addsignindescription) |Hur du lägger till en beskrivning på inloggningssidan. |
| [Ändra anspråksregler i AD FS](#modclaims) |Så här ändrar du AD FS-anspråk för olika federationsscenarier. |

## <a name="manage-ad-fs"></a>Hantera AD FS
Du kan utföra olika AD FS-relaterade uppgifter i Azure AD Connect med minimal användaren med hjälp av Azure AD Connect-guiden. När du är klar installerar Azure AD Connect genom att köra guiden kan köra du guiden igen om du vill utföra ytterligare åtgärder.

## <a name="repairthetrust"></a>Reparera förtroendet 
Du kan använda Azure AD Connect för att kontrollera det aktuella hälsotillståndet för AD FS och Azure AD litar på och vidta lämpliga åtgärder att reparera förtroendet. Följ dessa steg om du vill reparera din Azure AD och AD FS-förtroende.

1. Välj **reparera AAD och litar på AD FS** från listan över ytterligare aktiviteter.
   ![Reparera AAD och ADFS förtroende](media/active-directory-aadconnect-federation-management/RepairADTrust1.PNG)

2. På den **Anslut till Azure AD** anger dina autentiseringsuppgifter som global administratör för Azure AD, och klicka på **nästa**.
   ![Anslut till Azure AD](media/active-directory-aadconnect-federation-management/RepairADTrust2.PNG)

3. På den **fjärråtkomst autentiseringsuppgifter** ange autentiseringsuppgifter för domänadministratör.

   ![Autentiseringsuppgifter för fjärråtkomst](media/active-directory-aadconnect-federation-management/RepairADTrust3.PNG)

    När du klickar på **nästa**, Azure AD Connect söker efter certifikat hälsa och visar eventuella problem.

    ![Tillståndet för certifikat](media/active-directory-aadconnect-federation-management/RepairADTrust4.PNG)

    Den **redo att konfigurera** sidan innehåller en lista med åtgärder som kommer att utföras för att reparera förtroendet.

    ![Klart att konfigurera](media/active-directory-aadconnect-federation-management/RepairADTrust5.PNG)

4. Klicka på **installera** att reparera förtroendet.

> [!NOTE]
> Azure AD Connect kan bara reparera eller agera på självsignerade certifikat. Azure AD Connect kan inte åtgärda certifikat från tredjepart.

## <a name="alternateid"></a>Federera med Azure AD med hjälp av AlternateID 
Du rekommenderas att lokalt användarens huvudnamn Name(UPN) och molnet UPN-namnet blir desamma. Om lokala UPN använder en icke-dirigerbara domän (t.ex. Contoso.local) eller kan inte ändras på grund av lokala programberoenden, rekommenderar vi att konfigurera alternativa inloggnings-ID. Alternativt inloggnings-ID kan du konfigurera en inloggning där användare kan logga in med ett attribut än sina UPN-namnet, till exempel e-post. Alternativ för UPN-namnet i Azure AD Connect som standard attributet userPrincipalName i Active Directory. Om du väljer andra attribut för UPN-namnet och federerar med AD FS, sedan Azure AD Connect kommer att konfigurera AD FS alternativt inloggnings-ID Ett exempel på att välja ett annat attribut för UPN-namnet visas nedan:

![Val av alternativa ID-attribut](media/active-directory-aadconnect-federation-management/attributeselection.png)

Konfigurera alternativt inloggnings-ID för AD FS består av två Huvudsteg:
1. **Konfigurera rätt uppsättning utfärdande anspråk**: utfärdande anspråksregler i Azure AD förlitande part förtroende har ändrats för att använda det markerade attributet UserPrincipalName som alternativt ID för användaren.
2. **Aktivera alternativa inloggnings-ID i AD FS-konfigurationen**: AD FS-konfigurationen har uppdaterats så att AD FS kan söka efter användare i lämplig skogar med alternativa-ID. Den här konfigurationen stöds för AD FS i Windows Server 2012 R2 (med KB2919355) eller senare. Om AD FS-servrar 2012 R2, kontrollerar Azure AD Connect förekomsten av de nödvändiga KB. Om KB identifieras, visas en varning när konfigurationen är klar, enligt nedan:

    ![Varning för saknas KB på 2012R2](media/active-directory-aadconnect-federation-management/kbwarning.png)

    Installera de nödvändiga för att åtgärda konfigurationen vid saknas KB [KB2919355](http://go.microsoft.com/fwlink/?LinkID=396590) och reparera sedan den betrodda med hjälp av [reparera AAD och AD FS-förtroende](#repairthetrust).

> [!NOTE]
> Mer information om alternateID och steg för att manuellt konfigurera [konfigurera alternativa inloggnings-ID](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configuring-alternate-login-id)

## <a name="addadfsserver"></a>Lägga till en AD FS-server 

> [!NOTE]
> Om du vill lägga till en AD FS-server, kräver Azure AD Connect PFX-certifikat. Därför kan utföra du den här åtgärden endast om du konfigurerat AD FS-servergrupp med hjälp av Azure AD Connect.

1. Välj **distribuera ytterligare en federationsserver**, och klicka på **nästa**.

   ![Ytterligare federationsserver](media/active-directory-aadconnect-federation-management/AddNewADFSServer1.PNG)

2. På den **Anslut till Azure AD** , ange dina autentiseringsuppgifter som global administratör för Azure AD och klicka på **nästa**.

   ![Anslut till Azure AD](media/active-directory-aadconnect-federation-management/AddNewADFSServer2.PNG)

3. Ange administratörsautentiseringsuppgifter för domänen.

   ![Domänadministratörsbehörighet](media/active-directory-aadconnect-federation-management/AddNewADFSServer3.PNG)

4. Azure AD Connect begär lösenordet för PFX-filen som du angav när du konfigurerar din nya AD FS-grupp med Azure AD Connect. Klicka på **ange lösenord för** att ange lösenord för PFX-fil.

   ![Certifikatlösenord](media/active-directory-aadconnect-federation-management/AddNewADFSServer4.PNG)

    ![Ange SSL-certifikat](media/active-directory-aadconnect-federation-management/AddNewADFSServer5.PNG)

5. På den **AD FS-servrar** anger servernamnet eller IP-adress som ska läggas till i AD FS-servergruppen.

   ![AD FS-servrar](media/active-directory-aadconnect-federation-management/AddNewADFSServer6.PNG)

6. Klicka på **nästa**, och gå igenom slutliga **konfigurera** sidan. När Azure AD Connect är klar att lägga till servrar i AD FS-servergruppen, får du alternativet för att verifiera anslutning.

   ![Klart att konfigurera](media/active-directory-aadconnect-federation-management/AddNewADFSServer7.PNG)

    ![Installationen är klar](media/active-directory-aadconnect-federation-management/AddNewADFSServer8.PNG)

## <a name="addwapserver"></a>Lägga till en AD FS WAP-server 

> [!NOTE]
> Om du vill lägga till en server för WAP, kräver Azure AD Connect PFX-certifikat. Du kan därför bara utföra den här åtgärden om du konfigurerat AD FS-servergrupp med hjälp av Azure AD Connect.

1. Välj **distribuera Webbprogramproxy** från listan över tillgängliga uppgifter.

   ![Distribuera Webbprogramproxy](media/active-directory-aadconnect-federation-management/WapServer1.PNG)

2. Ange autentiseringsuppgifter för global administratör för Azure.

   ![Anslut till Azure AD](media/active-directory-aadconnect-federation-management/wapserver2.PNG)

3. På den **ange SSL-certifikat** anger lösenordet för PFX-fil som du angav när du konfigurerat AD FS-servergruppen med Azure AD Connect.
   ![Lösenord för certifikatet](media/active-directory-aadconnect-federation-management/WapServer3.PNG)

    ![Ange SSL-certifikat](media/active-directory-aadconnect-federation-management/WapServer4.PNG)

4. Lägg till servern som ska läggas till som en server för WAP. Eftersom WAP-servern inte kan anslutas till domänen, frågar guiden för administrativa autentiseringsuppgifter för servern som läggs till.

   ![Administrativa autentiseringsuppgifter](media/active-directory-aadconnect-federation-management/WapServer5.PNG)

5. På den **autentiseringsuppgifter för Proxy förtroende** ange administrativa autentiseringsuppgifter för att konfigurera proxyn förtroende och åtkomst till den primära servern i AD FS-servergruppen.

   ![Autentiseringsuppgifter för proxyförtroende](media/active-directory-aadconnect-federation-management/WapServer6.PNG)

6. På den **redo att konfigurera** sidan guiden visar en lista med åtgärder som kommer att utföras.

   ![Klart att konfigurera](media/active-directory-aadconnect-federation-management/WapServer7.PNG)

7. Klicka på **installera** att slutföra konfigurationen. När konfigurationen är klar, kan guiden välja att verifiera anslutning till servrar. Klicka på **Kontrollera** vill kontrollera anslutningen.

   ![Installationen är klar](media/active-directory-aadconnect-federation-management/WapServer8.PNG)

## <a name="addfeddomain"></a>Lägga till en federerad domän 

Det är lätt att lägga till en domän att bli federerad med Azure AD med hjälp av Azure AD Connect. Azure AD Connect lägger du till domänen för federation och ändrar anspråksregler för att återspegla utfärdaren korrekt när du har flera domäner federerade med Azure AD.

1. Om du vill lägga till en federerad domän, väljer du uppgiften **Lägg till en Azure AD-domänen**.

   ![Ytterligare Azure AD-domän](media/active-directory-aadconnect-federation-management/AdditionalDomain1.PNG)

2. På nästa sida i guiden anger du autentiseringsuppgifterna som global administratör för Azure AD.

   ![Anslut till Azure AD](media/active-directory-aadconnect-federation-management/AdditionalDomain2.PNG)

3. På den **fjärråtkomst autentiseringsuppgifter** ange administratörsbehörighet för domänen.

   ![Autentiseringsuppgifter för fjärråtkomst](media/active-directory-aadconnect-federation-management/additionaldomain3.PNG)

4. Guiden innehåller en lista över Azure AD-domäner som du kan federera din lokala katalog med på nästa sida. Välj domänen i listan.

   ![Azure AD-domän](media/active-directory-aadconnect-federation-management/AdditionalDomain4.PNG)

    När du väljer domänen, ger guiden lämplig information om ytterligare åtgärder som kommer att utföras i guiden och effekten av konfigurationen. I vissa fall, om du väljer en domän som inte har verifierats i Azure AD finns guiden information som hjälper dig att verifiera domänen. Se [lägga till ett eget domännamn i Azure Active Directory](../active-directory-domains-add-azure-portal.md) för mer information.

5. Klicka på **Nästa**. Den **redo att konfigurera** sidan innehåller en lista med åtgärder som kommer att utföras i Azure AD Connect. Klicka på **installera** att slutföra konfigurationen.

   ![Klart att konfigurera](media/active-directory-aadconnect-federation-management/AdditionalDomain5.PNG)

> [!NOTE]
> Användare från den tillagda federerade domänen måste synkroniseras innan de kan logga in på Azure AD.

## <a name="ad-fs-customization"></a>AD FS-anpassning
Följande avsnitt innehåller information om några av de aktiviteter du kan behöva utföra när du anpassar din AD FS-inloggningssida.

## <a name="customlogo"></a>Lägga till en anpassad logotyp eller bild 
Ändra logotypen för det företag som visas på den **inloggning** använder följande Windows PowerShell-cmdlet och syntax.

> [!NOTE]
> De rekommenderade måtten på logotypen är 260 x 35 96 dpi, samt med en filstorlek som är större än 10 KB.

    Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.PNG"}

> [!NOTE]
> Den *TargetName* parametern är obligatorisk. Standardtemat som släpps med AD FS kallas standard.

## <a name="addsignindescription"></a>Lägg till en beskrivning för inloggning 
Att lägga till en inloggningssida beskrivning av den **inloggningssidan**, Använd följande Windows PowerShell-cmdlet och syntax.

    Set-AdfsGlobalWebContent -SignInPageDescriptionText "<p>Sign-in to Contoso requires device registration. Click <A href='http://fs1.contoso.com/deviceregistration/'>here</A> for more information.</p>"

## <a name="modclaims"></a>Ändra anspråksregler i AD FS 
AD FS stöder ett omfattande anspråk språk som du kan använda för att skapa anpassade anspråksregler. Mer information finns i [roll Anspråksregelspråket](https://technet.microsoft.com/library/dd807118.aspx).

I följande avsnitt beskrivs hur du kan skriva anpassade regler för vissa scenarier som relaterar till Azure AD och AD FS-federation.

### <a name="immutable-id-conditional-on-a-value-being-present-in-the-attribute"></a>Oåterkalleliga villkorlig, baserat på ett värde i attributet ID
Azure AD Connect kan du ange ett attribut som ska användas som en källfästpunkt när objekt synkroniseras till Azure AD. Om värdet i det anpassade attributet inte är tom, kanske du vill utfärda ett ändras ID-anspråk.

Du kan till exempel välja **ms-ds-consistencyguid** som attribut för källfästpunkten och utfärda **ImmutableID** som **ms-ds-consistencyguid** om attributet har ett värde med den. Om det finns inget värde mot attributet, utfärda **objectGuid** som ändras-ID. Du kan skapa en uppsättning anpassade anspråksregler som beskrivs i följande avsnitt.

**Regel 1: Frågan attribut**

    c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
    => add(store = "Active Directory", types = ("http://contoso.com/ws/2016/02/identity/claims/objectguid", "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"), query = "; objectGuid,ms-ds-consistencyguid;{0}", param = c.Value);

I den här regeln du frågar värdena för **ms-ds-consistencyguid** och **objectGuid** för användare från Active Directory. Ändra namnet på det Arkiv till en lämplig store-namn i AD FS-distribution. Ändra anspråk typ till en korrekt anspråk också typ för din federationsserver som definierats för **objectGuid** och **ms-ds-consistencyguid**.

Dessutom med hjälp av **lägga till** och inte **problemet**, du undvika att lägga till ett utgående problem för entiteten och använda värden som mellanliggande värden. Du tänker utfärda anspråk i en senare regel när du har skapat vilket värde som ska användas som ändras-ID.

**Regel 2: Kontrollera om det finns ms-ds-consistencyguid för användaren**

    NOT EXISTS([Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"])
    => add(Type = "urn:anandmsft:tmp/idflag", Value = "useguid");

Den här regeln som definierar en tillfällig flagga kallas **idflag** som har angetts till **useguid** om det finns inga **ms-ds-consistencyguid** fyllts i för användaren. Logiken bakom detta är det faktum att AD FS inte tillåter tomma anspråk. När du lägger till anspråk http://contoso.com/ws/2016/02/identity/claims/objectguid och http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid i regel 1 kan, du få ett **msdsconsistencyguid** anspråk endast om värdet fylls för användaren. Om det inte är ifylld ser att den har ett tomt värde och släpper den direkt i AD FS. Alla objekt har **objectGuid**, så att anspråk kommer alltid att det efter regel 1 körs.

**Regel 3: Utfärda ms-ds-consistencyguid-ID som inte ändras om det finns**

    c:[Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c.Value);

Detta är en implicit **finns** kontrollera. Om värdet för anspråket finns sedan utfärda som som ändras-ID. Det föregående exemplet används den **nameidentifier** anspråk. Du måste ändra det till lämplig Anspråkstypen för ändras ID i din miljö.

**Regel 4: Utfärda objectGuid-ID som inte ändras om det inte finns någon ms-ds-consistencyGuid**

    c1:[Type == "urn:anandmsft:tmp/idflag", Value =~ "useguid"]
    && c2:[Type == "http://contoso.com/ws/2016/02/identity/claims/objectguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c2.Value);

I den här regeln du helt enkelt kontrollerar flaggan tillfälliga **idflag**. Du bestämma om du vill utfärda anspråk baserat på dess värde.

> [!NOTE]
> Sekvens med dessa regler är viktigt.

### <a name="sso-with-a-subdomain-upn"></a>Enkel inloggning med en underdomän UPN
Du kan lägga till fler än en domän att bli federerad med hjälp av Azure AD Connect, enligt beskrivningen i [lägga till en ny extern domän](active-directory-aadconnect-federation-management.md#addfeddomain). Eftersom federerade rotdomänen omfattar även underordnat måste du ändra användarens huvudnamn (UPN) anspråk så att utfärdaren-ID motsvarar rotdomän och inte underdomänen.

Anspråksregel för utfärdaren ID är som standard som:

    c:[Type
    == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(c.Value, “.+@(?<domain>.+)“, “http://${domain}/adfs/services/trust/“));

![Standard utfärdaren ID-anspråk](media/active-directory-aadconnect-federation-management/issuer_id_default.png)

Standardregeln bara tar UPN-suffix och används i ID-anspråk utfärdare. Till exempel John är en användare i sub.contoso.com och contoso.com är federerat med Azure AD. John anger john@sub.contoso.com som användarnamn när du loggar in på Azure AD. Utfärdaren ID anspråk Standardregeln i AD FS hanterar den på följande sätt:

    c:[Type
    == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(john@sub.contoso.com, “.+@(?<domain>.+)“, “http://${domain}/adfs/services/trust/“));

**Anspråkets värde:**  http://sub.contoso.com/adfs/services/trust/

Ändra regel för anspråk för att matcha följande om du vill att endast rotdomänen i anspråksvärdet utfärdare:

    c:[Type == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(c.Value, “^((.*)([.|@]))?(?<domain>[^.]*[.].*)$”, “http://${domain}/adfs/services/trust/“));

## <a name="next-steps"></a>Nästa steg
Lär dig mer om [användaren inloggningsalternativ](active-directory-aadconnect-user-signin.md).
