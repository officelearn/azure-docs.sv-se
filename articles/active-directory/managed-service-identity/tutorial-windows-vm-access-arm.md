---
title: Använda en MSI på en virtuell Windows-dator för att få åtkomst till Azure Resource Manager
description: En självstudiekurs som steg för steg beskriver hur du får åtkomst till Azure Resource Manager med hjälp av en hanterad tjänstidentitet på en virtuell Windows-dator.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: daveba
ms.openlocfilehash: 5dc4f498c416142977c5570cddf8b380a8c02ab4
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/24/2018
ms.locfileid: "42885149"
---
# <a name="use-a-windows-vm-managed-service-identity-to-access-resource-manager"></a>Använda en hanterad tjänstidentitet på en virtuell Windows-dator för att få åtkomst till Azure Resource Manager

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Den här snabbstarten visar hur du kommer åt Azure Resource Manager-API:t via en virtuell Windows-dator med aktiverad systemtilldelad identitet. Hanterade tjänstidentiteter hanteras automatiskt av Azure och gör att du kan autentisera mot tjänster som stöder Azure Active Directory-autentisering, utan att du behöver bädda in autentiseringsuppgifter i din kod. Lär dig att:

> [!div class="checklist"] 
> * Ge den virtuella datorn åtkomst till en resursgrupp i Azure Resource Manager 
> * Hämta en åtkomsttoken med hjälp av den virtuella datorns identitet och använda den för att anropa Azure Resource Manager

## <a name="prerequisites"></a>Nödvändiga komponenter

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

- [Logga in på Azure-portalen](https://portal.azure.com)

- [Skapa en virtuell Windows-dator](/azure/virtual-machines/windows/quick-create-portal)

- [Aktivera systemtilldelad identitet på den virtuella datorn](/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm#enable-system-assigned-identity-on-an-existing-vm)

## <a name="grant-your-vm-access-to-a-resource-group-in-resource-manager"></a>Ge den virtuella datorn åtkomst till en resursgrupp i Resource Manager
Med hjälp av en hanterade tjänstidentitet kan din kod hämta åtkomsttoken och autentisera mot resurser som stöder Azure AD-autentisering.  Azure Resource Manager har stöd för Azure AD-autentisering.  Först måste vi ge den virtuella datorns identitet åtkomst till en resurs i Resource Manager, i detta fall den resursgrupp som den virtuella datorn finns i.  

1.  Gå till fliken för **resursgrupper**. 
2.  Välj den specifika **resursgrupp** som du skapade för den **virtuella Windows-datorn**. 
3.  Gå till **Åtkomstkontroll (IAM)** på den vänstra panelen. 
4.  **Lägg till** en ny rolltilldelning för den **virtuella Windows-datorn**.  Välj **rollen** som **läsare**. 
5.  I nästa listruta **tilldelar du behörighet till** resursen **Virtuell dator**. 
6.  Kontrollera sedan att rätt prenumeration visas i listrutan **Prenumeration**. Och för **Resursgrupp** väljer du **Alla resursgrupper**. 
7.  I **Välj** väljer du slutligen din virtuella Windows-dator i listrutan och klickar på **Spara**.

    ![Alternativ bildtext](media/msi-tutorial-windows-vm-access-arm/msi-windows-permissions.png)

## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-call-azure-resource-manager"></a>Hämta en åtkomsttoken med hjälp av den virtuella datorns identitet och använd den för att anropa Azure Resource Manager 

Du måste använda **PowerShell** i den här delen.  Om du inte har installerat PowerShell kan du hämta det [här](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.3.1). 

1.  Gå till **Virtuella datorer** på portalen och sedan till den virtuella Windows-datorn. Under **Översikt** klickar du på **Anslut**. 
2.  Ange ditt **användarnamn** och **lösenord** som du lade till när du skapade den virtuella Windows-datorn. 
3.  Nu när du har skapat en **anslutning till fjärrskrivbord** med den virtuella datorn öppnar du **PowerShell** i fjärrsessionen. 
4.  Använd PowerShells Invoke-WebRequest och skicka en begäran till den lokala slutpunkten för hanterad tjänstidentitet för att hämta en åtkomsttoken för Azure Resource Manager.

    ```powershell
       $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Method GET -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > Värdet för parametern ”resource” måste vara en exakt matchning av vad som förväntas av Azure AD. När du använder Azure Resource Manager-resurs-ID:t måste du ta med det avslutande snedstrecket i URI:n.
    
    Extrahera sedan hela svaret som lagras som en JSON-formaterad sträng (JavaScript Object Notation) i $response-objektet. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    Extrahera sedan åtkomsttoken från svaret.
    
    ```powershell
    $ArmToken = $content.access_token
    ```
    
    Anropa slutligen Azure Resource Manager med din åtkomsttoken. I det här exemplet använder vi även PowerShells Invoke-WebRequest för att göra anropet till Azure Resource Manager och lägga till vår åtkomsttoken i auktoriseringshuvudet.
    
    ```powershell
    (Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-06-01 -Method GET -ContentType "application/json" -Headers @{ Authorization ="Bearer $ArmToken"}).content
    ```
    > [!NOTE] 
    > Eftersom URL:en är skiftlägeskänslig måste du använda exakt samma skiftläge som du använde tidigare när du namngav resursgruppen, samt versalt ”G” i ”resourceGroups”.
        
    Följande kommando returnerar information om resursgruppen:

    ```powershell
    {"id":"/subscriptions/98f51385-2edc-4b79-bed9-7718de4cb861/resourceGroups/DevTest","name":"DevTest","location":"westus","properties":{"provisioningState":"Succeeded"}}
    ```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du skapar en användartilldelad identitet och kopplar den till en virtuell Azure-dator för att komma åt Azure Resource Manager-API:et.  Mer information om Azure Resource Manager finns här:

> [!div class="nextstepaction"]
>[Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview)

