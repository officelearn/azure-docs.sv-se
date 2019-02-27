---
title: Azure AD Connect - AD FS-hantering och anpassning | Microsoft Docs
description: AD FS-hantering med Azure AD Connect och anpassning av AD FS-inloggningen användarupplevelsen med Azure AD Connect och PowerShell.
keywords: AD FS, ADFS, AD FS management, AAD Connect Connect, inloggning, AD FS-anpassning, reparera förtroende, O365, federation, förlitande part
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
ms.openlocfilehash: 021e13dafcc659337d4096a068e224312e69db1b
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56870949"
---
# <a name="manage-and-customize-active-directory-federation-services-by-using-azure-ad-connect"></a>Hantera och anpassa Active Directory Federation Services med hjälp av Azure AD Connect
Den här artikeln beskriver hur du hanterar och anpassa Active Directory Federation Services (AD FS) med hjälp av Azure Active Directory (Azure AD) Connect. Den innehåller också andra vanliga aktiviteter för AD FS som du kan behöva göra en fullständig konfiguration av AD FS-servergrupp.

| Avsnitt | Den behandlar |
|:--- |:--- |
| **Hantera AD FS** | |
| [Reparera förtroendet](#repairthetrust) |Så här att reparera federationsförtroende med Office 365. |
| [Federera med Azure AD med hjälp av alternativa inloggnings-ID](#alternateid) | Konfigurera federation med hjälp av alternativa inloggnings-ID  |
| [Lägg till en AD FS-server](#addadfsserver) |Hur du expanderar en AD FS-servergrupp med en ytterligare AD FS-servern. |
| [Lägga till en AD FS Web Application Proxy-server](#addwapserver) |Hur du expanderar en AD FS-servergrupp med en ytterligare Web Application Proxy (WAP)-server. |
| [Lägg till en federerad domän](#addfeddomain) |Hur du lägger till en federerad domän. |
| [Uppdatera SSL-certifikatet](how-to-connect-fed-ssl-update.md)| Så här uppdaterar SSL-certifikatet för AD FS-servergrupp. |
| **Anpassa AD FS** | |
| [Lägga till en anpassad logotyp eller bild](#customlogo) |Hur du anpassar en AD FS-inloggningssida med företagets logotyp och illustration. |
| [Lägg till en inloggning beskrivning](#addsignindescription) |Hur du lägger till en beskrivning på inloggningssidan. |
| [Ändra anspråksregler för AD FS](#modclaims) |Så här ändrar du AD FS-anspråk för olika scenarier. |

## <a name="manage-ad-fs"></a>Hantera AD FS
Du kan utföra olika AD FS-relaterade uppgifter i Azure AD Connect med minimal användaråtgärd med hjälp av Azure AD Connect-guiden. När du är klar installerar Azure AD Connect genom att köra guiden kan köra du guiden igen för att utföra ytterligare uppgifter.

## <a name="repairthetrust"></a>Reparera förtroendet 
Du kan använda Azure AD Connect för att kontrollera tillståndet för AD FS och Azure AD-förtroende och vidta lämpliga åtgärder att reparera förtroendet. Följ dessa steg för att reparera din Azure AD och AD FS-förtroende.

1. Välj **reparera AAD och AD FS-förtroende** från listan över ytterligare uppgifter.
   ![Reparera AAD och AD FS förtroende](./media/how-to-connect-fed-management/RepairADTrust1.PNG)

2. På den **Anslut till Azure AD** anger dina autentiseringsuppgifter som global administratör för Azure AD, och klicka på **nästa**.
   ![Anslut till Azure AD](./media/how-to-connect-fed-management/RepairADTrust2.PNG)

3. På den **fjärråtkomst autentiseringsuppgifter** ange autentiseringsuppgifter för domänadministratör.

   ![Autentiseringsuppgifter för fjärråtkomst](./media/how-to-connect-fed-management/RepairADTrust3.PNG)

    När du klickar på **nästa**, Azure AD Connect kontrollerar hälsotillståndet för certifikat och visar eventuella problem.

    ![Tillståndet för certifikat](./media/how-to-connect-fed-management/RepairADTrust4.PNG)

    Den **redo att konfigurera** sidan visar en lista med åtgärder som ska utföras för att reparera förtroendet.

    ![Klart att konfigurera](./media/how-to-connect-fed-management/RepairADTrust5.PNG)

4. Klicka på **installera** att reparera förtroendet.

> [!NOTE]
> Azure AD Connect kan bara reparera eller vidta åtgärder för certifikat som är självsignerat. Azure AD Connect inte kan reparera certifikat från tredje part.

## <a name="alternateid"></a>Federera med Azure AD med AlternateID 
Vi rekommenderar att den lokala användarens huvudnamn Name(UPN) och molnet User Principal Name blir desamma. Om lokala UPN använder en icke-dirigerbara domän (ex.) Contoso.local) eller kan inte ändras på grund av lokala programberoenden rekommenderar vi att konfigurera alternativa inloggnings-ID. Alternativa inloggnings-ID kan du konfigurera en inloggning där användare kan logga in med ett attribut än sina UPN, till exempel e-post. Val för användarens huvudnamn i Azure AD Connect standard till userPrincipalName-attribut i Active Directory. Om du väljer andra attribut för användarens huvudnamn och federerar med hjälp av AD FS, sedan Azure AD Connect kommer att konfigurera AD FS för alternativa inloggnings-ID. Ett exempel på att välja ett annat attribut för användarens huvudnamn visas nedan:

![Val av alternativa ID-attribut](./media/how-to-connect-fed-management/attributeselection.png)

Konfigurera alternativa inloggnings-ID för AD FS består av två Huvudsteg:
1. **Konfigurera rätt uppsättning utfärdande anspråk**: Utfärdande anspråksregler i Azure AD-förtroende för förlitande part har ändrats för att använda det valda UserPrincipalName-attributet som den alternativa ID för användaren.
2. **Aktivera alternativa inloggnings-ID i AD FS-konfigurationen**: AD FS-konfigurationen har uppdaterats så att AD FS kan slå upp användare i lämplig skogar med den alternativa-ID. Den här konfigurationen har stöd för AD FS i Windows Server 2012 R2 (med KB2919355) eller senare. Om AD FS-servrarna är 2012 R2, kontrollerar Azure AD Connect förekomsten av de nödvändiga KB. Om KB inte identifieras får visas en varning när konfigurationen är klar, enligt nedan:

    ![Varning för saknar KB 2012 R2](./media/how-to-connect-fed-management/kbwarning.png)

    Installera de nödvändiga för att åtgärda konfigurationen vid saknas KB [KB2919355](https://go.microsoft.com/fwlink/?LinkID=396590) och reparera förtroende med hjälp av [reparera AAD och AD FS-förtroendet](#repairthetrust).

> [!NOTE]
> Mer information om alternateID och hur du manuellt konfigurerar [konfigurera alternativa inloggnings-ID](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configuring-alternate-login-id)

## <a name="addadfsserver"></a>Lägg till en AD FS-server 

> [!NOTE]
> Om du vill lägga till en AD FS-server, kräver Azure AD Connect PFX-certifikat. Därför kan utföra du den här åtgärden endast om du har konfigurerat AD FS-servergrupp med hjälp av Azure AD Connect.

1. Välj **distribuera en ytterligare federationsserver**, och klicka på **nästa**.

   ![Ytterligare federationsserver](./media/how-to-connect-fed-management/AddNewADFSServer1.PNG)

2. På den **Anslut till Azure AD** , ange dina autentiseringsuppgifter som global administratör för Azure AD och på **nästa**.

   ![Anslut till Azure AD](./media/how-to-connect-fed-management/AddNewADFSServer2.PNG)

3. Ange administratörsbehörighet för domänen.

   ![Administratörsbehörighet för domänen](./media/how-to-connect-fed-management/AddNewADFSServer3.PNG)

4. Azure AD Connect ombeds att ange lösenordet för PFX-filen som du angav när du konfigurerar din nya AD FS-servergrupp med Azure AD Connect. Klicka på **ange lösenord** att ange lösenordet för PFX-filen.

   ![Certifikatlösenord](./media/how-to-connect-fed-management/AddNewADFSServer4.PNG)

    ![Ange SSL-certifikat](./media/how-to-connect-fed-management/AddNewADFSServer5.PNG)

5. På den **AD FS-servrar** anger servernamnet eller IP-adress som ska läggas till AD FS-servergrupp.

   ![AD FS-servrar](./media/how-to-connect-fed-management/AddNewADFSServer6.PNG)

6. Klicka på **nästa**, och gå igenom sista **konfigurera** sidan. När Azure AD Connect har slutfört att lägga till servrar i AD FS-servergrupp, kommer du få alternativet att verifiera anslutningen.

   ![Klart att konfigurera](./media/how-to-connect-fed-management/AddNewADFSServer7.PNG)

    ![Installationen har slutförts](./media/how-to-connect-fed-management/AddNewADFSServer8.PNG)

## <a name="addwapserver"></a>Lägga till en AD FS-WAP-server 

> [!NOTE]
> Om du vill lägga till en WAP-server, kräver Azure AD Connect PFX-certifikat. Du kan därför bara utföra den här åtgärden om du har konfigurerat AD FS-servergrupp med hjälp av Azure AD Connect.

1. Välj **distribuera Webbprogramproxy** i listan över tillgängliga uppgifter.

   ![Distribuera Webbprogramproxy](./media/how-to-connect-fed-management/WapServer1.PNG)

2. Ange autentiseringsuppgifter för global administratör för Azure.

   ![Anslut till Azure AD](./media/how-to-connect-fed-management/wapserver2.PNG)

3. På den **ange SSL-certifikat** anger du lösenordet för PFX-filen som du angav när du har konfigurerat AD FS-servergrupp med Azure AD Connect.
   ![Certifikatlösenord](./media/how-to-connect-fed-management/WapServer3.PNG)

    ![Ange SSL-certifikat](./media/how-to-connect-fed-management/WapServer4.PNG)

4. Lägg till servern som ska läggas till som en WAP-server. Eftersom WAP-servern inte kan anslutas till domänen, får administratörsbehörighet till servern som har lagts till.

   ![Administrativa autentiseringsuppgifter](./media/how-to-connect-fed-management/WapServer5.PNG)

5. På den **autentiseringsuppgifter för proxyförtroende** ange administrativa autentiseringsuppgifter för att konfigurera proxyn förtroende och få åtkomst till den primära servern i AD FS-servergrupp.

   ![Autentiseringsuppgifter för proxyförtroende](./media/how-to-connect-fed-management/WapServer6.PNG)

6. På den **redo att konfigurera** sidan i guiden visar en lista med åtgärder som ska utföras.

   ![Klart att konfigurera](./media/how-to-connect-fed-management/WapServer7.PNG)

7. Klicka på **installera** att slutföra konfigurationen. När konfigurationen är klar, ger guiden dig alternativet att verifiera anslutningen till servrarna. Klicka på **Kontrollera** att kontrollera anslutningen.

   ![Installationen har slutförts](./media/how-to-connect-fed-management/WapServer8.PNG)

## <a name="addfeddomain"></a>Lägg till en federerad domän 

Det är enkelt att lägga till en domän ska vara federerad med Azure AD med hjälp av Azure AD Connect. Azure AD Connect lägger till domänen för federation och ändrar anspråksreglerna för att återspegla utfärdaren korrekt när du har flera domäner som är federerad med Azure AD.

1. Om du vill lägga till en federerad domän, väljer du uppgiften **lägga till ytterligare Azure AD-domän**.

   ![Ytterligare Azure AD-domän](./media/how-to-connect-fed-management/AdditionalDomain1.PNG)

2. På nästa sida i guiden anger du autentiseringsuppgifterna för global administratör för Azure AD.

   ![Anslut till Azure AD](./media/how-to-connect-fed-management/AdditionalDomain2.PNG)

3. På den **fjärråtkomst autentiseringsuppgifter** anger du autentiseringsuppgifter för domänen.

   ![Autentiseringsuppgifter för fjärråtkomst](./media/how-to-connect-fed-management/additionaldomain3.PNG)

4. På nästa sida innehåller i guiden en lista över Azure AD-domäner som du kan federera din lokala katalog med. Välj domänen i listan.

   ![Microsoft Azure Active Directory-domän](./media/how-to-connect-fed-management/AdditionalDomain4.PNG)

    När du har valt domänen får i guiden du lämplig information om ytterligare åtgärder som ska utföras i guiden och effekten av konfigurationen. I vissa fall, om du väljer en domän som inte har verifierats i Azure AD ger guiden dig information som hjälper dig verifiera domänen. Se [lägga till ett anpassat domännamn i Azure Active Directory](../active-directory-domains-add-azure-portal.md) för mer information.

5. Klicka på **Nästa**. Den **redo att konfigurera** visar listan över åtgärder som utförs med Azure AD Connect. Klicka på **installera** att slutföra konfigurationen.

   ![Klart att konfigurera](./media/how-to-connect-fed-management/AdditionalDomain5.PNG)

> [!NOTE]
> Användare från har lagts till federerad domän måste synkroniseras innan de kommer att kunna logga in till Azure AD.

## <a name="ad-fs-customization"></a>AD FS-anpassning
Följande avsnitt innehåller information om några av de vanliga uppgifter som du kan behöva utföra när du anpassar din AD FS-inloggningssida.

## <a name="customlogo"></a>Lägga till en anpassad logotyp eller bild 
Ändra logotypen på företaget som visas på den **inloggning** kan du använda följande Windows PowerShell-cmdlet och syntax.

> [!NOTE]
> De rekommenderade måtten på logotypen är 260 x 35 \@ 96 dpi, samt med en filstorlek som är större än 10 KB.

    Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.PNG"}

> [!NOTE]
> Den *TargetName* parametern är obligatorisk. Standardtemat som släpps med AD FS heter standard.

## <a name="addsignindescription"></a>Lägg till en inloggning beskrivning 
Att lägga till en beskrivning på inloggningssidan för att den **inloggningssidan**, använder du följande Windows PowerShell-cmdlet och syntax.

    Set-AdfsGlobalWebContent -SignInPageDescriptionText "<p>Sign-in to Contoso requires device registration. Click <A href='http://fs1.contoso.com/deviceregistration/'>here</A> for more information.</p>"

## <a name="modclaims"></a>Ändra anspråksregler för AD FS 
AD FS stöder ett omfattande anspråk språk som du kan använda för att skapa anpassade anspråksregler. Mer information finns i [roll Anspråksregelspråket](https://technet.microsoft.com/library/dd807118.aspx).

I följande avsnitt beskrivs hur du kan skriva anpassade regler för vissa scenarier som är relaterade till Azure AD och AD FS-federation.

### <a name="immutable-id-conditional-on-a-value-being-present-in-the-attribute"></a>Oföränderligt ID villkorlig, baserat på ett värde som innehåller attributet
Azure AD Connect kan du ange ett attribut som ska användas som en källfästpunkt när objekt synkroniseras till Azure AD. Om värdet i det anpassade attributet inte är tom, kanske du vill utfärda ett oföränderligt ID-anspråk.

Du kan till exempel välja **ms-ds-consistencyguid** som attribut för källfästpunkt och problemet **ImmutableID** som **ms-ds-consistencyguid** om attributet har ett värde mot den. Om det finns inget värde mot attributet, utfärda **objectGuid** som inte kan ändras-ID Du kan skapa uppsättning av anpassade anspråksregler som beskrivs i följande avsnitt.

**Regel 1: Läsa attribut**

    c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
    => add(store = "Active Directory", types = ("http://contoso.com/ws/2016/02/identity/claims/objectguid", "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"), query = "; objectGuid,ms-ds-consistencyguid;{0}", param = c.Value);

I den här regeln du frågar värdena för **ms-ds-consistencyguid** och **objectGuid** för användare från Active Directory. Ändra namnet på Arkiv till en lämplig store-namn i AD FS-distribution. Även ändra anspråk skriver till en korrekt anspråk typ för din federationsserver som definierats för **objectGuid** och **ms-ds-consistencyguid**.

Dessutom med hjälp av **lägga till** och inte **problemet**, du undvika att lägga till ett utgående problem för entiteten och kan använda värden som mellanliggande värdena. Du tänker utfärda anspråk i en regel för senare när du har skapat vilket värde som ska användas som inte kan ändras-ID

**Regel 2: Kontrollera om ms-ds-consistencyguid för användaren**

    NOT EXISTS([Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"])
    => add(Type = "urn:anandmsft:tmp/idflag", Value = "useguid");

Den här regeln anger en tillfällig flagga som kallas **idflag** som har angetts till **useguid** om det finns inga **ms-ds-consistencyguid** ifyllda. Logiken bakom det här är det faktum att AD FS inte tillåter tom anspråk. När du lägger till anspråk http://contoso.com/ws/2016/02/identity/claims/objectguid och http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid i regel 1 att avslutas med en **msdsconsistencyguid** anspråk endast om värdet har fyllts i för användaren. Om det inte är ifylld, ser att den har ett tomt värde och släpper den direkt i AD FS. Alla objekt har **objectGuid**, så det vidare kommer alltid att vara det när regel 1 körs.

**Regel 3: Utfärda ms-ds-consistencyguid som oföränderligt ID om den finns**

    c:[Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c.Value);

Det här är en implicit **Exist** kontrollera. Om värdet för anspråket finns sedan utfärda som som inte kan ändras-ID Det föregående exemplet används den **nameidentifier** anspråk. Du måste ändra det till lämplig Anspråkstypen för oföränderligt ID i din miljö.

**Regel 4: Utfärda objectGuid som oföränderligt ID om ms-ds-consistencyGuid inte finns**

    c1:[Type == "urn:anandmsft:tmp/idflag", Value =~ "useguid"]
    && c2:[Type == "http://contoso.com/ws/2016/02/identity/claims/objectguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c2.Value);

I den här regeln du helt enkelt kontrollerar flaggan tillfälliga **idflag**. Du bestämma om du vill utfärda anspråk baserat på dess värde.

> [!NOTE]
> Sekvens med dessa regler är viktigt.

### <a name="sso-with-a-subdomain-upn"></a>Enkel inloggning med en underdomän UPN

Du kan lägga till fler än en domän som ska federeras med hjälp av Azure AD Connect, enligt beskrivningen i [lägga till en ny federerad domän](how-to-connect-fed-management.md#addfeddomain). Azure AD Connect-version 1.1.553.0 och senaste skapar rätt anspråksregel för issuerID automatiskt. Om du inte använda Azure AD Connect version 1.1.553.0 eller senaste, rekommenderar vi att [Anspråksregler för Azure AD Rpthuvud](https://aka.ms/aadrptclaimrules) verktyget används för att generera och ange rätt anspråksregler för Azure AD-förtroende för förlitande part.

## <a name="next-steps"></a>Nästa steg
Läs mer om [användaren inloggningsalternativ](plan-connect-user-signin.md).
