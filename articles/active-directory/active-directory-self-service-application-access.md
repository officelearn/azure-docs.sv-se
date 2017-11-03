---
title: "Självbetjäning programåtkomst och delegerad hantering med Azure Active Directory | Microsoft Docs"
description: "Den här artikeln beskriver hur du aktiverar självbetjäning programåtkomst och delegerad hantering med Azure Active Directory."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 448a7fe8-a162-475e-9ba2-2e3ab59302bc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2017
ms.author: curtand
ms.reviewer: asmalser
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 39c62461c9659b0cb4422de88686283ba462c53b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="self-service-application-access-and-delegated-management-with-azure-active-directory"></a>Självbetjäning programåtkomst och delegerad hantering med Azure Active Directory
Aktivera funktioner för självbetjäning för slutanvändare är ett vanligt scenario för företagets IT. Många användare, många program och den person som är best-informed att fatta beslut om bevilja åtkomst kanske inte directory-administratör. Den bästa personen att bestämma vem som kan komma åt ett program är ofta ett team lead eller andra delegerad administratör. Men det är den användare som använder appen och användaren vet vad de behöver för att kunna utföra sitt jobb.

> [!IMPORTANT]
> Microsoft rekommenderar att du hanterar Azure AD via [Azure AD administratörscenter](https://aad.portal.azure.com) på Azure Portal istället för via den klassiska Azure-portalen som nämns i den här artikeln. 

Självbetjäning programåtkomst är en funktion i [Azure Active Directory Premium](https://azure.microsoft.com/trial/get-started-active-directory/) P1 och P2 licensiering som tillåter directory-administratörerna:

* Användarna kan begära åtkomst till program som använder en ”hämta fler program” panel i den [Azure AD-åtkomstpanelen](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users)
* Ange vilka program som användare kan begära åtkomst till
* Ange om ett godkännande krävs för att användarna ska kunna koppla automatisk åtkomst till ett program
* Ange vem som ska godkänna begäranden och hantera åtkomsten för varje program

Idag den här funktionen stöds för alla redan integrerade och anpassade appar som har stöd för federerad eller lösenordsbaserade Enkel webbinloggning den [Azure Active Directory-programgalleriet](https://azure.microsoft.com/marketplace/active-directory/all/), inklusive appar som Salesforce Dropbox, Google Apps och mycket mer.
Den här artikeln beskriver hur du:

* Konfigurera självbetjäning programåtkomst för slutanvändare, inklusive hur du konfigurerar ett valfritt Godkännandearbetsflöde 
* Delegera hantering för specifika program till den lämpligaste personer i din organisation och att de kan använda Azure AD-åtkomstpanelen för att godkänna förfrågningar, tilldela direkt åtkomst till valda användare och (valfritt) ange autentiseringsuppgifter för programåtkomst när lösenordsbaserade enkel inloggning har konfigurerats

## <a name="configuring-self-service-application-access"></a>Konfigurera självbetjäning programåtkomst
Så här aktiverar du självbetjäning programåtkomst och konfigurerat vilka program som kan läggas till eller begärs av slutanvändare, följer du dessa instruktioner.

1. Logga in på den [klassiska Azure-portalen](https://manage.windowsazure.com/).

2.   Under den **Active Directory** avsnittet, Välj din katalog och välj sedan den **program** fliken. 

3. Välj den **Lägg till** knappen och använder alternativet Galleri för att välja och lägga till ett program.

4. När appen har lagts till, får du sidan Snabbstart app. Klicka på **Konfigurera enkel inloggning**, välja enkel inloggning läge och spara konfigurationen. 

5. Välj sedan den **konfigurera** fliken. Om du vill att användarna ska begära åtkomst till den här appen från Azure AD-åtkomstpanelen, ange **Tillåt självbetjäning programåtkomst** till **Ja**.
  
  ![][1]

6. Du kan också konfigurera ett Godkännandearbetsflöde för åtkomstbegäranden genom att ange **kräver godkännande innan åtkomst beviljas** till **Ja**. Sedan kan välja en eller flera granskare med hjälp av den **godkännare** knappen.

  Godkännare kan vara alla användare i organisationen med ett konto i Azure AD och kan vara ansvarig för konto-etablering, licensiering eller andra affärsprocess din organisation kräver innan åtkomst beviljas till en app. Godkännaren kan även vara gruppägare på en eller flera delade kontogrupper och kan tilldela användare till någon av dessa grupper för att ge dem åtkomst via ett delat konto. 

  Om ingen godkännande krävs få användare direkt programmet till sina Azure AD-åtkomstpanelen. Om programmet har ställts in för [automatisk användaretablering](active-directory-saas-app-provisioning.md), eller har ställts in [”användarhanterat” lösenord SSO läge](active-directory-appssoaccess-whatis.md#password-based-single-sign-on), användaren har redan en användare-kontot och känna till lösenordet.

7. Om programmet har konfigurerats för att använda lösenordsbaserade enkel inloggning, och sedan finns också ett alternativ för att tillåta godkännaren för att ange autentiseringsuppgifter för enkel inloggning för varje användares räkning. Mer information finns i avsnittet på [delegerad åtkomsthantering](#delegated-application-access-management).

8. Slutligen den **grupp för Self-Assigned användare** visar namnet på den grupp som används för att lagra de användare som beviljas eller tilldelad åtkomst till programmet. Åtkomst godkännaren blir ägare till den här gruppen. Om gruppnamnet visas inte finns, skapas den automatiskt. Alternativt kan du ange gruppnamnet till namnet på en befintlig grupp.

9. Om du vill spara konfigurationen klickar du på **spara** längst ned på skärmen. Användarna kan nu vill begära åtkomst till den här appen från åtkomstpanelen.

10. Logga in på din organisations Azure AD-åtkomstpanelen på https://myapps.microsoft.com, helst med ett annat konto som inte är godkännare app om du vill försöka slutanvändarens upplevelse. 

11. Under den **program** klickar du på den **få fler program** panelen. Den här panelen visar ett galleri med alla program som har aktiverats för självbetjäning programåtkomst i katalogen, möjligheten att söka och filtrera efter kategori app till vänster. 

12. Klicka på en app av systemtillstånd aktiveras process för begäran. Om inga godkännandeprocessen krävs kommer programmet läggs direkt den **program** fliken efter en kort bekräftelse. Om godkännande krävs då visas en dialogruta som anger detta och ett e-postmeddelande har skickats till godkännare. Du måste vara signerade till åtkomstpanelen som icke-godkännare att se den här processen för begäran.

13. E-postmeddelandet dirigerar godkännaren ska logga in på Azure AD-åtkomstpanelen och Godkänn begäran. När begäran har godkänts (och eventuella särskilda processer som du definierar har utförts av godkännaren) ser användaren det programmet som visas under deras **program** flik där de kan logga in på den.

## <a name="delegated-application-access-management"></a>Delegerad åtkomst för programhantering
Godkännare programmet åtkomst kan vara alla användare i organisationen som är den mest lämpliga för att godkänna eller neka åtkomst till programmet i fråga. Den här användaren kan vara ansvarig för konto-etablering, licenser, eller andra affärsprocess din organisation kräver innan åtkomst beviljas till en app.

När du konfigurerar självbetjäning programåtkomst som beskrivs ovan, någon tilldelat program godkännare finns ytterligare **hantera program** panelen i Azure AD-åtkomstpanelen som visar dem vilka program som de är på Access-administratör. Klicka på en app visas en skärm flera alternativ.

![][2]

### <a name="approve-requests"></a>Godkänna begäranden
Den **godkänna begäranden** panelen tillåter godkännare för att se alla väntande godkännanden som är specifika för appen och omdirigerar till fliken godkännanden där begäranden kan bekräftas eller nekas. Godkännaren tar också emot automatiserade e-postmeddelanden när en begäran skapas som instruerar vad du gör.

### <a name="add-users"></a>Lägg till användare
Den **Lägg till användare** panelen tillåter godkännare att direkt tilldela valda användare åtkomst till programmet. När du klickar på den här panelen ser godkännaren en dialogruta tillåter dem att visa och Sök efter användare i sina kataloger. Lägger till en användare blir i programmet som visas i dessa användarens Azure AD åtkomst paneler eller Office 365. Om alla manuella användare etableringsprocessen krävs vid appen innan användaren kan logga in på ska godkännaren utföra den här processen innan du tilldelar åtkomst.  

### <a name="manage-users"></a>Hantera användare
Den **hantera användare** panelen tillåter godkännare direkt uppdatera eller ta bort vilka användare som har åtkomst till programmet. 

### <a name="configure-password-sso-credentials-if-applicable"></a>Konfigurera enkel inloggning lösenordsinformation (om tillämpligt)
Den **konfigurera** panelen visas bara om programmet har konfigurerats av IT-administratören kan använda lösenordsbaserade enkel inloggning och administratören beviljas godkännaren möjligheten att ange lösenordsinformation för enkel inloggning som beskrivs tidigare. När du väljer visas godkännaren flera alternativ för hur autentiseringsuppgifterna som sprids till tilldelade användare:

![][3]

* **Användare som loggar in med sina egna lösenord** – i det här läget tilldelade användare vet vad användarnamn och lösenord för programmet och uppmanas att ange dem på sina första inloggning till programmet. Scenariot motsvarar lösenordet SSO fallet där det [användare hantera autentiseringsuppgifter](active-directory-appssoaccess-whatis.md#password-based-single-sign-on).
* **Användarna automatiskt loggas in med separata konton som jag hanterar** – i det här läget tilldelade användare behöver inte ange eller veta sina autentiseringsuppgifter för app-specifik när du loggar in på programmet. I stället godkännaren anger autentiseringsuppgifterna för varje användare efter att åtkomst med hjälp av den **Lägg till användare** panelen. När användaren klickar på programmet i sin åtkomstpanelen eller Office 365, loggas de automatiskt in med de autentiseringsuppgifter som anges av godkännaren. Scenariot motsvarar lösenordet SSO fallet där det [administratörer hantera autentiseringsuppgifter](active-directory-appssoaccess-whatis.md#password-based-single-sign-on).
* **Användarna automatiskt loggas in med ett konto som jag hanterar** -ett specialfall, det här fallet är lämpligt att använda när alla tilldelade användare måste beviljas åtkomst med hjälp av en enda delad konto. I de flesta användningsfall för den här funktionen är med sociala mediaprogram, om en organisation har en enda ”” företag och flera användare behöver göra uppdateringar till det kontot. Scenariot motsvarande lösenord SSO fallet där det [administratörer hantera autentiseringsuppgifter](active-directory-appssoaccess-whatis.md#password-based-single-sign-on). Det här alternativet uppmanas godkännaren att ange användarnamn och lösenord för det enda delade kontot. När slutfört, är alla tilldelade användare inloggad med det här kontot när du klickar på programmet i sin Azure AD åtkomst paneler eller Office 365.

## <a name="additional-resources"></a>Ytterligare resurser
* [Artikelindex för programhantering i Azure Active Directory](active-directory-apps-index.md)

<!--Image references-->
[1]: ./media/active-directory-self-service-application-access/ssaa_admin.PNG
[2]: ./media/active-directory-self-service-application-access/ssaa_ap_manage_app.PNG
[3]: ./media/active-directory-self-service-application-access/ssaa_ap_manage_app_config.PNG
