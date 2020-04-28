---
title: Ändra konto lösen ordet för Azure AD-kopplingen | Microsoft Docs
description: I det här avsnittet beskrivs hur du återställer Azure AD Connector-kontot.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "67204535"
---
# <a name="change-the-azure-ad-connector-account-password"></a>Ändra lösenord för Azure AD Connector-kontot
Azure AD Connector-kontot ska vara kostnads fritt. Om du behöver återställa autentiseringsuppgifterna är det här avsnittet för dig. Till exempel om en global administratör har av misstag kan återställa lösen ordet för kontot med hjälp av PowerShell.

## <a name="reset-the-credentials"></a>Återställ autentiseringsuppgifterna
Om Azure AD Connector-kontot inte kan kontakta Azure AD på grund av autentiseringsproblem kan lösen ordet återställas.

1. Logga in på Azure AD Connect Sync-servern och starta PowerShell.
2. Kör `Add-ADSyncAADServiceAccount`.  
   ![PowerShell-cmdlet addadsyncaadserviceaccount](./media/how-to-connect-azureadaccount/addadsyncaadserviceaccount.png)
3. Ange Azure AD global admin-autentiseringsuppgifter.

Denna cmdlet återställer lösen ordet för tjänst kontot och uppdaterar det både i Azure AD och i Synkroniseringsmotorn.

## <a name="known-issues-these-steps-can-solve"></a>Kända problem de här stegen kan lösa
Det här avsnittet innehåller en lista över fel som har rapporter ATS av kunder som åtgärd ATS av en återställning av autentiseringsuppgifter på Azure AD Connector-kontot.

---
Händelse 6900  
Ett oväntat fel uppstod i servern vid bearbetning av ett meddelande om lösen ords ändring:  
AADSTS70002: fel vid verifiering av autentiseringsuppgifter. AADSTS50054: det gamla lösen ordet används för autentisering.

---
Händelse 659  
Ett fel uppstod vid hämtning av konfigurationen för synkronisering av lösen ords principer. Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException:  
AADSTS70002: fel vid verifiering av autentiseringsuppgifter. AADSTS50054: det gamla lösen ordet används för autentisering.

## <a name="next-steps"></a>Nästa steg
**Översikts avsnitt**

* [Azure AD Connect synkronisering: förstå och anpassa synkronisering](how-to-connect-sync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)

