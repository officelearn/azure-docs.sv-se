---
title: Lägg till tjänsthuvudnamn i azure analysis services-administratörsrollen | Microsoft-dokument
description: Lär dig hur du lägger till ett huvudnamn för en automatiseringstjänst i serveradministratören för Azure Analysis Services
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.custom: fasttrack-edit
ms.openlocfilehash: 1370f65405963ebf825e986e6801607a0d96156e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78298096"
---
# <a name="add-a-service-principal-to-the-server-administrator-role"></a>Lägga till ett tjänsthuvudnamn i serveradministratörsrollen 

 Om du vill automatisera obevakade PowerShell-uppgifter **server administrator** måste ett tjänsthuvudnamn ha serveradministratörsbehörighet på Analysis Services-servern som hanteras. I den här artikeln beskrivs hur du lägger till ett tjänsthuvudnamn till serveradministratörsrollen på en Azure AS-server. Du kan göra detta med SQL Server Management Studio eller en Resource Manager-mall.

## <a name="before-you-begin"></a>Innan du börjar
Innan du slutför den här uppgiften måste du ha ett tjänsthuvudnamn registrerat i Azure Active Directory.

[Skapa tjänstens huvudnamn - Azure-portal](../active-directory/develop/howto-create-service-principal-portal.md)   
[Skapa tjänstens huvudnamn – PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="using-sql-server-management-studio"></a>Med hjälp av SQL Server Management Studio

Du kan konfigurera serveradministratörer med HJÄLP av SQL Server Management Studio (SSMS). För att kunna utföra den här uppgiften måste du ha [behörigheter för serveradministratörer](analysis-services-server-admins.md) på Azure AS-servern. 

1. Anslut till Din Azure AS-server i SSMS.
2. Klicka på Lägg **till**i**Säkerhet** **för serveregenskaper.** > 
3. Sök efter din registrerade app efter namn i **Välj en användare eller grupp**och klicka sedan på Lägg **till**.

    ![Sök efter tjänstens huvudkonto](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. Verifiera tjänstens huvudkonto-ID och klicka sedan på **OK**.
    
    ![Sök efter tjänstens huvudkonto](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)

## <a name="using-a-resource-manager-template"></a>Använda en Resource Manager-mall

Du kan också konfigurera serveradministratörer genom att distribuera Analysis Services-servern med hjälp av en Azure Resource Manager-mall. Identiteten som kör distributionen måste tillhöra **rollen Deltagare** för resursen i [RBAC (Azure Role-Based Access Control).](../role-based-access-control/overview.md)

> [!IMPORTANT]
> Tjänstens huvudnamn måste läggas `app:{service-principal-client-id}@{azure-ad-tenant-id}`till med formatet .

Följande Resource Manager-mall distribuerar en Analysis Services-server med ett angivet tjänsthuvudnamn som lagts till i rollen Administration av Analysis Services:

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

En hanterad identitet kan också läggas till i listan Administration av Analystjänster. Du kan till exempel ha en [Logic App med en systemtilldelad hanterad identitet](../logic-apps/create-managed-service-identity.md)och vill ge den möjlighet att administrera din Analysis Services-server.

I de flesta delar av Azure-portalen och API:er identifieras hanterade identiteter med hjälp av deras tjänsthuvudobjekt-ID. Analysis Services kräver dock att de identifieras med hjälp av sitt klient-ID. Om du vill hämta klient-ID:t för ett tjänsthuvudnamn kan du använda Azure CLI:

```bash
az ad sp show --id <ManagedIdentityServicePrincipalObjectId> --query appId -o tsv
```

Du kan också använda PowerShell:

```powershell
(Get-AzureADServicePrincipal -ObjectId <ManagedIdentityServicePrincipalObjectId>).AppId
```

Du kan sedan använda det här klient-ID:t tillsammans med klient-ID för att lägga till den hanterade identiteten i listan Administration av Analystjänster, enligt beskrivningen ovan.

## <a name="related-information"></a>Relaterad information

* [Ladda ned SQL Server PowerShell-modul](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Ladda ner SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   


