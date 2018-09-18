---
title: Hur du lägger till din anpassade domän till Azure Active Directory | Microsoft Docs
description: Lär dig hur du lägger till en anpassad domän med hjälp av Azure Active Directory-portalen.
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: lizross
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.openlocfilehash: b33f2e809ae5758e41f7a76680347b9487f3f461
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2018
ms.locfileid: "45735350"
---
# <a name="how-to-add-your-custom-domain-name-using-the-azure-active-directory-portal"></a>Så här: Lägg till ditt domännamn med hjälp av Azure Active Directory-portalen
Varje ny Azure AD-klienten levereras med ett första domännamn *domainname*. onmicrosoft.com. Du kan inte ändra eller ta bort det ursprungliga domännamnet, men du kan lägga till din organisations namn i listan. Att lägga till anpassade domännamn som hjälper dig att skapa användarnamn som dina användare känner igen som _alain@contoso.com_.

>[!Note]
>Du måste upprepa hela processen, från början till slut för var och en av dina egna domännamn.

## <a name="add-a-custom-domain-name"></a>Lägg till ett anpassat domännamn
Först måste du lägga till ett anpassat domännamn till Azure AD-klient.

### <a name="to-add-a-custom-domain-name"></a>Att lägga till ett anpassat domännamn
1. Logga in på den [Azure AD-portalen](https://portal.azure.com/) med ett konto som Global administratör för katalogen.

2. Välj **Azure Active Directory**väljer **anpassade domännamn**, och välj sedan **Lägg till anpassad domän**.

    ![Fabrikam - anpassad domän namn på bladet med Lägg till anpassad domänalternativet är markerat](media/add-custom-domain/add-custom-domain.png)

3. Skriv ny företagets domännamn till den **anpassade domännamn** box (till exempel _contoso.com_), och välj sedan **Lägg till domän**.

    >[!Important]
    >Du måste ta med .com, .net eller annat toppnivåtillägg för detta ska fungera korrekt.

    ![Fabrikam - anpassad domän namn på bladet med Lägg till domän knappen markerad](media/add-custom-domain/add-custom-domain-blade.png)

4. Kopiera DNS-informationen från den **Contoso** bladet.

    ![Contoso-bladet med information om DNS-post](media/add-custom-domain/contoso-blade-with-dns-info.png)

## <a name="add-your-domain-name-with-a-domain-name-registrar"></a>Lägg till ditt domännamn med en domännamnsregistrator
Därefter måste du uppdatera DNS-zonfilen för din nya anpassade domän. Du kan använda [DNS-zoner](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) för din Azure, Office 365, eller externa DNS-poster, eller du kan lägga till din nya DNS-post med hjälp av en annan DNS-registrator (till exempel [InterNIC](https://go.microsoft.com/fwlink/p/?LinkId=402770)).

### <a name="to-add-your-domain-name"></a>Att lägga till ditt domännamn 
1. Logga in på domännamnsregistratorn för domänen. Om du inte har rätt behörighet för att uppdatera ditt bidrag, kommer du behöva kontakta någon med dessa behörigheter.

2. När DNS-posten har uppdaterats med Registratorn måste du uppdatera DNS-zonfilen med den information som tillhandahålls av Azure AD.

    >[!Note]
    >DNS-posten ändrar hur din e-postdirigering eller webbhosting fungerar.

## <a name="verify-your-custom-domain-name"></a>Kontrollera ditt domännamn
När du har registrerat ditt eget domännamn, kan det ta några sekunder till några timmar innan DNS-information sprids till där Azure AD kan se det som giltig.

### <a name="to-verify-your-custom-domain-name"></a>Verifiera ditt domännamn
1. Logga in på den [Azure AD-portalen](https://portal.azure.com/) med ett konto som Global administratör för katalogen.

2. Välj **Azure Active Directory**, och välj sedan **anpassade domännamn**.

3. På den **Fabrikam - anpassade domännamn** bladet Välj det anpassade domännamnet **Contoso**.

    ![Fabrikam - anpassad domän namn på bladet med contoso markerat](media/add-custom-domain/custom-blade-with-contoso-highlighted.png)

4. På den **Contoso** bladet väljer **Kontrollera** så att den anpassade domänen har registrerats korrekt och är giltig för Azure AD.

    ![Contoso-bladet med information om DNS-post och knappen verifiera](media/add-custom-domain/contoso-blade-with-dns-info-verify.png)

### <a name="common-verification-issues"></a>Vanliga problem med verifiering
Om Azure AD inte kan verifiera ett anpassat domännamn, kan du prova följande rekommendationer:
- **Vänta minst en timme och försök igen**. DNS-posterna måste spridas innan Azure AD kan verifiera domänen och den här processen kan ta en timme eller mer.

- **Kontrollera att DNS-posten är korrekt.** Gå tillbaka till webbplatsen domain name Registratorn och kontrollera att posten finns och att den matchar DSN informationen tillhandahålls av Azure AD.

    Om du inte kan uppdatera posten på webbplatsen registrator, måste du dela posten med någon som har rätt behörighet för att lägga till posten och kontrollera att den är korrekt.

- **Kontrollera att domännamnet inte redan är i en annan katalog.** Ett domännamn kan bara verifieras i en katalog, vilket innebär att om ditt domännamn är för närvarande verifierat i en annan katalog, den inte kan också verifieras på den nya katalogen. Åtgärda problemet duplicering, måste du ta bort domännamnet från den gamla katalogen. Mer information om hur du tar bort domännamn finns [hantera egna domännamn](../users-groups-roles/domains-manage.md).    

## <a name="next-steps"></a>Nästa steg
- Lägga till användare i din domän, se [hantera egna domännamn](../users-groups-roles/domains-manage.md).

- Om du har lokala versioner av Windows Server som du vill använda tillsammans med Azure Active Directory, se [integrerar dina lokala kataloger med Azure Active Directory](../connect/active-directory-aadconnect.md).