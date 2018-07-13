---
title: Så här hanterar du rollaktiveringsinställningar | Microsoft Docs
description: Lär dig hur du ändrar standardinställningarna för privilegierade identiteter med Azure Active Directory Privileged Identity Management-tillägget.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: protection
ms.date: 06/06/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 13e00ac57bfc867ad13ae3d7958fca37eaf5beee
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38590452"
---
# <a name="how-to-manage-role-activation-settings-in-azure-ad-privileged-identity-management"></a>Så här hanterar du rollaktiveringsinställningar i Azure AD Privileged Identity Management
En administratör av Privilegierade roller kan anpassa Azure AD Privileged Identity Management (PIM) i deras organisation, inklusive ändra upplevelsen för en användare som är att aktivera en berättigad rolltilldelning.

## <a name="manage-the-role-activation-settings"></a>Hantera inställningar för aktivering av roll
1. Gå till den [Azure-portalen](https://portal.azure.com) och välj den **Azure AD Privileged Identity Management** app från instrumentpanelen.
2. Välj **hantera Privilegierade roller** > **inställningar** > **Privilegierade roller**.
3. Välj rollen vars inställningar du vill hantera.

Det finns ett antal inställningar som du kan konfigurera på inställningssidan för varje roll. De här inställningarna påverkar endast användare som är berättigade administratörer, inte permanenta administratörer.

**Aktiveringar**: tiden, i timmar, som en roll förblir aktiv innan den upphör. Detta kan vara mellan 1 och 72 timmar.

**Meddelanden**: du kan välja huruvida systemet skickar e-postmeddelanden till administratörer bekräftar att de har aktiverat en roll. Detta kan vara användbart för identifiering av obehöriga eller otillåtna aktiveringar.

**Biljett för incident/begäran**: du kan välja om du vill kräva berättigade administratörer att inkludera en Biljettnummer när de aktiverar sina roller eller inte. Detta kan vara användbart när du utför rollen åtkomst granskningar.

**Multifaktorautentisering**: du kan välja huruvida användarna ska verifiera sin identitet med MFA innan du kan aktivera sina roller eller inte. De behöver bara verifiera det här en gång per session, inte varje gång de aktivera en roll. Det finns två tips att tänka på när du aktiverar MFA:

* Användare som har Microsoft-konton för sina e-postadresser (vanligtvis @outlook.com, men inte alltid) det går inte att registrera dig för Azure MFA. Om du vill tilldela roller till användare med Microsoft-konton bör du göra dem permanenta administratörer eller inaktivera MFA för rollen.
* Du kan inte inaktivera MFA för mycket Privilegierade roller för Azure AD och Office 365. Det här är en säkerhetsfunktion eftersom dessa roller noggrant ska skyddas:  
  
  * Programadministratör
  * Programadministratör Proxy server
  * Faktureringsadministratör  
  * Efterlevnadsadministratör  
  * CRM-tjänstadministratör
  * Customer LockBox åtkomst godkännaren
  * Directory-skrivare  
  * Exchange-administratör  
  * Global administratör
  * Administratör för Intune-tjänsten
  * Postlådeadministratör  
  * Partnersupport, nivå 1  
  * Partnersupport, nivå 2  
  * Privilegierad rolladministratör   
  * Säkerhetsadministratör  
  * SharePoint-administratör  
  * Skype for Business-administratör  
  * Användarkonto-administratör  

Läs mer om hur du använder MFA med PIM [hur du kräver MFA](pim-how-to-require-mfa.md).

<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Nästa steg
[!INCLUDE [active-directory-privileged-identity-management-toc](../../../includes/active-directory-privileged-identity-management-toc.md)]

