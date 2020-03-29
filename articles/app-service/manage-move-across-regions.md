---
title: Flytta en app till en annan region
description: Läs om hur du flyttar App Service-resurser från en region till en annan.
ms.topic: how-to
ms.date: 02/27/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 7e68f12ce062831ad361c88345188aca61922c4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77925714"
---
# <a name="move-an-app-service-app-to-another-region"></a>Flytta en App Service-app till en annan region

I den här artikeln beskrivs hur du flyttar App Service-resurser till en annan Azure-region. Du kan flytta dina resurser till en annan region av flera skäl. Om du till exempel vill dra nytta av en ny Azure-region, distribuera funktioner eller tjänster som endast är tillgängliga i specifika regioner, för att uppfylla interna princip- och styrningskrav eller som svar på kapacitetsplaneringskrav.

App Service-resurser är regionspecifika och kan inte flyttas mellan regioner. Du måste skapa en kopia av dina befintliga App Service-resurser i målregionen och flytta över innehållet till den nya appen. Om källappen använder en anpassad domän kan du [migrera den till den nya appen i målområdet](manage-custom-dns-migrate-domain.md) när du är klar.

Om du vill göra det enklare att kopiera appen kan du [klona en enskild App Service-app](app-service-web-app-cloning.md) till en App Service-plan i en annan region, men den har [begränsningar,](app-service-web-app-cloning.md#current-restrictions)särskilt att den inte stöder Linux-appar.

## <a name="prerequisites"></a>Krav

- Kontrollera att App Service-appen finns i Azure-regionen som du vill flytta från.
- Kontrollera att målregionen stöder App Service och alla relaterade tjänster, vars resurser du vill flytta.
<!-- - Domain bindings, certificates, and managed identities can't replicated using the **Export template** method. You must create them manually. -->

## <a name="prepare"></a>Förbereda

Identifiera alla App Service-resurser som du använder för tillfället. Ett exempel:

- App Service-appar
- [App Service-planer](overview-hosting-plans.md)
- [Distributionsplatser](deploy-staging-slots.md)
- [Anpassade domäner som köpts i Azure](manage-custom-dns-buy-domain.md)
- [SSL-certifikat](configure-ssl-certificate.md)
- [Integrering av virtuella Azure-nätverk](web-sites-integrate-with-vnet.md)
- [Hybridanslutningar](app-service-hybrid-connections.md).
- [Hanterade identiteter](overview-managed-identity.md)
- [Inställningar för säkerhetskopiering](manage-backup.md)

Vissa resurser, till exempel importerade certifikat eller hybridanslutningar, innehåller integrering med andra Azure-tjänster. Information om hur du flyttar dessa resurser mellan regioner finns i dokumentationen för respektive tjänster.

## <a name="move"></a>Flytta

1. [Skapa en säkerhetskopiering av källappen](manage-backup.md).
1. [Skapa en app i en ny App Service-plan i målregionen](app-service-plan-manage.md#create-an-app-service-plan).
2. [Återställa säkerhetskopieringen i målappen](web-sites-restore.md)
2. Om du använder en anpassad domän [binder du den i förebyggande syfte till målappen](manage-custom-dns-migrate-domain.md#bind-the-domain-name-preemptively) med `awverify.` och aktiverar [domänen i målappen](manage-custom-dns-migrate-domain.md#enable-the-domain-for-your-app).
3. Konfigurera allt annat i målappen så att det blir som källappen och verifiera konfigurationen.
4. När du är redo för den anpassade domänen att peka på [målappen mappas om domännamnet](manage-custom-dns-migrate-domain.md#remap-the-active-dns-name).

<!-- 1. Login to the [Azure portal](https://portal.azure.com) > **Resource Groups**.
2. Locate the Resource Group that contains the source App Service resources and click on it.
3. Select > **Settings** > **Export template**.
4. Choose **Deploy** in the **Export template** blade.
5. Click **TEMPLATE** > **Edit template** to open the template in the online editor.
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"identity": {` to find any managed identity definition. The following is an example if you have a user-assigned managed identity.
    ```json
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/<group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<identity-name>": {
                "principalId": "00000000-0000-0000-0000-000000000000",
                "clientId": "00000000-0000-0000-0000-000000000000"
            }
        }
    },
    ```
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"Microsoft.Web/sites/hostNameBindings` to find all hostname bindings. The following is an example if you have a user-assigned managed identity.
    ```json
    {
        "type": "Microsoft.Web/sites/hostNameBindings",
        "apiVersion": "2018-11-01",
        "name": "[concat(parameters('sites_webapp_name'), '/', parameters('sites_webapp_name'), '.azurewebsites.net')]",
        "location": "West Europe",
        "dependsOn": [
            "[resourceId('Microsoft.Web/sites', parameters('sites_webapp_name'))]"
        ],
        "properties": {
            "siteName": "<app-name>",
            "hostNameType": "Verified"
        }
    },
    ```
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"Microsoft.Web/certificates` to find all hostname bindings. The following is an example if you have a user-assigned managed identity.
    ```json
    {
        "type": "Microsoft.Web/certificates",
        "apiVersion": "2018-11-01",
        "name": "[parameters('certificates_test2_cephaslin_com_name')]",
        "location": "West Europe",
        "properties": {
            "hostNames": [
                "[parameters('certificates_test2_cephaslin_com_name')]"
            ],
            "password": "[parameters('certificates_test2_cephaslin_com_password')]"
        }
    },
    ```
7. Delete the entire JSON block. Click **Save** in the online editor.
8. Click **BASICS** > **Create new** to create a new resource group. Type the group name and click **OK**.
9. In **BASICS** > **Location**, select the region you want.   -->

## <a name="clean-up-source-resources"></a>Rensa källresurser

Ta bort källappen och apptjänstplanen. [En App Service-plan på den icke-fria nivån medför en avgift, även om ingen app körs i den.](app-service-plan-manage.md#delete-an-app-service-plan)

## <a name="next-steps"></a>Nästa steg

[Kloning av Azure App-tjänst-appar med PowerShell](app-service-web-app-cloning.md)