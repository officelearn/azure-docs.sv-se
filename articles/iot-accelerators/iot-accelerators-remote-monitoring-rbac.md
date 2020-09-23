---
title: Åtkomst kontroll för fjärr styrning – Azure | Microsoft Docs
description: Den här artikeln innehåller information om hur du kan konfigurera rollbaserad åtkomst kontroll (RBAC) i lösnings acceleratorn för fjärrövervakning
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.custom: has-adal-ref, devx-track-csharp
ms.openlocfilehash: 817bc7624bb3a6b69d56265e40681287b7fc09fa
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90969596"
---
# <a name="configure-role-based-access-control-in-the-remote-monitoring-solution-accelerator"></a>Konfigurera rollbaserad åtkomst kontroll i Solution Accelerator för fjärr styrning

Den här artikeln innehåller information om hur du konfigurerar rollbaserad åtkomst kontroll i Solution Accelerator för fjärr styrning. Med rollbaserad åtkomst kontroll kan du begränsa åtkomsten för enskilda användare till specifika funktioner i lösningen.

## <a name="default-settings"></a>Standardinställningar

När du först distribuerar lösningen för fjärrövervakning finns det två roller: **admin** och **skrivskyddad**.

Alla användare i **Administratörs** rollen har fullständig åtkomst till lösningen, inklusive följande behörigheter nedan. En användare i den **skrivskyddade** rollen har bara åtkomst för att Visa lösningen.

| Behörighet            | Administratör | Skrivskydd |
|----------------       |-------|-----------|
| Visa lösning         | Ja   | Ja       |
| Uppdatera larm         | Ja   | Nej        |
| Ta bort larm         | Ja   | Nej        |
| Skapa enheter        | Ja   | Nej        |
| Uppdatera enheter        | Ja   | Nej        |
| Ta bort enheter        | Ja   | Nej        |
| Skapa enhets grupper  | Ja   | Nej        |
| Uppdatera enhets grupper  | Ja   | Nej        |
| Ta bort enhets grupper  | Ja   | Nej        |
| Skapa regler          | Ja   | Nej        |
| Uppdatera regler          | Ja   | Nej        |
| Ta bort regler          | Ja   | Nej        |
| Skapa jobb           | Ja   | Nej        |
| Uppdatera SIM-hantering | Ja   | Nej        |

Som standard tilldelas den användare som distribuerade lösningen automatiskt **Administratörs** rollen och är en Azure Active Directory program ägare. Som program ägare kan du tilldela roller till andra användare via Azure Portal. Om du vill att en annan användare ska tilldela roller i lösningen måste de också anges som en program ägare i Azure Portal.

> [!NOTE]
> Den användare som distribuerade lösningen är den **enda personen** som kan visa den direkt efter att den har skapats. Om du vill ge andra åtkomst till att visa programmet som antingen en skrivskyddad, administratör eller en anpassad roll, se följande instruktioner under Lägg till eller ta bort användare.

## <a name="add-or-remove-users"></a>Lägg till eller ta bort användare

Som Azure Active Directory program ägare kan du använda Azure Portal för att lägga till eller ta bort en användare till en roll från lösningen för fjärrövervakning. Följande steg använder [Azure Active Directory företags program](../active-directory/manage-apps/view-applications-portal.md) som skapades när du distribuerade lösningen för fjärrövervakning.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Kontrol lera att [användaren finns i den katalog](../active-directory/fundamentals/add-users-azure-active-directory.md) som du använder. Du väljer den katalog som ska användas när du loggade in på [Microsoft Azure IoT Solution acceleratorer](https://www.azureiotsolutions.com/Accelerators) -webbplatsen. Katalog namnet visas i det övre högra hörnet på [sidan](https://www.azureiotsolutions.com/Accelerators).

1. Hitta **företags programmet** för din lösning i Azure Portal. När så är fallet filtrerar du listan genom att ange **program typ** för **alla program**. Sök efter ditt program efter program namn. Program namnet är namnet på din lösning för fjärr styrning. I följande skärm bild är lösnings-och visnings namnen för programmet **contoso-RM4**.

    ![Företags program](media/iot-accelerators-remote-monitoring-rbac/appregistration.png)

1. Kontrol lera att du är ägare till programmet genom att klicka på programmet och sedan på **ägare**. I följande skärm bild är **contoso-administratören** en ägare till **contoso-RM4-** programmet:

    ![Skärm bild som visar alternativet Hantera ägare valt, som visar ägare för Contoso r m 4-programmet.](media/iot-accelerators-remote-monitoring-rbac/owners.png)

    Om du inte är ägare måste du be en befintlig ägare att lägga till dig i listan. Endast ägare kan tilldela program roller som **admin** eller **skrivskyddad** till andra användare.

1. Om du vill se en lista över användare som har tilldelats roller i programmet klickar du på **användare och grupper**.

1. Lägg till en användare genom att klicka på **+ Lägg till användare**och sedan klicka på **användare och grupper, ingen valt** för att välja en användare från katalogen.

1. Om du vill tilldela användaren rollen klickar du på **Välj roll, ingen har marker ATS** och väljer antingen **Administratörs** rollen eller den **skrivskyddade** rollen för användaren. Klicka på **Välj**och sedan på **tilldela**.

    ![Välj roll](media/iot-accelerators-remote-monitoring-rbac/selectrole.png)

1. Användaren kan nu komma åt fjärr styrnings lösningen med behörigheterna som definieras av rollen.

1. Du kan ta bort användare från programmet på sidan **användare och grupper** i portalen.

## <a name="create-a-custom-role"></a>Skapa en anpassad roll

Lösningen för fjärrövervakning inkluderar bara **Administratörer** och **skrivskyddade** roller när den först distribueras. Du kan lägga till anpassade roller med olika behörighets uppsättningar. För att definiera en anpassad roll måste du:

- Lägg till en ny roll i programmet i Azure Portal.
- Definiera en princip för den nya rollen i mikrotjänsten för autentisering och auktorisering.
- Uppdatera lösningens webb gränssnitt.

### <a name="define-a-custom-role-in-the-azure-portal"></a>Definiera en anpassad roll i Azure Portal

Följande steg beskriver hur du lägger till en roll i ett program i Azure Active Directory. I det här exemplet skapar du en ny roll som gör att medlemmar kan skapa, uppdatera och ta bort enheter i lösningen för fjärrövervakning.

1. Hitta **appens registrering** för din lösning i Azure Portal. Program namnet är namnet på din lösning för fjärr styrning. I följande skärm bild är lösnings-och visnings namnen för programmet **contoso-RM4**.

    ![Appregistrering](media/iot-accelerators-remote-monitoring-rbac/app-registration-2.png)

1. Välj ditt program och klicka sedan på **manifest**. Du kan se de två befintliga [app-rollerna](https://docs.microsoft.com/azure/architecture/multitenant-identity/app-roles) som definierats för programmet:

    ![Visa manifest](media/iot-accelerators-remote-monitoring-rbac/view-manifest.png)

1. Redigera manifestet för att lägga till en roll med namnet **ManageDevices** som visas i följande kodfragment. Du behöver en unik sträng, till exempel ett GUID för det nya roll-ID: t. Du kan skapa ett nytt GUID med hjälp av en tjänst, till exempel [GUID-generatorn online](https://www.guidgenerator.com/):

    ```json
    "appRoles": [
      {
        "allowedMemberTypes": [
          "User"
        ],
        "displayName": "Admin",
        "id": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
        "isEnabled": true,
        "description": "Administrator access to the application",
        "value": "Admin"
      },
      {
        "allowedMemberTypes": [
          "User"
        ],
        "displayName": "Read Only",
        "id": "e5bbd0f5-128e-4362-9dd1-8f253c6082d7",
        "isEnabled": true,
        "description": "Read only access to device information",
        "value": "ReadOnly"
      },
      {
        "allowedMemberTypes": [
          "User"
        ],
        "displayName": "ManageDevices",
        "id": "ADD A NEW GUID HERE",
        "isEnabled": true,
        "description": "A new role for the application.",
        "value": "ManageDevices"
      }
    ],
    ```

    Spara ändringarna.

### <a name="define-a-policy-for-the-new-role"></a>Definiera en princip för den nya rollen

När du har lagt till rollen i appen i Azure Portal måste du definiera en princip i [roles.jspå](https://github.com/Azure/remote-monitoring-services-dotnet/blob/master/auth/Services/data/policies/roles.json) för rollen som tilldelar de behörigheter som krävs för att hantera enheter.

1. Klona [fjärr styrnings databasen för fjärrtjänster](https://github.com/Azure/remote-monitoring-services-dotnet) från GitHub till den lokala datorn.

1. Redigera **autentiseringen/tjänsterna/data/principer/roles.jsi** filen för att lägga till principen för **ManageDevices** -rollen enligt följande kodfragment. **ID-** och **roll** värdena måste matcha roll definitionen i app-manifestet från föregående avsnitt. Listan över tillåtna åtgärder gör att någon i **ManageDevices** -rollen kan skapa, uppdatera och ta bort enheter som är anslutna till lösningen:

    ```json
    {
    "Items": [
      {
        "Id": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
        "Role": "admin",
        "AllowedActions": [
          "UpdateAlarms",
          "DeleteAlarms",
          "CreateDevices",
          "UpdateDevices",
          "DeleteDevices",
          "CreateDeviceGroups",
          "UpdateDeviceGroups",
          "DeleteDeviceGroups",
          "CreateRules",
          "UpdateRules",
          "DeleteRules",
          "CreateJobs",
          "UpdateSimManagement"
        ]
      },
      {
        "Id": "e5bbd0f5-128e-4362-9dd1-8f253c6082d7",
        "Role": "readOnly",
        "AllowedActions": []
      },
      {
        "Id": "GUID FROM APP MANIFEST",
        "Role": "ManageDevices",
        "AllowedActions": [
          "CreateDevices",
          "UpdateDevices",
          "DeleteDevices"
        ]
      }
    ]
    }
    ```

1. När du är klar med att redigera **tjänsterna/data/policys/roles.jspå** en fil, måste du bygga om och omdistribuera mikrotjänsten för autentisering och auktorisering till Solution Accelerator.

### <a name="how-the-web-ui-enforces-permissions"></a>Så tillämpar webb gränssnittet behörigheter

Webb gränssnittet använder [mikrotjänsten autentisering och auktorisering](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/auth) för att avgöra vilka åtgärder en användare får utföra och vilka kontroller som visas i användar gränssnittet. Om din lösning till exempel kallas **contoso-RM4**hämtar webb gränssnittet en lista över tillåtna åtgärder för den aktuella användaren genom att skicka följande begäran:

```http
http://contoso-rm4.azurewebsites.net/v1/users/current
headers:
X-Source: true
Authorization: Bearer <JWT Token from ADAL>
```

För en användare som kallas **Enhetshanteraren** i **ManageDevices** -rollen innehåller svaret följande JSON i texten:

```json
{
  "Id": "userIdExample",
  "Email": "devicemanager@contoso.com",
  "Name": "Device Manager",
  "AllowedActions": [
    "CreateDevices",
    "UpdateDevices",
    "DeleteDevices"
  ]
}
```

Följande kodfragment från [deviceDelete.js](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/components/pages/devices/flyouts/deviceDelete/deviceDelete.js) i [webb gränssnittet](https://github.com/Azure/pcs-remote-monitoring-webui/) visar hur behörigheterna tvingas:

```json
<FlyoutContent>
  <Protected permission={permissions.deleteDevices}>
    <form className="device-delete-container" onSubmit={this.deleteDevices}>
      ...
    </form>
  </Protected>
</FlyoutContent>
```

Mer information finns i [skyddade komponenter](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/components/shared/protected/README.md). Du kan definiera ytterligare behörigheter i [authModel.js](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/services/models/authModels.js) -filen.

### <a name="how-the-microservices-enforce-permissions"></a>Hur mikrotjänsterna tvingar behörigheter

Mikrotjänsterna kontrollerar också behörigheter för att skydda mot oauktoriserade API-begäranden. När en mikrotjänst tar emot en API-begäran avkodas och verifierar JWT-token för att hämta användar-ID och behörigheter som är associerade med användarens roll.

Följande kodfragment från [DevicesController.cs](https://github.com/Azure/remote-monitoring-services-dotnet/blob/master/iothub-manager/WebService/v1/Controllers/DevicesController.cs) -filen i [IoTHub Manager mikroservice](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/iothub-manager), visar hur behörigheterna tillämpas:

```csharp
[HttpDelete("{id}")]
[Authorize("DeleteDevices")]
public async Task DeleteAsync(string id)
{
    await this.devices.DeleteAsync(id);
}
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur rollbaserad åtkomst kontroll implementeras i lösnings acceleratorn för fjärrövervakning.

Se [Konfigurera åtkomst kontroller för Time Series Insights Explorer](iot-accelerators-remote-monitoring-rbac-tsi.md) för information om hur du hanterar åtkomst till Time Series Insights Explorer i Solution Accelerator för fjärr övervakning.

Mer information om lösnings acceleratorn för fjärrövervakning finns i [arkitektur för fjärrövervakning](iot-accelerators-remote-monitoring-sample-walkthrough.md)

Mer information om hur du anpassar lösningen för fjärrövervakning finns i [Anpassa och distribuera om en mikrotjänst](iot-accelerators-microservices-example.md)
<!-- Next tutorials in the sequence -->