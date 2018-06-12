---
title: Konfigurera Azure Active Directory-anslutna enheter | Microsoft Docs
description: Lär dig hur du ställer in Azure Active Directory anslutna enheter.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 791181f11af38e4b78743ddb372207bd21893a6b
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35260937"
---
# <a name="set-up-azure-active-directory-joined-devices"></a>Konfigurera Azure Active Directory-anslutna enheter

Med hantering av enheter i Azure Active Directory (Azure AD), kan du se till att dina användare har åtkomst till dina resurser från enheter som uppfyller dina krav för säkerhet och efterlevnad. Mer information finns i [introduktion till hantering av enheter i Azure Active Directory](device-management-introduction.md).

Om du vill få Windows 10 enheter som ägs av arbetet under kontroll av Azure AD åstadkommer detta genom att konfigurera Azure AD anslutna enheter. Det här avsnittet ger relaterade steg. 


## <a name="prerequisites"></a>Förutsättningar

Registreringstjänsten för enheter måste konfigureras så att du kan registrera enheter för att ansluta till en Windows 10-enhet. Förutom att du har behörighet att ansluta enheter i din Azure AD-klient, måste du ha färre enheter som har registrerats än den konfigurerade maximalt. Mer information finns i [konfigurera Enhetsinställningar](device-management-azure-portal.md#configure-device-settings).



## <a name="what-you-should-know"></a>Vad du bör känna till


- Windows ansluter enheten i organisationens katalog i Azure AD.

- Du kan behöva gå igenom multifaktorautentisering utmaning. Denna utmaning kan konfigureras av IT-administratören.

- Azure AD kontrollerar om enheten kräver registrering för hantering av mobila enheter och registrerar den om tillämpligt.

- Om du använder en hanterad går Windows du till skrivbordet via automatisk inloggning.

- Om du är en federerad användare, måste du logga in med dina autentiseringsuppgifter.

- Om du är federerad måste identitetsprovider stödja slutpunkten för WS-Fed och WS-Trust användarnamn/lösenord. Detta kan vara version 1.3 eller 2005. Den här protokollstöd krävs till både ansluta enheten till Azure AD och logga in på enheten med ett lösenord. 




## <a name="joining-a-device"></a>Koppla en enhet

Det här avsnittet ger dig stegen för att ansluta din Windows 10-enhet till din Azure AD. En enhet som har anslutits visas som **ansluten till \<din Azure AD\>**.

![Ansluten](./media/device-management-azuread-joined-devices-setup/13.png)


**Att ansluta till Windows 10-enhet:**

1. I den **starta** -menyn klickar du på **inställningar**.

    ![Inställningar](./media/device-management-azuread-joined-devices-setup/01.png)

2. Klicka på **konton**.

    ![Konton](./media/device-management-azuread-joined-devices-setup/02.png)


3. Klicka på **åtkomst arbetet eller skolan**.

    ![Åtkomst till arbetet eller skolan](./media/device-management-azuread-joined-devices-setup/03.png)

4. På den **åtkomst arbetet eller skolan** dialogrutan klickar du på **Anslut**.

    ![Anslut](./media/device-management-azuread-joined-devices-setup/04.png)


5. På den **konfigurera ett konto för arbetet eller skolan** dialogrutan klickar du på **ansluta enheten till Azure Active Directory**.

    ![Anslut](./media/device-management-azuread-joined-devices-setup/08.png)


6. På den **registrera dig** dialogrutan, ange namnet på ditt konto (till exempel someone@example.com), och klicka sedan på **nästa**.

    ![Registrera dig](./media/device-management-azuread-joined-devices-setup/10.png)


6. På den **ange lösenord** dialogrutan, ange ditt lösenord och klicka sedan på **logga in**.

    ![Ange lösenord](./media/device-management-azuread-joined-devices-setup/05.png)


7. På den **se till att det här är din organisation** dialogrutan klickar du på **ansluta**.

    ![Kontrollera att det här är din organisation](./media/device-management-azuread-joined-devices-setup/11.png)


8. På den **allt är klart** dialogrutan klickar du på **klar**.

    ![Du har allt klart](./media/device-management-azuread-joined-devices-setup/12.png)

## <a name="verification"></a>Verifiering

Om du vill kontrollera om en enhet är ansluten till en Azure AD, kan du granska den **åtkomst arbetet eller skolan** dialogrutan på enheten.

![Ansluten](./media/device-management-azuread-joined-devices-setup/13.png)

Du kan också köra följande kommando: `dsregcmd /status`  
På en enhet som har anslutits **AzureAdJoined** är **Ja**.

![Ansluten](./media/device-management-azuread-joined-devices-setup/14.png)

Du kan också granska inställningar för enheter på Azure AD-portalen.

![Ansluten](./media/device-management-azuread-joined-devices-setup/15.png)

Mer information finns i [hitta enheter](device-management-azure-portal.md#locate-devices).


## <a name="next-steps"></a>Nästa steg

Mer information finns i: 

- Den [introduktion till hantering av enheter i Azure Active Directory](device-management-introduction.md)
- [Hantera enheter med hjälp av Azure portal](device-management-azure-portal.md)
- 



