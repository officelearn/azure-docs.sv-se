---
title: Lägg till din anpassade domän - Azure Active Directory | Microsoft Docs
description: Anvisningar om hur du lägger till en anpassad domän med Azure Active Directory.
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
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073540"
---
# <a name="add-your-custom-domain-name-using-the-azure-active-directory-portal"></a>Lägg till ditt domännamn med hjälp av Azure Active Directory-portalen

Varje ny Azure AD-klient kommer med ett första domän namn *\<domän namn >. onmicrosoft. com*. Du kan inte ändra eller ta bort det ursprungliga domän namnet, men du kan lägga till organisationens namn. Genom att lägga till anpassade domän namn kan du skapa användar namn som är bekanta för dina användare, till exempel *alain\@contoso.com*.

## <a name="before-you-begin"></a>Innan du börjar

Innan du kan lägga till ett anpassat domän namn skapar du domän namnet med en domän registrator. En auktoriserad domänregistrator Se [ICANN-Accredited Registratorer](https://www.icann.org/registrar-reports/accredited-list.html).

## <a name="create-your-directory-in-azure-ad"></a>Skapa din katalog i Azure AD

När du får ditt domännamn kan skapa du din första Azure AD-katalog. Logga in på Azure Portal för din katalog med hjälp av ett konto med **ägar** rollen för prenumerationen.

Skapa en ny katalog genom att följa stegen i [skapar en ny klient för din organisation](active-directory-access-create-new-tenant.md#create-a-new-tenant-for-your-organization).

>[!IMPORTANT]
>Den person som skapar klienten blir automatiskt den globala administratören för den klienten. Global administratör kan lägga till ytterligare administratörer för klienten.

Mer information om prenumerations roller finns i [Azure RBAC-roller](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-rbac-roles).

>[!TIP]
> Om du planerar att federera din lokala Windows Server AD med Azure AD måste du välja **Jag planerar att konfigurera den här domänen för enkel inloggning med min lokala Active Directory** när du kör Azure AD Connect-verktyget för att synkronisera dina kataloger.
>
> Du behöver även registrera samma domännamn som du väljer för federering med din lokala katalog i steget **Azure AD-domän** i guiden. Information om hur installationen ser ut finns i [Verifiera att den Azure AD-domän som valts för Federation](../hybrid/how-to-connect-install-custom.md#verify-the-azure-ad-domain-selected-for-federation). Om du inte har verktyget Azure AD Connect kan du [Ladda ned det här](https://go.microsoft.com/fwlink/?LinkId=615771).

## <a name="add-your-custom-domain-name-to-azure-ad"></a>Lägga till ett anpassat domännamn i Azure AD

När du har skapat din katalog kan du lägga till ditt domännamn.

1. Logga in på [Azure-portalen](https://portal.azure.com/) med ett Globalt administratörskonto för katalogen.

1. Sök efter och välj *Azure Active Directory* från vilken sida som helst. Välj sedan **anpassade domän namn** > **Lägg till anpassad domän**.

    ![Sidan anpassade domän namn med Lägg till anpassad domän visas](media/add-custom-domain/add-custom-domain.png)

1. I **eget domän namn**anger du organisationens nya namn, i det här exemplet *contoso.com*. Välj **Add domain** (Lägg till domän).

    ![Sidan anpassade domän namn med sidan Lägg till anpassad domän](media/add-custom-domain/add-custom-domain-blade.png)

    >[!IMPORTANT]
    >Du måste inkludera *. com*, *.net*eller något annat tillägg på den översta nivån för att detta ska fungera korrekt.

    Den overifierade domänen har lagts till. Sidan **contoso.com** visas med din DNS-information. Spara den här informationen. Du behöver den senare för att kunna skapa en TXT-post för att konfigurera DNS.

    ![Contoso-sida med information om DNS-post](media/add-custom-domain/contoso-blade-with-dns-info.png)

## <a name="add-your-dns-information-to-the-domain-registrar"></a>Lägga till DNS-information i domänregistratorn

När du lägger till ett anpassat domännamn till Azure AD måste du gå tillbaka till din domänregistrator och lägga till Azure AD-DNS-information från den kopierade TXT-filen. Om du skapar den här TXT-posten för din domän verifieras ägarskapet för ditt domän namn.

Gå tillbaka till din domän registrator och skapa en ny TXT-post för din domän baserat på din kopierade DNS-information. Ange TTL-värdet (Time to Live) till 3600 sekunder (60 minuter) och spara sedan posten.

>[!IMPORTANT]
>Du kan registrera så många domännamn som du vill ha. Varje domän får dock sin egen TXT-posten från Azure AD. Var försiktig när du anger TXT-filinformationen på domän registratorn. Om du anger fel eller Duplicerad information av misstag måste du vänta tills TTL-tiden nåtts (60 minuter) innan du kan försöka igen.

## <a name="verify-your-custom-domain-name"></a>Kontrollera ditt domännamn

När du har registrerat ditt anpassade domän namn kontrollerar du att det är giltigt i Azure AD. Spridningen från din domän registrator till Azure AD kan vara omedelbar eller så kan det ta några dagar, beroende på din domän registrator.

Följ dessa steg om du vill verifiera ditt anpassade domän namn:

1. Logga in på [Azure-portalen](https://portal.azure.com/) med ett Globalt administratörskonto för katalogen.

1. Sök efter och välj *Azure Active Directory* från vilken sida som helst, och välj sedan **anpassade domän namn**.

1. I **anpassade domän namn**väljer du det anpassade domän namnet. I det här exemplet väljer du **contoso.com**.

    ![Fabrikam - anpassad domän, sida med contoso markerat](media/add-custom-domain/custom-blade-with-contoso-highlighted.png)

1. På sidan **contoso.com** väljer du **Verifiera** för att kontrol lera att din anpassade domän är korrekt registrerad och är giltig för Azure AD.

    ![Contoso-sida med information om DNS-post och knappen verifiera](media/add-custom-domain/contoso-blade-with-dns-info-verify.png)

När du har verifierat ditt anpassade domän namn kan du ta bort verifierings-TXT-eller MX-filen.

## <a name="common-verification-issues"></a>Vanliga problem med verifiering

Om Azure AD inte kan verifiera ett anpassat domännamn, kan du prova följande rekommendationer:

- **Vänta minst en timme och försök igen**. DNS-posterna måste spridas innan Azure AD kan verifiera domänen. Processen kan ta en timma eller mer.

- **Kontrollera att DNS-posten är korrekt.** Gå tillbaka till webbplatsen för domän namns registratorn. Kontrol lera att posten finns där och att den matchar DNS-postinformationen som tillhandahålls av Azure AD.

  Om du inte kan uppdatera posten på registrator-webbplatsen delar du posten med någon som har behörighet att lägga till posten och kontrollerar att den är korrekt.

- **Kontrollera att domännamnet inte redan används i en annan katalog.** Ett domän namn kan bara verifieras i en katalog. Om ditt domän namn för närvarande är verifierat i en annan katalog kan det inte också verifieras i den nya katalogen. Åtgärda problemet duplicering, måste du ta bort domännamnet från den gamla katalogen. Mer information om hur du tar bort domännamn finns [hantera egna domännamn](../users-groups-roles/domains-manage.md).

- **Kontrollera att du inte har någon ohanterade Power BI-klienter.** Om användarna har aktiverat Powerbi via självanmälan och skapat en ohanterad klient för din organisation, måste du vidta över hantering som interna eller externa-administratör med hjälp av PowerShell. Mer information finns i [Ta över en ohanterad katalog som administratör i Azure Active Directory](../users-groups-roles/domains-admin-takeover.md).

## <a name="next-steps"></a>Nästa steg

- Lägg till en annan Global administratör i din katalog. Mer information finns i [så här tilldelar du roller och administratörer](active-directory-users-assign-role-azure-portal.md).

- Lägg till användare till din domän. Mer information finns i [så här lägger du till eller tar bort användare](add-users-azure-active-directory.md).

- Hantera ditt domännamn i Azure AD. Mer information finns i [Hantera anpassade domän namn](../users-groups-roles/domains-manage.md).

- Om du har lokala versioner av Windows Server som du vill använda tillsammans med Azure Active Directory, se [integrerar dina lokala kataloger med Azure Active Directory](../connect/active-directory-aadconnect.md).
