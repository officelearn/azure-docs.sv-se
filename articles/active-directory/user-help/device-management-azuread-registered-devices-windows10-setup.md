---
title: Konfigurera Azure Active Directory registrerade enheter | Microsoft Docs
description: Lär dig hur du ställer in Azure Active Directory registrerade enheter.
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
ms.openlocfilehash: 7ce632c76a86fb00101db6664e9e79615484f9a1
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39059797"
---
# <a name="set-up-azure-active-directory-registered-windows-10-devices"></a>Konfigurera Azure Active Directory-registrerade Windows 10-enheter

Med enhetshantering i Azure Active Directory (AD Azure), kan du se till att dina användare kommer åt dina resurser från enheter som uppfyller dina krav för säkerhet och efterlevnad. Mer information finns i [introduktionen till enhetshantering i Azure Active Directory](../device-management-introduction.md).

Om du vill aktivera den **Bring Your Own Device (BYOD)** scenariot kan du göra detta genom att konfigurera Azure AD-registrerade enheter. I Azure AD kan du konfigurera Azure AD-registrerade enheter för Windows 10, iOS, Android och macOS. Den här artikeln innehåller relaterade steg för Windows 10-enheter. 


## <a name="before-you-begin"></a>Innan du börjar

Om du vill registrera en Windows 10-enhet, måste registreringstjänsten för enheter konfigureras så att du kan registrera enheter. Du måste dessutom ha färre enheter som har registrerats än det konfigurerade maxantalet. Mer information finns i [konfigurerar du inställningar för](../device-management-azure-portal.md#configure-device-settings).

## <a name="what-you-should-know"></a>Vad du bör känna till

När du registrerar en enhet, bör du ha följande i åtanke:

- Windows registrerar enheten i organisationens katalog i Azure AD

- Du kan behöva gå igenom multifaktorautentisering utmaning. Den här utmaningen kan konfigureras av IT-administratören.

- Azure AD kontrollerar om enheten kräver registrering för hantering av mobila enheter och registrerar den om det är tillämpligt.

- Om du är en hanterad användare tar Windows dig till skrivbordet genom automatisk inloggning.

- Om du är en federerad användare, kommer du tas till en Windows-inloggningsskärmen att ange dina autentiseringsuppgifter.


## <a name="registering-a-device"></a>Registrera en enhet

Det här avsnittet ger dig stegen för att registrera din Windows 10-enhet till din Azure AD. En enhet som har registrerats som visas med en **arbets- eller skolkonto** posten.

![Registrera dig](./media/device-management-azuread-registered-devices-windows10-setup/08.png)


**Registrera din Windows 10-enhet:**

1. I den **starta** -menyn klickar du på **inställningar**.

    ![Inställningar](./media/device-management-azuread-registered-devices-windows10-setup/01.png)

2. Klicka på **konton**.

    ![Konton](./media/device-management-azuread-registered-devices-windows10-setup/02.png)


3. Klicka på **åtkomst till arbete eller skola**.

    ![Åtkomst till arbete eller skola](./media/device-management-azuread-registered-devices-windows10-setup/03.png)

4. På den **åtkomst till arbete eller skola** dialogrutan klickar du på **Connect**.

    ![Connect](./media/device-management-azuread-registered-devices-windows10-setup/04.png)


5. På den **upprätta ett arbets- eller skolkonto konto** dialogrutan Ange namnet på ditt konto (till exempel someone@example.com), och klicka sedan på **nästa**.

    ![Connect](./media/device-management-azuread-registered-devices-windows10-setup/06.png)


6. På den **ange lösenord** dialogrutan, ange ditt lösenord och klicka sedan på **nästa**.

    ![Connect](./media/device-management-azuread-registered-devices-windows10-setup/05.png)


7. På den **allt är klart** dialogrutan klickar du på **klar**.

    ![Connect](./media/device-management-azuread-registered-devices-windows10-setup/07.png)

## <a name="verification"></a>Verifiering

För att kontrollera om en enhet är ansluten till en Azure AD, kan du granska den **åtkomst till arbete eller skola** dialogrutan på din enhet.

![Registrera dig](./media/device-management-azuread-registered-devices-windows10-setup/08.png)

Du kan också granska inställningar för enheter på Azure AD-portalen.

![Registrera dig](./media/device-management-azuread-registered-devices-windows10-setup/09.png)





## <a name="next-steps"></a>Nästa steg

- Mer information finns i den [introduktionen till enhetshantering i Azure Active Directory](../device-management-introduction.md)

- Mer information om hur du hanterar enheter i Azure AD-portalen finns i den [hantera enheter med hjälp av Azure-portalen ](../device-management-azure-portal.md).




