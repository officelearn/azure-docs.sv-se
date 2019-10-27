---
title: Hantera användare och roller i Azure IoT Central program | Microsoft Docs
description: Som administratör kan du hantera användare och roller i ditt Azure IoT Central-program
author: v-krghan
ms.author: v-krghan
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 937c4a4dbf976564cbf8dc562bdec3b328fc2bc0
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72950098"
---
# <a name="manage-users-and-roles-in-your-iot-central-application"></a>Hantera användare och roller i ditt IoT Central program

Den här artikeln beskriver hur du, som administratör, kan lägga till, redigera och ta bort användare i ditt Azure IoT Central-program och även hantera roller i ditt Azure IoT Central-program.

För att få åtkomst till och använda avsnittet **Administration** måste du ha rollen **administratör** för ett Azure IoT Central-program. Om du skapar ett Azure IoT Central-program tilldelas du automatiskt rollen som **administratör** för programmet.


## <a name="add-users"></a>Lägga till användare

Varje användare måste ha ett användar konto innan de kan logga in och komma åt ett Azure IoT Central-program. Microsoft-konton (MSA: er) och Azure Active Directory-konton (Azure AD) stöds i Azure IoT Central. Azure Active Directory grupper stöds för närvarande inte i Azure IoT Central.

Mer information finns i [Microsoft-konto Hjälp](https://support.microsoft.com/products/microsoft-account?category=manage-account) och [snabb start: Lägg till nya användare i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

1. Om du vill lägga till en användare i ett IoT Central-program går du till sidan **användare** i avsnittet **Administration** .

    ![Lista över användare](media/howto-administer/image1.png)

1. Om du vill lägga till en användare går du till sidan **användare** och väljer **+ Lägg till användare**.

1. Välj en roll för användaren på den nedrullningsbara menyn **roll** . Lär dig mer om roller i avsnittet [hantera roller](#manage-roles) i den här artikeln.

    ![Roll val](media/howto-administer/image3.png)

    > [!NOTE]
    >  Om du vill lägga till användare i grupp anger du användar-ID för alla användare som du vill lägga till avgränsade med semikolon. Välj en roll på den nedrullningsbara menyn **roll** . Välj sedan **Spara**.

### <a name="edit-the-roles-that-are-assigned-to-users"></a>Redigera de roller som är tilldelade till användare

Roller kan inte ändras efter att de har tilldelats. Om du vill ändra rollen som är tilldelad en användare tar du bort användaren och lägger sedan till användaren igen med en annan roll.

> [!NOTE]
> De roller som är tilldelade är specifika för IoT Central program och kan inte hanteras från Azure Portal.

## <a name="delete-users"></a>Ta bort användare

Om du vill ta bort användare markerar du en eller flera kryss rutor på sidan **användare** . Välj sedan **Ta bort**.

## <a name="manage-roles"></a>Hantera roller

Med roller kan du styra vem i organisationen som kan utföra olika uppgifter i IoT Central. Det finns tre roller som du kan tilldela till användare av ditt program.

### <a name="administrator"></a>Administratör

Användare i rollen **administratör** har åtkomst till alla funktioner i ett program.

Den användare som skapar ett program tilldelas automatiskt rollen **administratör** . Det måste alltid finnas minst en användare i **Administratörs** rollen.

### <a name="application-builder"></a>Application Builder

Användare i **Application Builder** -rollen kan göra allt i ett program, förutom att administrera programmet. Konstruktörer kan skapa, redigera och ta bort enhetsspecifika och enheter, hantera enhets uppsättningar och köra analyser och jobb. Konstruktörer har inte åtkomst till avsnittet **Administration** i programmet.

### <a name="application-operator"></a>Program operatör

Användare i rollen **program operatör** kan inte ändra enhets mallar och kan inte administrera programmet. Operatörer kan lägga till och ta bort enheter, hantera enhets uppsättningar och köra analyser och jobb. Operatörer har inte åtkomst till **Application Builder** -och **administrations** sidorna.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du hanterar användare och roller i din Azure-IoT Central, är det föreslagna nästa steg att lära dig mer om att [Visa din faktura](howto-view-bill.md) i Azure IoT Central.
