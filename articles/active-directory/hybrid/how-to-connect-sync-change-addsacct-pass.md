---
title: 'Azure AD Connect-synkronisering: Ändra AD DS-kontolösenordet | Microsoft-dokument'
description: I det här ämnesdokumentet beskrivs hur du uppdaterar Azure AD Connect när lösenordet för AD DS-kontot har ändrats.
services: active-directory
keywords: AD DS-konto, Active Directory-konto, lösenord
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 76b19162-8b16-4960-9e22-bd64e6675ecc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 35e04be046e20883f60c576745a29342add68a81
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60241593"
---
# <a name="changing-the-ad-ds-account-password"></a>Ändra AD DS-kontolösenordet
AD DS-kontot refererar till användarkontot som används av Azure AD Connect för att kommunicera med lokala Active Directory. Om du ändrar lösenordet för AD DS-kontot måste du uppdatera Azure AD Connect-synkroniseringstjänsten med det nya lösenordet. Annars kan synkroniseringen inte längre synkroniseras korrekt med den lokala Active Directory och du kommer att stöta på följande fel:

* I Synkroniseringstjänsthanteraren misslyckas alla import- eller exportoperationer med lokalt AD utan att **något startautentiseringsuppgifter har** fel.

* Under Windows Loggboken innehåller programhändelseloggen ett fel med **händelse-ID 6000** och meddelandet **"Hanteringsagenten "contoso.com" kunde inte köras eftersom autentiseringsuppgifterna var ogiltiga.**


## <a name="how-to-update-the-synchronization-service-with-new-password-for-ad-ds-account"></a>Så här uppdaterar du synkroniseringstjänsten med nytt lösenord för AD DS-konto
Så här uppdaterar du synkroniseringstjänsten med det nya lösenordet:

1. Starta synkroniseringstjänsthanteraren (START → Synkroniseringstjänst).
</br>![Synkronisera tjänsthanteraren](./media/how-to-connect-sync-change-addsacct-pass/startmenu.png)  

2. Gå till fliken **Kontakter.**

3. Välj den **AD-anslutningsapp** som motsvarar DET AD DS-konto som lösenordet har ändrats för.

4. Välj **Egenskaper**under **Åtgärder**.

5. Välj Anslut till Active **Directory Forest**i popup-dialogrutan:

6. Ange det nya lösenordet för AD DS-kontot i textrutan **Lösenord.**

7. Klicka på **OK** om du vill spara det nya lösenordet och stänga popup-dialogrutan.

8. Starta om synkroniseringstjänsten för Azure AD Connect under Windows Service Control Manager. Detta för att säkerställa att alla referenser till det gamla lösenordet tas bort från minnescachen.

## <a name="next-steps"></a>Nästa steg
**Avsnitt om översikt**

* [Synkronisering av Azure AD Connect: Förstå och anpassa synkronisering](how-to-connect-sync-whatis.md)

* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)
