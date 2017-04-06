---
title: "Komma igång med Azure Active Directory Premium"
description: "Ett avsnitt som förklarar hur du registrerar dig för Azure Active Directory Premium-versionen via webbplatsen för volymlicensiering."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
ms.assetid: 391764e5-c4eb-4ae6-b8a9-3d09f0de04a6
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/24/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 73cbbce9ae3d727632b8cac2a29c2b1b92332ae7
ms.lasthandoff: 12/07/2016


---
# <a name="getting-started-with-azure-active-directory-premium"></a>Komma igång med Azure Active Directory Premium
Om du vill registrera dig för Active Directory Premium kan du välja mellan flera alternativ: 

**Azure eller Office 365** – Som prenumerant på Azure eller Office 365 kan du köpa Active Directory Premium online. Detaljerade anvisningar finns i [Köpa Azure Active Directory Premium – befintliga kunder](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/How-to-Purchase-Azure-Active-Directory-Premium-Existing-Customer) eller [Köpa Azure Active Directory Premium – nya kunder](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/How-to-Purchase-Azure-Active-Directory-Premium-New-Customers).  

**Enterprise Mobility + Security** – Enterprise Mobility + Security (tidigare Enterprise Mobility Suite) är ett kostnadseffektivt sätt för organisationer att använda följande tjänster tillsammans i samma licensieringsplan: Active Directory Premium, Azure Rights Management och Microsoft Intune. Mer information finns på webbplatsen för [Enterprise Mobility + Security](https://www.microsoft.com/en-us/server-cloud/enterprise-mobility/overview.aspx). Om du vill skaffa en kostnadsfri 30-dagars utvärderingsversion klickar du [här](https://portal.office.com/Signup/Signup.aspx?OfferId=2E63A04D-BE0B-4A0F-A8CF-407C1C299221&dl=EMS&ali=1#0).

**Microsoft Volymlicensiering** –Azure Active Directory Premium är tillgängligt genom ett [Microsoft Enterprise-avtal](https://www.microsoft.com/en-us/licensing/licensing-programs/enterprise.aspx) (250 eller fler licenser) eller genom [Open Volume-licensprogrammet](https://www.microsoft.com/en-us/licensing/licensing-programs/open-license.aspx) (5–250 licenser).

Det här avsnittet beskriver hur du kommer igång med Azure Active Directory Premium som du har köpt genom volymlicensieringsprogrammet. Om du inte känner till de olika versionerna av Azure Active Directory läser du avsnittet [Azure Active Directory-versioner](active-directory-editions.md).  

> [!NOTE]
> Azure Active Directory Premium och Basic är tillgängliga för kunder i Kina genom den globala instansen av Azure Active Directory. Azure Active Directory Premium och Basic stöds inte för närvarande i Microsoft Azure-tjänsten som drivs av 21Vianet i Kina. Om du vill ha mer information kontaktar du oss via [Azure Active Directory-forumet](https://feedback.azure.com/forums/169401-azure-active-directory/).
> 
> 

## <a name="step-1-sign-up-for-active-directory-premium"></a>Steg 1: Registrera dig för Active Directory Premium
Om du vill registrera dig läser du [Köpa genom volymlicensiering](http://www.microsoft.com/en-us/licensing/how-to-buy/how-to-buy.aspx).

## <a name="step-2-activate-your-license-plan"></a>Steg 2: Aktivera din licensplan
Är det här den första licensplanen som du köper genom volymlicensieringprogrammet från Microsoft?
I så fall får du ett bekräftelsemeddelande via e-post när köpet är klart.
Du behöver det här e-postmeddelandet för att kunna aktivera din första licensplan.

Vid eventuella efterföljande köp för den här katalogen aktiveras licenserna automatiskt i samma katalog.

**Du aktiverar din licensplan genom att utföra något av följande steg:**

1. Starta aktiveringen genom att klicka på **Logga In** eller **Registrera dig**.
   
    ![Logga in][1]

    - Om du har en befintlig klient klickar du på **Logga in** för att logga in med ditt befintliga administratörskonto. Du måste logga in med de globala autentiseringsuppgifterna från den katalog där licenserna ska aktiveras.

    - Om du vill skapa en ny Azure Active Directory-klient och använda den med din licensieringsplan klickar du på **Registrera dig** för att öppna dialogrutan **Skapa kontoprofil**.

        ![Skapa kontoprofil][2]

När du är klar visas följande dialogruta som en bekräftelse på aktiveringen av licensplanen för din klient.

![Bekräftelse][3]

## <a name="step-3-activate-your-azure-active-directory-access"></a>Steg 3: Aktivera din åtkomst till Azure Active Directory
Om du har använt Microsoft Azure förut kan du gå vidare till [Steg 4](#step-4-assign-license-to-user-accounts). 

När licenserna har etablerats i din katalog skickas ett **välkomstmeddelande** till din e-postadresss. E-postmeddelandet bekräftar att du kan börja hantera dina licenser och funktioner för Azure Active Directory Premium eller Enterprise Mobility Suite. 

Om du försöker aktivera din åtkomst till Azure Active Directory innan du får välkomstmeddelandet visas följande felmeddelande. 

![Åtkomst är inte tillgängligt][9]

Försök igen om några minuter när du har fått e-postmeddelandet.

Nya administratörer i din prenumeration kan också aktivera sin åtkomst till den klassiska Azure-portalen via den här länken.

**Utför följande steg för att aktivera din åtkomst till Azure Active Directory:**

1. Klicka på **Logga In** i **välkomstmeddelandet**. 
   
    ![Välkomstmeddelande][4]
2. När du har loggat in måste du utföra en tvåfaktorsautentisering i form av en mobil verifiering:
   
    ![Mobil verifiering][5]

Aktiveringen kan ta några minuter. När din åtkomst har aktiverats försvinner det bruna fältet och du kan klicka på **Portal**.

![Vänta medan vi konfigurerar inställningarna][6]

I detta fall är din Azure-åtkomst begränsad till Azure Active Directory.

![Azure-funktioner][7]

Du kanske redan har haft tillgång till Azure från tidigare användning. Du kan också uppgradera din Azure Active Directory till fullständig Azure-åtkomst genom att aktivera ytterligare Azure-prenumerationer. I dessa fall har den klassiska Azure-portalen fler funktioner.

![Azure-funktioner][8]

## <a name="step-4-assign-license-to-user-accounts"></a>Steg 4: Tilldela användarkonton licenser
Innan du kan börja använda planen som du har köpt måste du manuellt tilldela licenser till användarkonton i organisationen så att de kan använda de omfattande funktioner som tillhandahålls med Premium. Utför följande steg för att tilldela licenser till användare så att de kan använda Azure Active Directory Premium-funktioner.

**Utför följande steg för att tilldela licenser till användare:**

1. Logga in på den klassiska Azure-portalen som global administratör för den katalog som du vill anpassa.
2. Klicka på **Active Directory** och välj sedan den katalog där du vill tilldela licenser.
3. Välj fliken **Licenser**, välj **Active Directory Premium** eller **Enterprise Mobility Suite** och klicka sedan på **Tilldela**.
   
    ![Licensplaner][10]
4. I dialogrutan väljer du de användare som du vill tilldela licenser till och klickar sedan på bockmarkeringen för att spara ändringarna.
   
    ![Tilldela licenser][11]

### <a name="license-restrictions"></a>Licensbegränsningar
Vissa licensplaner är underordnade eller överordnade uppsättningar av andra licensplaner. Normalt kan användare inte tilldelas en licensplan som de redan har tilldelats. Om du vill tilldela en överordnad licensplan måste du först ta bort den underordnade licensplanen.

### <a name="license-requirements"></a>Licenskrav
När du tilldelar en licens till en användare kan du ange en primär användningsplats i egenskaperna för användarens konto. Om ingen användningsplats anges tilldelas användaren automatiskt klientens plats.

![Användarplats][12]

Tillgängligheten av tjänster och funktioner för en Microsoft-molntjänst varierar beroende på land eller region. En tjänst, till exempel VoIP (Voice over Internet Protocol), kan vara tillgänglig i ett land eller en region men inte i en annan. Funktioner i en tjänst kan vara begränsade av juridiska skäl i vissa länder eller regioner. Leta upp ditt land eller din region på webbplatsen för licensbegränsningar för en tjänst om du vill veta om en tjänst är tillgänglig med eller utan begränsningar.

## <a name="whats-next"></a>Nästa steg
* [Anpassa inloggnings- och åtkomstpanelsidorna till ditt företag](active-directory-add-company-branding.md)
* [Visa åtkomst- och användningsrapporterna](active-directory-view-access-usage-reports.md)

<!--Image references-->
[1]: ./media/active-directory-get-started-premium/MOLSEmail.png
[2]: ./media/active-directory-get-started-premium/MOLSAccountProfile.png
[3]: ./media/active-directory-get-started-premium/MOLSThankYou.png
[4]: ./media/active-directory-get-started-premium/AADEmail.png
[5]: ./media/active-directory-get-started-premium/SignUppage.png
[6]: ./media/active-directory-get-started-premium/Subscriptionspage.png
[7]: ./media/active-directory-get-started-premium/Premiuminportal.png
[8]: ./media/active-directory-get-started-premium/Premiuminportal_large.png
[9]: ./media/active-directory-get-started-premium/Signuppage_oops.png
[10]: ./media/active-directory-get-started-premium/contosolicenseplan.png
[11]: ./media/active-directory-get-started-premium/Assignlicensespicker.png
[12]: ./media/active-directory-get-started-premium/Usagelocation.png

