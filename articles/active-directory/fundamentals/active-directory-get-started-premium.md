---
title: Registrera dig för Azure AD Premium | Microsoft Docs
description: Här förklaras hur du registrerar dig för Azure Active Directory Premium-versionen
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: quickstart
ms.date: 09/07/2017
ms.author: lizross
ms.reviewer: piotrci
ms.custom: it-pro;
ms.openlocfilehash: c15cbb632410eb0b6867677d7802960033dfdd44
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36268390"
---
# <a name="quickstart-sign-up-for-azure-active-directory-premium"></a>Snabbstart: Registrera dig för Azure Active Directory Premium
Du kommer igång med Azure Active Directory (Azure AD) Premium genom att först köpa licenser och koppla dem till din Azure-prenumeration. Om du skapar en ny Azure-prenumeration måste du även aktivera din licensieringsplan och åtkomst till Azure AD-tjänsten enligt beskrivningen i följande avsnitt. 

## <a name="sign-up-for-active-directory-premium"></a>Registrera dig för Active Directory Premium
Om du vill registrera dig för Active Directory Premium kan du välja mellan flera alternativ: 
* Använda din Azure- eller Office 365-prenumeration
* Använda en Enterprise Mobility + Security-licensieringsplan
* Använda en Microsoft-volymlicensieringsplan

### <a name="azure-or-office-365"></a>Azure eller Office 365 
Som prenumerant på Azure eller Office 365 kan du köpa Azure Active Directory Premium online. 

Detaljerade anvisningar finns i [Köpa Azure Active Directory Premium – befintliga kunder](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/How-to-Purchase-Azure-Active-Directory-Premium-Existing-Customer) eller [Köpa Azure Active Directory Premium – nya kunder](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/How-to-Purchase-Azure-Active-Directory-Premium-New-Customers).  

### <a name="enterprise-mobility--security"></a>Enterprise Mobility + Security
Enterprise Mobility + Security (EMS) är ett kostnadseffektivt sätt för organisationer att använda följande tjänster tillsammans i samma licensieringsplan: Azure Active Directory Premium, Azure Information Protection och Microsoft Intune. Läs mer om EMS på webbplatsen för [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security) och mer om vilka EMS-licenstyper som finns att köpa på sidan [Priser på Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-pricing).  

Kom igång med Azure AD via EMS-licenser med något av följande licensieringsalternativ:

- Testa EMS med en kostnadsfri [Enterprise Mobility + Security E5-utvärderingsversion](https://signup.microsoft.com/Signup?OfferId=87dd2714-d452-48a0-a809-d2f58c4f68b7&ali=1)
- Köp [Enterprise Mobility + Security E5-licenser](https://signup.microsoft.com/Signup?OfferId=e6de2192-536a-4dc3-afdc-9e2602b6c790&ali=1)
- Köp [Enterprise Mobility + Security E3-licenser](https://signup.microsoft.com/Signup?OfferId=4BBA281F-95E8-4136-8B0F-037D6062F54C&ali=1)

### <a name="microsoft-volume-licensing"></a>Microsoft-volymlicensiering
Azure Active Directory Premium är tillgängligt genom ett [Microsoft Enterprise-avtal](https://www.microsoft.com/en-us/licensing/licensing-programs/enterprise.aspx) (250 eller fler licenser) eller genom [Open Volume-licensprogrammet](https://www.microsoft.com/en-us/licensing/licensing-programs/open-license.aspx) (5–250 licenser).

Läs mer om att alternativ för köp via volymlicensiering på sidan [How to purchase through Volume Licensing](https://www.microsoft.com/en-us/Licensing/how-to-buy/how-to-buy.aspx) (Så här köper du via volymlicensieringsprogram).

> [!NOTE]
> Azure Active Directory Premium och Basic är tillgängliga för kunder i Kina genom den globala instansen av Azure Active Directory. Azure Active Directory Premium och Basic stöds inte för närvarande i Microsoft Azure-tjänsten som drivs av 21Vianet i Kina. Om du vill ha mer information kontaktar du oss via [Azure Active Directory-forumet](https://feedback.azure.com/forums/169401-azure-active-directory/).

Om du tidigare har köpt och aktiverat Azure AD-licenser för samma Azure-prenumeration som du använde i föregående steg, aktiveras licenserna automatiskt i samma katalog. Annars fortsätter du med stegen som beskrivs i resten av den här artikeln.

## <a name="activate-your-license-plan"></a>Aktivera din licensplan
Är det här den första Azure AD-licensplan som du köpt från Microsoft? I så fall skickas ett bekräftelsemejl till dig när köpet har slutförts. Du behöver det här e-postmeddelandet för att kunna aktivera din första licensplan.

**Du aktiverar din licensplan genom att utföra något av följande steg:**

1. Starta aktiveringen genom att klicka på **Logga In** eller **Registrera dig**.
   
    ![Logga in](media/active-directory-get-started-premium/MOLSEmail.png)

    - Om du har en befintlig klient klickar du på **Logga in** för att logga in med ditt befintliga administratörskonto. Logga in med autentiseringsuppgifterna för global administratör eller klienten där licenserna ska aktiveras.

    - Om du vill skapa en ny Azure AD-klientorganisation och använda den med licensplanen klickar du på **Registrera** så att dialogrutan **Skapa kontoprofil** öppnas.

        ![Skapa kontoprofil](media/active-directory-get-started-premium/MOLSAccountProfile.png)

När du är klar visas följande dialogruta som en bekräftelse på aktiveringen av licensplanen för klientorganisationen:

![Bekräftelse](media/active-directory-get-started-premium/MOLSThankYou.png)

## <a name="activate-your-azure-active-directory-access"></a>Aktivera din åtkomst till Azure Active Directory
Om du lägger till nya Azure AD Premium-licenser i en befintlig prenumeration, är din Azure AD-åtkomst förmodligen redan aktiverad. Annars måste du aktivera Azure AD-åtkomst när du har fått **välkomstmeddelandet**.  

När de köpta licenserna har installerats i din katalog skickas ett **välkomstmejl** till dig. E-postmeddelandet bekräftar att du kan börja hantera dina licenser och funktioner för Azure Active Directory Premium eller Enterprise Mobility + Security. 

> [!TIP]
> Du har inte åtkomst till Azure AD för den nya klientorganisationen förrän du har aktiverat Azure AD-katalogåtkomst via det välkomstmejl som skickas ut automatiskt när licensinstallationen är klar. 

**Gör så här för aktivera åtkomst till Azure AD:**

1. Klicka på **Logga In** i **välkomstmeddelandet**. 
   
    ![Välkomstmeddelande](media/active-directory-get-started-premium/AADEmail.png)
2. Efter inloggningen måste du också göra en andra autentisering med hjälp av en mobil enhet:
   
    ![Mobil verifiering](media/active-directory-get-started-premium/SignUppage.png)

Aktiveringen tar bara några minuter och ger dig åtkomst till att hantera Azure AD. 

## <a name="next-steps"></a>Nästa steg
I den här snabbstarten har du lärt dig hur du registrerar dig för Azure AD Premium och aktiverar åtkomst till Azure Active Directory. 

Om du redan har en Azure-prenumeration kan du använda följande länk för att starta en utvärderingsversion eller köpa Azure AD Premium-licenser från Azure Portal.

> [!div class="nextstepaction"]
> [Aktivera Azure AD Premium-licenser](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/TryBuyProductBlade)