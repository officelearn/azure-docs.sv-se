---
title: Så här hanterar du produktaktivering rollinställningar | Microsoft Docs
description: Lär dig hur du ändrar standardinställningarna för privilegierade identiteter med Azure Active Directory Privileged Identity Management-tillägget.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.component: protection
ms.date: 06/06/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 4557457e28a9a9b8bcd7f5c3bda40fbba8cdd24b
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2018
ms.locfileid: "35233313"
---
# <a name="how-to-manage-role-activation-settings-in-azure-ad-privileged-identity-management"></a>Så här hanterar du inställningar för aktivering av rollen i Azure AD Privileged Identity Management
En administratör av Privilegierade roller kan anpassa Azure AD Privileged Identity Management (PIM) i organisationen, inklusive ändra upplevelsen för en användare som aktivera en berättigad rolltilldelning.

## <a name="manage-the-role-activation-settings"></a>Hantera produktaktivering rollinställningar
1. Gå till den [Azure-portalen](https://portal.azure.com) och välj den **Azure AD Privileged Identity Management** app från instrumentpanelen.
2. Välj **hantera Privilegierade roller** > **inställningar** > **Privilegierade roller**.
3. Välj rollen vars inställningar du vill hantera.

Det finns ett antal inställningar som du kan konfigurera på inställningssidan för varje roll. Dessa inställningar påverkar endast användare som är berättigade administratörer, inte permanenta administratörer.

**Aktiveringar**: tid, i timmar, som en roll förblir aktiv innan den upphör. Detta kan vara mellan 1 och 72 timmar.

**Meddelanden**: du kan välja huruvida systemet skickar e-post till administratörer bekräftar att de har aktiverat en roll. Detta kan vara användbart för att upptäcka otillåten eller otillåtna aktiveringar.

**Incident/begäran biljett**: du kan välja om du vill kräver berättigade administratörer att inkludera en Biljettnummer när de aktiverar rollen. Detta kan vara användbart när du utför roll åtkomst granskningar.

**Multifaktorautentisering**: du kan välja om du vill att användare ska verifiera sin identitet med MFA innan de kan aktivera sina roller eller inte. De behöver bara kontrollera detta en gång per session inte varje gång de aktivera en roll. Det finns två tips att tänka på när du aktiverar MFA:

* Användare som har Microsoft-konton för sina e-postadresser (vanligtvis @outlook.com, men inte alltid) kan inte registrera dig för Azure MFA. Om du vill tilldela roller till användare med Microsoft-konton, bör du göra dem permanenta administratörer eller inaktivera MFA för rollen.
* Du kan inte inaktivera MFA för mycket Privilegierade roller för Azure AD och Office 365. Det här är en säkerhetsfunktion eftersom dessa roller ska skyddas noggrant:  
  
  * Programadministratör
  * Programmet proxyserverns administratör
  * Faktureringsadministratör  
  * Efterlevnadsadministratör  
  * CRM-tjänstadministratör
  * Kunden LockBox åtkomst godkännare
  * Directory-skrivare  
  * Exchange-administratör  
  * Global administratör
  * Administratör för Intune-tjänsten
  * Administratör för postlåda  
  * Partnersupport, nivå 1  
  * Partnersupport, nivå 2  
  * Privilegierad rolladministratör   
  * Säkerhetsadministratör  
  * SharePoint-administratör  
  * Skype for Business-administratör  
  * Kontoadministratör för användaren  

Läs mer om hur du använder MFA med PIM [så kräver MFA](active-directory-privileged-identity-management-how-to-require-mfa.md).

<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Nästa steg
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

