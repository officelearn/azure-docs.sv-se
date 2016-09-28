<properties
    pageTitle="Lägga till ett anpassat domännamn och konfigurera sammansluten inloggning i Azure Active Directory | Microsoft Azure"
    description="Här lär du dig hur du lägger till ditt företags domännamn i Azure Active Directory och hur du konfigurerar sammansluten inloggning mellan Azure Active Directory och din lokala federationslösning."
    services="active-directory"
    documentationCenter=""
    authors="jeffsta"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/18/2016"
    ms.author="curtand;jeffsta"/>


# Lägga till ett eget domännamn i Azure Active Directory

Du kan konfigurera ett anpassat domännamn, t.ex contoso.com, så att användare i contoso.com kan använda federerad enkel inloggning från företagsnätverket. Om du redan har AD FS (Active Directory Federation Services) eller en annan federationsserver som körs i företagets nätverk kan du konfigurera Azure AD att använda ditt anpassade domännamn med verktyget Azure AD Connect. Du kan också använda Azure AD Connect för att distribuera en ny AD FS-miljö och konfigurera den för federerad enkel inloggning till Azure AD.

Om du inte har och inte planerar att distribuera AD FS eller en annan federationsserver följa du dessa anvisningar: [Lägg till ett anpassat domännamn i Azure Active Directory](active-directory-add-domain.md).

## Lägga till ett eget domännamn i katalogen

1. Logga in på [den klassiska Azure-portalen](https://manage.windowsazure.com/) med ett användarkonto som är en global administratör i Azure AD-katalogen.

2. I **Active Directory** öppnar du din katalog och väljer fliken **Domäner**.

3. Välj **Lägg till** i kommandofältet och ange sedan namnet på din domän, till exempel ”contoso.com”. Glöm inte att ta med .com, .net eller ett annat tillägg för toppnivån.

4. Markera kryssrutan **Jag planerar att konfigurera den här domänen för enkel inloggning med min lokala Active Directory **.

5. Välj **Lägg till**.

Kör verktyget Azure AD Connect för att hämta DNS-posten som Azure AD ska använda för att verifiera domänen. DNS-posten visas i steget **Azure AD-domän** i guiden. Du kan se hur det steget i guiden ser ut [i dessa anvisningar](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation). Om du inte har verktyget Azure AD Connect kan du [ladda ned det här](http://go.microsoft.com/fwlink/?LinkId=615771).

## Lägga till DNS-posten hos domännamnsregistratorn för domänen

Nästa steg mot att kunna använda ditt anpassade domännamn med Azure AD är att du uppdaterar domänens DNS-zonfil. Detta gör att Azure AD kan kontrollera att din organisation äger det anpassade domännamnet.

1. Logga in på webbplatsen för domännamnsregistratorn för ditt domännamn. Om du inte har nödvändig åtkomst ber du den person eller det team i din organisation som har den här åtkomsten att utföra steg 2 och att meddela dig när det är klart.

2. Uppdatera DNS-zonfilen för domänen genom att lägga till DNS-posten som du fått från Azure AD. Den här DNS-posten gör att Azure AD kan verifiera ditt ägarskap för domänen. DNS-posten ändrar inga beteenden, till exempel e-postdirigering eller webbhosting.

Om du behöver hjälp med det här steget läser du [Instruktioner för hur du lägger till en DNS-post hos populära DNS-registratorer](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## Verifiera domännamnet med Azure AD

När du har lagt till DNS-posten är du redo att verifiera domännamnet med Azure AD.

Verifiera domänen genom att välja **Nästa** i steget **Azure AD-domän** i Azure AD Connect-guiden. Azure AD söker efter DNS-posten i DNS-zonfilen för domänen. Azure AD verifierar bara domännamnet när DNS-posterna har distribuerats. Distributionen tar ofta bara några sekunder, men kan ibland ta en timme eller mer. Om verifieringen inte fungerar första gången försöker du igen lite senare.

Fortsätt sedan med de återstående stegen i Azure AD Connect-guiden. I dessa steg synkroniserar du användare från Windows Server AD till Azure AD. Synkroniserade användare i den domän som du konfigurerade för federation kan använda federerad enkel inloggning från företagets nätverk till Azure AD.

## Felsökning

Om du inte kan verifiera ett anpassat domännamn så pröva med följande. Vi börjar med de vanligaste och arbetar oss vidare till de minst vanliga.

1.  **Vänta en timma**. DNS-posterna måste spridas innan Azure AD kan verifiera domänen. Detta kan ta en timma eller mer.

2.  **Kontrollera att DNS-posten har angetts och att den är korrekt**. Utför det här steget på webbplatsen för domännamnsregistratorn för domänen. Azure AD kan inte verifiera domännamnet om DNS-posten inte finns i DNS-zonfilen, eller om den inte exakt matchar DNS-posten från Azure AD. Om du inte har den åtkomst som krävs för att uppdatera DNS-poster för domänen hos domännamnsregistratorn så dela DNS-posten med den person eller grupp i din organisation som har den åtkomst som krävs och be dem att lägga till DNS-posten.

3.  **Ta bort domännamnet från en annan katalog i Azure AD**. Ett domännamn kan bara verifieras i en enskild katalog. Om ett domännamn tidigare verifierades i en annan katalog måste du ta bort det därifrån innan du kan verifiera det i din nya katalog. Mer information om hur du tar bort domännamn finns i [Hantera anpassade domännamn](active-directory-add-manage-domain-names.md).

## Lägga till fler anpassade domännamn

Om din organisation använder flera anpassade domännamn, t.ex. contoso.com och contosobank.com, kan du lägga till dem. Du kan lägga till upp till 900 domännamn. Lägg till vart och ett av dina domännamn genom att följa samma steg i den här artikeln.

## Nästa steg

-   [Hantera egna domännamn](active-directory-add-manage-domain-names.md)
-   [Lär dig mer om domänhanteringsbegrepp i Azure AD](active-directory-add-domain-concepts.md)
-   [Visa en företagsanpassad sida när användarna loggar in](active-directory-add-company-branding.md)
-   [Hantera domännamn i Azure AD med hjälp av PowerShell](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)



<!--HONumber=Sep16_HO3-->


