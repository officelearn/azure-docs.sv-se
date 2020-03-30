---
title: Ange tidsgränsen för inaktivitet på katalognivå för användare av Azure-portalen | Microsoft-dokument
description: Administratörer kan framtvinga den maximala inaktiva tiden innan en session loggas ut. Timeout-principen för inaktivitet anges på katalognivå.
services: azure-portal
keywords: inställningar, timeout
author: mgblythe
ms.author: mblythe
ms.date: 02/10/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: e27135d09da7060f2a948e37f6026fe66fbef5b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79096605"
---
# <a name="set-directory-level-inactivity-timeout"></a>Ange tidsgränsen för inaktivitet på katalognivå

Timeout-inställningen för inaktivitet hjälper till att skydda dina resurser från obehörig åtkomst om användarna glömmer att skydda sin arbetsstation. När en användare har varit inaktiv ett tag loggas deras Azure-portalsession ut automatiskt. Administratörer i [rollen Global administratör](../active-directory/users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) kan framtvinga maximal inaktiv tid innan en session loggas ut. Timeout-inställningen för inaktivitet gäller på katalognivå. Mer information om kataloger finns i [Översikt över Active Directory Domain Services](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview).

## <a name="configure-the-inactive-timeout-setting"></a>Konfigurera inställningen för inaktiv timeout

Om du är global administratör och vill tillämpa en inaktiv timeout-inställning för alla användare av Azure-portalen gör du så här:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **Inställningar** i det globala sidhuvudet.
3. Markera länktexten **Konfigurera tidsgränsen för katalognivå**.

    ![Skärmbild som visar portalinställningar med länktext markerad](./media/admin-timeout/settings.png)

4. En ny sida öppnas. På **timeout-sidan Konfigurera tidsgränsen för katalognivå** väljer du **Aktivera tidsgränsen för inaktiv information på katalognivå för azure-portalen för** att aktivera inställningen.
5. Ange sedan **timmar** och **minuter** under den maximala tid som en användare kan vara inaktiv innan sessionen loggas ut automatiskt.
6. Välj **Använd**.

    ![Skärmbild som visar sidan för att ange tidsgränsen för inaktivitet på katalognivå](./media/admin-timeout/configure.png)

Om du vill bekräfta att timeout-principen för inaktivitet är korrekt inställd väljer du **Meddelanden** i det globala sidhuvudet. Kontrollera att ett meddelande om lyckade uppgifter visas.

  ![Skärmbild som visar lyckat meddelandemeddelande för tidsgränsen för inaktivitet på katalognivå](./media/admin-timeout/confirmation.png)

Inställningen börjar gälla för nya sessioner. Det gäller inte omedelbart för användare som redan är inloggade.

> [!NOTE]
> Om en global administratör har konfigurerat en timeout-inställning på katalognivå kan användarna åsidosätta principen och ange sin egen inaktiva utcheckningstid. Användaren måste dock välja ett tidsintervall som är mindre än det som anges på katalognivå av den globala administratören.
>

## <a name="next-steps"></a>Nästa steg

* [Ange Azure-portalinställningar](set-preferences.md)
* [Exportera eller ta bort användarinställningar](azure-portal-export-delete-settings.md)
* [Aktivera hög kontrast eller ändra tema](azure-portal-change-theme-high-contrast.md)
