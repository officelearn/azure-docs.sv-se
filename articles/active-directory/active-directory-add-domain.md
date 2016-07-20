<properties
    pageTitle="Lägga till ett eget domännamn i Azure Active Directory | Microsoft Azure"
    description="Lär dig hur du lägger till ditt företags domännamn i Azure Active Directory och hur du verifierar namnet."
    services="active-directory"
    documentationCenter=""
    authors="jeffsta"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="04/20/2016"
    ms.author="curtand;jeffsta"/>

# Lägga till ett eget domännamn i Azure Active Directory

Din organisation använder ett eller flera domännamn för att göra affärer och dina användare loggar in i företagsnätverket med företagets domännamn. Nu när du använder Azure Active Directory (AD Azure) kan du även lägga till företagets domännamn i Azure AD. På så sätt kan du tilldela användarnamn i katalogen som dina användare känner igen, t.ex. ”alice@contoso.com”. Processen är enkel:

- Lägg till domännamnet i guiden **Lägg till domän** på den klassiska Azure-portalen.

- Hämta DNS-posten på den klassiska Azure AD-portalen eller med verktyget Azure AD Connect.

- Lägg till DNS-posten för domännamnet i DNS-zonfilen på DNS-registratorns webbplats.

- Verifiera domännamnet på den klassiska Azure AD-portalen eller med Azure AD Connect.


Innan du verifierar ditt domännamn måste användarna logga in med användarnamn som ”alice@contoso.onmicrosoft.com”, som använder det ursprungliga domännamnet för din katalog. Om du behöver flera anpassade domännamn, till exempel ”contoso.com” och ”contosobank.com”, kan du lägga till dem. Du kan lägga till upp till 900 domännamn. Följ samma steg i den här artikeln för att lägga till alla domännamnen.

## Lägga till ett eget domännamn i katalogen

1. Logga in på [den klassiska Azure-portalen](https://manage.windowsazure.com/) med ett användarkonto som är en global administratör i Azure AD-katalogen.

2. I **Active Directory** öppnar du din katalog och väljer fliken **Domäner**.

3. Välj **Lägg till** i kommandofältet och ange sedan namnet på din domän, till exempel ”contoso.com”. Glöm inte att ta med .com, .net eller ett annat tillägg för toppnivån.

4. Markera kryssrutan om du planerar att konfigurera den här domänen för [federerad inloggning](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect) med din lokala Active Directory.

5. Välj **Lägg till**.

Nu när du har lagt till domännamnet måste Azure AD kontrollera att din organisation äger domännamnet. Innan Azure AD kan utföra den här kontrollen måste du lägga till en DNS-post i DNS-zonfilen för domännamnet. Den här uppgiften utförs på webbplatsen för domännamnsregistratorn för domännamnet.

## Hämta DNS-posterna för domännamnet

DNS-posterna finns på den andra sidan i guiden **Lägg till domän** om du inte federerar med en lokal Windows Server Active Directory.

Om du konfigurerar domänen för federation uppmanas du att ladda ned verktyget Azure AD Connect Kör verktyget Azure AD Connect för att [hämta DNS-posterna som du behöver lägga till hos domännamnsregistratorn](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation). Azure AD Connect kontrollerar också domännamnet för Azure AD.

## Lägga till DNS-posten i DNS-zonfilen

1.  Logga in hos domännamnsregistratorn för domänen. Om du inte har tillräcklig behörighet för att uppdatera DNS-posten ber du den person eller det team som har nödvändig behörighet att lägga till DNS-posten.

2.  Uppdatera DNS-zonfilen för domänen genom att lägga till DNS-posten som du fått från Azure AD. Den här DNS-posten gör att Azure AD kan verifiera ditt ägarskap för domänen. DNS-posten ändrar inga beteenden, till exempel e-postdirigering eller webbhosting. Det kan ta upp till en timme för DNS-posterna att spridas.

[Instruktioner för hur du lägger till en DNS-post hos populära DNS-registratorer](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## Verifiera domännamnet med Azure AD

När du har lagt till DNS-posten måste du kontrollera att domännamnet har verifierats av Azure AD. Detta är det sista steget.

Om guiden **Lägg till domän** fortfarande är öppen väljer du **Verifiera** på den tredje sidan i guiden. Du kan behöva vänta upp till en timme på att DNS-posten ska spridas innan du får bekräftelsen.

Om guiden **Lägg till domän** inte är öppen kan du verifiera domänen på [den klassiska Azure-portalen](https://manage.windowsazure.com/):

1.  Logga in med ett användarkonto som är en global administratör i Azure AD-katalogen.

2.  Öppna katalogen och välj fliken **Domäner**.

3.  Välj den domän som du vill verifiera.

4.  Välj **Verifiera** på kommandoraden och välj **Verifiera** i dialogrutan.

Grattis! Nu kan du [tilldela användarnamn som innehåller ditt domännamn](active-directory-add-domain-add-users.md). Om du hade problem med att verifiera domännamnet läser du avsnittet [Felsökning](#troubleshooting).

## Felsökning
Om du inte kan verifiera ett eget domännamn kan det bero på några möjliga orsaker. Vi börjar med de vanligaste och arbetar oss vidare till de minst vanliga.

- Du försökte verifiera domännamnet innan DNS-posten hade spridits. Vänta en stund och försök igen.

- DNS-posten registrerades inte över huvud taget. Verifiera DNS-posten, vänta tills den har spridits och försök igen.

- Domännamnet har redan verifierats i en annan katalog. Leta upp domännamnet, ta bort den från den andra katalogen och försök igen.

- DNS-posten innehåller ett fel. Åtgärda felet och försök igen.

- Du saknar behörighet att uppdatera DNS-poster. Dela DNS-posterna med den person eller det team i din organisation som har nödvändig behörighet och be dem att lägga till DNS-posten.


## Nästa steg

-   [Tilldela användarnamn som innehåller ditt domännamn](active-directory-add-domain-add-users.md)
-   [Hantera egna domännamn](active-directory-add-manage-domain-names.md)
-   [Lär dig mer om domänhanteringsbegrepp i Azure AD](active-directory-add-domain-concepts.md)
-   [Visa en företagsanpassad sida när användarna loggar in](active-directory-add-company-branding.md)
-   [Hantera domännamn i Azure AD med hjälp av PowerShell](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)



<!--HONumber=Jun16_HO2-->


