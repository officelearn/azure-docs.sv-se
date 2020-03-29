---
title: Åtkomstkontroll för fjärrövervakning – Azure | Microsoft-dokument
description: Den här artikeln innehåller information om hur du kan konfigurera rollbaserade åtkomstkontroller (RBAC) i lösningsacceleratorn för fjärrövervakning
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.openlocfilehash: b0c9699bccbb539c9617fac2f3296483139e7188
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67203172"
---
# <a name="configure-role-based-access-controls-in-the-remote-monitoring-solution-accelerator"></a>Konfigurera rollbaserade åtkomstkontroller i lösningsacceleratorn för fjärrövervakning

Den här artikeln innehåller information om hur du konfigurerar rollbaserade åtkomstkontroller i lösningsacceleratorn för fjärrövervakning. Med rollbaserade åtkomstkontroller kan du begränsa åtkomsten för enskilda användare till specifika funktioner i lösningen.

## <a name="default-settings"></a>Standardinställningar

När du först distribuerar lösningen för fjärrövervakning finns det två roller: **Admin** och **Skrivskyddad**.

Alla användare i **administratörsrollen** har fullständig åtkomst till lösningen, inklusive följande behörigheter nedan. En användare i rollen **Skrivskydd** har bara åtkomst till att visa lösningen.

| Behörighet            | Admin | Skrivskydd |
|----------------       |-------|-----------|
| Visa lösning         | Ja   | Ja       |
| Uppdatera larm         | Ja   | Inga        |
| Ta bort larm         | Ja   | Inga        |
| Skapa enheter        | Ja   | Inga        |
| Uppdatera enheter        | Ja   | Inga        |
| Ta bort enheter        | Ja   | Inga        |
| Skapa enhetsgrupper  | Ja   | Inga        |
| Uppdatera enhetsgrupper  | Ja   | Inga        |
| Ta bort enhetsgrupper  | Ja   | Inga        |
| Skapa regler          | Ja   | Inga        |
| Uppdatera regler          | Ja   | Inga        |
| Ta bort regler          | Ja   | Inga        |
| Skapa jobb           | Ja   | Inga        |
| Uppdatera SIM-hantering | Ja   | Inga        |

Som standard tilldelas användaren som distribuerade lösningen automatiskt **administratörsrollen** och är ägare till Azure Active Directory-program. Som programägare kan du tilldela roller till andra användare via Azure-portalen. Om du vill att en annan användare ska tilldela roller i lösningen måste de också anges som programägare i Azure-portalen.

> [!NOTE]
> Användaren som distribuerade lösningen är den **enda person** som kan visa den direkt efter att den har skapats. Om du vill ge andra åtkomst att visa programmet som antingen en skrivskyddad, administratör eller en anpassad roll läser du följande anvisningar nedan om att lägga till eller ta bort användare.

## <a name="add-or-remove-users"></a>Lägga till eller ta bort användare

Som ägare av Azure Active Directory-program kan du använda Azure-portalen för att lägga till eller ta bort en användare till en roll från lösningen för fjärrövervakning. I följande steg används [Azure Active Directory-företagsprogrammet](../active-directory/manage-apps/add-application-portal.md#find-your-azure-ad-tenant-application) som skapades när du distribuerade lösningen för fjärrövervakning.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Kontrollera [att användaren finns i den katalog](../active-directory/fundamentals/add-users-azure-active-directory.md) du använder. Du valde den katalog som ska användas när du loggade in på webbplatsen [Microsoft Azure IoT Solution Accelerators.](https://www.azureiotsolutions.com/Accelerators) Katalognamnet visas längst upp till höger på [sidan](https://www.azureiotsolutions.com/Accelerators).

1. Hitta **Enterprise-programmet** för din lösning i Azure-portalen. En gång där, filtrera listan genom att ställa **in Programtyp** till **alla program**. Sök efter ditt program efter programnamn. Programnamnet är namnet på fjärrövervakningslösningen. I följande skärmdump är lösningen och programvisningsnamnen **contoso-rm4**.

    ![Program för företag](media/iot-accelerators-remote-monitoring-rbac/appregistration.png)

1. Kontrollera att du är ägare till programmet genom att klicka på programmet och sedan klicka på **Ägare**. I följande skärmdump är **Contoso admin** en ägare av **contoso-rm4** ansökan:

    ![Ägare](media/iot-accelerators-remote-monitoring-rbac/owners.png)

    Om du inte är ägare måste du be en befintlig ägare att lägga till dig i listan. Endast ägare kan tilldela programroller som **Admin** eller **Skrivskydd** till andra användare.

1. Klicka på **Användare och grupper**om du vill visa listan över användare som tilldelats roller i programmet .

1. Om du vill lägga till en användare klickar du på **+ Lägg till användare**och klickar sedan på Användare och **grupper, Ingen markerad** för att välja en användare i katalogen.

1. Om du vill tilldela användaren till en roll klickar du på **Välj roll, Ingen markerad** och väljer antingen rollen **Admin** eller **Skrivskydd** för användaren. Klicka på **Markera**och sedan på **Tilldela**.

    ![Välja en roll](media/iot-accelerators-remote-monitoring-rbac/selectrole.png)

1. Användaren kan nu komma åt lösningen för fjärrövervakning med de behörigheter som definierats av rollen.

1. Du kan ta bort användare från programmet på sidan **Användare och grupper** i portalen.

## <a name="create-a-custom-role"></a>Skapa en anpassad roll

Lösningen för fjärrövervakning innehåller rollerna **Admin** och **Skrivskydd** när den först distribueras. Du kan lägga till anpassade roller med olika behörighetsgrupper. Om du vill definiera en anpassad roll måste du:

- Lägg till en ny roll i programmet i Azure-portalen.
- Definiera en princip för den nya rollen i mikrotjänsten Autentisering och auktorisering.
- Uppdatera lösningens webbgränssnitt.

### <a name="define-a-custom-role-in-the-azure-portal"></a>Definiera en anpassad roll i Azure-portalen

I följande steg beskrivs hur du lägger till en roll i ett program i Azure Active Directory. I det här exemplet skapar du en ny roll som gör det möjligt för medlemmar att skapa, uppdatera och ta bort enheter i lösningen för fjärrövervakning.

1. Hitta **appregistreringen** för din lösning i Azure-portalen. Programnamnet är namnet på fjärrövervakningslösningen. I följande skärmdump är lösningen och programvisningsnamnen **contoso-rm4**.

    ![Appregistrering](media/iot-accelerators-remote-monitoring-rbac/app-registration-2.png)

1. Markera ditt program och klicka sedan på **Manifest**. Du kan se de två befintliga [approllerna](https://docs.microsoft.com/azure/architecture/multitenant-identity/app-roles) som definierats för programmet:

    ![Visa manifest](media/iot-accelerators-remote-monitoring-rbac/view-manifest.png)

1. Redigera manifestet om du vill lägga till en roll som heter **ManageDevices** enligt följande utdrag. Du behöver en unik sträng som ett GUID för det nya roll-ID:t. Du kan generera ett nytt GUID med hjälp av en tjänst som [Online GUID Generator:](https://www.guidgenerator.com/)

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

När du har lagt till rollen i appen i Azure-portalen måste du definiera en princip i [roles.json](https://github.com/Azure/remote-monitoring-services-dotnet/blob/master/auth/Services/data/policies/roles.json) för den roll som tilldelar de behörigheter som behövs för att hantera enheter.

1. Klona databasen [för mikrotjänster för fjärrövervakning](https://github.com/Azure/remote-monitoring-services-dotnet) från GitHub till den lokala datorn.

1. Redigera **filen auth/Services/data/policies/roles.json** om du vill lägga till principen för rollen **ManageDevices** enligt följande kodavsnitt. **ID-** och **rollvärdena** måste matcha rolldefinitionen i appmanifestet från föregående avsnitt. Listan över tillåtna åtgärder gör det möjligt för någon i rollen **ManageDevices** att skapa, uppdatera och ta bort enheter som är anslutna till lösningen:

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

1. När du har redigerat filen **Tjänster/data/policies/roles.json** återskapas och distribueras om mikrotjänsten Autentisering och auktorisering till lösningsacceleratorn.

### <a name="how-the-web-ui-enforces-permissions"></a>Så här tillämpar webbgränssnittet behörigheter

Webbgränssnittet använder [mikrotjänsten Autentisering och auktorisering](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/auth) för att avgöra vilka åtgärder en användare får vidta och vilka kontroller som är synliga i användargränssnittet. Om lösningen till exempel kallas **contoso-rm4**hämtar webbgränssnittet en lista över tillåtna åtgärder för den aktuella användaren genom att skicka följande begäran:

```http
http://contoso-rm4.azurewebsites.net/v1/users/current
headers:
X-Source: true
Authorization: Bearer <JWT Token from ADAL>
```

För en användare som heter **Enhetshanteraren** i rollen **ManageDevices** innehåller svaret följande JSON i brödtexten:

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

Följande kodavsnitt från [deviceDelete.js](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/components/pages/devices/flyouts/deviceDelete/deviceDelete.js) i [webbgränssnittet](https://github.com/Azure/pcs-remote-monitoring-webui/) visar hur behörigheterna tillämpas deklarativt:

```json
<FlyoutContent>
  <Protected permission={permissions.deleteDevices}>
    <form className="device-delete-container" onSubmit={this.deleteDevices}>
      ...
    </form>
  </Protected>
</FlyoutContent>
```

Mer information finns i [Skyddade komponenter](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/components/shared/protected/README.md). Du kan definiera ytterligare behörigheter i [authModel.js-filen.](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/services/models/authModels.js)

### <a name="how-the-microservices-enforce-permissions"></a>Så här framtvingar mikrotjänster behörigheter

Mikrotjänsterna kontrollerar också behörigheter för att skydda mot obehöriga API-begäranden. När en mikrotjänst tar emot en API-begäran avkodar och valideras JWT-token för att hämta användar-ID och behörigheter som är associerade med användarens roll.

Följande kodavsnitt från [DevicesController.cs-filen](https://github.com/Azure/remote-monitoring-services-dotnet/blob/master/iothub-manager/WebService/v1/Controllers/DevicesController.cs) i [mikrotjänsten IoTHub Manager](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/iothub-manager)visar hur behörigheterna tillämpas:

```csharp
[HttpDelete("{id}")]
[Authorize("DeleteDevices")]
public async Task DeleteAsync(string id)
{
    await this.devices.DeleteAsync(id);
}
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln fick du lära dig hur rollbaserade åtkomstkontroller implementeras i lösningsacceleratorn för fjärrövervakning.

Se [Konfigurera åtkomstkontroller för Time Series Insights explorer](iot-accelerators-remote-monitoring-rbac-tsi.md) för information om hur du hanterar åtkomsten till Time Series Insights-utforskaren i lösningsacceleratorn för fjärrövervakning.

Mer begreppsmässig information om lösningsacceleratorn för fjärrövervakning finns i [Fjärrövervakningsarkitektur](iot-accelerators-remote-monitoring-sample-walkthrough.md)

Mer information om hur du anpassar lösningen för fjärrövervakning finns i [Anpassa och distribuera om en mikrotjänst](iot-accelerators-microservices-example.md)
<!-- Next tutorials in the sequence -->