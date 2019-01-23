---
title: 'Azure AD Connect-synkronisering: Så här hanterar du Azure AD-tjänstkontot | Microsoft Docs'
description: Det här avsnittet beskrivs hur du återställer Azure AD-tjänstkontot.
services: active-directory
keywords: AADSTS70002, AADSTS50054, hur du återställer lösenordet för Azure AD Connect-synkronisering Connector-tjänstkontot
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6077043a-27f1-4304-a44b-81dc46620f24
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 8fd8667484284fba8ba30e2f078538e7ed1888ef
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54461374"
---
# <a name="azure-ad-connect-sync-how-to-manage-the-azure-ad-service-account"></a>Azure AD Connect-synkronisering: Så här hanterar du Azure AD-tjänstkontot
Tjänstkontot som används av Azure AD-anslutningen ska vara service kostnadsfritt. Om du behöver återställa autentiseringsuppgifterna är det här avsnittet för dig. Till exempel om en Global administratör har av misstag återställa lösenordet för kontot med hjälp av PowerShell.

## <a name="reset-the-credentials"></a>Återställa autentiseringsuppgifterna
Om det tjänstkonto som definierats i Azure AD Connector inte kan kontakta Azure AD på grund av autentiseringsproblem, kan du återställa lösenordet.

1. Logga in på Azure AD Connect-synkroniseringsservern och starta PowerShell.
2. Kör `Add-ADSyncAADServiceAccount`.  
   ![PowerShell-cmdlet addadsyncaadserviceaccount](./media/how-to-connect-azureadaccount/addadsyncaadserviceaccount.png)
3. Ange autentiseringsuppgifter för Azure AD-Global administratör.

Denna cmdlet återställer lösenordet för tjänstkontot och uppdatera den både i Azure AD och i Synkroniseringsmotorn.

## <a name="known-issues-these-steps-can-solve"></a>Kända problem som kan lösa de här stegen
Det här avsnittet är en lista över fel som rapporterats av kunder som korrigerades med ett autentiseringsuppgifter nollställs Azure AD-tjänstkontot.

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

