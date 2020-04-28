---
title: Hantera användar inställningar för Azure Multi-Factor Authentication-Azure Active Directory
description: Lär dig hur du kan konfigurera Azure Active Directory användar inställningar för Azure Multi-Factor Authentication
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 048224a55c2bbcbc99281d070d88d34e2dc77168
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81309758"
---
# <a name="manage-user-settings-for-azure-multi-factor-authentication"></a>Hantera användar inställningar för Azure Multi-Factor Authentication

För att hjälpa till att hantera Azure-Multi-Factor Authentication kan du kräva att användarna återställer sina lösen ord, omregistrerar sig för MFA eller återkalla befintliga MFA-sessioner. För användare som har definierat applösenord kan du också välja att ta bort dessa lösen ord, vilket gör att äldre autentisering inte fungerar i dessa program. De här åtgärderna kan vara nödvändiga om du behöver ge hjälp till en användare eller vill återställa säkerhets statusen.

## <a name="manage-user-authentication-options"></a>Hantera alternativ för användarautentisering

Om du har tilldelats rollen som *administratör för autentisering* kan du kräva att användarna återställer sina lösen ord, omregistrerar sig för MFA eller återkalla befintliga MFA-sessioner från sina användar objekt. Utför följande steg för att hantera användar inställningar:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **Azure Active Directory** > **användare** > **alla användare**till vänster.
1. Välj den användare som du vill utföra en åtgärd på och välj **autentiseringsmetoder**. Överst i fönstret väljer du något av följande alternativ för användaren:
   - **Återställ lösen ord** återställer användarens lösen ord och tilldelar ett tillfälligt lösen ord som måste ändras vid nästa inloggning.
   - **Kräv omregistrering av MFA** gör det så att när användaren loggar in nästa gång uppmanas de att ställa in en ny MFA-autentiseringsmetod.
   - **Återkalla MFA-sessioner** rensar användarens sparade MFA-sessioner och kräver att de utför MFA nästa gång den krävs av principen på enheten.

   ![Hantera autentiseringsmetoder från Azure Portal](./media/howto-mfa-userdevicesettings/manage-authentication-methods-in-azure.png)

## <a name="delete-users-existing-app-passwords"></a>Ta bort användare befintliga applösenord

Om det behövs kan du ta bort alla applösenord som en användare har skapat. Icke-webbläsarbaserade appar som kopplats till dessa applösenord slutar fungera tills ett nytt applösenord har skapats. Det krävs *globala administratörs* behörigheter för att utföra den här åtgärden.

För att ta bort en användares applösenord, utför följande steg:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. På den vänstra sidan väljer du **Azure Active Directory** > **användare** > **alla användare**.
1. Välj **Multi-Factor Authentication**. Du kan behöva bläddra till höger för att se det här meny alternativet. Välj skärm bilden nedan om du vill se hela Azure Portals fönster och meny plats:[![](media/howto-mfa-userstates/selectmfa-cropped.png "Välj Multi-Factor Authentication från fönstret användare i Azure AD")](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. Markera kryss rutan bredvid den eller de användare som du vill hantera. En lista med snabb stegs alternativ visas till höger.
1. Välj **hantera användar inställningar**och markera sedan kryss rutan för **ta bort alla befintliga applösenord som har genererats av de valda användarna**, som du ser i ![följande exempel: ta bort alla befintliga applösenord](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
1. Välj **Spara**och sedan **Stäng**.

## <a name="next-steps"></a>Nästa steg

Den här artikeln hjälpte dig att konfigurera enskilda användar inställningar. Information om hur du konfigurerar inställningar för Azure Multi-Factor Authentication-tjänsten finns i [Konfigurera inställningar för azure Multi-Factor Authentication](howto-mfa-mfasettings.md)

Om användarna behöver hjälp kan du läsa [användar handboken för Azure Multi-Factor Authentication](../user-help/multi-factor-authentication-end-user.md).
