---
title: Azure AD Connect moln etablering ny agent konfiguration
description: I det här avsnittet beskrivs hur du installerar Cloud-etablering.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f08a1359cfd8a2793d92315a6b03567b0b3f847d
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74997130"
---
# <a name="azure-ad-connect-cloud-provisioning-new-configuration"></a>Azure AD Connect ny konfiguration för moln etablering

När du har installerat agenten måste du logga in på Azure Portal och konfigurera etableringen.  Använd följande steg för att aktivera agenten.

## <a name="configure-provisioning"></a>Konfigurera etablering
Använd följande steg för att konfigurera etablering:

1.  I Azure AD-portalen klickar du på **Azure Active Directory**
2.  Klicka på **Azure AD Connect**
3.  Välj **Hantera etablering (förhands granskning)** 
![](media/how-to-configure/manage1.png)

4.  Klicka på **ny konfiguration**.
5.  På konfigurations skärmen fylls den lokala domänen i förväg
6. Ange ett **e-postmeddelande för avisering**. Det här e-postmeddelandet kommer att meddelas när etableringen inte är felfri.  
8. Flytta väljaren för att **Aktivera** och klicka på **Spara**.
![](media/tutorial-single-forest/configure2.png)

## <a name="scoping-provisioning-to-specific-users-and-groups"></a>Omfattnings etablering för vissa användare och grupper
Om du vill att agenten bara ska synkronisera vissa användare och grupper kan du göra detta. Du kan använda ett omfång med lokala AD-grupper eller organisationsenheter. Det går inte att konfigurera grupper och organisationsenheter i en konfiguration. 

1.  I Azure AD-portalen klickar du på **Azure Active Directory**
2.  Klicka på **Azure AD Connect**
3.  Välj **Hantera etablering (förhands granskning)**
4.  Klicka på konfigurationen under **konfiguration** .  
![](media/how-to-configure/scope1.png)

5.  Under **Konfigurera**väljer du **alla användare** för att ändra omfånget för konfigurations regeln.
![](media/how-to-configure/scope2.png)

6. Till höger kan du ändra omfattningen så att den endast inkluderar säkerhets grupper genom att ange gruppens unika namn och klicka på **Lägg till**.
![](media/how-to-configure/scope3.png)

7. Eller ändra det så att endast vissa organisationsenheter tas med. Klicka på **färdig** och **Spara**.
![](media/how-to-configure/scope4.png)


## <a name="restart-provisioning"></a>Starta om etablering 
Om du inte vill vänta på nästa schemalagda körning kan du utlösa etablerings körningen med hjälp av knappen starta om etablering. 
1.  I Azure AD-portalen klickar du på **Azure Active Directory**
2.  Klicka på **Azure AD Connect**
3.  Välj **Hantera etablering (förhands granskning)**
4.  Klicka på konfigurationen under **konfiguration** .  
![](media/how-to-configure/scope1.png)

5.  Klicka på **starta om etablering**längst upp.

## <a name="removing-a-configuration"></a>Ta bort en konfiguration
Om du vill ta bort en konfiguration kan du göra det med hjälp av följande steg.

1.  I Azure AD-portalen klickar du på **Azure Active Directory**
2.  Klicka på **Azure AD Connect**
3.  Välj **Hantera etablering (förhands granskning)**
4.  Klicka på konfigurationen under **konfiguration** .  
![](media/how-to-configure/scope1.png)

5.  Klicka på **ta bort**högst upp.
![](media/how-to-configure/remove1.png)

>[!IMPORTANT]
>Det finns ingen bekräftelse innan du tar bort en konfiguration så se till att det är den åtgärd du vill utföra innan du klickar på **ta bort**.


## <a name="next-steps"></a>Nästa steg 

- [Vad är etablering?](what-is-provisioning.md)
- [Vad är Azure AD Connect Cloud revisioner?](what-is-cloud-provisioning.md)
