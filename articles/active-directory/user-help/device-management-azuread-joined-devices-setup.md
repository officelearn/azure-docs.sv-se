---
title: Konfigurera Azure Active Directory-anslutna enheter | Microsoft Docs
description: Lär dig hur du ställer in Azure Active Directory-anslutna enheter.
services: active-directory
author: eross-msft
manager: mtillman
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: lizross
ms.reviewer: jairoc
ms.openlocfilehash: 511580b95646677a231a68862cae8a6aa57abcb0
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2018
ms.locfileid: "39345539"
---
# <a name="set-up-azure-active-directory-joined-devices"></a>Konfigurera Azure Active Directory-anslutna enheter

Med enhetshantering i Azure Active Directory (AD Azure), kan du se till att dina användare kommer åt dina resurser från enheter som uppfyller dina krav för säkerhet och efterlevnad. Mer information finns i [introduktionen till enhetshantering i Azure Active Directory](../device-management-introduction.md).

Om du vill ta med ägs av arbetet Windows 10-enheter kontrolleras av Azure AD kan du göra detta genom att konfigurera Azure AD-anslutna enheter. Det här avsnittet ger dig relaterade steg. 


## <a name="prerequisites"></a>Förutsättningar

Om du vill ansluta till en Windows 10-enhet, måste registreringstjänsten för enheter konfigureras så att du kan registrera enheter. Du måste ha färre enheter som har registrerats än det konfigurerade maxantalet förutom att ha behörighet att ansluta enheter i din Azure AD-klient. Mer information finns i [konfigurerar du inställningar för](../device-management-azure-portal.md#configure-device-settings).



## <a name="what-you-should-know"></a>Vad du bör känna till


- Windows kopplar ihop enheten i organisationens katalog i Azure AD.

- Du kan behöva gå igenom multifaktorautentisering utmaning. Den här utmaningen kan konfigureras av IT-administratören.

- Azure AD kontrollerar om enheten kräver registrering för hantering av mobila enheter och registrerar den om det är tillämpligt.

- Om du är en hanterad användare tar Windows dig till skrivbordet genom automatisk inloggning.

- Om du är en federerad användare kan behöva du logga in med dina autentiseringsuppgifter.

- Om du är externa din identitetsprovider måste ha stöd för WS-Fed och WS-Trust användarnamn/lösenord-slutpunkten. Detta kan vara version 1.3 eller 2005. Den här protokollstöd krävs till både ansluta enheten till Azure AD och logga in på enheten med ett lösenord. 




## <a name="joining-a-device"></a>Ansluta till en enhet

Det här avsnittet ger dig stegen för att ansluta din Windows 10-enhet till din Azure AD. En enhet som har anslutits visas som **är anslutna till \<din Azure AD\>**.

![Ansluten](./media/device-management-azuread-joined-devices-setup/13.png)


**Att ansluta till Windows 10-enheten:**

1. I den **starta** -menyn klickar du på **inställningar**.

    ![Inställningar](./media/device-management-azuread-joined-devices-setup/01.png)

2. Klicka på **konton**.

    ![Konton](./media/device-management-azuread-joined-devices-setup/02.png)


3. Klicka på **åtkomst till arbete eller skola**.

    ![Åtkomst till arbete eller skola](./media/device-management-azuread-joined-devices-setup/03.png)

4. På den **åtkomst till arbete eller skola** dialogrutan klickar du på **Connect**.

    ![Anslut](./media/device-management-azuread-joined-devices-setup/04.png)


5. På den **konfigurera ett arbets- eller skolkonto konto** dialogrutan klickar du på **Anslut den här enheten till Azure Active Directory**.

    ![Anslut](./media/device-management-azuread-joined-devices-setup/08.png)


6. På den **registrera dig** dialogrutan, ange namnet på ditt konto (till exempel someone@example.com), och klicka sedan på **nästa**.

    ![Registrera dig](./media/device-management-azuread-joined-devices-setup/10.png)


6. På den **ange lösenord** dialogrutan, ange ditt lösenord och klicka sedan på **logga in**.

    ![Ange lösenord](./media/device-management-azuread-joined-devices-setup/05.png)


7. På den **se till att det här är din organisation** dialogrutan klickar du på **ansluta**.

    ![Kontrollera att det här är din organisation](./media/device-management-azuread-joined-devices-setup/11.png)


8. På den **allt är klart** dialogrutan klickar du på **klar**.

    ![Allt då var klart](./media/device-management-azuread-joined-devices-setup/12.png)

## <a name="verification"></a>Verifiering

För att kontrollera om en enhet är ansluten till en Azure AD, kan du granska den **åtkomst till arbete eller skola** dialogrutan på din enhet.

![Ansluten](./media/device-management-azuread-joined-devices-setup/13.png)

Du kan också köra följande kommando: `dsregcmd /status`  
På en enhet som har anslutits **AzureAdJoined** är **Ja**.

![Ansluten](./media/device-management-azuread-joined-devices-setup/14.png)

Du kan också granska inställningar för enheter på Azure AD-portalen.

![Ansluten](./media/device-management-azuread-joined-devices-setup/15.png)

Mer information finns i [hitta enheter](../device-management-azure-portal.md#locate-devices).


## <a name="next-steps"></a>Nästa steg

Mer information finns i: 

- Den [introduktionen till enhetshantering i Azure Active Directory](../device-management-introduction.md)
- [Hantera enheter med hjälp av Azure-portalen](../device-management-azure-portal.md)
- 



