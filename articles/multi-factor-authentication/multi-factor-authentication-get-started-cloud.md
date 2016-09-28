<properties 
    pageTitle="Komma igång med Microsoft Azure Multi-Factor Authentication i molnet"
    description="Det här är sidan om Microsoft Azure Multi-Factor Authentication som beskriver hur du kommer igång med Azure MFA i molnet."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/15/2016"
    ms.author="kgremban"/>


# Komma igång med Azure Multi-Factor Authentication i molnet
I följande artikel lär du dig hur du kommer igång med Azure Multi-Factor Authentication i molnet.

> [AZURE.NOTE]  Följande dokumentation innehåller information om hur du aktiverar användare med hjälp av **den klassiska Azure-portalen**. Information om hur du konfigurerar Azure Multi-Factor Authentication för O365-användare finns i [Konfigurera Multi-Factor Authentication för Office 365.](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6?ui=en-US&rs=en-US&ad=US)

![MFA i molnet](./media/multi-factor-authentication-get-started-cloud/mfa_in_cloud.png)

## Krav
Följande krav måste vara uppfyllda innan du kan aktivera Azure Multi-Factor Authentication för användarna.




- [Registrera dig för en Azure-prenumeration](https://azure.microsoft.com/pricing/free-trial/) – Om du inte redan har en Azure-prenumeration måste du registrera dig för en. Om du precis börjat använda Azure MFA kan du använda en utvärderingsprenumeration.
2. [Skapa en Multi-Factor Auth-provider](multi-factor-authentication-get-started-auth-provider.md) och tilldela den till din katalog eller [tilldela licenser till användare](multi-factor-authentication-get-started-assign-licenses.md)

> [AZURE.NOTE]  Licenser är tillgängliga för användare som har Azure MFA, Azure AD Premium eller Enterprise Mobility Suite (EMS).  MFA ingår i Azure AD Premium och EMS. Om du har tillräckligt med licenser behöver du inte skapa en autentiseringsprovider.


## Så här aktiverar du Multi-Factor Authentication för användare
Om du vill aktivera Multi-Factor Authentication för en användare ändrar du bara användarens status från inaktiv till aktiv.  Mer information om användarstatus finns i [Användarstatus i Azure Multi-Factor Authentication](multi-factor-authentication-get-started-user-states.md)

Följ stegen nedan om du vill aktivera MFA för dina användare.

### Så här aktiverar du Multi-Factor Authentication
--------------------------------------------------------------------------------
1.  Logga in på **den klassiska Azure-portalen** som administratör.
2.  Klicka på **Active Directory** till vänster.
3.  Under **Katalog** klickar du på katalogen för den användare som du vill aktivera.
![Klicka på Katalog](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4.  Klicka på **Användare** överst på sidan.
5.  Klicka på **Hantera Multi-Factor Auth** längst ned på sidan.
![Klicka på Katalog](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6.  En ny webbläsarflik öppnas.  Leta upp den användare som du vill aktivera för Multi-Factor Authentication. Du kan behöva ändra vyn överst på sidan. Kontrollera att statusen är **inaktiverad.**
![Aktivera användare](./media/multi-factor-authentication-get-started-cloud/enable1.png)
7.  Markera **rutan** bredvid användarens namn.
7.  Klicka på **Aktivera** till höger.
![Aktivera användare](./media/multi-factor-authentication-get-started-cloud/user1.png)
8.  Klicka på **Aktivera Multi-Factor Auth**.
![Aktivera användare](./media/multi-factor-authentication-get-started-cloud/enable2.png)
9.  Nu ändras användarens status från **inaktiverad** till **aktiverad**.
![Aktivera användare](./media/multi-factor-authentication-get-started-cloud/user.png)
10.  När du har aktiverat användarna rekommenderar vi att du meddelar dem om detta via e-post.  Informera dem också om hur de kan använda sina icke-webbläsarbaserade appar för att undvika att bli utelåsta.


## Automatisera aktiveringen av Multi-Factor Authentication med PowerShell

Du kan ändra [status](multi-factor-authentication-whats-next.md) med [Azure AD PowerShell](../powershell-install-configure.md) genom att använda följande.  Du kan ändra `$st.State` till någon av följande statusar:


- Enabled
- Enforced
- Disabled  

> [AZURE.IMPORTANT]  Tänk på att om du går direkt från statusen Disable till Enforced så slutar äldre autentiseringsklienter att fungera eftersom användaren inte har gått igenom MFA-registreringen och erhållit ett [applösenord](multi-factor-authentication-whats-next.md#app-passwords).  Om du har äldre autentiseringsklienter och kräver applösenord rekommenderar vi att du går från statusen Disabled till Enabled.  På så sätt kan användarna registrera sig och erhålla sina lösenord.   

        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta

PowerShell är ett alternativ om du vill aktivera många användare samtidigt.  För närvarande finns det inget alternativ för massaktivering på Azure-portalen och du måste markera varje användare separat.  Detta kan vara ganska tidskrävande om du har många användare.  Genom att skapa ett PowerShell-skript med ovanstående kod kan du loopa igenom listan med användare och aktivera dem.  Här är ett exempel:

    $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
    foreach ($user in $users)
    {
        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
    }


Mer information om användarstatus finns i [Användarstatus i Azure Multi-Factor Authentication](multi-factor-authentication-get-started-user-states.md)

## Nästa steg
Nu när du har konfigurerat Multi-Factor Authentication i molnet kan du konfigurera din distribution.  Se [Konfigurera Azure Multi-Factor Authentication.]



<!--HONumber=Sep16_HO3-->


