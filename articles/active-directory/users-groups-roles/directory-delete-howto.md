---
title: Ta bort en Azure AD-katalog – Azure Active Directory | Microsoft Docs
description: Beskriver hur du förbereder Azure AD-katalog för borttagning, inklusive självbetjäning kataloger
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/15/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 91ac6b4530414850c52605bac8cb701aa2b877d4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60473198"
---
# <a name="delete-a-directory-in-azure-active-directory"></a>Ta bort en katalog i Azure Active Directory

När Azure AD-katalog tas bort så raderas även alla resurser som finns i katalogen. Förbereda din organisation genom att minimera dess kopplade resurser innan du tar bort. Endast en global administratör för Azure Active Directory (Azure AD) kan ta bort en Azure AD-katalog från portalen.

## <a name="prepare-the-directory"></a>Förbereda katalogen

Du kan inte ta bort en katalog i Azure AD tills den skickar flera kontroller. De här kontrollerna minska risken för att ta bort en Azure AD-katalog negativt påverkar användaråtkomst, till exempel möjligheten att logga in på Office 365 eller åtkomst till resurser i Azure. Till exempel om den katalog som är associerade med en prenumeration tas bort av misstag, sedan användarna inte komma åt Azure-resurser för den prenumerationen. Följande villkor kontrolleras:

* Det kan vara inga användare i katalogen förutom en global administratör som ska ta bort katalogen. Andra användare måste tas bort innan katalogen kan tas bort. Om användarna synkroniseras lokalt, och sedan synkronisera först måste inaktiveras och användarna måste tas bort i molnkatalogen med hjälp av Azure portal eller Azure PowerShell-cmdlets.
* Det får inte finnas några program i katalogen. Alla program måste tas bort innan katalogen kan tas bort.
* Det kan vara inga Multi-Factor authentication-leverantörer som är kopplade till katalogen.
* Det får inte finnas några prenumerationer för Microsoft Online Services, till exempel Microsoft Azure, Office 365 eller Azure AD Premium, som är kopplade till katalogen. Om en standardkatalog skapades för dig i Azure kan du inte ta bort den katalogen om din Azure-prenumeration fortfarande är beroende av den här katalogen för autentisering. På liknande sätt kan du inte ta bort en katalog om en annan användare har associerat en prenumeration med den.

## <a name="delete-the-directory"></a>Ta bort katalogen

1. Logga in på den [Azure AD administratörscenter](https://aad.portal.azure.com) med ett konto som är Global administratör för din organisation.

2. Välj **Azure Active Directory**.

3. Växla till den katalog som du vill ta bort.
  
   ![Bekräfta organisation innan du tar bort](./media/directory-delete-howto/delete-directory-command.png)

4. Välj **ta bort katalogen**.
  
   ![Välj kommandot för att ta bort organisationen](./media/directory-delete-howto/delete-directory-list.png)

5. Om din katalog inte klarar en eller flera kontroller, får du en länk till mer information om hur du skickar. När du skickar alla kontroller, väljer **ta bort** att slutföra processen.

## <a name="if-you-cant-delete-the-directory"></a>Om du inte ta bort katalogen

När du har konfigurerat din Azure AD-katalog kan har du också aktiverat licensbaserade prenumerationer för din organisation som Azure AD Premium P2, Office 365 Business Premium eller Enterprise Mobility + Security E5. Du kan inte ta bort en katalog förrän prenumerationerna som har tagits bort fullständigt för att undvika dataförluster. Prenumerationerna måste finnas i en **avetablerad** tillstånd och tillåter katalogen tas bort. En **har upphört att gälla** eller **avbruten** prenumerationen flyttas till den **inaktiverad** tillstånd och det sista steget är det **avetablerad** tillstånd.

Vad som händer när en Office 365-utvärderingsprenumeration upphör att gälla (inkluderar inte betald Partner/CSP, Enterprise-avtal eller Volume Licensing), finns i följande tabell. Mer information om Office 365 livscykeln för kvarhållning och prenumeration finns i [vad händer med mina data och åtkomst när Office 365 för företag-prenumerationen har upphört?](https://support.office.com/article/what-happens-to-my-data-and-access-when-my-office-365-for-business-subscription-ends-4436582f-211a-45ec-b72e-33647f97d8a3). 

Prenumerationens status | Data | Åtkomst till data
----- | ----- | -----
Aktiv (30 dagar för utvärderingsversion) | Data som är tillgängliga för alla | Användarna har normal åtkomst till Office 365-filer eller appar<br>Administratörer har normal åtkomst till Microsoft 365 Administrationscenter och resurser 
Har upphört att gälla (30 dagar) | Data som är tillgängliga för alla| Användarna har normal åtkomst till Office 365-filer eller appar<br>Administratörer har normal åtkomst till Microsoft 365 Administrationscenter och resurser
Inaktiverad (30 dagar) | Data som är tillgängliga för enbart administratör | Användare kan inte komma åt Office 365-filerna eller apparna<br>Administratörer kan få åtkomst till Microsoft 365-administrationscentret, men det går inte att tilldela licenser till eller uppdatera användare
Inaktiveringen (30 dagar efter inaktiverad) | Data som tas bort (bort automatiskt om inga andra tjänster används) | Användare kan inte komma åt Office 365-filerna eller apparna<br>Administratörer kan få åtkomst till Microsoft 365-administrationscentret för att köpa och hantera andra prenumerationer

## <a name="delete-a-subscription"></a>Ta bort en prenumeration

Du kan placera en prenumeration i avetablerad tillstånd som ska tas bort i tre dagar med hjälp av Administrationscenter för Microsoft 365.

1. Logga in på den [Microsoft 365 Administrationscenter](https://admin.microsoft.com) med ett konto som är en global administratör i din organisation. Om du vill ta bort katalogen ”Contoso” som har den initiala domänen contoso.onmicrosoft.com, logga in med ett UPN som admin@contoso.onmicrosoft.com.

2. Välj **fakturering** och välj **prenumerationer**, Välj den prenumeration som du vill avbryta. När du klickar på **Avbryt**, uppdatera sidan.
  
   ![Ta bort länken för att ta bort prenumeration](./media/directory-delete-howto/delete-command.png)
  
3. Välj **ta bort** att ta bort prenumerationen och acceptera de allmänna villkoren. Alla data raderas permanent inom tre dagar. Du kan återaktivera prenumerationen under tre dagar, om du ändrar dig.
  
   ![Läs villkoren noggrant](./media/directory-delete-howto/delete-terms.png)

4. Nu prenumerationens status har ändrats, har prenumerationen markerats för borttagning. Prenumerationen försätts den **avetablerad** tillstånd 72 timmar senare.

5. När du har tagit bort en prenumeration i din katalog och 72 timmar har gått ut kan du registrera ska tillbaka till Azure AD administratörscenter igen och det vara något krävs och inga prenumerationer som blockerar borttagningen din katalog. Du bör kunna togs bort Azure AD-katalogen.
  
   ![Skicka prenumeration kontroll på skärmen för borttagning](./media/directory-delete-howto/delete-checks-passed.png)

## <a name="i-have-a-trial-subscription-that-blocks-deletion"></a>Jag har en utvärderingsprenumeration som blockerar borttagningen

Det finns [självbetjänad registrering produkter](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide) som Microsoft Power BI, Rights Management Services, Microsoft Power Apps eller Dynamics 365 enskilda användare kan registrera sig via Office 365, som även skapar en gästanvändare för autentisering i Azure AD-katalogen. Produkterna självbetjäning blockera directory borttagningar tills de tas bort helt från katalogen för att undvika dataförlust. De kan tas bort bara av Azure AD-administratören om användaren har registrerat sig individuellt eller har tilldelats produkten.

Det finns två typer av självbetjäning registrering produkter i hur de är tilldelade: 

* Tilldelning av org-nivå: En Azure AD-administratören tilldelar produkten för hela organisationen och en användare kan aktivt med hjälp av tjänsten med denna organisationsnivå tilldelning även om de inte är licensierad separat.
* Tilldelning av användaren: En enskild användare under självanmälan i stort sett tilldelar produkten till själva utan att en administratör. När organisationen hanteras av en administratör (se [administratör övertagande av en ohanterad katalog](domains-admin-takeover.md), kan administratören tilldela produkten direkt till användare utan registrering via självbetjäning.  

När du påbörjar borttagningen av självbetjäning registrering produkten tas data bort permanent i åtgärden och tar bort alla användaråtkomst till tjänsten. Alla användare som har tilldelats erbjudandet individuellt eller på organisationsnivå blockeras sedan logga in eller komma åt befintliga data. Om du vill förhindra förlust av data med självbetjäning registrering produkten som [Microsoft Power BI-instrumentpaneler](https://docs.microsoft.com/power-bi/service-export-to-pbix) eller [Rights Management Services principkonfigurationen](https://docs.microsoft.com/azure/information-protection/configure-policy#how-to-configure-the-azure-information-protection-policy), se till att data säkerhetskopieras och spara någon annanstans.

Mer information om tillgängliga självbetjäning registrering produkter och tjänster finns i [tillgängliga program med självbetjäning](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide#available-self-service-programs).

Vad som händer när en Office 365-utvärderingsprenumeration upphör att gälla (inkluderar inte betald Partner/CSP, Enterprise-avtal eller Volume Licensing), finns i följande tabell. Mer information om Office 365 livscykeln för kvarhållning och prenumeration finns i [vad händer med mina data och åtkomst när Office 365 för företag-prenumerationen har upphört?](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/what-if-my-subscription-expires?view=o365-worldwide).

Produkttillstånd | Data | Åtkomst till data
------------- | ---- | --------------
Aktiv (30 dagar för utvärderingsversion) | Data som är tillgängliga för alla | Användare har normal åtkomst till självbetjänad registrering produkten, filer eller appar<br>Administratörer har normal åtkomst till Microsoft 365 Administrationscenter och resurser
Borttagen | Data som tas bort | Användare kan inte komma åt självbetjänad registrering produkten, filer eller appar<br>Administratörer kan få åtkomst till Microsoft 365-administrationscentret för att köpa och hantera andra prenumerationer

## <a name="how-can-i-delete-a-self-service-sign-up-product-in-the-azure-portal"></a>Hur kan jag ta bort en självbetjäning registrering produkt i Azure-portalen?

Du kan placera en självbetjäning registrering produkt som Microsoft Power BI eller Azure Rights Management Services i en **ta bort** tillstånd ska raderas omedelbart i Azure AD-portalen.

1. Logga in på den [Azure AD administratörscenter](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) med ett konto som är en Global administratör i organisationen. Om du vill ta bort katalogen ”Contoso” som har den initiala domänen contoso.onmicrosoft.com, logga in med ett UPN som admin@contoso.onmicrosoft.com.

2. Välj **licenser**, och välj sedan **självbetjänad registrering produkter**. Du kan se alla självbetjänad registrering produkter separat från klientbaserade prenumerationer. Välj den produkt som du vill ta bort permanent. Här är ett exempel i Microsoft Power BI:

    ![användarnamnet är felskrivet eller hittades inte](./media/directory-delete-howto/licenses-page.png)

3. Välj **ta bort** att ta bort produkten och accepterar villkoren data tas bort omedelbart och oåterkallerligen. Den här åtgärden ta bort tas bort alla användare och ta bort organisation tillgång till produkten. Klicka på Ja om du vill gå vidare med borttagningen.  

    ![användarnamnet är felskrivet eller hittades inte](./media/directory-delete-howto/delete-product.png)

4. När du väljer **Ja**, borttagning av självbetjäning produkten kommer att initieras. Det finns ett meddelande som informerar dig om borttagningen pågår.  

    ![användarnamnet är felskrivet eller hittades inte](./media/directory-delete-howto/progress-message.png)

5. Nu självbetjänad registrering Produkttillstånd har ändrats till **borttagna**. När du uppdaterar sidan produkten ska tas bort från den **självbetjänad registrering produkter** sidan.  

    ![användarnamnet är felskrivet eller hittades inte](./media/directory-delete-howto/product-deleted.png)

6. När du har tagit bort alla produkter logga tillbaka till Azure AD administratörscenter igen och det bör finnas något krävs och inga produkter som blockerar borttagningen din katalog. Du bör kunna togs bort Azure AD-katalogen.

    ![användarnamnet är felskrivet eller hittades inte](./media/directory-delete-howto/delete-organization.png)

## <a name="next-steps"></a>Nästa steg

[Dokumentation om Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)
