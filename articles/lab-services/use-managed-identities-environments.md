---
title: Använd Azure-hanterade identiteter för att skapa miljöer i DevTest Labs | Microsoft-dokument
description: Lär dig hur du använder hanterade identiteter i Azure för att distribuera miljöer i ett labb i Azure DevTest Labs.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: spelluru
ms.openlocfilehash: a4ba4206c01e492f2ae980c5806de1e72c7051c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73931162"
---
# <a name="use-azure-managed-identities-to-deploy-environments-in-a-lab"></a>Använda Azure-hanterade identiteter för att distribuera miljöer i ett labb 
Som labbägare kan du använda en hanterad identitet för att distribuera miljöer i ett labb. Den här funktionen är användbar i scenarier där miljön innehåller eller har referenser till Azure-resurser som nyckelvalv, delade bildgallerier och nätverk som är externa för miljöns resursgrupp. Det gör det möjligt att skapa sandbox-miljöer som inte är begränsade till resursgruppen för den miljön.

> [!NOTE]
> För närvarande stöds en enda användartilldelad identitet per labb. 

## <a name="prerequisites"></a>Krav
- [Skapa, lista, ta bort eller tilldela en roll till en användartilldelad hanterad identitet med Hjälp av Azure-portalen](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). 

## <a name="use-azure-portal"></a>Använda Azure-portalen
I det här avsnittet använder du som labbägare Azure-portalen för att lägga till en användarhanterad identitet i labbet. 

1. På labbsidan väljer du **Konfiguration och principer**. 
1. Välj **Identitet** i avsnittet **Inställningar.**
1. Om du vill lägga till en användarbedelad identitet väljer du **Lägg till** i verktygsfältet. 
1. Välj en **identitet** i en förifylld listruta.
1. Välj **OK**.

    ![Lägga till användarhanterad identitet](./media/use-managed-identities-environments/add-user-managed-identity.png)
2. Du ser den tillagda användarhanterade identiteten i listan. 

    ![Användarhanterad identitet i listan](./media/use-managed-identities-environments/identity-in-list.png)

När det har sparats använder labbet den här identiteten när du distribuerar alla labbmiljöer. Du kan också komma åt identitetsresursen i Azure genom att välja identiteten i listan. 

Labbägaren behöver inte göra något speciellt när du distribuerar en miljö så länge identiteten som läggs till i labbet har behörighet till de externa resurser som miljön behöver komma åt. 

Om du vill ändra den användarhanterade identitet som tilldelats labbet tar du först bort identiteten som är kopplad till labbet och lägger sedan till ytterligare en i labbet. Om du vill ta bort en identitet som är kopplad till labbet väljer du **... (ellips)** och klicka på **Ta bort**. 

![Användarhanterad identitet i listan](./media/use-managed-identities-environments/replace-identity.png)  

## <a name="use-api"></a>Använda API

1. När du har skapat en identitet noterar du resurs-ID:n för den här identiteten. Det bör se ut som följande exempel: 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
1. Utför en PUT Https-metod `ServiceRunner` för att lägga till en ny resurs i labbet som liknar följande exempel. Servicerunner-resurs är en proxyresurs för att hantera och styra hanterade identiteter i DevTest Labs. Tjänstlöparens namn kan vara valfritt giltigt namn, men vi rekommenderar att du använder namnet på den hanterade identitetsresursen. 
 
    ```json
    PUT https://management.azure.com/subscriptions/{subId}/resourceGroups/{rg}/providers/Microsoft.Devtestlab/labs/{yourlabname}/serviceRunners/{serviceRunnerName}

    {
        "location": "{location}",
        "identity":{
            "type": "userAssigned",
            "userAssignedIdentities":{
                "[userAssignedIdentityResourceId]":{}
            }
        }
        "properties":{
            "identityUsageType":"Environment"
                     }
          
    }
    ```
 
    Här är ett exempel: 

    ```json
    PUT https://management.azure.com/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/exampleRG/providers/Microsoft.Devtestlab/labs/mylab/serviceRunners/sampleuseridentity

    {
        "location": "eastus",
        "identity":{
            "type": "userAssigned",
            "userAssignedIdentities":{
                "/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/exampleRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/sampleuseridentity":{}
            }
        }
        "properties":{
            "identityUsageType":"Environment"
                     }
    }
    ```
 
När den tilldelade användaridentiteten har lagts till i labbet använder Azure DevTest Labs-tjänsten den när azure Resource Manager-miljöer distribueras. Om du till exempel behöver din Resource Manager-mall för att komma åt en extern bild av det delade bildgalleriet kontrollerar du att den identitet som du har lagt till i labbet har minsta behörighet för den delade bildgalleriresursen. 
