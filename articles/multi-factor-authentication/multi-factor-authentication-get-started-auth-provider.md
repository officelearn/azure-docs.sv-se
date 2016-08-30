<properties 
    pageTitle="Komma igång med en Microsoft Azure Multi-Factor Authentication-provider" 
    description="Lär dig hur du skapar en Azure Multi-Factor Authentication-provider." 
    services="multi-factor-authentication" 
    documentationCenter="" 
    authors="billmath" 
    manager="stevenpo" 
    editor="curtand"/>

<tags 
    ms.service="multi-factor-authentication" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="05/12/2016" 
    ms.author="billmath"/>



# Komma igång med en Azure Multi-Factor Authentication-provider
Multi-Factor Authentication är tillgängligt som standard för globala administratörer med Azure Active Directory och Office 365-användare. Men om du vill dra nytta av [avancerade funktioner](multi-factor-authentication-whats-next.md) måste du köpa den fullständiga versionen av Azure MFA. 

> [AZURE.NOTE]  En Azure Multi-Factor Authentication-provider används för att dra nytta av funktionerna i den fullständiga versionen av Azure MFA. Den är avsedd för användare som **inte har licenser genom Azure MFA, Azure AD Premium eller EMS**.  Azure MFA Azure AD Premium och EMS innehåller den fullständiga versionen av Azure MFA som standard.  Om du har licenser behöver du inte någon Azure Multi-Factor Authentication-provider. 
 
Följ stegen nedan om du vill skapa en Azure Multi-FactorAuthentication-provider.

## Så här skapar du en Multi-Factor Authentication-provider
--------------------------------------------------------------------------------

1. Logga in på **den klassiska Azure-portalen** som administratör.
2. Välj **Active Directory** till vänster.
3. Längst upp på sidan Active Directory väljer du **Multi-Factor Authentication-providers**.
![Skapa en MFA-provider](./media/multi-factor-authentication-get-started-auth-provider/authprovider1.png)
4. Klicka på **Ny** längst ned på sidan.
![Skapa en MFA-provider](./media/multi-factor-authentication-get-started-auth-provider/authprovider2.png)
5. Under **Apptjänster** väljer du **Multi-Factor Auth-provider**
![Skapa en MFA-provider](./media/multi-factor-authentication-get-started-auth-provider/authprovider3.png)
6. Välj **Snabbregistrering**.
![Skapa en MFA-provider](./media/multi-factor-authentication-get-started-auth-provider/authprovider4.png)
5. Fyll i följande fält och välj **Skapa**.
    1. **Namn** – Namnet på Multi-Factor Auth-providern.
    2. **Användningsmodell** – Multi-Factor Authentication-providerns användningsmodell.
        - Per autentisering – köpmodell som debiterar per autentisering. Används vanligtvis för scenarier som använder Azure Multi-Factor Authentication i ett program som riktar sig till konsumenter.
        - Per aktiverad användare – köpmodell som debiterar per aktiverad användare. Används vanligtvis för medarbetaråtkomst till program som Office 365.
    2. **Katalog** – Azure Active Directory-klienten som Multi-Factor Authentication-providern är kopplad till. Tänk på följande:
        - Du behöver ingen Azure AD-katalog för att skapa en Multi-Factor Auth-provider.  Lämna bara rutan tom om du endast planerar att använda Azure Multi-Factor Authentication Server eller SDK.
        - Multi-Factor Auth-providern måste vara associerad med en Azure AD-katalog för att du ska kunna dra nytta av de avancerade funktionerna.
        - Azure AD Connect, AAD Sync eller DirSync är endast ett krav om du synkroniserar din lokala Active Directory-miljö med en Azure AD-katalog.  Om du bara använder en Azure AD-katalog som inte är synkroniserad så ![Skapa en MFA-provider](./media/multi-factor-authentication-get-started-auth-provider/authprovider5.png)    
5. När du klickar på Skapa skapas Multi-Factor Authentication-providern och ett meddelande visas som anger att **Multi-Factor Authentication-providern** har skapats. Klicka på **OK**.
![Skapa en MFA-provider](./media/multi-factor-authentication-get-started-auth-provider/authprovider6.png)    


<!--HONumber=jun16_HO2-->


