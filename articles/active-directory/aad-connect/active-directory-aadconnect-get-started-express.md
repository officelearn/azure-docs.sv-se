<properties
    pageTitle="Azure AD Connect: Komma igång med standardinställningarna | Microsoft Azure"
    description="Lär dig hur du laddar ned, installerar och kör installationsguiden för Azure AD Connect."
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/13/2016"
    ms.author="billmath;andkjell"/>


# Komma igång med Azure AD Connect med standardinställningar
**Standardinställningar** för Azure AD Connect används om du har en topologi med en enda skog och om du använder [lösenordssynkronisering](../active-directory-aadconnectsync-implement-password-synchronization.md) för autentisering. **Standardinställningar** är standardalternativet och används i de vanligaste distributionsscenarierna. Du är bara några få klick bort från att utöka din lokala katalog till molnet.

Innan du börjar installera Azure AD Connect [laddar du ned Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771) och kontrollerar att du uppfyller kraven i [Azure AD Connect: Maskinvara och krav](../active-directory-aadconnect-prerequisites.md).

Om standardinställningarna inte passar din topologi läser du den [relaterade dokumentationen](#related-documentation) för andra scenarier.

## Snabbinstallation av Azure AD Connect
Du kan se dessa steg i praktiken i avsnittet med [videoklipp](#videos).

1. Logga in som lokal administratör på den server som du vill installera Azure AD Connect på. Gör detta på den server som ska vara synkroniseringsservern.
2. Navigera till och dubbelklicka på **AzureADConnect.msi**.
3. På välkomstskärmen markerar du kryssrutan för att acceptera licensvillkoren och klickar sedan på **Fortsätt**.  
4. På skärmen Standardinställningar klickar du på **Använd standardinställningar**.  
![Välkommen till Azure AD Connect](./media/active-directory-aadconnect-get-started-express/express.png)
5. På sidan Anslut till Azure AD anger du användarnamnet och lösenordet för en global administratör för din Azure AD. Klicka på **Next**.  
![Anslut till Azure AD ](./media/active-directory-aadconnect-get-started-express/connectaad.png) Om du får ett fel och har problem med anslutningen läser du [Felsöka anslutningsproblem](../active-directory-aadconnect-troubleshoot-connectivity.md).
6. På skärmen Anslut till AD DS anger du användarnamnet och lösenordet för ett företagsadministratörskonto. Du kan ange domändelen i NetBios- eller FQDN-format, d.v.s. FABRIKAM\administrator eller fabrikam.com\administrator. Klicka på **Next**.  
![Anslut till AD DS](./media/active-directory-aadconnect-get-started-express/connectad.png)
7. Sidan [**Konfigurera Azure AD-inloggning**](../active-directory-aadconnect-user-signin.md#azure-ad-sign-in-configuration) visas bara om du inte slutförde [domänverifieringen](../active-directory-add-domain.md) under [kravfasen](../active-directory-aadconnect-prerequisites.md).
![Overifierade domäner](./media/active-directory-aadconnect-get-started-express/unverifieddomain.png)  
Om den här sidan visas granskar du alla domäner som har markerats med **Inte tillagd** och **Inte verifierad**. Kontrollera att de domäner som du använder har verifierats i Azure AD. Klicka på symbolen Uppdatera när du har verifierat dina domäner.
8. Klicka på **Installera** på skärmen Klart att konfigurera.
    - Du kan också välja att avmarkera kryssrutan **Starta synkroniseringen så fort konfigurationen är klar** på sidan Klart att konfigurera. Du måste avmarkera kryssrutan om du vill göra ytterligare konfigurationsinställningar, t.ex. [filtrering](../active-directory-aadconnectsync-configure-filtering.md). Om du avmarkerar det här alternativet konfigurerar guiden synkronisering men lämnar schemaläggaren inaktiverad. Den körs inte förrän du aktiverar den manuellt genom att [köra installationsguiden igen](../active-directory-aadconnectsync-installation-wizard.md).
    - Om du har Exchange i din lokala Active Directory kan du också välja att aktivera [**Exchange-hybridinstallation**](https://technet.microsoft.com/library/jj200581.aspx). Aktivera det här alternativet om du planerar att ha Exchange-postlådor både i molnet och lokalt samtidigt.
![Redo att konfigurera Azure AD Connect](./media/active-directory-aadconnect-get-started-express/readytoconfigure.png)
9. När installationen är klar klickar du på **Avsluta**.
10. När installationen har slutförts kan du logga ut och logga in igen innan du använder Synchronization Service Manager eller Synchronization Rule Editor.

## Videoklipp

Här finns ett videoklipp om snabbinstallationer:

>[AZURE.VIDEO azure-active-directory-connect-express-settings]

## Nästa steg
Nu när Azure AD Connect är installerat kan du [verifiera installationen och tilldela licenser](../active-directory-aadconnect-whats-next.md).

Lär dig mer om dessa funktioner, som aktiverades med installationen: [Automatisk uppgradering](../active-directory-aadconnect-feature-automatic-upgrade.md), [Förhindra oavsiktliga borttagningar](../active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) och [Azure AD Connect Health](../active-directory-aadconnect-health-sync.md).

Läs mer om [schemaläggaren och hur du utlöser synkronisering](../active-directory-aadconnectsync-feature-scheduler.md).

Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](../active-directory-aadconnect.md).

## Relaterad dokumentation

Avsnitt |  
--------- | ---------
Översikt över Azure AD Connect | [Integrera dina lokala identiteter med Azure Active Directory](../active-directory-aadconnect.md)
Installera med anpassade inställningar | [Anpassad installation av Azure AD Connect](active-directory-aadconnect-get-started-custom.md)
Uppgradera från DirSync | [Uppgradera från Azure AD-synkroniseringsverktyget (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md)
Konton som används för installation | [Mer information om konton och behörigheter för Azure AD Connect](active-directory-aadconnect-accounts-permissions.md)



<!--HONumber=Oct16_HO1-->


