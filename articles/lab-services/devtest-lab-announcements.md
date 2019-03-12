---
title: Publicera en announcment till ett labb i Azure DevTest Labs | Microsoft Docs
description: Lär dig hur du lägger till ett meddelande till ett labb i Azure DevTest Labs
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
ms.openlocfilehash: fca78c86849ef8210bb3aaeb8ad96e1fba3d7c05
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57542496"
---
# <a name="post-an-announcement-to-a-lab-in-azure-devtest-labs"></a>Publicera ett meddelande till ett labb i Azure DevTest Labs

Som en labb-administratör kan publicera du ett anpassat meddelande i en befintlig labbet för att meddela användare om de senaste ändringarna eller tillägg till labbet. Du kanske exempelvis vill informera användarna om:

- Nya VM-storlekar som är tillgängliga
- Bilder som är för närvarande inte kan användas
- Uppdateringar av principer för labbet

När publicerats meddelandet visas på översiktssidan i labbet och användaren kan välja för mer information.

Meddelande-funktionen är avsedd att användas för tillfälliga meddelanden.  När den inte längre behövs kan du enkelt inaktivera ett meddelande.

## <a name="steps-to-post-an-announcement-in-an-existing-lab"></a>Steg för att publicera ett meddelande i en befintlig labb

1. Logga in på [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Om det behövs väljer **alla tjänster**, och välj sedan **DevTest Labs** i listan. (Labbet kanske redan visas på instrumentpanelen under **alla resurser**).
1. I listan över labbar Välj labb där du vill publicera ett meddelande.  
1. På testmiljön **översikt** Välj **konfiguration och principer**.  

    ![Knappen för konfiguration och principer](./media/devtest-lab-announcements/devtestlab-config-and-policies.png)

1. Till vänster under **inställningar**väljer **Lab meddelande**.

    ![Lab meddelande knappen](./media/devtest-lab-announcements/devtestlab-announcements.png)

1. Så här skapar du ett meddelande för användare i den här övningen, **aktiverad** till **Ja**.

1. Du kan ange en **förfallodatum** att ange ett datum och tid då meddelandet inte längre visas för användare. Om du inte anger ett förfallodatum, förblir meddelandet tills du inaktiverar den.

   > [!NOTE]
   > När meddelandet har gått ut, den inte längre visas för användare, men finns fortfarande i den **Lab meddelande** fönstret. Du kan göra ändringar och återaktivera det att aktivera den igen.
   >
   >

1. Ange en **meddelande rubrik** och **meddelandetext**.

   Rubriken får innehålla högst 100 tecken och visas för användaren på översiktssidan för den testmiljön. Om användaren väljer rubriken, visas meddelandet texten.

   Meddelandetext accepterar markdown. När du anger meddelandetext, kan du visa meddelandet under exempel längst ned på skärmen.

    ![Lab meddelande skärmen för att skapa meddelandet.](./media/devtest-lab-announcements/devtestlab-post-announcement.png)


1. Välj **spara** när ditt meddelande är redo för publicering.

När du inte längre vill visa det här meddelandet till labbanvändare tillbaka till den **Lab meddelande** sidan och ange **aktiverad** till **nr**. Om du har angett ett förfallodatum inaktiveras meddelandet automatiskt vid denna tidpunkt.

## <a name="steps-for-users-to-view-an-announcement"></a>Steg för användare att visa ett meddelande

1. Från den [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040), Välj ett labb.

1. Om labbet har ett meddelande som publicerats för den, visas ett meddelande längst ned i labbet översiktssidan. Detta meddelande om information är meddelande rubriken som angavs när meddelandet skapades.

    ![Lab meddelande-översikt](./media/devtest-lab-announcements/devtestlab-user-announcement.png)

1. Användaren kan välja meddelandet för att visa hela meddelandet.

    ![Mer information på labb-meddelandet](./media/devtest-lab-announcements/devtestlab-user-announcement-text.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-mall
Du kan ange ett meddelande som en del av en Azure Resource Manager-mall som du ser i följande exempel: 

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

Du kan distribuera en Azure Resource Manager-mall med hjälp av något av följande sätt:

- [Azure Portal](../azure-resource-manager/resource-group-template-deploy-portal.md)
- [Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
- [Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md)
- [REST-API](../azure-resource-manager/resource-group-template-deploy-rest.md)

## <a name="next-steps"></a>Nästa steg
* Om du ändrar eller skapa en princip för labbet, kanske du vill publicera ett meddelande för att informera användare. [Ange principer och scheman](devtest-lab-set-lab-policy.md) innehåller information om hur du tillämpar begränsningar och konventioner i hela prenumerationen genom att använda anpassade principer.
* Utforska den [DevTest Labs Azure Resource Manager QuickStart mallgalleriet](https://github.com/Azure/azure-devtestlab/tree/master/Samples).
