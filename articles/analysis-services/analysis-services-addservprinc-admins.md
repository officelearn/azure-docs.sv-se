---
title: Lägg till tjänstens huvud namn i Azure Analysis Services admin-rollen | Microsoft Docs
description: Lär dig hur du lägger till ett Automation-tjänstens huvud namn i rollen Azure Analysis Services Server-administratör
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.custom: fasttrack-edit
ms.openlocfilehash: 1370f65405963ebf825e986e6801607a0d96156e
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78298096"
---
# <a name="add-a-service-principal-to-the-server-administrator-role"></a>Lägg till ett huvud namn för tjänsten i Server administratörs rollen 

 För att automatisera obevakade PowerShell-aktiviteter måste ett huvud namn för tjänsten ha **Server administratörs** behörighet på den Analysis Services server som hanteras. Den här artikeln beskriver hur du lägger till ett huvud namn för tjänsten i rollen Server administratörer på en Azure AS-Server. Du kan göra detta med hjälp av SQL Server Management Studio eller en Resource Manager-mall.

## <a name="before-you-begin"></a>Innan du börjar
Innan du slutför den här uppgiften måste du ha ett huvud namn för tjänsten som registrerats i Azure Active Directory.

[Skapa tjänstens huvud namn – Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md)   
[Skapa tjänstens huvudnamn – PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="using-sql-server-management-studio"></a>Med hjälp av SQL Server Management Studio

Du kan konfigurera Server administratörer med SQL Server Management Studio (SSMS). För att slutföra den här uppgiften måste du ha [Server administratörs](analysis-services-server-admins.md) behörighet på Azure as Server. 

1. I SSMS ansluter du till din Azure AS-Server.
2. I **Server egenskaper** > **säkerhet**klickar du på **Lägg till**.
3. I **Välj en användare eller grupp**söker du efter din registrerade app efter namn, väljer och klickar sedan på **Lägg till**.

    ![Sök efter tjänstens huvud konto](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. Kontrol lera konto-ID för tjänstens huvud namn och klicka sedan på **OK**.
    
    ![Sök efter tjänstens huvud konto](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)

## <a name="using-a-resource-manager-template"></a>Använda en Resource Manager-mall

Du kan också konfigurera Server administratörer genom att distribuera Analysis Services-servern med hjälp av en Azure Resource Manager-mall. Den identitet som kör distributionen måste tillhöra rollen **deltagare** för resursen i [Azure ROLLBASERAD Access Control (RBAC)](../role-based-access-control/overview.md).

> [!IMPORTANT]
> Tjänstens huvud namn måste läggas till med formatet `app:{service-principal-client-id}@{azure-ad-tenant-id}`.

Följande Resource Manager-mall distribuerar en Analysis Services-server med ett angivet huvud namn för tjänsten som läggs till i rollen Analysis Services administratör:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "analysisServicesServerName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "analysisServicesSkuName": {
            "type": "string"
        },
        "analysisServicesCapacity": {
            "type": "int"
        },
        "servicePrincipalClientId": {
            "type": "string"
        },
        "servicePrincipalTenantId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('analysisServicesServerName')]",
            "type": "Microsoft.AnalysisServices/servers",
            "apiVersion": "2017-08-01",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('analysisServicesSkuName')]",
                "capacity": "[parameters('analysisServicesCapacity')]"
            },
            "properties": {
                "asAdministrators": {
                    "members": [
                        "[concat('app:', parameters('servicePrincipalClientId'), '@', parameters('servicePrincipalTenantId'))]"
                    ]
                }
            }
        }
    ]
}
```

## <a name="using-managed-identities"></a>Använda hanterade identiteter

Du kan också lägga till en hanterad identitet i listan Analysis Services admins. Du kan till exempel ha en [Logic-app med en systemtilldelad hanterad identitet](../logic-apps/create-managed-service-identity.md)och vill ge den möjlighet att administrera Analysis Services-servern.

I de flesta delar av Azure Portal och API: er identifieras hanterade identiteter med hjälp av objekt-ID för tjänstens huvud namn. Analysis Services kräver dock att de identifieras med hjälp av klient-ID. För att hämta klient-ID för ett huvud namn för tjänsten kan du använda Azure CLI:

```bash
az ad sp show --id <ManagedIdentityServicePrincipalObjectId> --query appId -o tsv
```

Du kan också använda PowerShell:

```powershell
(Get-AzureADServicePrincipal -ObjectId <ManagedIdentityServicePrincipalObjectId>).AppId
```

Du kan sedan använda det här klient-ID: t tillsammans med klient-ID: t för att lägga till den hanterade identiteten i listan Analysis Services admins, enligt beskrivningen ovan.

## <a name="related-information"></a>Relaterad information

* [Ladda ned SQL Server PowerShell-modul](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Ladda ned SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   


