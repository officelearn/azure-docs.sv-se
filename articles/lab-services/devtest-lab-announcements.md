---
title: Publicera en utlysning i ett labb i Azure DevTest Labs | Microsoft-dokument
description: Lär dig hur du lägger till ett meddelande i ett labb i Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: 67a09946-4584-425e-a94c-abe57c9cbb82
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 84120b07de3a03a049493eb973b6dc46f8668387
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75976455"
---
# <a name="post-an-announcement-to-a-lab-in-azure-devtest-labs"></a>Publicera ett meddelande i ett labb i Azure DevTest Labs

Som labbadministratör kan du publicera ett anpassat meddelande i ett befintligt labb för att meddela användarna om de senaste ändringarna eller tilläggen i labbet. Du kanske till exempel vill informera användarna om:

- Nya VM-storlekar som är tillgängliga
- Bilder som för närvarande är ouppnbara
- Uppdateringar av labbprinciper

När meddelandet har publicerats visas det på labbets översiktssida och användaren kan välja det för mer information.

Meddelandefunktionen är avsedd att användas för tillfälliga meddelanden.  Du kan enkelt inaktivera ett meddelande när det inte längre behövs.

## <a name="steps-to-post-an-announcement-in-an-existing-lab"></a>Steg för att publicera ett meddelande i ett befintligt labb

1. Logga in på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Om det behövs väljer du **Alla tjänster**och väljer sedan **DevTest Labs** i listan. (Ditt labb kanske redan visas på instrumentpanelen under **Alla resurser**).
1. Välj det labb där du vill publicera ett meddelande i listan över labb.
1. I labbets **översiktsområde** väljer du **Konfiguration och principer**.

    ![Knappen Konfiguration och principer](./media/devtest-lab-announcements/devtestlab-config-and-policies.png)

1. Till vänster under **INSTÄLLNINGAR**väljer du **Lab announcement**.

    ![Knappen Tillkännagivande av labb](./media/devtest-lab-announcements/devtestlab-announcements.png)

1. Om du vill skapa ett meddelande för användarna i det här labbet anger du **Aktiverad** till **Ja**.

1. Du kan ange ett **utgångsdatum** för att ange ett datum och en tid efter vilket meddelandet inte längre visas för användarna. Om du inte anger något utgångsdatum kvarstår meddelandet tills du inaktiverar det.

   > [!NOTE]
   > När meddelandet har gått ut visas det inte längre för användarna, men finns fortfarande i **fönstret Lab-meddelande.** Du kan göra ändringar i den och återaktivera den så att den blir aktiv igen.
   >
   >

1. Ange en **meddelanderubrik** och **meddelandetexten**.

   Titeln kan vara upp till 100 tecken och visas för användaren på labbets översiktssida. Om användaren väljer rubriken visas meddelandetexten.

   Meddelandetexten accepterar markering. När du anger meddelandetexten kan du visa meddelandet i förhandsgranskningsområdet längst ned på skärmen.

    ![Labbmeddelandeskärmen för att skapa meddelandet.](./media/devtest-lab-announcements/devtestlab-post-announcement.png)


1. Välj **Spara** när meddelandet är klart att publiceras.

När du inte längre vill visa det här meddelandet för labbanvändare går du tillbaka till sidan **Labbmeddelande** och anger **Aktiverad** till **Nej**. Om du har angett ett utgångsdatum inaktiveras meddelandet automatiskt vid det datumet och tidpunkten.

## <a name="steps-for-users-to-view-an-announcement"></a>Steg för användare att visa ett meddelande

1. Välj ett labb på [Azure-portalen.](https://go.microsoft.com/fwlink/p/?LinkID=525040)

1. Om labbet har ett meddelande publicerat för det visas ett informationsmeddelande högst upp på labbets översiktssida. Detta informationsmeddelande är meddelandetiteln som angavs när meddelandet skapades.

    ![Labbmeddelande på översiktssidan](./media/devtest-lab-announcements/devtestlab-user-announcement.png)

1. Användaren kan välja meddelandet för att visa hela meddelandet.

    ![Mer information för labbmeddelandet](./media/devtest-lab-announcements/devtestlab-user-announcement-text.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-mall
Du kan ange ett meddelande som en del av en Azure Resource Manager-mall som visas i följande exempel:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "string",
            "defaultValue": "devtestlabfromarm"
        },
        "regionId": {
            "type": "string",
            "defaultValue": "eastus"
        }
    },
    "resources": [
        {
            "apiVersion": "2017-04-26-preview",
            "name": "[parameters('name')]",
            "type": "Microsoft.DevTestLab/labs",
            "location": "[parameters('regionId')]",
            "tags": {},
            "properties": {
                "labStorageType": "Premium",
                "announcement":
                {
                    "title": "Important! Three images are currently inaccessible. Click for more information.",
                    "markdown":"# Images are inaccessible\n\nThe following 3 images are currently not available for use: \n\n- image1\n- image2\n- image3\n\nI am working to fix the problem ASAP.",
                    "enabled": "Enabled",
                    "expirationDate":"2018-12-31T06:00:00+00:00",
                    "expired": "false"
                },
                "support": {
                    "markdown": "",
                    "enabled": "Enabled"
                }
            },
            "resources": [
                {
                    "apiVersion": "2017-04-26-preview",
                    "name": "LabVmsShutdown",
                    "location": "[parameters('regionId')]",
                    "type": "schedules",
                    "dependsOn": [
                        "[resourceId('Microsoft.DevTestLab/labs', parameters('name'))]"
                    ],
                    "properties": {
                        "status": "Enabled",
                        "timeZoneId": "Eastern Standard Time",
                        "dailyRecurrence": {
                            "time": "1900"
                        },
                        "taskType": "LabVmsShutdownTask",
                        "notificationSettings": {
                            "status": "Disabled",
                            "timeInMinutes": 30
                        }
                    }
                },
                {
                    "apiVersion": "2017-04-26-preview",
                    "name": "[concat('Dtl', parameters('name'))]",
                    "type": "virtualNetworks",
                    "location": "[parameters('regionId')]",
                    "dependsOn": [
                        "[resourceId('Microsoft.DevTestLab/labs', parameters('name'))]"
                    ]
                }
            ]
        }
    ]
}
```

Du kan distribuera en Azure Resource Manager-mall på något av följande sätt:

- [Azure-portal](../azure-resource-manager/templates/deploy-portal.md)
- [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
- [Azure CLI](../azure-resource-manager/templates/deploy-cli.md)
- [REST API](../azure-resource-manager/templates/deploy-rest.md)

## <a name="next-steps"></a>Nästa steg
* Om du ändrar eller anger en labbprincip kanske du vill publicera ett meddelande för att informera användarna. [Ange principer och scheman](devtest-lab-set-lab-policy.md) innehåller information om hur du tillämpar begränsningar och konventioner i din prenumeration med hjälp av anpassade principer.
* Utforska [snabbstartsgalleriet DevTest Labs Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
