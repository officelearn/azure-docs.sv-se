---
title: Ändra lösenordet för Azure AD Connector-kontot | Microsoft-dokument
description: Det här avsnittet dokumenterar hur du återställer Azure AD Connector-kontot.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6077043a-27f1-4304-a44b-81dc46620f24
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ea151ee79fccd66f1d9422744d8f57829677ec0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67204535"
---
# <a name="change-the-azure-ad-connector-account-password"></a>Ändra lösenord för Azure AD Connector-kontot
Azure AD Connector-kontot ska vara tjänstfritt. Om du behöver återställa dess autentiseringsuppgifter, då det här avsnittet är för dig. Om en global administratör till exempel av misstag har återställt lösenordet på kontot med PowerShell.

## <a name="reset-the-credentials"></a>Återställa autentiseringsuppgifterna
Om Azure AD Connector-kontot inte kan kontakta Azure AD på grund av autentiseringsproblem kan lösenordet återställas.

1. Logga in på Azure AD Connect-synkroniseringsservern och starta PowerShell.
2. Kör `Add-ADSyncAADServiceAccount`.  
   ![PowerShell cmdlet addadsyncaadserviceaccount](./media/how-to-connect-azureadaccount/addadsyncaadserviceaccount.png)
3. Ange Azure AD Global-administratörsautentiseringsuppgifter.

Den här cmdleten återställer lösenordet för tjänstkontot och uppdaterar det både i Azure AD och i synkroniseringsmotorn.

## <a name="known-issues-these-steps-can-solve"></a>Kända problem dessa steg kan lösa
Det här avsnittet är en lista över fel som rapporterats av kunder som har åtgärdats av en återställning av autentiseringsuppgifter på Azure AD Connector-kontot.

---
Händelse 6900  
Servern påträffade ett oväntat fel när ett meddelande om lösenordsändring behandlades:  
AADSTS70002: Fel uppstod när autentiseringsuppgifter valideras. AADSTS50054: Gammalt lösenord används för autentisering.

---
Händelse 659  
Fel vid hämtning av synkronisering av lösenordsprincipsynkronisering. Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException:  
AADSTS70002: Fel uppstod när autentiseringsuppgifter valideras. AADSTS50054: Gammalt lösenord används för autentisering.

## <a name="next-steps"></a>Nästa steg
**Avsnitt om översikt**

* [Synkronisering av Azure AD Connect: Förstå och anpassa synkronisering](how-to-connect-sync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)

