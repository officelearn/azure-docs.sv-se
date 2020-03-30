---
title: Hantera lokala administratörer på Azure AD-anslutna enheter
description: Lär dig hur du tilldelar Azure-roller till den lokala administratörsgruppen för en Windows-enhet.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc1812d955590ec0c7372e1311c9d69f93b9957c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128886"
---
# <a name="how-to-manage-the-local-administrators-group-on-azure-ad-joined-devices"></a>Hantera den lokala administratörsgruppen på Azure AD-anslutna enheter

Om du vill hantera en Windows-enhet måste du vara medlem i den lokala administratörsgruppen. Som en del av Azure Active Directory (Azure AD) anslutningsprocessen uppdaterar Azure AD medlemskap i den här gruppen på en enhet. Du kan anpassa medlemskapsuppdateringen så att den uppfyller dina affärskrav. En uppdatering av medlemskap är till exempel användbar om du vill att din helpdesk-personal ska kunna utföra uppgifter som kräver administratörsrättigheter på en enhet.

I den här artikeln beskrivs hur medlemskapsuppdateringen fungerar och hur du kan anpassa den under en Azure AD-koppling. Innehållet i den här artikeln gäller inte för en **hybrid** Azure AD-koppling.

## <a name="how-it-works"></a>Hur det fungerar

När du ansluter en Windows-enhet till Azure AD med en Azure AD-koppling lägger Azure AD till följande säkerhetsprinciper i den lokala administratörsgruppen på enheten:

- Den globala administratörsrollen Azure AD
- Administratörsrollen för Azure AD-enheten 
- Användaren som utför Azure AD-kopplingen   

Genom att lägga till Azure AD-roller i den lokala administratörsgruppen kan du uppdatera användare som kan hantera en enhet när som helst i Azure AD utan att ändra något på enheten. För närvarande kan du inte tilldela grupper till en administratörsroll.
Azure AD lägger också till Azure AD-enhetsadministratörsrollen i den lokala administratörsgruppen för att stödja principen om lägsta behörighet (PoLP). Förutom de globala administratörerna kan du också aktivera användare som *bara* har tilldelats rollen enhetsadministratör för att hantera en enhet. 

## <a name="manage-the-global-administrators-role"></a>Hantera rollen globala administratörer

Så här visar och uppdaterar du medlemskapet i den globala administratörsrollen:

- [Visa alla medlemmar i en administratörsroll i Azure Active Directory](../users-groups-roles/directory-manage-roles-portal.md)
- [Tilldela administratörsroller till en användare i Azure Active Directory](../fundamentals/active-directory-users-assign-role-azure-portal.md)


## <a name="manage-the-device-administrator-role"></a>Hantera enhetsadministratörsrollen 

I Azure-portalen kan du hantera enhetsadministratörsrollen på sidan **Enheter.** Så här öppnar du sidan **Enheter:**

1. Logga in på din [Azure-portal](https://portal.azure.com) som global administratör.
1. Sök efter och välj *Azure Active Directory*.
1. Klicka på **Enheter**i avsnittet **Hantera.**
1. Klicka på **Enhetsinställningar**på sidan **Enheter** .

Om du vill ändra enhetsadministratörsrollen konfigurerar du **Ytterligare lokala administratörer på Azure AD-anslutna enheter**.  

![Ytterligare lokala administratörer](./media/assign-local-admin/10.png)

>[!NOTE]
> Det här alternativet kräver en Azure AD Premium-klientorganisation. 

Enhetsadministratörer tilldelas alla Azure AD-anslutna enheter. Du kan inte begränsa enhetsadministratörer till en viss uppsättning enheter. Att uppdatera rollen enhetsadministratör påverkar inte nödvändigtvis de berörda användarna omedelbart. På enheter där en användare redan är inloggad sker privilegieuppdateringen när *båda* åtgärderna nedan inträffar:

- 4 timmar har gått för Azure AD att utfärda en ny primär uppdateringstoken med rätt behörighet. 
- Användaren loggar ut och loggar in igen, inte lås/lås upp, för att uppdatera sin profil.

## <a name="manage-regular-users"></a>Hantera vanliga användare

Som standard lägger Azure AD till användaren som utför Azure AD-kopplingen till administratörsgruppen på enheten. Om du vill förhindra att vanliga användare blir lokala administratörer har du följande alternativ:

- [Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot) - Windows Autopilot ger dig en möjlighet att förhindra att primär användare som utför kopplingen blir en lokal administratör. Du kan åstadkomma detta genom [att skapa en Autopilot-profil](/intune/enrollment-autopilot#create-an-autopilot-deployment-profile).
- [Massregistrering](/intune/windows-bulk-enroll) - En Azure AD-koppling som utförs i samband med en massregistrering sker i kontexten för en användare som skapats automatiskt. Användare som loggar in efter att en enhet har anslutits läggs inte till i administratörsgruppen.   

## <a name="manually-elevate-a-user-on-a-device"></a>Höjer en användare manuellt på en enhet 

Förutom att använda Azure AD-anslutningsprocessen kan du också manuellt höja en vanlig användare för att bli en lokal administratör på en viss enhet. Det här steget kräver att du redan är medlem i den lokala administratörsgruppen. 

Från och med **Windows 10 1709-versionen** kan du utföra den här uppgiften från **Inställningar -> Konton -> Andra användare**. Välj **Lägg till en arbets- eller skolanvändare,** ange användarens UPN under **Användarkonto** och välj *Administratör* under **Kontotyp**  
 
Dessutom kan du också lägga till användare med kommandotolken:

- Om klientanvändarna synkroniseras från lokala Active `net localgroup administrators /add "Contoso\username"`Directory använder du .
- Om dina klientanvändare skapas i Azure AD använder du`net localgroup administrators /add "AzureAD\UserUpn"`

## <a name="considerations"></a>Överväganden 

Du kan inte tilldela grupper till enhetsadministratörsrollen, endast enskilda användare tillåts.

Enhetsadministratörer tilldelas alla Azure AD-anslutna enheter. De kan inte begränsas till en viss uppsättning enheter.

När du tar bort användare från rollen enhetsadministratör har de fortfarande den lokala administratörsbehörigheten på en enhet så länge de är inloggade på den. Privilegiet återkallas under nästa inloggning, eller efter 4 timmar när en ny primär uppdateringstoken utfärdas.

## <a name="next-steps"></a>Nästa steg

- Om du vill ha en översikt över hantering av enheter i Azure-portalen kan du läsa om att [hantera enheter med Azure-portalen](device-management-azure-portal.md)
- Mer information om enhetsbaserad villkorlig åtkomst finns i [konfigurera Azure Active Directory-enhetsbaserade principer för villkorlig åtkomst](../conditional-access/require-managed-devices.md).
