---
title: Azure AD Connect moln etablering ny agent konfiguration
description: Den här artikeln beskriver hur du installerar Cloud-etablering.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c086227d23901cb7904d8cc0a768e9f4b5ad43db
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "77620977"
---
# <a name="create-a-new-configuration-for-azure-ad-connect-cloud-based-provisioning"></a>Skapa en ny konfiguration för Azure AD Connect molnbaserad etablering

När du har installerat agenten måste du logga in på Azure Portal och konfigurera Azure Active Directory (Azure AD) Anslut till moln etablering. Följ dessa steg om du vill aktivera agenten.

## <a name="configure-provisioning"></a>Konfigurera etablering
Följ dessa steg om du vill konfigurera etableringen.

1.  Välj **Azure Active Directory** i Azure Portal.
1.  Välj **Azure AD Connect**.
1.  Välj **Hantera etablering (för hands version)**.

    ![Hantera etablering (för hands version)](media/how-to-configure/manage1.png)

1.  Välj **ny konfiguration**.
1.  På konfigurations skärmen är den lokala domänen förifylld.
1.  Ange ett **e-postmeddelande för avisering**. Det här e-postmeddelandet kommer att meddelas när etableringen inte är felfritt.
1.  Flytta väljaren för att **Aktivera**och välj **Spara**.

    ![Azure AD-etablering (för hands version)](media/tutorial-single-forest/configure2.png)

## <a name="scope-provisioning-to-specific-users-and-groups"></a>Omfattnings etablering för vissa användare och grupper
Du kan begränsa agenten till att synkronisera vissa användare och grupper genom att använda lokala Active Directory grupper eller organisationsenheter. Du kan inte konfigurera grupper och organisationsenheter i en konfiguration. 

1.  Välj **Azure Active Directory** i Azure Portal.
1.  Välj **Azure AD Connect**.
1.  Välj **Hantera etablering (för hands version)**.
1.  Under **konfiguration**väljer du din konfiguration.

    ![Konfigurations avsnitt](media/how-to-configure/scope1.png)

1.  Under **Konfigurera**väljer du **alla användare** för att ändra omfånget för konfigurations regeln.

    ![Alternativet alla användare](media/how-to-configure/scope2.png)

1. Till höger kan du ändra omfattningen så att endast säkerhets grupper tas med. Ange gruppens unika namn och välj **Lägg till**.

    ![Alternativet valda säkerhets grupper](media/how-to-configure/scope3.png)

1.  Eller så kan du ändra omfattningen så att endast vissa organisationsenheter tas med. Välj **färdig** och **Spara**.  
2.  När du har ändrat omfattning bör du [starta om etableringen](#restart-provisioning) för att initiera en omedelbar synkronisering av ändringarna.

    ![Alternativ för valda organisationsenheter](media/how-to-configure/scope4.png)


## <a name="restart-provisioning"></a>Starta om etablering 
Om du inte vill vänta på nästa schemalagda körning utlöser du etablerings körningen med hjälp av knappen **starta om etablering** . 
1.  Välj **Azure Active Directory** i Azure Portal.
1.  Välj **Azure AD Connect**.
1.  Välj **Hantera etablering (för hands version)**.
1.  Under **konfiguration**väljer du din konfiguration.

    ![Konfigurations val för att starta om etableringen](media/how-to-configure/scope1.png)

1.  Välj **starta om etablering**längst upp.

## <a name="remove-a-configuration"></a>Ta bort en konfiguration
Följ dessa steg om du vill ta bort en konfiguration.

1.  Välj **Azure Active Directory** i Azure Portal.
1.  Välj **Azure AD Connect**.
1.  Välj **Hantera etablering (för hands version)**.
1.  Under **konfiguration**väljer du din konfiguration.

    ![Konfigurations val för att ta bort konfiguration](media/how-to-configure/scope1.png)

1.  Klicka på **ta bort**överst på konfigurations skärmen.

    ![Knappen Ta bort](media/how-to-configure/remove1.png)

>[!IMPORTANT]
>Det finns ingen bekräftelse innan du tar bort en konfiguration. Kontrol lera att detta är den åtgärd du vill vidta innan du väljer **ta bort**.


## <a name="next-steps"></a>Nästa steg 

- [Vad är etablering?](what-is-provisioning.md)
- [Vad är Azure AD Connect-molnetablering?](what-is-cloud-provisioning.md)
