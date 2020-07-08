---
title: Ange tids gräns för inaktivitet på katalog nivå för användare av Azure Portal | Microsoft Docs
description: Administratörer kan framtvinga den längsta inaktiva tiden innan en session loggas ut. Tids gräns principen för inaktivitet anges på katalog nivå.
services: azure-portal
keywords: inställningar, timeout
author: mgblythe
ms.author: mblythe
ms.date: 02/10/2020
ms.topic: how-to
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 2efee29ba709607e1e7935215ca7689a39966de1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84764170"
---
# <a name="set-directory-level-inactivity-timeout"></a>Ange tids gräns för inaktivitet på katalog nivå

Inställningen tids gräns för inaktivitet hjälper till att skydda dina resurser från obehörig åtkomst om användare glömmer att skydda sin arbets Station. När en användare har varit inaktiv under en stund loggas Azure Portal-sessionen automatiskt ut. Administratörer i [rollen global administratör](../active-directory/users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) kan genomdriva den längsta inaktiva tiden innan en session loggas ut. Inställningen för tids gräns för inaktivitet gäller på katalog nivå. Mer information om kataloger finns i [Active Directory Domain Services översikt](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview).

## <a name="configure-the-inactive-timeout-setting"></a>Konfigurera inställningen för inaktiv tids gräns

Om du är global administratör och vill framtvinga en tids gräns för inaktivitet för alla användare av Azure Portal följer du dessa steg:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **Inställningar** från det globala sidhuvudet.
3. Välj länk texten **Konfigurera timeout för katalog nivå**.

    ![Skärm bild som visar Portal inställningar med länk text markerad](./media/admin-timeout/settings.png)

4. En ny sida öppnas. På sidan **Konfigurera tids gräns för inaktivitet på katalog nivå** väljer du **Aktivera timeout för inaktiv katalog nivå för Azure Portal** aktivera inställningen.
5. Ange sedan **timmar** och **minuter** för den längsta tid som en användare kan vara inaktiv innan sessionen loggas ut automatiskt.
6. Välj **Använd**.

    ![Skärm bild som visar sidan för att ange tids gräns för inaktivitet på katalog nivå](./media/admin-timeout/configure.png)

Om du vill bekräfta att tids gränsen för inaktivitet har angetts korrekt väljer du **meddelanden** från den globala sidhuvudet. Kontrol lera att en lyckad avisering visas.

  ![Skärm bild som visar ett meddelande om att aviseringar har slutförts på katalog nivå](./media/admin-timeout/confirmation.png)

Inställningen börjar gälla för nya sessioner. Den gäller inte omedelbart för användare som redan är inloggade.

> [!NOTE]
> Om en global administratör har konfigurerat en timeout-inställning på katalog nivå kan användarna åsidosätta principen och ange sina egna inaktiva inloggnings tider. Användaren måste dock välja ett tidsintervall som är mindre än det som anges på katalog nivå av den globala administratören.
>

## <a name="next-steps"></a>Nästa steg

* [Ange Azure-portalinställningar](set-preferences.md)
* [Exportera eller ta bort användarinställningar](azure-portal-export-delete-settings.md)
* [Aktivera hög kontrast eller ändra tema](azure-portal-change-theme-high-contrast.md)
