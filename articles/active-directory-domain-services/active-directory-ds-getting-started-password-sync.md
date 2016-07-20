<properties
    pageTitle="Azure AD Domain Services: Aktivera lösenordssynkronisering | Microsoft Azure"
    description="Komma igång med Azure Active Directory Domain Services"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="04/25/2016"
    ms.author="maheshu"/>

# Azure AD Domain Services *(förhandsversion)* – Aktivera lösenordssynkronisering till Azure AD DS

## Uppgift 5: Aktivera lösenordssynkronisering till AAD Domain Services för en molnbaserad Azure AD-katalog
När du har aktiverat Azure AD Domain Services för din Azure AD-klient är nästa uppgift att konfigurera synkroniseringen av autentiseringsuppgifter till Azure AD Domain Services. Detta gör det möjligt för användare att logga in i den hanterade domänen med sina företagsuppgifter.

Stegen skiljer sig beroende på om din organisation bara har en molnbaserad Azure AD-katalog eller om du synkroniserar med din lokala katalog med hjälp av Azure AD Connect.

<br>

> [AZURE.SELECTOR]
- [Endast molnbaserad Azure AD-katalog](active-directory-ds-getting-started-password-sync.md)
- [Synkroniserad Azure AD-katalog](active-directory-ds-getting-started-password-sync-synced-tenant.md)

<br>

### Aktivera genereringen av hashvärden för NTLM- och Kerberos-autentisering för en molnbaserad Azure AD-katalog
Om din organisation bara har en molnbaserad Azure AD-katalog måste användare som behöver använda Azure AD Domain Services ändra sina lösenord. Den här lösenordsändringsprocessen gör att autentiseringshasherna som krävs av Azure AD Domain Services för Kerberos- och NTLM-autentisering genereras i Azure AD. Du kan antingen ange att lösenorden för alla användare i klienten som behöver använda Azure AD Domain Services ska förfalla eller uppmana dessa användare att ändra sina lösenord.

Här är instruktionerna som du måste ge slutanvändarna för att de ska kunna ändra sina lösenord:

1. Gå till Azure AD-åtkomstpanelen för din organisation. Den här sidan finns vanligtvis på [http://myapps.microsoft.com](http://myapps.microsoft.com).

2. Välj fliken **Profil** på den här sidan.

3. Klicka på ikonen **Ändra lösenord** på den här sidan för att initiera en lösenordsändring.

    ![Skapa ett virtuellt nätverk för Azure AD Domain Services.](./media/active-directory-domain-services-getting-started/user-change-password.png)

4. Nu öppnas sidan **Ändra lösenord**. Användaren kan ange sitt befintliga (gamla) lösenord och sedan ändra lösenordet.

    ![Skapa ett virtuellt nätverk för Azure AD Domain Services.](./media/active-directory-domain-services-getting-started/user-change-password2.png)

När användaren har ändrat sitt lösenord kan det nya lösenordet användas i Azure AD Domain Services strax därefter. Efter ett par minuter kan användaren logga in på datorer som är anslutna till den hanterade domänen med hjälp av sitt nyligen ändrade lösenord.


<br>

## Relaterat innehåll

- [Aktivera lösenordssynkronisering till AAD Domain Services för en synkroniserad Azure AD-katalog](active-directory-ds-getting-started-password-sync-synced-tenant.md)

- [Administrera en Azure AD Domain Services-hanterad domän](active-directory-ds-admin-guide-administer-domain.md)

- [Ansluta en virtuell Windows-dator till en Azure AD Domain Services-hanterad domän](active-directory-ds-admin-guide-join-windows-vm.md)

- [Ansluta en virtuell Linux-dator med Red Hat Enterprise till en Azure AD Domain Services-hanterad domän](active-directory-ds-admin-guide-join-rhel-linux-vm.md)



<!--HONumber=Jun16_HO2-->


