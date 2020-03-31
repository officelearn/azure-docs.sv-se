---
title: Hantera användare och roller i Azure IoT Central-programmet | Microsoft-dokument
description: Som administratör, hur du hanterar användare och roller i ditt Azure IoT Central-program
author: lmasieri
ms.author: lmasieri
ms.date: 12/05/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: c00f9d8baa55ef0d0cf6322ee71f22e739e6acdc
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365506"
---
# <a name="manage-users-and-roles-in-your-iot-central-application"></a>Hantera användare och roller i ditt IoT Central-program

I den här artikeln beskrivs hur du som administratör kan lägga till, redigera och ta bort användare i ditt Azure IoT Central-program. Artikeln beskriver också hur du hanterar roller i ditt Azure IoT Central-program.

För att komma åt och använda avsnittet **Administration** måste du vara i **administratörsrollen** för ett Azure IoT Central-program. Om du skapar ett Azure IoT Central-program läggs du automatiskt till i **administratörsrollen** för det programmet.

## <a name="add-users"></a>Lägga till användare

Varje användare måste ha ett användarkonto innan de kan logga in och komma åt ett Azure IoT Central-program. Microsoft-konton och Azure Active Directory-konton stöds i Azure IoT Central. Azure Active Directory-grupper stöds för närvarande inte i Azure IoT Central.

Mer information finns i [Hjälpen för Microsoft-konton](https://support.microsoft.com/products/microsoft-account?category=manage-account) och [Snabbstart: Lägg till nya användare i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

1. Om du vill lägga till en användare i ett IoT Central-program går du till sidan **Användare** i avsnittet **Administration.**
    
    > [!div class="mx-imgBorder"]
    >![Hantera användare](media/howto-manage-users-roles/manage-users-pnp.png)

1. Om du vill lägga till en användare väljer du **+ Lägg till användare**på sidan **Användare** .

1. Välj en roll för användaren på listrutan **Roll.** Läs mer om roller i avsnittet [Hantera roller](#manage-roles) i den här artikeln.

    > [!div class="mx-imgBorder"]
    >![Lägga till användare och välj en roll](media/howto-manage-users-roles/add-user-pnp.png)

    > [!NOTE]
    > En användare som har en anpassad roll som ger dem behörighet att lägga till andra användare kan bara lägga till användare i en roll med samma eller färre behörigheter än sin egen roll.

Om ett IoT Central-användar-ID tas bort från Azure Active Directory och sedan läsades kan användaren inte logga in i IoT Central-programmet. Om du vill återaktivera åtkomsten bör IoT Central-administratören ta bort och läsa användaren i programmet.

### <a name="edit-the-roles-that-are-assigned-to-users"></a>Redigera de roller som har tilldelats användare

Roller kan inte ändras när de har tilldelats. Om du vill ändra den roll som har tilldelats en användare tar du bort användaren och lägger sedan till användaren igen med en annan roll.

> [!NOTE]
> Rollerna som tilldelats är specifika för IoT Central-programmet och kan inte hanteras från Azure Portal.

## <a name="delete-users"></a>Ta bort användare

Om du vill ta bort användare markerar du en eller flera kryssrutor på sidan **Användare.** Välj sedan **Ta bort**.

## <a name="manage-roles"></a>Hantera roller

Med roller kan du styra vem inom organisationen som får utföra olika uppgifter i IoT Central. Det finns tre inbyggda roller som du kan tilldela användare av ditt program. Du kan också [skapa anpassade roller](#create-a-custom-role) om du behöver finarekornig kontroll.

> [!div class="mx-imgBorder"]
> ![Hantera val av roller](media/howto-manage-users-roles/manage-roles-pnp.png)

### <a name="administrator"></a>Administratör

Användare i **administratörsrollen** kan hantera och kontrollera alla delar av programmet, inklusive fakturering.

Användaren som skapar ett program tilldelas automatiskt **administratörsrollen.** Det måste alltid finnas minst en användare i **administratörsrollen.**

### <a name="builder"></a>Builder

Användare i **builder-rollen** kan hantera alla delar av appen, men kan inte göra ändringar på flikarna Administration eller Kontinuerlig dataexport.

### <a name="operator"></a>Operator

Användare i rollen **Operatör** kan övervaka enhetens hälsa och status. De får inte göra ändringar i enhetsmallar eller administrera programmet. Operatörer kan lägga till och ta bort enheter, hantera enhetsuppsättningar och köra analyser och jobb. 

## <a name="create-a-custom-role"></a>Skapa en anpassad roll

Om lösningen kräver finare åtkomstkontroller kan du skapa anpassade roller med anpassade behörighetsuppsättningar. Om du vill skapa en anpassad roll navigerar du till sidan **Roller** i avsnittet **Administration** i programmet. Välj sedan **+ Ny roll**och lägg till ett namn och en beskrivning för din roll. Välj de behörigheter som rollen kräver och välj sedan **Spara**.

Du kan lägga till användare i din anpassade roll på samma sätt som du lägger till användare i en inbyggd roll.

> [!div class="mx-imgBorder"]
> ![Skapa en anpassad roll](media/howto-manage-users-roles/create-custom-role-pnp.png)

### <a name="custom-role-options"></a>Alternativ för anpassad roll

När du definierar en anpassad roll väljer du den uppsättning behörigheter som en användare beviljas om de är medlemmar i rollen. Vissa behörigheter är beroende av andra. Om du till exempel lägger till behörigheten **Uppdatera programinstrumentpaneler** i en roll läggs behörigheten **Visa programinstrumentpaneler** automatiskt till. I följande tabeller sammanfattas de tillgängliga behörigheterna och deras beroenden som du kan använda när du skapar anpassade roller.

#### <a name="managing-devices"></a>Hantera enheter

**Behörigheter för enhetsmall**

| Namn | Beroenden |
| ---- | -------- |
| Visa | Inget     |
| Hantera | Visa <br/> Andra beroenden: Visa enhetsinstanser  |
| Fullständig kontroll | Visa, hantera <br/> Andra beroenden: Visa enhetsinstanser |

**Behörigheter för enhetsinstans**

| Namn | Beroenden |
| ---- | -------- |
| Visa | Inget <br/> Andra beroenden: Visa enhetsmallar och enhetsgrupper |
| Uppdatering | Visa <br/> Andra beroenden: Visa enhetsmallar och enhetsgrupper  |
| Skapa | Visa <br/> Andra beroenden: Visa enhetsmallar och enhetsgrupper  |
| Ta bort | Visa <br/> Andra beroenden: Visa enhetsmallar och enhetsgrupper  |
| Köra kommandon | Uppdatera, visa <br/> Andra beroenden: Visa enhetsmallar och enhetsgrupper  |
| Fullständig kontroll | Visa, uppdatera, skapa, ta bort, köra kommandon <br/> Andra beroenden: Visa enhetsmallar och enhetsgrupper  |

**Behörigheter för enhetsgrupper**

| Namn | Beroenden |
| ---- | -------- |
| Visa | Inget <br/> Andra beroenden: Visa enhetsmallar och enhetsinstanser |
| Uppdatering | Visa <br/> Andra beroenden: Visa enhetsmallar och enhetsinstanser   |
| Skapa | Visa, uppdatera <br/> Andra beroenden: Visa enhetsmallar och enhetsinstanser   |
| Ta bort | Visa <br/> Andra beroenden: Visa enhetsmallar och enhetsinstanser   |
| Fullständig kontroll | Visa, uppdatera, skapa, ta bort <br/> Andra beroenden: Visa enhetsmallar och enhetsinstanser |

**Behörigheter för hantering av enhetsanslutning**

| Namn | Beroenden |
| ---- | -------- |
| Läs instans | Inget <br/> Andra beroenden: Visa enhetsmallar, enhetsgrupper, enhetsinstanser |
| Hantera instans | Inget |
| Läs globalt | Inget   |
| Hantera globalt | Läs globalt |
| Fullständig kontroll | Läs instans, Hantera instans, Läs globalt, Hantera globalt. <br/> Andra beroenden: Visa enhetsmallar, enhetsgrupper, enhetsinstanser |

**Behörigheter för jobb**

| Namn | Beroenden |
| ---- | -------- |
| Visa | Inget <br/> Andra beroenden: Visa enhetsmallar, enhetsinstanser och enhetsgrupper |
| Uppdatering | Visa <br/> Andra beroenden: Visa enhetsmallar, enhetsinstanser och enhetsgrupper |
| Skapa | Visa, uppdatera <br/> Andra beroenden: Visa enhetsmallar, enhetsinstanser och enhetsgrupper |
| Ta bort | Visa <br/> Andra beroenden: Visa enhetsmallar, enhetsinstanser och enhetsgrupper |
| Genomförande | Visa <br/> Andra beroenden: Visa enhetsmallar, enhetsinstanser och enhetsgrupper. Uppdatera enhetsinstanser; Köra kommandon på enhetsinstanser |
| Fullständig kontroll | Visa, uppdatera, skapa, ta bort, köra <br/> Andra beroenden: Visa enhetsmallar, enhetsinstanser och enhetsgrupper. Uppdatera enhetsinstanser; Köra kommandon på enhetsinstanser |

**Behörigheter för regler**

| Namn | Beroenden |
| ---- | -------- |
| Visa | Inget <br/> Andra beroenden: Visa enhetsmallar |
| Uppdatering | Visa <br/> Andra beroenden: Visa enhetsmallar |
| Skapa | Visa, uppdatera <br/> Andra beroenden: Visa enhetsmallar |
| Ta bort | Visa <br/> Andra beroenden: Visa enhetsmallar |
| Fullständig kontroll | Visa, uppdatera, skapa, ta bort <br/> Andra beroenden: Visa enhetsmallar |

#### <a name="managing-the-app"></a>Hantera appen

**Behörigheter för programinställningar**

| Namn | Beroenden |
| ---- | -------- |
| Visa | Inget     |
| Uppdatering | Visa   |
| Kopiera | Visa <br/> Andra beroenden: Visa enhetsmallar, enhetsinstanser, enhetsgrupper, instrumentpaneler, dataexport, varumärkesprofilering, hjälplänkar, anpassade roller, regler |
| Ta bort | Visa   |
| Fullständig kontroll | Visa, uppdatera, kopiera, ta bort <br/> Andra beroenden: Visa enhetsmallar, enhetsgrupper, instrumentpaneler för program, dataexport, varumärkesprofilering, hjälplänkar, anpassade roller, regler |

**Exportbehörigheter för programmall**

| Namn | Beroenden |
| ---- | -------- |
| Visa | Inget     |
| Exportera | Visa <br/> Andra beroenden: Visa enhetsmallar, enhetsinstanser, enhetsgrupper, instrumentpaneler, dataexport, varumärkesprofilering, hjälplänkar, anpassade roller, regler |
| Fullständig kontroll | Visa, exportera <br/> Andra beroenden: Visa enhetsmallar, enhetsgrupper, instrumentpaneler för program, dataexport, varumärkesprofilering, hjälplänkar, anpassade roller, regler |

**Faktureringsbehörigheter**

| Namn | Beroenden |
| ---- | -------- |
| Hantera | Inget     |
| Fullständig kontroll | Hantera |

#### <a name="managing-users-and-roles"></a>Hantera användare och roller

**Behörigheter för anpassade roller**

| Namn | Beroenden |
| ---- | -------- |
| Visa | Inget |
| Uppdatering | Visa |
| Skapa | Visa, uppdatera |
| Ta bort | Visa |
| Fullständig kontroll | Visa, uppdatera, skapa, ta bort |

**Behörigheter för användarhantering**

| Namn | Beroenden |
| ---- | -------- |
| Visa | Inget <br/> Andra beroenden: Visa anpassade roller |
| Lägg till | Visa <br/> Andra beroenden: Visa anpassade roller |
| Ta bort | Visa <br/> Andra beroenden: Visa anpassade roller |
| Fullständig kontroll | Visa, lägga till, ta bort <br/> Andra beroenden: Visa anpassade roller |

> [!NOTE]
> En användare som har en anpassad roll som ger dem behörighet att lägga till andra användare kan bara lägga till användare i en roll med samma eller färre behörigheter än sin egen roll.

#### <a name="customizing-the-app"></a>Anpassa appen

**Behörigheter för programinstrumentpanel**

| Namn | Beroenden |
| ---- | -------- |
| Visa | Inget     |
| Uppdatering | Visa   |
| Skapa | Visa, uppdatera |
| Ta bort | Visa   |
| Fullständig kontroll | Visa, uppdatera, skapa, ta bort |

**Behörigheter för personliga instrumentpaneler**

| Namn | Beroenden |
| ---- | -------- |
| Visa | Inget     |
| Uppdatering | Visa   |
| Skapa | Visa, uppdatera   |
| Ta bort | Visa   |
| Fullständig kontroll | Visa, uppdatera, skapa, ta bort |

**Behörigheter för branding, favicon och färger**

| Namn | Beroenden |
| ---- | -------- |
| Visa | Inget     |
| Uppdatering | Visa   |
| Fullständig kontroll | Visa, uppdatera |

**Hjälplänkar behörigheter**

| Namn | Beroenden |
| ---- | -------- |
| Visa | Inget     |
| Uppdatering | Visa   |
| Fullständig kontroll | Visa, uppdatera |

#### <a name="extending-the-app"></a>Utöka appen

**Behörigheter för dataexport**

| Namn | Beroenden |
| ---- | -------- |
| Visa | Inget     |
| Uppdatering | Visa   |
| Skapa | Visa, uppdatera  |
| Ta bort | Visa   |
| Fullständig kontroll | Visa, uppdatera, skapa, ta bort |

**API-tokenbehörigheter**

| Namn | Beroenden |
| ---- | -------- |
| Visa | Inget     |
| Skapa | Visa   |
| Ta bort | Visa   |
| Fullständig kontroll | Visa, skapa, ta bort |

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig om hur du hanterar användare och roller i ditt Azure IoT Central-program är det föreslagna nästa steget att lära dig hur du [hanterar din faktura](howto-view-bill.md).
