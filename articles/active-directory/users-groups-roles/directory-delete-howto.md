---
title: Ta bort en Azure AD-katalog - Azure Active Directory | Microsoft-dokument
description: Förklarar hur du förbereder en Azure AD-katalog för borttagning, inklusive självbetjäningskataloger
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
ms.reviewer: addimitu
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 47a60ed44ddf057ef983f8f76f23fd784bc3efd5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73961824"
---
# <a name="delete-a-directory-in-azure-active-directory"></a>Ta bort en katalog i Azure Active Directory

När en Azure AD-katalog tas bort tas även alla resurser som finns i katalogen bort. Förbered din organisation genom att minimera de associerade resurserna innan du tar bort dem. Endast en global Azure Active Directory-administratör (Azure AD) kan ta bort en Azure AD-katalog från portalen.

## <a name="prepare-the-directory"></a>Förbereda katalogen

Du kan inte ta bort en katalog i Azure AD förrän den har godkänts flera kontroller. Dessa kontroller minskar risken för att ta bort en Azure AD-katalog negativt påverkar användaråtkomst, till exempel möjligheten att logga in på Office 365 eller komma åt resurser i Azure. Om katalogen som är associerad med en prenumeration oavsiktligt tas bort, kan användarna inte komma åt Azure-resurserna för den prenumerationen. Följande villkor kontrolleras:

* Det kan inte finnas några användare i katalogen förutom en global administratör som ska ta bort katalogen. Andra användare måste tas bort innan katalogen kan tas bort. Om användare synkroniseras från lokala måste synkronisering först inaktiveras och användarna måste tas bort i molnkatalogen med azure-portalen eller Azure PowerShell-cmdlets.
* Det får inte finnas några program i katalogen. Alla program måste tas bort innan katalogen kan tas bort.
* Det kan inte finnas några multifaktorautentiseringsleverantörer som är länkade till katalogen.
* Det får inte finnas några prenumerationer för Microsoft Online Services, till exempel Microsoft Azure, Office 365 eller Azure AD Premium, som är kopplade till katalogen. Om en standardkatalog skapades för dig i Azure kan du inte ta bort den katalogen om din Azure-prenumeration fortfarande är beroende av den här katalogen för autentisering. På liknande sätt kan du inte ta bort en katalog om en annan användare har associerat en prenumeration med den.

## <a name="delete-the-directory"></a>Ta bort katalogen

1. Logga in på [Azure AD-administrationscentret](https://aad.portal.azure.com) med ett konto som är global administratör för din organisation.

2. Välj **Azure Active Directory**.

3. Växla till den katalog som du vill ta bort.
  
   ![Bekräfta organisation innan du tar bort](./media/directory-delete-howto/delete-directory-command.png)

4. Välj **Ta bort katalog**.
  
   ![markera kommandot för att ta bort organisationen](./media/directory-delete-howto/delete-directory-list.png)

5. Om katalogen inte klarar en eller flera kontroller får du en länk till mer information om hur du skickar. När du har klarat alla kontroller väljer du **Ta bort** för att slutföra processen.

## <a name="if-you-cant-delete-the-directory"></a>Om du inte kan ta bort katalogen

När du har konfigurerat din Azure AD-katalog kan du också ha aktiverat licensbaserade prenumerationer för din organisation som Azure AD Premium P2, Office 365 Business Premium eller Enterprise Mobility + Security E5. För att undvika oavsiktlig dataförlust kan du inte ta bort en katalog förrän prenumerationerna är helt borttagna. Prenumerationerna måste vara i ett **avetablerat** tillstånd för att tillåta katalogborttagning. En **utgången** eller **avbruten** prenumeration flyttas till tillståndet **Inaktiverat** och det sista steget är tillståndet **Avetablerat.**

Vad du kan förvänta dig när en utvärderingsversion av Office 365-prenumerationen upphör att gälla (exklusive betald partner/CSP, Enterprise-avtal eller volymlicensiering) finns i följande tabell. Mer information om livscykeln för lagring och prenumeration i Office 365 finns i Vad händer med [mina data och åtkomst när min Office 365 för företag-prenumeration avslutas?](https://support.office.com/article/what-happens-to-my-data-and-access-when-my-office-365-for-business-subscription-ends-4436582f-211a-45ec-b72e-33647f97d8a3). 

Tillstånd för prenumeration | Data | Tillgång till data
----- | ----- | -----
Aktiv (30 dagar för rättegång) | Data som är tillgängliga för alla | Användare har normal åtkomst till Office 365-filer eller -appar<br>Administratörer har normal åtkomst till Microsoft 365 administrationscenter och resurser 
Utgångna (30 dagar) | Data som är tillgängliga för alla| Användare har normal åtkomst till Office 365-filer eller -appar<br>Administratörer har normal åtkomst till Microsoft 365 administrationscenter och resurser
Inaktiverad (30 dagar) | Data som endast är tillgängliga för administratörer | Användare kan inte komma åt Office 365-filer eller -appar<br>Administratörer kan komma åt Microsoft 365-administrationscentret men kan inte tilldela licenser till eller uppdatera användare
Avetableras (30 dagar efter funktionshindrade) | Data raderas (raderas automatiskt om inga andra tjänster används) | Användare kan inte komma åt Office 365-filer eller -appar<br>Administratörer kan komma åt Microsoft 365-administrationscentret för att köpa och hantera andra prenumerationer

## <a name="delete-a-subscription"></a>Ta bort en prenumeration

Du kan placera en prenumeration i det **avetablerade** tillståndet som ska tas bort inom tre dagar med hjälp av administrationscentret för Microsoft 365.

1. Logga in på [Microsoft 365-administrationscentret](https://admin.microsoft.com) med ett konto som är en global administratör i organisationen. Om du försöker ta bort katalogen "Contoso" som har den ursprungliga standarddomänen admin@contoso.onmicrosoft.comcontoso.onmicrosoft.com loggar du in med ett UPN som .

2. Förhandsgranska det nya administrationscentret för Microsoft 365 genom att se till att **växlingsknappen Prova det nya administrationscentret** är aktiverat.

   ![Förhandsgranska den nya M365-administratörscentret](./media/directory-delete-howto/preview-toggle.png)

3. När det nya administrationscentret är aktiverat måste du avbryta en prenumeration innan du kan ta bort den. Välj **Fakturering** och välj **Produkter & tjänster**och välj sedan Avbryt **prenumeration** för den prenumeration som du vill avbryta. Du kommer att föras till en feedbacksida.

   ![Välj en prenumeration som ska avbrytas](./media/directory-delete-howto/cancel-choose-subscription.png)

4. Fyll i feedbackformuläret och välj **Avbryt prenumeration** för att avbryta prenumerationen.

   ![Kommandot Avbryt i förhandsversionen av prenumerationen](./media/directory-delete-howto/cancel-command.png)

5. Nu kan du ta bort prenumerationen. Välj **Ta bort** för den prenumeration som du vill ta bort. Om du inte hittar prenumerationen på sidan **Produkter & tjänster** kontrollerar du att **prenumerationsstatusen** är inställd på **Alla**.

   ![Ta bort länk för att ta bort prenumeration](./media/directory-delete-howto/delete-command.png)

6. Välj **Ta bort prenumeration** för att ta bort prenumerationen och acceptera villkoren. Alla data raderas permanent inom tre dagar. Du kan [återaktivera prenumerationen](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/reactivate-your-subscription?view=o365-worldwide) under tredagarsperioden om du ändrar dig.
  
   ![noggrant läsa igenom villkor](./media/directory-delete-howto/delete-terms.png)

7. Nu har prenumerationstillståndet ändrats och prenumerationen har markerats för borttagning. Prenumerationen går in i **avetablerat** tillstånd 72 timmar senare.

8. När du har tagit bort en prenumeration i din katalog och 72 timmar har förflutit kan du logga in på Azure AD-administrationscentret igen och det bör inte finnas någon åtgärd som krävs och inga prenumerationer som blockerar din katalogborttagning. Du bör kunna ta bort din Azure AD-katalog.
  
   ![passera prenumerationskontroll vid raderingsskärmen](./media/directory-delete-howto/delete-checks-passed.png)

## <a name="i-have-a-trial-subscription-that-blocks-deletion"></a>Jag har en utvärderingsprenumeration som blockerar radering

Det finns [självbetjäningsannonsprodukter](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide) som Microsoft Power BI, Rights Management Services, Microsoft Power Apps eller Dynamics 365, enskilda användare kan registrera sig via Office 365, vilket också skapar en gästanvändare för autentisering i din Azure AD-katalog. Dessa självbetjäningsprodukter blockerar katalogborttagningar tills de tas bort helt från katalogen för att undvika dataförlust. De kan endast tas bort av Azure AD-administratören oavsett om användaren har registrerat sig individuellt eller tilldelats produkten.

Det finns två typer av självbetjäningsanmälningsprodukter i hur de tilldelas: 

* Tilldelning på organisationsnivå: En Azure AD-administratör tilldelar produkten till hela organisationen och en användare kan aktivt använda tjänsten med den här tilldelningen på organisationsnivå även om de inte är licensierade individuellt.
* Tilldelning på användarnivå: En enskild användare under självbetjäningsanmälan tilldelar i huvudsak produkten till sig själva utan administratör. När organisationen blir hanterad av en administratör (se [Administratörens övertagande av en ohanterad katalog](domains-admin-takeover.md)kan administratören direkt tilldela produkten till användare utan självbetjäningsanskadeanmälning.  

När du påbörjar borttagningen av självbetjäningsanmälningsprodukten tar åtgärden bort data permanent och tar bort all användaråtkomst till tjänsten. Alla användare som har tilldelats erbjudandet individuellt eller på organisationsnivå blockeras sedan från att logga in eller komma åt befintliga data. Om du vill förhindra dataförlust med självbetjäningsansskadeprodukten som [Microsoft Power BI-instrumentpaneler](https://docs.microsoft.com/power-bi/service-export-to-pbix) eller [Rights Management Services-principkonfiguration](https://docs.microsoft.com/azure/information-protection/configure-policy#how-to-configure-the-azure-information-protection-policy)kontrollerar du att data säkerhetskopieras och sparas någon annanstans.

Mer information om tillgängliga självbetjäningsprodukter och -tjänster finns i [Tillgängliga självbetjäningsprogram](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide#available-self-service-programs).

Vad du kan förvänta dig när en utvärderingsversion av Office 365-prenumerationen upphör att gälla (exklusive betald partner/CSP, Enterprise-avtal eller volymlicensiering) finns i följande tabell. Mer information om livscykeln för lagring och prenumeration i Office 365 finns i Vad händer med [mina data och åtkomst när min Office 365 för företag-prenumeration avslutas?](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/what-if-my-subscription-expires?view=o365-worldwide).

Produkttillstånd | Data | Tillgång till data
------------- | ---- | --------------
Aktiv (30 dagar för rättegång) | Data som är tillgängliga för alla | Användare har normal åtkomst till självbetjäningsanmälningsprodukt, filer eller appar<br>Administratörer har normal åtkomst till Microsoft 365 administrationscenter och resurser
Borttagen | Data har raderats | Användare kan inte komma åt självbetjäningsanmälningsprodukt, filer eller appar<br>Administratörer kan komma åt Microsoft 365-administrationscentret för att köpa och hantera andra prenumerationer

## <a name="how-can-i-delete-a-self-service-sign-up-product-in-the-azure-portal"></a>Hur tar jag bort en självbetjäningsregistreringsprodukt i Azure-portalen?

Du kan placera en självbetjäningsregistreringsprodukt som Microsoft Power BI eller Azure Rights Management Services i ett **borttagningsläge** som omedelbart tas bort i Azure AD-portalen.

1. Logga in på [Azure AD-administrationscentret](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) med ett konto som är global administratör i organisationen. Om du försöker ta bort katalogen "Contoso" som har den ursprungliga standarddomänen admin@contoso.onmicrosoft.comcontoso.onmicrosoft.com loggar du in med ett UPN som .

2. Välj **Licenser**och välj sedan **självbetjäningsanmälningsprodukter**. Du kan se alla självbetjäningsupp registreringsprodukter separat från de platsbaserade prenumerationerna. Välj den produkt som du vill ta bort permanent. Här är ett exempel i Microsoft Power BI:

    ![användarnamnet är felskriven eller hittades inte](./media/directory-delete-howto/licenses-page.png)

3. Välj **Ta bort** om du vill ta bort produkten och acceptera villkoren för att data tas bort omedelbart och oåterkalleligt. Den här borttagningsåtgärden tar bort alla användare och tar bort organisationens åtkomst till produkten. Klicka på Ja om du vill gå vidare med borttagningen.  

    ![användarnamnet är felskriven eller hittades inte](./media/directory-delete-howto/delete-product.png)

4. När du väljer **Ja**initieras borttagningen av självbetjäningsprodukten. Det finns ett meddelande som kommer att berätta om borttagning pågår.  

    ![användarnamnet är felskriven eller hittades inte](./media/directory-delete-howto/progress-message.png)

5. Nu har självbetjäningstillståndet för registreringsprodukt ändrats till **Borttaget**. När du uppdaterar sidan ska produkten tas bort från sidan **Självbetjäningsprodukter.**  

    ![användarnamnet är felskriven eller hittades inte](./media/directory-delete-howto/product-deleted.png)

6. När du har tagit bort alla produkter kan du logga in på Azure AD-administrationscentret igen och det bör inte finnas någon åtgärd som krävs och inga produkter som blockerar din katalogborttagning. Du bör kunna ta bort din Azure AD-katalog.

    ![användarnamnet är felskriven eller hittades inte](./media/directory-delete-howto/delete-organization.png)

## <a name="next-steps"></a>Nästa steg

[Azure Active Directory-dokumentation](https://docs.microsoft.com/azure/active-directory/)
