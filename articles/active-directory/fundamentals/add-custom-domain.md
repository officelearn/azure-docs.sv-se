---
title: Lägg till din anpassade domän - Azure Active Directory | Microsoft-dokument
description: Instruktioner om hur du lägger till en anpassad domän med Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: ajburnle
ms.reviewer: elkuzmen
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: de91bd7e1e4c5f9909213f663dd3ede0f979d4de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262157"
---
# <a name="add-your-custom-domain-name-using-the-azure-active-directory-portal"></a>Lägga till ditt eget domännamn med Azure Active Directory-portalen

Varje ny Azure AD-klient levereras med ett första domännamn, * \<domännamn>.onmicrosoft.com*. Du kan inte ändra eller ta bort det ursprungliga domännamnet, men du kan lägga till organisationens namn. Genom att lägga till anpassade domännamn kan du skapa användarnamn som är välbekanta för användarna, till exempel *alain\@contoso.com*.

## <a name="before-you-begin"></a>Innan du börjar

Innan du kan lägga till ett eget domännamn skapar du ditt domännamn med en domänregistratorer. För en ackrediterad domänregistratorer, se [ICANN-ackrediterade registratorer](https://www.icann.org/registrar-reports/accredited-list.html).

## <a name="create-your-directory-in-azure-ad"></a>Skapa din katalog i Azure AD

När du har fått ditt domännamn kan du skapa din första Azure AD-katalog. Logga in på Azure-portalen för din katalog med hjälp av ett konto med **ägarrollen** för prenumerationen.

Skapa den nya katalogen genom att följa stegen i [Skapa en ny klient för din organisation](active-directory-access-create-new-tenant.md#create-a-new-tenant-for-your-organization).

>[!IMPORTANT]
>Den person som skapar klienten är automatiskt global administratör för den klienten. Den globala administratören kan lägga till ytterligare administratörer till klienten.

Mer information om prenumerationsroller finns i [Azure RBAC-roller](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-rbac-roles).

>[!TIP]
> Om du planerar att federera din lokala Windows Server AD med Azure AD, måste du välja **jag planerar att konfigurera den här domänen för enkel inloggning med min lokala Active Directory** när du kör Azure AD Connect-verktyget för att synkronisera dina kataloger.
>
> Du behöver även registrera samma domännamn som du väljer för federering med din lokala katalog i steget **Azure AD-domän** i guiden. Information om hur installationen ser ut finns i [Verifiera den Azure AD-domän som valts för federationen](../hybrid/how-to-connect-install-custom.md#verify-the-azure-ad-domain-selected-for-federation). Om du inte har Azure AD Connect-verktyget kan du [hämta det här](https://go.microsoft.com/fwlink/?LinkId=615771).

## <a name="add-your-custom-domain-name-to-azure-ad"></a>Lägga till ditt eget domännamn i Azure AD

När du har skapat katalogen kan du lägga till ditt eget domännamn.

1. Logga in på [Azure-portalen](https://portal.azure.com/) med ett Globalt administratörskonto för katalogen.

1. Sök efter och välj *Azure Active Directory* från valfri sida. Välj sedan **Anpassade domännamn** > **Lägg till anpassad domän**.

    ![Anpassad sida med en anpassad domän visas](media/add-custom-domain/add-custom-domain.png)

1. I **Anpassat domännamn**anger du organisationens nya namn i det här exemplet *contoso.com*. Välj **Add domain** (Lägg till domän).

    ![Anpassad sida med domännamn med sidan Lägg till anpassad domän](media/add-custom-domain/add-custom-domain-blade.png)

    >[!IMPORTANT]
    >Du måste inkludera *.com*, *.net*eller något annat tillägg på den översta nivån för att det ska fungera korrekt.

    Den overifierade domänen läggs till. Den **contoso.com** sidan visas med din DNS-information. Spara den här informationen. Du behöver det senare för att skapa en TXT-post för att konfigurera DNS.

    ![Contoso-sida med DNS-inmatningsinformation](media/add-custom-domain/contoso-blade-with-dns-info.png)

## <a name="add-your-dns-information-to-the-domain-registrar"></a>Lägga till din DNS-information i domänregistratorern

När du har lagt till ditt eget domännamn i Azure AD måste du gå tillbaka till domänregistraren och lägga till Azure AD DNS-informationen från din kopierade TXT-fil. Om du skapar den här TXT-posten för domänen verifieras äganderätten till ditt domännamn.

Gå tillbaka till domänregistraren och skapa en ny TXT-post för din domän baserat på din kopierade DNS-information. Ställ in tiden för att leva (TTL) till 3600 sekunder (60 minuter) och spara sedan posten.

>[!IMPORTANT]
>Du kan registrera så många domännamn du vill. Varje domän får dock sin egen TXT-post från Azure AD. Var försiktig när du anger TXT-filinformationen hos domänregistraren. Om du anger fel eller duplicera information av misstag måste du vänta tills TTL tar time out (60 minuter) innan du kan försöka igen.

## <a name="verify-your-custom-domain-name"></a>Verifiera ditt eget domännamn

När du har registrerat ditt eget domännamn kontrollerar du att det är giltigt i Azure AD. Spridningen från domänregistrtorn till Azure AD kan vara omedelbar eller det kan ta några dagar, beroende på din domänregistratorer.

Så här verifierar du ditt eget domännamn:

1. Logga in på [Azure-portalen](https://portal.azure.com/) med ett Globalt administratörskonto för katalogen.

1. Sök efter och välj *Azure Active Directory* på valfri sida och välj sedan Anpassade **domännamn**.

1. Välj det anpassade domännamnet i **Anpassade domännamn.** I det här exemplet väljer du **contoso.com**.

    ![Fabrikam - Anpassad domännamnssida, med contoso markerat](media/add-custom-domain/custom-blade-with-contoso-highlighted.png)

1. På sidan **contoso.com** väljer du **Verifiera** för att se till att din anpassade domän är korrekt registrerad och gäller för Azure AD.

    ![Contoso-sida med DNS-inmatningsinformation och knappen Verifiera](media/add-custom-domain/contoso-blade-with-dns-info-verify.png)

När du har verifierat ditt eget domännamn kan du ta bort verifierings-TXT- eller MX-filen.

## <a name="common-verification-issues"></a>Vanliga verifieringsproblem

Om Azure AD inte kan verifiera ett eget domännamn kan du prova följande förslag:

- **Vänta minst en timme och försök igen**. DNS-posterna måste spridas innan Azure AD kan verifiera domänen. Processen kan ta en timma eller mer.

- **Kontrollera att DNS-posten är korrekt.** Gå tillbaka till domännamnsregistrarwebbplatsen. Kontrollera att posten finns där och att den matchar DNS-inmatningsinformationen som tillhandahålls av Azure AD.

  Om du inte kan uppdatera posten på registratorwebbplatsen delar du posten med någon som har behörighet att lägga till posten och verifiera att den är korrekt.

- **Kontrollera att domännamnet inte redan används i en annan katalog.** Ett domännamn kan bara verifieras i en katalog. Om ditt domännamn för närvarande verifieras i en annan katalog kan det inte också verifieras i den nya katalogen. Om du vill åtgärda problemet med duplicering måste du ta bort domännamnet från den gamla katalogen. Mer information om hur du tar bort domännamn finns i [Hantera anpassade domännamn](../users-groups-roles/domains-manage.md).

- **Se till att du inte har några ohanterade Power BI-klienter.** Om användarna har aktiverat Power BI genom självbetjäningsannons och skapat en ohanterad klient för din organisation måste du ta över hanteringen som en intern eller extern administratör med PowerShell. Mer information finns i [Ta över en ohanterad katalog som administratör i Azure Active Directory](../users-groups-roles/domains-admin-takeover.md).

## <a name="next-steps"></a>Nästa steg

- Lägg till ytterligare en global administratör i katalogen. Mer information finns i [Så här tilldelar du roller och administratörer](active-directory-users-assign-role-azure-portal.md).

- Lägg till användare i domänen. Mer information finns i [Så här lägger du till eller tar bort användare](add-users-azure-active-directory.md).

- Hantera din domännamnsinformation i Azure AD. Mer information finns i [Hantera anpassade domännamn](../users-groups-roles/domains-manage.md).

- Om du har lokala versioner av Windows Server som du vill använda tillsammans med Azure Active Directory läser du [Integrera dina lokala kataloger med Azure Active Directory](../connect/active-directory-aadconnect.md).
