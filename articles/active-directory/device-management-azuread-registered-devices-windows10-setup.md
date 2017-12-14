---
title: Konfigurera Azure Active Directory registrerade enheter | Microsoft Docs
description: "Lär dig hur du ställer in Azure Active Directory registrerade enheter."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/27/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 1ff50a63aca93c4b9ba79eb113064a41a5e05cd7
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="set-up-azure-active-directory-registered-windows-10-devices"></a>Konfigurera Azure Active Directory registrerade Windows 10-enheter

Med hantering av enheter i Azure Active Directory (Azure AD), kan du se till att dina användare har åtkomst till dina resurser från enheter som uppfyller dina krav för säkerhet och efterlevnad. Mer information finns i [introduktion till hantering av enheter i Azure Active Directory](device-management-introduction.md).

Om du vill aktivera den **Bring Your Own Device (BYOD)** scenario du åstadkommer detta genom att konfigurera Azure AD som registrerade enheter. Du kan konfigurera Azure AD som registrerade enheter för Windows 10, iOS, Android och macOS i Azure AD. Det här avsnittet innehåller relaterade steg för Windows 10-enheter. 


## <a name="before-you-begin"></a>Innan du börjar

Registreringstjänsten för enheter måste konfigureras så att du kan registrera enheter om du vill registrera en Windows 10-enhet. Förutom att du har behörighet att registrera enheter i din Azure AD-klient, måste du ha färre enheter som har registrerats än den konfigurerade maximalt. Mer information finns i [konfigurera Enhetsinställningar](device-management-azure-portal.md#configure-device-settings).

## <a name="what-you-should-know"></a>Vad du bör känna till

När du registrerar en enhet, bör du tänka på följande i åtanke:

- Windows registrerar enheten i organisationens katalog i Azure AD

- Du kan behöva gå igenom multifaktorautentisering utmaning. Denna utmaning kan konfigureras av IT-administratören.

- Azure AD kontrollerar om enheten kräver registrering för hantering av mobila enheter och registrerar den om tillämpligt.

- Om du använder en hanterad går Windows du till skrivbordet via automatisk inloggning.

- Om du är en federerad användare kommer du tas till en Windows-inloggning skärm att ange dina autentiseringsuppgifter.


## <a name="registering-a-device"></a>Registrerar en enhet

Det här avsnittet ger dig stegen för att registrera din Windows 10-enhet till din Azure AD. Om du har registrerat enheten till Azure AD din **åtkomst arbetet eller skolan** dialogrutan anger detta med en **fungerar- eller skolkonto** transaktionen.

![Registrera dig](./media/device-management-azuread-registered-devices-windows10-setup/08.png)


**För att registrera din Windows 10-enhet:**

1. I den **starta** -menyn klickar du på **inställningar**.

    ![Inställningar](./media/device-management-azuread-registered-devices-windows10-setup/01.png)

2. Klicka på **konton**.

    ![Konton](./media/device-management-azuread-registered-devices-windows10-setup/02.png)


3. Klicka på **åtkomst arbetet eller skolan**.

    ![Åtkomst till arbetet eller skolan](./media/device-management-azuread-registered-devices-windows10-setup/03.png)

4. På den **åtkomst arbetet eller skolan** dialogrutan klickar du på **Anslut**.

    ![Anslut](./media/device-management-azuread-registered-devices-windows10-setup/04.png)


5. På den **konfigurera ett arbets- eller skolkonto konto** dialogrutan, ange namnet på ditt konto (t.ex.: someone@example.com), och klicka sedan på **nästa**.

    ![Anslut](./media/device-management-azuread-registered-devices-windows10-setup/06.png)


6. På den **ange lösenord** dialogrutan, ange ditt lösenord och klicka sedan på **nästa**.

    ![Anslut](./media/device-management-azuread-registered-devices-windows10-setup/05.png)


7. På den **allt är klart** dialogrutan klickar du på **klar**.

    ![Anslut](./media/device-management-azuread-registered-devices-windows10-setup/07.png)

## <a name="verification"></a>Verifiering

Om du vill kontrollera om en enhet är ansluten till en Azure AD, kan du granska den **åtkomst arbetet eller skolan** dialogrutan på enheten.

![Registrera dig](./media/device-management-azuread-registered-devices-windows10-setup/08.png)

Alternativt kan du också granska inställningar för enheter på Azure AD-portalen.

![Registrera dig](./media/device-management-azuread-registered-devices-windows10-setup/09.png)





## <a name="next-steps"></a>Nästa steg

- Mer information finns i [introduktion till hantering av enheter i Azure Active Directory](device-management-introduction.md)

- Mer information om hur du hanterar enheter i Azure AD-portalen finns i [hantera enheter med hjälp av Azure portal ](device-management-azure-portal.md).




