---
title: 'Azure Active Directory Domain Services: Lösen ords princip | Microsoft Docs'
description: Förstå lösen ords principer på hanterade domäner
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 1a14637e-b3d0-4fd9-ba7a-576b8df62ff2
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2019
ms.author: iainfou
ms.openlocfilehash: 30f4558339bbfddd2296cd1cb918c6ef8999b67e
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68879198"
---
# <a name="password-and-account-lockout-policies-on-managed-domains"></a>Principer för lösen ords-och konto utelåsning på hanterade domäner
I den här artikeln beskrivs standard lösen ords principerna på en hanterad domän. Det beskriver också hur du kan konfigurera dessa principer.

## <a name="fine-grained-password-policies-fgpp"></a>Detaljerade lösen ords principer (FGPP)
Använd detaljerade lösen ords principer för att ange många lösen ords principer inom en enda domän. Med FGPP kan du använda olika begränsningar för lösen ord och principer för konto utelåsning för olika uppsättningar med användare i en domän. Du kan till exempel använda strikta lösen ords inställningar för privilegierade konton.

Du kan konfigurera följande lösen ords inställningar med FGPP:
* Minsta längd på lösen ord
* Lösen ords historik
* Lösen ord måste uppfylla komplexitets kraven
* Lägsta ålder för lösen ord
* Högsta ålder för lösen ord
* Princip för konto utelåsning
    * Konto Utelåsningens varaktighet
    * Antal misslyckade inloggnings försök som tillåts
    * Återställ antal misslyckade inloggnings försök efter
    
FGPP påverkar endast användare som skapats direkt i Azure AD DS. Moln användare och domän användare som synkroniseras till den hanterade Azure AD DS-domänen från Azure AD påverkas inte av inställningarna för lösen ords komplexitet. FGPP distribueras via grupp associationen i den hanterade domänen i Azure AD DS och alla ändringar du gör tillämpas vid nästa användar inloggning. Att ändra principen låser inte upp ett användar konto som redan är låst.

## <a name="default-fine-grained-password-policy-settings-on-a-managed-domain"></a>Standardiserade detaljerade lösen ords princip inställningar på en hanterad domän
Följande skärm bild visar standard principen för detaljerad lösen ords princip som kon figurer ATS på en Azure AD Domain Services hanterad domän.

![Standard för detaljerad lösen ords princip](./media/how-to/default-fgpp.png)

> [!NOTE]
> Du kan inte ändra eller ta bort standard den inbyggda detaljerade lösen ords principen. Medlemmar i gruppen "AAD DC-administratörer" kan skapa anpassade FGPP och konfigurera den att åsidosätta (prioriteras framför) den inbyggda FGPP som är standard.
>
>

## <a name="password-policy-settings"></a>Inställningar för lösen ords princip
I en hanterad domän konfigureras följande lösen ords principer som standard:
* Minsta längd på lösen ord (tecken): 7
* Högsta ålder för lösen ord (livs längd): 90 dagar
* Lösen ord måste uppfylla komplexitets kraven

### <a name="account-lockout-settings"></a>Inställningar för konto utelåsning
I en hanterad domän konfigureras följande principer för konto utelåsning som standard:
* Varaktighet för konto utelåsning: 30
* Antal misslyckade inloggnings försök som tillåts: 5
* Återställ antal misslyckade inloggnings försök efter: 30 minuter

Användar konton låses effektivt i 30 minuter om fem ogiltiga lösen ord används inom 2 minuter. Konton låses automatiskt upp efter 30 minuter.


## <a name="create-a-custom-fine-grained-password-policy-fgpp-on-a-managed-domain"></a>Skapa en anpassad detaljerad lösen ords princip (FGPP) på en hanterad domän
Du kan skapa en anpassad FGPP och tillämpa den på vissa grupper i din hanterade domän. Den här konfigurationen åsidosätter en effektiv standard-FGPP som kon figurer ATS för den hanterade domänen.

> [!TIP]
> Endast medlemmar i gruppen **"AAD DC-administratörer"** har behörighet att skapa anpassade, detaljerade lösen ords principer.
>
>

Dessutom kan du också skapa anpassade, detaljerade lösen ords principer och tillämpa dem på alla anpassade organisationsenheter som du skapar på den hanterade domänen.

Du kan konfigurera en anpassad FGPP av följande orsaker:
* Ange en annan princip för konto utelåsning.
* Konfigurera en standard livstids inställning för lösen ord för den hanterade domänen.

Så här skapar du en anpassad FGPP i din hanterade domän:
1. Logga in på den virtuella Windows-dator som du använder för att administrera din hanterade domän (måste vara minst Windows Server 2012 R2). Om du inte har någon, följer du anvisningarna för att [hantera en Azure AD Domain Services domän](manage-domain.md).
2. Starta **Active Directory Administrationscenter** på den virtuella datorn.
3. Klicka på domän namnet (till exempel ' contoso100.com ').
4. Öppna system behållaren genom att dubbelklicka på **system** .
5. Dubbelklicka på **Password Settings Container**.
6. Du ser den inbyggda standard FGPP för den hanterade domänen med namnet **AADDSSTFPSO**. Du kan inte ändra den här inbyggda FGPP. Du kan dock skapa en ny anpassad FGPP Åsidosätt standardvärdet FGPP.
7. I **aktivitets** panelen till höger klickar du på **nytt** och sedan på **lösen ords inställningar**.
8. I dialog rutan **skapa lösen ords inställningar** anger du de anpassade lösen ords inställningar som ska användas som en del av den anpassade FGPP. Kom ihåg att ange prioriteten korrekt för att åsidosätta standard FGPP.

   ![Skapa anpassad FGPP](./media/how-to/custom-fgpp.png)

   > [!TIP]
   > **Kom ihåg att avmarkera alternativet skydda från oavsiktlig borttagning.** Om du väljer det här alternativet går det inte att spara FGPP.
   >
   >

9. I **direkt för**, klickar du på knappen **Lägg till** . I dialog rutan **Välj användare eller grupper** klickar du på knappen **platser** .

   ![Välj användare och grupper](./media/how-to/fgpp-applies-to.png)

10. I dialog rutan **platser** expanderar du domän namnet och klickar på **AADDC-användare**. Nu kan du välja en grupp från den inbyggda OU-användare som FGPP ska tillämpas på.

    ![Välj den ORGANISATIONSENHET som gruppen tillhör](./media/how-to/fgpp-container.png)

11. Skriv namnet på gruppen och klicka på knappen **kontrol lera namn** för att verifiera att gruppen finns.

    ![Välj den grupp som ska användas för FGPP](./media/how-to/fgpp-apply-group.png)

12. Namnet på gruppen visas **direkt för** avsnittet. Klicka på knappen **OK** för att spara ändringarna.

    ![FGPP tillämpas](./media/how-to/fgpp-applied.png)

> [!TIP]
> **Tillämpa anpassade lösen ords principer för användar konton i en anpassad ORGANISATIONSENHET:** Detaljerade lösen ords principer kan bara tillämpas på grupper. Om du bara vill konfigurera en anpassad lösen ords princip för användare från en anpassad ORGANISATIONSENHET, skapar du en grupp som inkluderar användare i ORGANISATIONSENHETen.
>
>

## <a name="next-steps"></a>Nästa steg
* [Lär dig mer om Active Directory detaljerade lösen ords principer](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [Konfigurera detaljerade lösen ords principer med hjälp av administrations Center för AD](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)
