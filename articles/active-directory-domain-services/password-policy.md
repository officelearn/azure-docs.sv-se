---
title: Skapa och använda lösenordsprinciper i Azure AD Domain Services | Microsoft-dokument
description: Lär dig hur och varför du använder detaljerade lösenordsprinciper för att skydda och kontrollera kontolösenord i en Azure AD DS-hanterad domän.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 1a14637e-b3d0-4fd9-ba7a-576b8df62ff2
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: c4402c1ce2f051c8d1911e7c0332d4cac787ce1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77613191"
---
# <a name="password-and-account-lockout-policies-on-managed-domains"></a>Principer för lösenords- och kontoutelåsning på hanterade domäner

Om du vill hantera användarsäkerhet i Azure Active Directory Domain Services (Azure AD DS) kan du definiera detaljerade lösenordsprinciper som styr inställningar för kontoutelåsning eller minsta lösenordslängd och komplexitet. En standardprincip för finkorniga lösenord skapas och tillämpas på alla användare i en Azure AD DS-hanterad domän. För att tillhandahålla detaljerad kontroll och uppfylla specifika affärs- eller efterlevnadsbehov kan ytterligare principer skapas och tillämpas på specifika användargrupper.

Den här artikeln visar hur du skapar och konfigurerar en detaljerad lösenordsprincip i Azure AD DS med Active Directory Administrationscenter.

> [!NOTE]
> Lösenordsprinciper är endast tillgängliga för Azure AD DS-hanterade domäner som skapats med hjälp av Resurshanterarens distributionsmodell. För äldre hanterade domäner som skapats med Classic [migrerar du från den klassiska virtuella nätverksmodellen till Resource Manager][migrate-from-classic].

## <a name="before-you-begin"></a>Innan du börjar

För att kunna slutföra den här artikeln behöver du följande resurser och privilegier:

* En aktiv Azure-prenumeration.
  * Om du inte har en Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* En Azure Active Directory-klient som är associerad med din prenumeration, antingen synkroniserad med en lokal katalog eller en katalog med endast molnet.
  * Om det behövs [skapar du en Azure Active Directory-klientorganisation][create-azure-ad-tenant] eller [associerar en Azure-prenumeration med ditt konto][associate-azure-ad-tenant].
* En hanterad Azure Active Directory Domain Services-domän aktiverad och konfigurerad i din Azure AD-klientorganisation.
  * Om det behövs slutför du självstudien för att [skapa och konfigurera en Azure Active Directory Domain Services-instans][create-azure-ad-ds-instance].
  * Azure AD DS-instansen måste ha skapats med hjälp av Resurshanterarens distributionsmodell. Om det behövs [migreras du från den klassiska virtuella nätverksmodellen till Resource Manager][migrate-from-classic].
* En virtuell windows serverhantering som är ansluten till den Hanterade Azure AD DS-domänen.
  * Om det behövs slutför du självstudien för att [skapa en vm-hantering][tutorial-create-management-vm].
* Ett användarkonto som är medlem i azure *AD DC-administratörsgruppen* i din Azure AD-klientorganisation.

## <a name="default-password-policy-settings"></a>Standardinställningar för lösenordsprincip

Med detaljerade lösenordsprinciper kan du tillämpa särskilda begränsningar för principer för lösenords- och kontoutelåsning på olika användare i en domän. Om du till exempel vill skydda privilegierade konton kan du använda striktare inställningar för kontoutelåsning än vanliga konton som inte har privilegierade konton. Du kan skapa flera FGPPs inom en Azure AD DS-hanterad domän och ange prioritetsordningen för att tillämpa dem på användare.

Principer distribueras via gruppassociation i en Azure AD DS-hanterad domän och alla ändringar du gör tillämpas vid nästa användarloggning. Om du ändrar principen låses inte upp ett användarkonto som redan är utelåst.

Lösenordsprinciper fungerar lite olika beroende på hur användarkontot de tillämpas på skapades. Det finns två sätt att skapa ett användarkonto i Azure AD DS:

* Användarkontot kan synkroniseras från Azure AD. Detta inkluderar molnbaserade användarkonton som skapats direkt i Azure och hybridanvändarkonton synkroniserade från en lokal AD DS-miljö med Azure AD Connect.
    * Majoriteten av användarkontona i Azure AD DS skapas via synkroniseringsprocessen från Azure AD.
* Användarkontot kan skapas manuellt i en Azure AD DS-hanterad domän och finns inte i Azure AD.

Alla användare, oavsett hur de skapas, har följande principer för kontoutelåsning tillämpad enligt standardordprincipen i Azure AD DS:

* **Varaktighet för kontoutelåsning:** 30
* **Antal tillåtna misslyckade inloggningsförsök:** 5
* **Återställning av misslyckade inloggningsförsök räknas efter:** 30 minuter
* **Maximal lösenordsålder (livslängd):** 90 dagar

Med dessa standardinställningar är användarkonton utelåst i 30 minuter om fem ogiltiga lösenord används inom 2 minuter. Konton låses automatiskt upp efter 30 minuter.

Kontoutelåsningar sker endast inom den hanterade domänen. Användarkonton är bara utelåsta i Azure AD DS och endast på grund av misslyckade inloggningsförsök mot den hanterade domänen. Användarkonton som synkroniserades från Azure AD eller lokalt är inte utelåsta i sina källkataloger, bara i Azure AD DS.

Om du har en Azure AD-lösenordsprincip som anger en maximal lösenordsålder som är längre än 90 dagar, tillämpas den lösenordsåldern på standardprincipen i Azure AD DS. Du kan konfigurera en anpassad lösenordsprincip för att definiera en annan maximal lösenordsålder i Azure AD DS. Var försiktig om du har en kortare maximal lösenordsålder konfigurerad i en Azure AD DS-lösenordsprincip än i Azure AD eller en lokal AD DS-miljö. I det scenariot kan en användares lösenord upphöra att gälla i Azure AD DS innan de uppmanas att ändras i Azure AD eller en lokal AD DS-miljö.

För användarkonton som skapats manuellt i en Azure AD DS-hanterad domän tillämpas även följande ytterligare lösenordsinställningar från standardprincipen. Dessa inställningar gäller inte för användarkonton som synkroniseras från Azure AD, eftersom en användare inte kan uppdatera sitt lösenord direkt i Azure AD DS.

* **Minsta lösenordslängd (tecken):** 7
* **Lösenord måste uppfylla komplexitetskrav**

Du kan inte ändra inställningarna för kontoutelåsning eller lösenord i standardlösenordsprincipen. I stället kan medlemmar i gruppen *AAD DC-administratörer* skapa anpassade lösenordsprinciper och konfigurera den för att åsidosätta (ha företräde framför) standardprincipen för inbyggt program, som visas i nästa avsnitt.

## <a name="create-a-custom-password-policy"></a>Skapa en anpassad lösenordsprincip

När du skapar och kör program i Azure kanske du vill konfigurera en anpassad lösenordsprincip. Du kan till exempel skapa en princip för att ange olika principinställningar för kontoutelåsning.

Principer för anpassade lösenord tillämpas på grupper i en Azure AD DS-hanterad domän. Den här konfigurationen åsidosätter standardprincipen effektivt.

Om du vill skapa en anpassad lösenordsprincip använder du Administrationsverktygen för Active Directory från en domänansluten virtuell dator. Med Active Directory Administrationscenter kan du visa, redigera och skapa resurser i en Azure AD DS-hanterad domän, inklusive ru: er.

> [!NOTE]
> Om du vill skapa en anpassad lösenordsprincip i en Azure AD DS-hanterad domän måste du vara inloggad på ett användarkonto som är medlem i gruppen *AAD DC-administratörer.*

1. Välj **Administrationsverktyg**på Startskärmen . En lista över tillgängliga hanteringsverktyg visas som installerades i självstudien för att [skapa en hantering VM][tutorial-create-management-vm].
1. Om du vill skapa och hantera ru:er väljer du **Administrationscenter** för Active Directory i listan över administrativa verktyg.
1. I den vänstra rutan väljer du din Azure AD DS-hanterade domän, till exempel *aaddscontoso.com*.
1. Öppna **systembehållaren** och sedan **behållaren för lösenordsinställningar**.

    En inbyggd lösenordsprincip för azure AD DS-hanterad domän visas. Du kan inte ändra den här inbyggda principen. Skapa i stället en anpassad lösenordsprincip för att åsidosätta standardprincipen.

    ![Skapa en lösenordsprincip i Administrationscenter för Active Directory](./media/password-policy/create-password-policy-adac.png)

1. Välj **Nya > lösenordsinställningar**på aktivitetspanelen till höger . **Tasks**
1. Ange ett namn för principen i dialogrutan **Skapa lösenordsinställningar,** till exempel *MyCustomFGPP*.
1. När det finns flera lösenordsprinciper tillämpas principen med högst prioritet eller prioritet på en användare. Ju lägre siffra, desto högre prioritet. Standardordordprincipen har prioritet *200*.

    Ange att prioriteten för din anpassade lösenordsprincip ska åsidosätta standardinställningen, till exempel *1*.

1. Redigera andra lösenordsprincipinställningar efter behov. Kom ihåg följande viktiga punkter:

    * Inställningar som lösenordskomplexitet, ålder eller förfallotid endast för användare som skapats manuellt i en Azure AD DS-hanterad domän.
    * Kontoutelåsningsinställningar gäller för alla användare, men börjar bara gälla inom den hanterade domänen och inte i Azure AD själv.

    ![Skapa en anpassad detaljerad lösenordsprincip](./media/how-to/custom-fgpp.png)

1. Avmarkera **Skydda mot oavsiktlig borttagning**. Om det här alternativet är markerat kan du inte spara FGPP.
1. Välj knappen **Lägg** till i avsnittet **Direkt gäller** för. Välj knappen **Platser** i dialogrutan **Välj användare eller grupper.**

    ![Välj de användare och grupper som ska tillämpas på lösenordsprincipen](./media/how-to/fgpp-applies-to.png)

1. Lösenordsprinciper kan endast tillämpas på grupper. Expandera domännamnet på plats i dialogrutan **Platser,** till exempel *aaddscontoso.com*, och välj sedan en organisationsenhet, till exempel **AADDC-användare**. Om du har en anpassad organisationsenhet som innehåller en grupp användare som du vill använda väljer du den organisationsenheten.

    ![Välj den organisationsenheten som gruppen tillhör](./media/how-to/fgpp-container.png)

1. Skriv namnet på den grupp som du vill koppla principen till och välj sedan **Kontrollera namn** för att verifiera att gruppen finns.

    ![Sök efter och välj den grupp som ska användas för kvinnlig könsstympning](./media/how-to/fgpp-apply-group.png)

1. När namnet på den grupp som du har valt nu visas i avsnittet **Direkt gäller för** väljer du **OK** för att spara din anpassade lösenordsprincip.

## <a name="next-steps"></a>Nästa steg

Mer information om lösenordsprinciper och användning av Active Directory Administrationscenter finns i följande artiklar:

* [Läs mer om detaljerade lösenordsprinciper](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [Konfigurera detaljerade lösenordsprinciper med AD Administration Center](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[migrate-from-classic]: migrate-from-classic-vnet.md
