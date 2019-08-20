---
title: Skapa och Använd lösen ords principer i Azure AD Domain Services | Microsoft Docs
description: Lär dig hur och varför du kan använda detaljerade lösen ords principer för att skydda och kontrol lera konto lösen ord i en hanterad Azure AD DS-domän.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 1a14637e-b3d0-4fd9-ba7a-576b8df62ff2
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 08/08/2019
ms.author: iainfou
ms.openlocfilehash: 45fb2daaeaf9ee788207d43d805e070320372ca0
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617144"
---
# <a name="password-and-account-lockout-policies-on-managed-domains"></a>Principer för lösen ords-och konto utelåsning på hanterade domäner

Om du vill hantera konto säkerhet i Azure Active Directory Domain Services (Azure AD DS) kan du definiera detaljerade lösen ords principer som styr inställningar som minsta längd på lösen ord, lösen ordets giltighets tid eller lösen ords komplexitet. En standard lösen ords princip används för alla användare i en Azure AD DS-hanterad domän. För att ge detaljerad kontroll och uppfylla specifika krav för affärs-eller efterlevnad kan ytterligare principer skapas och tillämpas på specifika användar grupper.

Den här artikeln visar hur du skapar och konfigurerar en detaljerad lösen ords princip med hjälp av Active Directory Administrationscenter.

## <a name="before-you-begin"></a>Innan du börjar

För att slutföra den här artikeln behöver du följande resurser och behörigheter:

* En aktiv Azure-prenumeration.
  * [Skapa ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)om du inte har någon Azure-prenumeration.
* En Azure Active Directory klient som är associerad med din prenumeration, antingen synkroniserad med en lokal katalog eller en katalog som endast är moln.
  * Om det behövs kan du [skapa en Azure Active Directory klient][create-azure-ad-tenant] eller [associera en Azure-prenumeration med ditt konto][associate-azure-ad-tenant].
* En Azure Active Directory Domain Services hanterad domän aktive rad och konfigurerad i Azure AD-klienten.
  * Om det behövs, slutför du själv studie kursen för att [skapa och konfigurera en Azure Active Directory Domain Services-instans][create-azure-ad-ds-instance].
* En virtuell Windows Server Management-dator som är ansluten till den hanterade Azure AD DS-domänen.
  * Om det behövs kan du slutföra självstudien för att [skapa en virtuell hanterings dator][tutorial-create-management-vm].
* Ett användar konto som är medlem i administratörs gruppen för *Azure AD DC* i din Azure AD-klient.

## <a name="fine-grained-password-policies-fgpp-overview"></a>Översikt över detaljerade lösen ords principer (FGPP)

Med detaljerade lösen ords principer (FGPP) kan du använda vissa begränsningar för lösen ord och konto utelåsnings principer för olika användare i en domän. För att skydda privilegierade konton kan du till exempel använda striktare lösen ords inställningar än vanliga icke-privilegierade konton. Du kan skapa flera FGPP för att ange lösen ords principer inom en Azure AD DS-hanterad domän.

Följande inställningar för lösen ord kan konfigureras med FGPP:

* Minsta längd på lösen ord
* Lösen ords historik
* Lösen ord måste uppfylla komplexitets kraven
* Lägsta ålder för lösen ord
* Högsta ålder för lösen ord
* Princip för konto utelåsning
  * Konto Utelåsningens varaktighet
  * Antal misslyckade inloggnings försök som tillåts
  * Återställ antal misslyckade inloggnings försök efter

FGPP påverkar endast användare som skapats i Azure AD DS. Moln användare och domän användare som synkroniseras till den hanterade Azure AD DS-domänen från Azure AD påverkas inte av lösen ords principerna.

Principer distribueras via grupp associationen i den hanterade domänen i Azure AD DS, och alla ändringar du gör tillämpas vid nästa användar inloggning. Att ändra principen låser inte upp ett användar konto som redan är låst.

## <a name="default-fine-grained-password-policy-settings"></a>Standardinställningar för detaljerade lösen ords principer

I en Azure AD DS-hanterad domän konfigureras följande lösen ords principer som standard och tillämpas på alla användare:

* **Minsta längd på lösen ord (tecken):** 7
* **Högsta ålder för lösen ord (livs längd):** 90 dagar
* **Lösen ord måste uppfylla komplexitets kraven**

Följande principer för konto utelåsning konfigureras sedan som standard:

* **Varaktighet för konto utelåsning:** 30
* **Antal misslyckade inloggnings försök som tillåts:** 5
* **Återställ antal misslyckade inloggnings försök efter:** 30 minuter

Med dessa standardinställningar är användar konton utelåsta i 30 minuter om fem ogiltiga lösen ord används inom två minuter. Konton låses automatiskt upp efter 30 minuter.

Du kan inte ändra eller ta bort standard den inbyggda detaljerade lösen ords principen. I stället kan medlemmar i administratörs gruppen för *AAD-domänkontrollanter* skapa anpassade FGPP och konfigurera den att åsidosätta (prioriteras framför) den inbyggda standard FGPP som visas i nästa avsnitt.

## <a name="create-a-custom-fine-grained-password-policy"></a>Skapa en egen detaljerad lösen ords princip

När du skapar och program i Azure kanske du vill konfigurera en anpassad FGPP. Några exempel på behovet av att skapa en anpassad FGPP är att ange en annan princip för konto utelåsning eller konfigurera en standard inställning för lösen ords livs längd för den hanterade domänen.

Du kan skapa en anpassad FGPP och tillämpa den på vissa grupper i din Azure AD DS-hanterade domän. Den här konfigurationen åsidosätter en standard FGPP. Du kan också skapa egna detaljerade lösen ords principer och tillämpa dem på alla anpassade organisationsenheter som du skapar i den hanterade domänen i Azure AD DS.

Om du vill skapa en detaljerad lösen ords princip använder du Active Directory administrations verktyg från en domänansluten virtuell dator. Med Active Directory Administrationscenter kan du Visa, redigera och skapa resurser i en hanterad Azure AD DS-domän, inklusive organisationsenheter.

> [!NOTE]
> Om du vill skapa en detaljerad lösen ords princip i en Azure AD DS-hanterad domän måste du vara inloggad på ett användar konto som är medlem i administratörs gruppen för *AAD* -domänkontrollanten.

1. Välj **administrations verktyg**på Start skärmen. En lista över tillgängliga hanterings verktyg visas som har installerats i självstudien för att [skapa en virtuell hanterings dator][tutorial-create-management-vm].
1. Om du vill skapa och hantera organisationsenheter väljer du **Active Directory Administrationscenter** i listan över administrations verktyg.
1. I den vänstra rutan väljer du din Azure AD DS-hanterade domän, till exempel *contoso.com*.
1. I **aktivitets** panelen till höger väljer du **nytt > lösen ords inställningar**.
1. I dialog rutan **skapa lösen ords inställningar** anger du ett namn för principen, till exempel *MyCustomFGPP*. Ställ in prioriteten på lämpligt sätt om du vill åsidosätta standard-FGPP (som är *200*), till exempel *1*.

    Redigera andra inställningar för lösen ords principer som du vill, till exempel **tillämpa lösen ords historik** för att kräva att användaren skapar ett lösen ord som skiljer sig från de föregående *24* lösen orden.

    ![Skapa en egen detaljerad lösen ords princip](./media/how-to/custom-fgpp.png)

1. Avmarkera **skydda från oavsiktlig borttagning**. Om det här alternativet är markerat kan du inte spara FGPP.
1. I avsnittet **tillämpas direkt på** väljer du knappen **Lägg till** . I dialog rutan **Välj användare eller grupper** klickar du på knappen **platser** .

    ![Välj de användare och grupper som lösen ords principen ska tillämpas på](./media/how-to/fgpp-applies-to.png)

1. Detaljerade lösen ords principer kan bara tillämpas på grupper. I dialog rutan **platser** expanderar du domän namnet, till exempel *contoso.com*, och väljer sedan en organisationsenhet, till exempel **AADDC-användare**. Om du har en anpassad ORGANISATIONSENHET som innehåller en grupp med användare som du vill tillämpa väljer du den ORGANISATIONSENHETen.

    ![Välj den ORGANISATIONSENHET som gruppen tillhör](./media/how-to/fgpp-container.png)

1. Skriv namnet på den grupp som du vill tillämpa principen på och välj sedan **kontrol lera namn** för att kontrol lera att gruppen finns.

    ![Sök efter och välj gruppen som ska användas för FGPP](./media/how-to/fgpp-apply-group.png)

1. Med namnet på den grupp som du valde nu, som visas **direkt** i avsnittet, väljer du **OK** för att spara din anpassade lösen ords princip.

## <a name="next-steps"></a>Nästa steg

Mer information om detaljerade lösen ords principer och hur du använder Active Directory administrations centret finns i följande artiklar:

* [Lär dig mer om detaljerade lösen ords principer](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [Konfigurera detaljerade lösen ords principer med hjälp av administrations Center för AD](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
