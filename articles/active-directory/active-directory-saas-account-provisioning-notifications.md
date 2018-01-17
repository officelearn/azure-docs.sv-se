---
title: Kontot som etablerar meddelanden | Microsoft Docs
description: "Lär dig mer om att se till att du meddelas om problem som rör användaretablering som kräver din uppmärksamhet genom att aktivera konto etablering meddelanden."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: a637aac7-f06b-48ef-a66d-639835a8edec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.openlocfilehash: 16ec2b320f733d8828b0046c20f7f7b0e341daf9
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2018
---
# <a name="account-provisioning-notifications"></a>Kontoetableringsmeddelanden
Med användaretablering, kan du automatisera processen för att hantera användare i SaaS-program från tredje part. <br>
Även om detta är en automatiserad process krävs då interaktionen med den här processen. <br>
Detta gäller, till exempel, när lösenordet för det konto som du har konfigurerat för att utbyta data med tredje part SaaS-program har upphört att gälla. 

Genom att aktivera konto etablering meddelanden, kan du se till att du meddelas om problem som rör användaretablering som kräver din uppmärksamhet.

Du aktiverar eller inaktiverar kontot etablering meddelanden som en del av din konfiguration för tredje part SaaS-program för användaretablering.

![Användaretablering][1] 

Om du vill aktivera konto för etablering av meddelanden, markerar du kryssrutan relaterade på den **bekräftelse** dialogrutan sidan och skriv sedan e postalias av mottagaren.

![Kontoetableringsmeddelanden][2]

Du kan ange en distributionslista som mottagaren. Det är dock viktigt att notera att e-postmeddelandet innehåller länkar till rapporter som endast är tillgängliga för Azure AD-administratörer.

Om du har konto etablering meddelanden aktiverad, får e-postmeddelanden om kritiska problem som är relaterade till användaretablering. Men för att undvika en e-överlagring kan får endast du ett e-postmeddelande per dag för varje SaaS-program som e-postmeddelandet har aktiverats för.

## <a name="related-articles"></a>Relaterade artiklar
* [Artikelindex för programhantering i Azure Active Directory](active-directory-apps-index.md)
* [Automatisera användaren etablering/avetablering för SaaS-appar](active-directory-saas-app-provisioning.md)
* [Anpassa attributmappning för Användaretablering](active-directory-saas-customizing-attribute-mappings.md)
* [Skriva uttryck för attributmappning](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Omfångsfilter för Användaretablering](active-directory-saas-scoping-filters.md)
* [Använda SCIM för att aktivera automatisk etablering av användare och grupper från Azure Active Directory till program](active-directory-scim-provisioning.md)
* [Lista över självstudier om hur du integrerar SaaS-appar](active-directory-saas-tutorial-list.md)

<!--Image references-->
[1]: ./media/active-directory-saas-account-provisioning-notifications/ic766307.png
[2]: ./media/active-directory-saas-account-provisioning-notifications/ic766308.png
