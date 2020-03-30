---
title: Azure AD Connect – AD FS-hantering och anpassning | Microsoft-dokument
description: AD FS-hantering med Azure AD Connect och anpassning av AD FS-inloggningsupplevelsen för användare med Azure AD Connect och PowerShell.
keywords: AD FS, ADFS, AD FS-hantering, AAD Connect, Connect, inloggning, AD FS-anpassning, reparationsförtroende, O365, federation, förlitande part
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 2593b6c6-dc3f-46ef-8e02-a8e2dc4e9fb9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: fcbeedddc65a916f869a778616779917a9571181
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331986"
---
# <a name="manage-and-customize-active-directory-federation-services-by-using-azure-ad-connect"></a>Hantera och anpassa Active Directory Federation Services med hjälp av Azure AD Connect
I den här artikeln beskrivs hur du hanterar och anpassar AD FS (Active Directory Federation Services) med hjälp av Azure Active Directory (Azure AD) Connect. Den innehåller även andra vanliga AD FS-uppgifter som du kan behöva göra för en fullständig konfiguration av en AD FS-servergrupp.

| Hjälpavsnitt | Vad det täcker |
|:--- |:--- |
| **Hantera AD FS** | |
| [Reparera förtroendet](#repairthetrust) |Så här reparerar du federationsförtroendet med Office 365. |
| [Federera med Azure AD med alternativt inloggnings-ID](#alternateid) | Konfigurera federation med hjälp av alternativt inloggnings-ID  |
| [Lägga till en AD FS-server](#addadfsserver) |Så här expanderar du en AD FS-server med ytterligare en AD FS-server. |
| [Lägga till en PROXYSERVER för AD FS-webbprogram](#addwapserver) |Så här expanderar du en AD FS-server med ytterligare en WAP-server (Web Application Proxy). |
| [Lägga till en federerad domän](#addfeddomain) |Så här lägger du till en federerad domän. |
| [Uppdatera TLS/SSL-certifikatet](how-to-connect-fed-ssl-update.md)| Så här uppdaterar du TLS/SSL-certifikatet för en AD FS-servergrupp. |
| **Anpassa AD FS** | |
| [Lägga till en anpassad företagslogotyp eller illustration](#customlogo) |Så här anpassar du en AD FS-inloggningssida med företagets logotyp och illustration. |
| [Lägga till en inloggningsbeskrivning](#addsignindescription) |Så här lägger du till en beskrivning av inloggningssidan. |
| [Ändra AD FS-anspråksregler](#modclaims) |Så här ändrar du AD FS-anspråk för olika federationsscenarier. |

## <a name="manage-ad-fs"></a>Hantera AD FS
Du kan utföra olika AD FS-relaterade uppgifter i Azure AD Connect med minimala användaråtgärder med hjälp av Azure AD Connect-guiden. När du har installerat Azure AD Connect genom att köra guiden kan du köra guiden igen för att utföra ytterligare uppgifter.

## <a name="repair-the-trust"></a><a name="repairthetrust"></a>Reparera förtroendet 
Du kan använda Azure AD Connect för att kontrollera den aktuella hälsan för AD FS- och Azure AD-förtroendet och vidta lämpliga åtgärder för att reparera förtroendet. Följ dessa steg för att reparera ditt Azure AD- och AD FS-förtroende.

1. Välj **Reparera AAD och ADFS Trust** i listan över ytterligare uppgifter.
   ![Reparera AAD och ADFS Trust](./media/how-to-connect-fed-management/RepairADTrust1.PNG)

2. På sidan **Anslut till Azure AD** anger du dina globala administratörsbehörighet för Azure AD och klickar på **Nästa**.
   ![Anslut till Azure AD](./media/how-to-connect-fed-management/RepairADTrust2.PNG)

3. Ange autentiseringsuppgifterna för domänadministratören på sidan **Autentiseringsuppgifter för fjärråtkomst.**

   ![Autentiseringsuppgifter för fjärråtkomst](./media/how-to-connect-fed-management/RepairADTrust3.PNG)

    När du har klickat på **Nästa**söker Azure AD Connect efter certifikathälsa och visar eventuella problem.

    ![Certifikatens tillstånd](./media/how-to-connect-fed-management/RepairADTrust4.PNG)

    På sidan **Klar för konfiguration** visas en lista över åtgärder som ska utföras för att reparera förtroendet.

    ![Klart att konfigurera](./media/how-to-connect-fed-management/RepairADTrust5.PNG)

4. Klicka på **Installera** om du vill reparera förtroendet.

> [!NOTE]
> Azure AD Connect kan bara reparera eller agera på certifikat som är självsignerade. Azure AD Connect kan inte reparera certifikat från tredje part.

## <a name="federate-with-azure-ad-using-alternateid"></a><a name="alternateid"></a>Federera med Azure AD med AlternateID 
Vi rekommenderar att det lokala användarnamnet(UPN) och molnets användarnamn behålls på samma sätt. Om lokala UPN använder en icke-dirigerbara domän (ex. Contoso.local) eller inte kan ändras på grund av lokala programberoenden rekommenderar vi att du ställer in alternativt inloggnings-ID. Med alternativt inloggnings-ID kan du konfigurera en inloggningsupplevelse där användare kan logga in med ett annat attribut än sitt UPN, till exempel e-post. Valet för Användarnamn i Azure AD Connect är standard för attributet userPrincipalName i Active Directory. Om du väljer något annat attribut för användarnamn och matar med AD FS konfigurerar Azure AD Connect AD FS för alternativt inloggnings-ID. Ett exempel på att välja ett annat attribut för användarnamn visas nedan:

![Val av alternativt ID-attribut](./media/how-to-connect-fed-management/attributeselection.png)

Konfigurera alternativt inloggnings-ID för AD FS består av två huvudsteg:
1. **Konfigurera rätt uppsättning utfärdandeanspråk:** Reglerna för utfärdandeanspråk i Azure AD-förlitande partens förtroende ändras för att använda det valda UserPrincipalName-attributet som användarens alternativa ID.
2. **Aktivera alternativt inloggnings-ID i AD FS-konfigurationen:** AD FS-konfigurationen uppdateras så att AD FS kan söka efter användare i lämpliga skogar med hjälp av det alternativa ID:t. Den här konfigurationen stöds för AD FS på Windows Server 2012 R2 (med KB2919355) eller senare. Om AD FS-servrarna är 2012 R2 söker Azure AD Connect efter att det finns den kB som krävs. Om KB inte identifieras visas en varning när konfigurationen är klar, som visas nedan:

    ![Varning för saknade KB på 2012R2](./media/how-to-connect-fed-management/kbwarning.png)

    Om du vill korrigera konfigurationen om kb saknas installerar du den [kB2919355](https://go.microsoft.com/fwlink/?LinkID=396590) som krävs och reparerar sedan förtroendet med [Reparera AAD och AD FS Trust](#repairthetrust).

> [!NOTE]
> Mer information om alternateID och steg för att konfigurera manuellt finns i [Konfigurera alternativt inloggnings-ID](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configuring-alternate-login-id)

## <a name="add-an-ad-fs-server"></a><a name="addadfsserver"></a>Lägga till en AD FS-server 

> [!NOTE]
> För att lägga till en AD FS-server kräver Azure AD Connect PFX-certifikatet. Därför kan du bara utföra den här åtgärden om du har konfigurerat AD FS-servergruppen med hjälp av Azure AD Connect.

1. Välj **Distribuera ytterligare en federationsserver**och klicka på **Nästa**.

   ![Ytterligare federationsserver](./media/how-to-connect-fed-management/AddNewADFSServer1.PNG)

2. På sidan **Anslut till Azure AD** anger du dina globala administratörsuppgifter för Azure AD och klickar på **Nästa**.

   ![Anslut till Azure AD](./media/how-to-connect-fed-management/AddNewADFSServer2.PNG)

3. Ange autentiseringsuppgifter för domänadministratören.

   ![Autentiseringsuppgifter för domänadministratör](./media/how-to-connect-fed-management/AddNewADFSServer3.PNG)

4. Azure AD Connect frågar efter lösenordet för PFX-filen som du angav när du konfigurerade din nya AD FS-servergrupp med Azure AD Connect. Klicka på **Ange lösenord** om du vill ange lösenordet för PFX-filen.

   ![Lösenord för certifikat](./media/how-to-connect-fed-management/AddNewADFSServer4.PNG)

    ![Ange TLS/SSL-certifikat](./media/how-to-connect-fed-management/AddNewADFSServer5.PNG)

5. På sidan **AD FS-servrar** anger du det servernamn eller den IP-adress som ska läggas till i AD FS-servergruppen.

   ![AD FS-servrar](./media/how-to-connect-fed-management/AddNewADFSServer6.PNG)

6. Klicka på **Nästa**och gå igenom den sista **konfigurera** sidan. När Azure AD Connect har lagt till servrarna i AD FS-servergruppen får du möjlighet att verifiera anslutningen.

   ![Klart att konfigurera](./media/how-to-connect-fed-management/AddNewADFSServer7.PNG)

    ![Installationen är klar](./media/how-to-connect-fed-management/AddNewADFSServer8.PNG)

## <a name="add-an-ad-fs-wap-server"></a><a name="addwapserver"></a>Lägga till en AD FS WAP-server 

> [!NOTE]
> För att lägga till en WAP-server kräver Azure AD Connect PFX-certifikatet. Därför kan du bara utföra den här åtgärden om du har konfigurerat AD FS-servergruppen med hjälp av Azure AD Connect.

1. Välj **Distribuera webbprogramproxy** i listan över tillgängliga uppgifter.

   ![Distribuera proxy för webbprogram](./media/how-to-connect-fed-management/WapServer1.PNG)

2. Ange globala azure-administratörsautentiseringsuppgifter.

   ![Anslut till Azure AD](./media/how-to-connect-fed-management/wapserver2.PNG)

3. Ange lösenordet för PFX-filen som du angav när du konfigurerade AD FS-servergruppen med Azure AD Connect på sidan **Ange SSL-certifikat.**
   ![Lösenord för certifikat](./media/how-to-connect-fed-management/WapServer3.PNG)

    ![Ange TLS/SSL-certifikat](./media/how-to-connect-fed-management/WapServer4.PNG)

4. Lägg till servern som ska läggas till som en WAP-server. Eftersom WAP-servern kanske inte är ansluten till domänen, frågar guiden efter administrativa autentiseringsuppgifter till servern som läggs till.

   ![Autentiseringsuppgifter för administrativa servrar](./media/how-to-connect-fed-management/WapServer5.PNG)

5. På sidan **Autentiseringsuppgifter för proxyförtroende** anger du administrativa autentiseringsuppgifter för att konfigurera proxyförtroendet och komma åt den primära servern i AD FS-servergruppen.

   ![Autentiseringsuppgifter för proxyförtroende](./media/how-to-connect-fed-management/WapServer6.PNG)

6. På sidan **Klar att konfigurera** visas listan över åtgärder som ska utföras.

   ![Klart att konfigurera](./media/how-to-connect-fed-management/WapServer7.PNG)

7. Klicka på **Installera** för att slutföra konfigurationen. När konfigurationen är klar ger guiden dig möjlighet att verifiera anslutningen till servrarna. Klicka på **Kontrollera** om du vill kontrollera anslutningen.

   ![Installationen är klar](./media/how-to-connect-fed-management/WapServer8.PNG)

## <a name="add-a-federated-domain"></a><a name="addfeddomain"></a>Lägga till en federerad domän 

Det är enkelt att lägga till en domän som ska federeras med Azure AD med hjälp av Azure AD Connect. Azure AD Connect lägger till domänen för federation och ändrar anspråksreglerna för att korrekt återspegla utfärdaren när du har flera domäner federerade med Azure AD.

1. Om du vill lägga till en federerad domän väljer du uppgiften **Lägg till ytterligare en Azure AD-domän**.

   ![Ytterligare Azure AD-domän](./media/how-to-connect-fed-management/AdditionalDomain1.PNG)

2. Ange globala administratörsbehörighet för Azure AD på nästa sida i guiden.

   ![Anslut till Azure AD](./media/how-to-connect-fed-management/AdditionalDomain2.PNG)

3. Ange **autentiseringsuppgifterna** för domänadministratören på sidan Autentiseringsuppgifter för fjärråtkomst.

   ![Autentiseringsuppgifter för fjärråtkomst](./media/how-to-connect-fed-management/additionaldomain3.PNG)

4. På nästa sida innehåller guiden en lista över Azure AD-domäner som du kan federera din lokala katalog med. Välj domänen i listan.

   ![Azure AD-domän](./media/how-to-connect-fed-management/AdditionalDomain4.PNG)

    När du har valt domänen får du lämplig information om ytterligare åtgärder som guiden ska vidta och konfigurationens inverkan. I vissa fall, om du väljer en domän som ännu inte har verifierats i Azure AD, ger guiden dig information som hjälper dig att verifiera domänen. Mer information finns [i Lägga till ditt eget domännamn i Azure Active Directory.](../active-directory-domains-add-azure-portal.md)

5. Klicka på **Nästa**. På sidan **Klar för konfiguration** visas en lista över åtgärder som Azure AD Connect ska utföra. Klicka på **Installera** för att slutföra konfigurationen.

   ![Klart att konfigurera](./media/how-to-connect-fed-management/AdditionalDomain5.PNG)

> [!NOTE]
> Användare från den tillagda federerade domänen måste synkroniseras innan de kan logga in på Azure AD.

## <a name="ad-fs-customization"></a>AD FS-anpassning
I följande avsnitt finns information om några av de vanliga uppgifter som du kan behöva utföra när du anpassar ad FS-inloggningssidan.

## <a name="add-a-custom-company-logo-or-illustration"></a><a name="customlogo"></a>Lägga till en anpassad företagslogotyp eller illustration 
Om du vill ändra logotypen för det **Sign-in** företag som visas på inloggningssidan använder du följande Windows PowerShell-cmdlet och syntax.

> [!NOTE]
> De rekommenderade måtten för logotypen är 260 x 35 \@ 96 dpi med en filstorlek som inte är större än 10 kB.

    Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.PNG"}

> [!NOTE]
> Parametern *TargetName* krävs. Standardtemat som släpps med AD FS heter Standard.

## <a name="add-a-sign-in-description"></a><a name="addsignindescription"></a>Lägga till en inloggningsbeskrivning 
Om du vill lägga till en beskrivning av inloggningssidan **på inloggningssidan**använder du följande Windows PowerShell-cmdlet och syntax.

    Set-AdfsGlobalWebContent -SignInPageDescriptionText "<p>Sign-in to Contoso requires device registration. Click <A href='http://fs1.contoso.com/deviceregistration/'>here</A> for more information.</p>"

## <a name="modify-ad-fs-claim-rules"></a><a name="modclaims"></a>Ändra AD FS-anspråksregler 
AD FS stöder ett omfattande anspråksspråk som du kan använda för att skapa anpassade anspråksregler. Mer information finns [i Rollen för anspråksregelspråket](https://technet.microsoft.com/library/dd807118.aspx).

I följande avsnitt beskrivs hur du kan skriva anpassade regler för vissa scenarier som relaterar till Azure AD- och AD FS-federation.

### <a name="immutable-id-conditional-on-a-value-being-present-in-the-attribute"></a>Oföränderligt ID villkorar att ett värde finns i attributet
Med Azure AD Connect kan du ange ett attribut som ska användas som källankare när objekt synkroniseras med Azure AD. Om värdet i det anpassade attributet inte är tomt kanske du vill utfärda ett oföränderligt ID-anspråk.

Du kan till exempel välja **ms-ds-consistencyguid** som attribut för källankaret och utfärda **ImmutableID** som **ms-ds-consistencyguid** om attributet har ett värde emot det. Om det inte finns något värde mot attributet utfärdar du **objectGuid** som oföränderligt ID. Du kan skapa uppsättningen anpassade anspråksregler enligt beskrivningen i följande avsnitt.

**Regel 1: Frågeattribut**

    c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
    => add(store = "Active Directory", types = ("http://contoso.com/ws/2016/02/identity/claims/objectguid", "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"), query = "; objectGuid,ms-ds-consistencyguid;{0}", param = c.Value);

I den här regeln frågar du efter värdena **ms-ds-consistencyguid** och **objectGuid** för användaren från Active Directory. Ändra butiksnamnet till ett lämpligt butiksnamn i AD FS-distributionen. Ändra också anspråkstypen till en korrekt anspråkstyp för federationen, enligt definitionen för **objectGuid** och **ms-ds-consistencyguid**.

Genom att använda **lägg till** och inte **utfärda**undviker du också att lägga till ett utgående problem för entiteten och kan använda värdena som mellanliggande värden. Du kommer att utfärda anspråket i en senare regel när du har fastställt vilket värde som ska användas som oföränderligt ID.

**Regel 2: Kontrollera om ms-ds-consistencyguid finns för användaren**

    NOT EXISTS([Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"])
    => add(Type = "urn:anandmsft:tmp/idflag", Value = "useguid");

Den här regeln definierar en tillfällig flagga som kallas **idflag** som är inställd på **useguid** om det inte finns någon **ms-ds-consistencyguid** som befolkas för användaren. Logiken bakom detta är det faktum att AD FS inte tillåter tomma anspråk. Så när du `http://contoso.com/ws/2016/02/identity/claims/objectguid` `http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid` lägger till anspråk och i regel 1, slutar du med en **msdsconsistencyguid** anspråk endast om värdet är befolkat för användaren. Om den inte är ifylld ser AD FS att den kommer att ha ett tomt värde och släpper det omedelbart. Alla objekt kommer att ha **objectGuid**, så att påståendet alltid kommer att vara där efter regel 1 körs.

**Regel 3: Utfärda ms-ds-consistencyguid som oföränderligt ID om det finns**

    c:[Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"]
    => issue(Type = "http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID", Value = c.Value);

Detta är **Exist** en implicit Exist-kontroll. Om värdet för anspråket finns utfärdar du det som oföränderligt ID. I föregående exempel används **namnidentifieraranspråket.** Du måste ändra detta till lämplig anspråkstyp för det oföränderliga ID:t i din miljö.

**Regel 4: Utfärda objectGuid som oföränderligt ID om ms-ds-consistencyGuid inte finns**

    c1:[Type == "urn:anandmsft:tmp/idflag", Value =~ "useguid"]
    && c2:[Type == "http://contoso.com/ws/2016/02/identity/claims/objectguid"]
    => issue(Type = "http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID", Value = c2.Value);

I den här regeln kontrollerar du helt enkelt den tillfälliga flaggan **idflag**. Du bestämmer om anspråket ska lämnas ut baserat på dess värde.

> [!NOTE]
> Sekvensen av dessa regler är viktig.

### <a name="sso-with-a-subdomain-upn"></a>SSO med underdomän UPN

Du kan lägga till mer än en domän som ska federeras med hjälp av Azure AD Connect, enligt beskrivningen i [Lägg till en ny federerad domän](how-to-connect-fed-management.md#addfeddomain). Azure AD Connect version 1.1.553.0 och senast skapar rätt anspråksregel för utfärdarID automatiskt. Om du inte kan använda Azure AD Connect version 1.1.553.0 eller senast, rekommenderar vi att [Azure AD RPT-anspråksregler](https://aka.ms/aadrptclaimrules) används för att generera och ange korrekta anspråksregler för Azure AD-förlitande partens förtroende.

## <a name="next-steps"></a>Nästa steg
Läs mer om [alternativ för inloggning för användare](plan-connect-user-signin.md).
