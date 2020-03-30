---
title: Azure AD Connect-molnetablering av ny agentkonfiguration
description: I den här artikeln beskrivs hur du installerar molnetablering.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77620977"
---
# <a name="create-a-new-configuration-for-azure-ad-connect-cloud-based-provisioning"></a>Skapa en ny konfiguration för Azure AD Connect molnbaserad etablering

När du har installerat agenten måste du logga in på Azure-portalen och konfigurera Azure Active Directory (Azure AD) Connect-molnetablering. Följ dessa steg för att aktivera agenten.

## <a name="configure-provisioning"></a>Konfigurera etablering
Så här konfigurerar du etablering.

1.  Välj **Azure Active Directory** i Azure Portal.
1.  Välj **Azure AD Connect**.
1.  Välj **Hantera etablering (förhandsgranskning)**.

    ![Hantera etablering (förhandsgranskning)](media/how-to-configure/manage1.png)

1.  Välj **Ny konfiguration**.
1.  På konfigurationsskärmen är den lokala domänen förifylld.
1.  Ange ett **e-postmeddelande**. Det här e-postmeddelandet meddelas när etableringen inte är felfri.
1.  Flytta väljaren till **Aktivera**och välj **Spara**.

    ![Azure AD-etablering (förhandsversion)](media/tutorial-single-forest/configure2.png)

## <a name="scope-provisioning-to-specific-users-and-groups"></a>Tillhandahållande av omfattning till specifika användare och grupper
Du kan begränsa agenten så att den synkroniserar specifika användare och grupper med hjälp av lokala Active Directory-grupper eller organisationsenheter. Du kan inte konfigurera grupper och organisationsenheter i en konfiguration. 

1.  Välj **Azure Active Directory** i Azure Portal.
1.  Välj **Azure AD Connect**.
1.  Välj **Hantera etablering (förhandsgranskning)**.
1.  Välj konfiguration under **Konfiguration.**

    ![Avsnittet Konfiguration](media/how-to-configure/scope1.png)

1.  Under **Konfigurera**väljer du **Alla användare** om du vill ändra konfigurationsregelns omfattning.

    ![Alla användare alternativ](media/how-to-configure/scope2.png)

1. Till höger kan du ändra omfånget så att det bara omfattar säkerhetsgrupper. Ange det unika namnet på gruppen och välj **Lägg till**.

    ![Alternativet Markerade säkerhetsgrupper](media/how-to-configure/scope3.png)

1.  Du kan också ändra omfånget så att det bara omfattar specifika organisationsenheter. Välj **Klar** och **Spara**.  
2.  När du har ändrat omfånget bör du [starta om etableringen](#restart-provisioning) för att initiera en omedelbar synkronisering av ändringarna.

    ![Alternativet Valda organisationsenheter](media/how-to-configure/scope4.png)


## <a name="restart-provisioning"></a>Starta om etablering 
Om du inte vill vänta på nästa schemalagda körning utlöser du etableringen med hjälp av knappen **Starta om etablering.** 
1.  Välj **Azure Active Directory** i Azure Portal.
1.  Välj **Azure AD Connect**.
1.  Välj **Hantera etablering (förhandsgranskning)**.
1.  Välj konfiguration under **Konfiguration.**

    ![Konfigurationsval för att starta om etablering](media/how-to-configure/scope1.png)

1.  Högst upp väljer du **Starta om etableringen**.

## <a name="remove-a-configuration"></a>Ta bort en konfiguration
Så här tar du bort en konfiguration.

1.  Välj **Azure Active Directory** i Azure Portal.
1.  Välj **Azure AD Connect**.
1.  Välj **Hantera etablering (förhandsgranskning)**.
1.  Välj konfiguration under **Konfiguration.**

    ![Val av konfiguration för att ta bort konfigurationen](media/how-to-configure/scope1.png)

1.  Högst upp på konfigurationsskärmen väljer du **Ta bort**.

    ![Knappen Ta bort](media/how-to-configure/remove1.png)

>[!IMPORTANT]
>Det finns ingen bekräftelse innan du tar bort en konfiguration. Kontrollera att det här är den åtgärd du vill vidta innan du väljer **Ta bort**.


## <a name="next-steps"></a>Nästa steg 

- [Vad är etablering?](what-is-provisioning.md)
- [Vad är Azure AD Connect-molnetablering?](what-is-cloud-provisioning.md)
