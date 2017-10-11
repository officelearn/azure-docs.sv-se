---
title: "Azure AD Connect-synkronisering: ändra lösenordet för AD DS | Microsoft Docs"
description: "Det här avsnittet dokumentet beskriver hur du uppdaterar Azure AD Connect när lösenordet för AD DS-konto har ändrats."
services: active-directory
keywords: "AD DS-konto, Active Directory-konto, lösenord"
documentationcenter: 
author: cychua
manager: femila
editor: 
ms.assetid: 76b19162-8b16-4960-9e22-bd64e6675ecc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 14e16a238e60ecfeeb3cbf88c3922a79349dcc75
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="changing-the-ad-ds-account-password"></a>Ändra lösenordet för AD DS
AD DS-konto refererar till det användarkonto som används av Azure AD Connect för att kommunicera med lokala Active Directory. Om du ändrar lösenordet för AD DS-konto, måste du uppdatera Azure AD Connect-synkroniseringstjänsten med det nya lösenordet. Annars synkronisering kan inte längre synkronisera korrekt med lokala Active Directory och visas följande felmeddelanden:

* I Synchronization Service Manager, alla importera eller exportera igen med lokala AD misslyckas med **inga start autentiseringsuppgifter** fel.

* Under Windows Loggboken i programmets händelselogg innehåller ett fel med **händelse-ID 6000** och meddelandet **'hanteringsagenten ”contoso.com” kunde inte köras eftersom autentiseringsuppgifterna var ogiltiga '**.


## <a name="how-to-update-the-synchronization-service-with-new-password-for-ad-ds-account"></a>Hur du uppdaterar synkroniseringstjänsten med nytt lösenord för AD DS-konto
Uppdatera synkroniseringstjänsten med det nya lösenordet:

1. Starta hanteraren för synkroniseringstjänsten (START → synkroniseringstjänsten).
</br>![Synkronisering av Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/startmenu.png)  

2. Gå till den **kopplingar** fliken.

3. Välj den **AD-koppling** som motsvarar AD DS-konto som dess lösenord har ändrats.

4. Under **åtgärder**väljer **egenskaper**.

5. I popup-fönstret väljer **Anslut till Active Directory-skog**:

6. Ange det nya lösenordet för AD DS-konto i den **lösenord** textruta.

7. Klicka på **OK** att spara det nya lösenordet och stänga popup-fönstret.

8. Starta om Azure AD Connect synkroniseringstjänsten under Windows Service Control Manager. Detta är att säkerställa att alla referenser till det gamla lösenordet tas bort från cacheminnet.

## <a name="next-steps"></a>Nästa steg
**Översiktsavsnitt**

* [Azure AD Connect-synkronisering: Förstå och anpassa synkronisering](active-directory-aadconnectsync-whatis.md)

* [Integrera dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md)
