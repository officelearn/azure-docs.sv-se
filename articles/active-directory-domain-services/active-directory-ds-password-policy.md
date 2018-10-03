---
title: 'Azure Active Directory Domain Services: Lösenordsprincip | Microsoft Docs'
description: Förstå lösenordsprinciper på hanterade domäner
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 1a14637e-b3d0-4fd9-ba7a-576b8df62ff2
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: maheshu
ms.openlocfilehash: 66db13ea2b40d32b03d57e7d039863315312d734
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2018
ms.locfileid: "48043115"
---
# <a name="password-and-account-lockout-policies-on-managed-domains"></a>Lösenord och kontoutelåsningar på hanterade domäner
Den här artikeln förklarar standardprinciper för lösenord på en hanterad domän. Den behandlar också hur du konfigurerar dessa principer.

## <a name="fine-grained-password-policies-fgpp"></a>Bra detaljerade lösenordsprinciper (FGPP)
Använd detaljerade lösenordsprinciper för att ange flera lösenordsprinciper inom en enda domän. FGPP kan du använda olika begränsningar för lösenord och principer för kontoutelåsning för olika uppsättningar av användare i en domän. Du kan till exempel använda strikt lösenordsinställningar till behöriga konton.

Du kan konfigurera följande lösenordsinställningar med hjälp av FGPP:
* Minsta längd på lösenord
* Tidigare lösenord
* Lösenord måste uppfylla krav på komplexitet
* Lägsta ålder för lösenord
* Högsta ålder för lösenord
* Kontoutelåsning
    * Kontots utelåsningstid
    * Antalet misslyckade inloggningsförsök som tillåts
    * Återställning av inloggningsförsök misslyckade antal efter


## <a name="default-fine-grained-password-policy-settings-on-a-managed-domain"></a>Standard de detaljerade lösenordsprincip på en hanterad domän
Följande skärmbild illustrerar den standard bra lösenordsprincip konfigurerats på en Azure AD Domain Services-hanterad domän.

![Detaljerade lösenordsprincip som standard](./media/how-to/default-fgpp.png)

> [!NOTE]
> Du kan inte ändra eller ta bort den standard inbyggda detaljerade lösenordsprincipen. Medlemmar i gruppen ”AAD DC-administratörer” skapa anpassade FGPP och konfigurera den för att åsidosätta (företräde framför) standard inbyggda FGPP.
>
>

## <a name="password-policy-settings"></a>Lösenordsprincip
Följande lösenordsprinciper är konfigurerade som standard på en hanterad domän:
* Minsta längd på lösenord (tecken): 7
* Högsta ålder för lösenord (livslängd): 90 dagar
* Lösenord måste uppfylla krav på komplexitet

### <a name="account-lockout-settings"></a>För kontoutelåsning
Följande kontoutelåsningsprinciper är konfigurerade som standard på en hanterad domän:
* Kontots utelåsningstid: 30
* Antalet misslyckade inloggningsförsök som tillåts: 5
* Återställning av misslyckade inloggningsförsök antal efter: 30 minuter

Ett effektivt sätt, är användarkonton utelåst under 30 minuter om fem ogiltigt lösenord används inom två minuter. Konton är automatiskt bort efter 30 minuter.


## <a name="create-a-custom-fine-grained-password-policy-fgpp-on-a-managed-domain"></a>Skapa en anpassad detaljerade lösenordsprincip (FGPP) på en hanterad domän
Du kan skapa en anpassad FGPP och tillämpa den på specifika grupper i din hanterade domän. Den här konfigurationen åsidosätter effektivt standard FGPP som konfigurerats för den hanterade domänen.

> [!TIP]
> Endast medlemmar i den **”AAD DC-administratörer”** gruppen har behörighet att skapa anpassade de detaljerade lösenordsprinciper.
>
>

Dessutom kan du också skapa anpassade bra detaljerade lösenordsprinciper och koppla dem till alla anpassade organisationsenheter som du skapar i den hanterade domänen.

Du kan konfigurera en anpassad FGPP av följande skäl:
* Att ange en annan princip för kontoutelåsning.
* Att konfigurera ett lösenord livslängd standardinställningen för den hanterade domänen.

Skapa en anpassad FGPP på din hanterade domän:
1. Logga in på Windows-VM som du använder för att administrera din hanterade domän. Om du inte har något, följ instruktionerna för att [administrera en hanterad domän](active-directory-ds-admin-guide-administer-domain.md)
2. Starta den **Active Directory Administrationscenter** på den virtuella datorn.
3. Klicka på domännamnet (till exempel ”contoso100.com”).
4. Dubbelklicka på **System** att öppna System-behållaren.
5. Dubbelklicka på **Password Settings Container**.
6. Du ser standard inbyggda FGPP för den hanterade domänen kallas **AADDSSTFPSO**. Du kan inte ändra den här inbyggda FGPP. Du kan dock skapa en ny anpassad FGPP åsidosättning standard FGPP.
7. På den **uppgifter** panelen i höger klickar du på **New** och klicka på **lösenordsinställningar**.
8. I den **skapa lösenordsinställningar** dialogrutan Ange för anpassade lösenordsinställningar för den som en del av den anpassade FGPP. Kom ihåg att ange prioriteten på lämpligt sätt för att åsidosätta standarden FGPP.

  ![Skapa anpassade FGPP](./media/how-to/custom-fgpp.png)

  > [!TIP]
  > **Kom ihåg att avmarkera Skydda mot oavsiktlig borttagning alternativet.** Om det här alternativet väljs, går inte att spara FGPP.
  >
  >

9. I **tillämpas direkt för**, klickar du på den **Lägg till** knappen. I den **Välj användare eller grupper** dialogrutan klickar du på den **platser** knappen.

  ![Välj användare och grupper](./media/how-to/fgpp-applies-to.png)

10. I den **platser** dialogrutan expanderar du domännamnet och klickar på **AADDC-användare**. Nu kan du välja en grupp från inbyggda Organisationsenheten för användare, som att tillämpa FGPP.

  ![Välj Organisationsenheten gruppen tillhör](./media/how-to/fgpp-container.png)

11. Skriv namnet på gruppen och klicka på den **Kontrollera namn** knappen för att verifiera gruppen finns.

  ![Markera gruppen för att tillämpa FGPP](./media/how-to/fgpp-apply-group.png)

12. Namnet på gruppen visas i **tillämpas direkt för** avsnittet. Klicka på den **OK** för att spara ändringarna.

  ![FGPP tillämpas](./media/how-to/fgpp-applied.png)

> [!TIP]
> **Att tillämpa anpassade lösenordsprinciper för användarkonton i en anpassad Organisationsenhet:** bra detaljerade lösenordsprinciper kan tillämpas endast för grupper. Skapa en grupp som innehåller användare i denna Organisationsenhet för att konfigurera en anpassad princip för endast för användare från en anpassad Organisationsenhet.
>
>

## <a name="next-steps"></a>Nästa steg
* [Lär dig mer om Active Directory bra detaljerade lösenordsprinciper](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394)
* [Konfigurera detaljerade lösenordsprinciper med hjälp av AD-administrationscentret](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)
