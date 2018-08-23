---
title: Ta bort en katalog för Azure Active Directory-klient | Microsoft Docs
description: Beskriver hur du förbereder Azure AD-klient-katalog för borttagning
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 06/13/2018
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.openlocfilehash: b1d3439412e324c71687c43aa9e47c520cb72262
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2018
ms.locfileid: "42057379"
---
# <a name="delete-an-azure-active-directory-tenant"></a>Ta bort en Azure Active Directory-klient
När en klient tas bort så raderas även alla resurser som finns i klienten. Du måste förbereda klienten genom att minimera dess kopplade resurser innan du tar bort. Endast en global administratör för Azure Active Directory (Azure AD) kan ta bort en Azure AD-klient från portalen.

## <a name="prepare-the-tenant-for-deletion"></a>Förbereda klienten för borttagning

Du kan inte ta bort en klient i Azure AD tills den skickar flera kontroller. De här kontrollerna minska risken för att ta bort en klient negativt påverkar användaråtkomst, till exempel möjligheten att logga in på Office 365 eller åtkomst till resurser i Azure. Till exempel om innehavaren som associeras med en prenumeration tas bort av misstag, sedan användarna inte komma åt Azure-resurser för den prenumerationen. Nedan beskrivs de villkor som är markerade:

* Det kan vara inga användare i klienten utom en global administratör som ska ta bort klienten. Andra användare måste tas bort innan klienten kan tas bort. Om användarna synkroniseras lokalt måste sedan synkronisering måste vara avstängd och användarna måste tas bort i molnet klienten med hjälp av Azure portal eller Azure PowerShell-cmdlets. 
* Det kan finnas några program i klienten. Alla program måste tas bort innan klienten kan tas bort.
* Det kan vara inga Multi-Factor authentication-providers länkad till klienten.
* Det kan finnas några prenumerationer för Microsoft Online Services, till exempel Microsoft Azure, Office 365 eller Azure AD Premium kopplade till klienten. Till exempel om en standard-klient har skapats för dig i Azure kan du inte ta bort den här klienten om din Azure-prenumeration fortfarande är beroende av på den här klienten för autentisering. På samma sätt kan du ta bort en klient om en annan användare har associerat en prenumeration med den. 

## <a name="delete-an-azure-ad-tenant"></a>Ta bort en Azure AD-klient

1. Logga in på den [Azure AD administratörscenter](https://aad.portal.azure.com) med ett konto som är globala nytt för klienten.

2. Välj **Azure Active Directory**.

3. Växla till klienten som du vill ta bort.
  
  ![ta bort katalog](./media/directory-delete-howto/delete-directory-command.png)

4. Välj **ta bort katalogen**.
  
  ![ta bort katalog](./media/directory-delete-howto/delete-directory-list.png)

5. Om din klient inte skickar en eller flera kontroller, får du en länk till mer information om hur du skickar. När du skickar alla kontroller, väljer **ta bort** att slutföra processen.

## <a name="i-have-an-expired-subscription-but-i-cant-delete-the-tenant"></a>Jag har en prenumeration som har upphört att gälla men jag kan inte ta bort klienten

När du har konfigurerat din Azure Active Directory-klient kan har du också aktiverat licensbaserade prenumerationer för din organisation som Azure Active Directory Premium P2, Office 365 Business Premium eller Enterprise Mobility + Security E5. De här prenumerationerna blockera directory borttagning tills de är helt bort för att undvika dataförluster. Prenumerationerna måste finnas i en **avetablerad** tillstånd att ta bort klienter. En **har upphört att gälla** eller **avbruten** prenumerationen flyttas till den **inaktiverad** tillstånd och det sista steget är det **Deprovisoned** tillstånd. 

Vad som händer när en Office 365-utvärderingsprenumeration upphör att gälla (inkluderar inte betald Partner/CSP, Enterprise-avtal eller Volume Licensing), finns i följande tabell. Mer information om Office 365 livscykeln för kvarhållning och prenumeration finns i [vad händer med mina data och åtkomst när Office 365 för företag-prenumerationen har upphört?](https://support.office.com/article/what-happens-to-my-data-and-access-when-my-office-365-for-business-subscription-ends-4436582f-211a-45ec-b72e-33647f97d8a3). 

Prenumerationens status | Data | Åtkomst till data
----- | ----- | -----
Aktiv (30 dagar för utvärderingsversion)  | Data som är tillgängliga för alla    | <li>Användarna har normal åtkomst till Office 365-filer eller appar<li>Administratörer har normal åtkomst till Office 365 Administrationscenter och resurser 
Har upphört att gälla (30 dagar)   | Data som är tillgängliga för alla    | <li>Användarna har normal åtkomst till Office 365-filer eller appar<li>Administratörer har normal åtkomst till Office 365 Administrationscenter och resurser
Inaktiverad (30 dagar) | Data som är tillgängliga för enbart administratör  | <li>Användare kan inte komma åt Office 365-filerna eller apparna<li>Administratörer kan få åtkomst till administrationscentret för Office 365, men det går inte att tilldela licenser till eller uppdatera användare
Inaktiveringen (30 dagar efter inaktiverad) | Data som tas bort (bort automatiskt om inga andra tjänster används) | <li>Användare kan inte komma åt Office 365-filerna eller apparna<li>Administratörer kan komma åt Office 365 Administrationscenter för att köpa och hantera andra prenumerationer 

Du kan placera en prenumeration till en **Deprovisoned** tillstånd och kan inte tas bort inom 3 dagar med Microsoft Store för företag-administrationscentret. Den här funktionen kommer snart att Office 365 Administrationscenter.

1. Logga in på den [Microsoft Store för företag-administrationscentret](https://businessstore.microsoft.com/manage/) med ett konto som är en Global administratör i klienten. Om du vill ta bort ”Contoso”-klient som har den initiala domänen contoso.onmicrosoft.com, logga in med ett UPN som admin@contoso.onmicrosoft.com.

2. Gå till den **hantera** fliken och markera **produkter och tjänster**, Välj den prenumeration som du vill avbryta. När du klickar på **Avbryt**, uppdatera sidan.
  
  ![Ta bort länken för att ta bort prenumeration](./media/directory-delete-howto/delete-command.png)
  
3. Välj **ta bort** att ta bort prenumerationen och acceptera de allmänna villkoren. Alla data tas bort permanent inom tre dagar. Du kan återaktivera prenumerationen under tre dagar, om du ändrar dig.
  
  ![allmänna villkor](./media/directory-delete-howto/delete-terms.png)

4. Nu prenumerationens status har ändrats, har prenumerationen markerats för borttagning. Prenumeration eneters den **avetablerad** tillstånd 72 timmar senare.

5. När du har tagit bort en prenumeration på din klient och 72 timmar har gått ut kan du registrera ska tillbaka till Azure AD administratörscenter igen och det vara något krävs och inga prenumerationer som blockerar borttagningen din klient. Du bör kunna togs bort Azure AD-klienten.
  
  ![Skicka prenumeration kontroll på skärmen för borttagning](./media/directory-delete-howto/delete-checks-passed.png)

## <a name="next-steps"></a>Nästa steg
[Dokumentation om Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)
