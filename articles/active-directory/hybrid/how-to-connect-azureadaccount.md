---
title: Ändra lösenordet för Azure AD Connector | Microsoft Docs
description: Det här avsnittet beskrivs hur du återställer Azure AD-anslutningskontot.
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
ms.openlocfilehash: d68c190b51b9bbb5faf21e8ea75b07d1a82005e5
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64571415"
---
# <a name="change-the-azure-ad-connector-account-password"></a>Ändra lösenordet för Azure AD-koppling
Azure AD-anslutningskontot ska vara service kostnadsfritt. Om du behöver återställa autentiseringsuppgifterna är det här avsnittet för dig. Till exempel om en Global administratör har av misstag återställer lösenordet för kontot med hjälp av PowerShell.

## <a name="reset-the-credentials"></a>Återställa autentiseringsuppgifterna
Om Azure AD-anslutningskontot inte kan kontakta Azure AD på grund av autentiseringsproblem, kan du återställa lösenordet.

1. Logga in på Azure AD Connect-synkroniseringsservern och starta PowerShell.
2. Kör `Add-ADSyncAADServiceAccount`.  
   ![PowerShell-cmdlet addadsyncaadserviceaccount](./media/how-to-connect-azureadaccount/addadsyncaadserviceaccount.png)
3. Ange autentiseringsuppgifter för Azure AD-Global administratör.

Denna cmdlet återställer lösenordet för tjänstkontot och uppdatera den både i Azure AD och i Synkroniseringsmotorn.

## <a name="known-issues-these-steps-can-solve"></a>Kända problem som kan lösa de här stegen
Det här avsnittet är en lista över fel som rapporterats av kunder som korrigerades med en återställning på Azure AD-anslutningskontot autentiseringsuppgifter.

- - -
Händelsen 6900  
Ett oväntat fel uppstod vid bearbetning av ett meddelande om lösenordsändring:  
AADSTS70002: Fel vid verifiering av autentiseringsuppgifter. AADSTS50054: Det gamla lösenordet krävs för autentiseringen.

- - -
Händelsen 659  
Fel vid hämtning av lösenord principkonfigurationen synkronisering. Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException:  
AADSTS70002: Fel vid verifiering av autentiseringsuppgifter. AADSTS50054: Det gamla lösenordet krävs för autentiseringen.

## <a name="next-steps"></a>Nästa steg
**Översiktsavsnitt**

* [Azure AD Connect-synkronisering: Förstå och anpassa synkronisering](how-to-connect-sync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)

