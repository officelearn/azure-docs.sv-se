---
title: Använd en virtuell MSI-dator för Windows för att få åtkomst till Azure Resource Manager
description: En självstudiekurs som vägleder dig genom processen med att använda en Windows VM hanterad tjänstidentitet (MSI) för att få åtkomst till Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: a7960ab4aee80c7d15ea0f031790dd089424565d
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38610265"
---
# <a name="use-a-windows-vm-managed-service-identity-msi-to-access-resource-manager"></a>Använd en Windows VM hanterad tjänstidentitet (MSI) för att få åtkomst till Resource Manager

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Den här självstudien visar hur du aktiverar hanterad tjänstidentitet (MSI) för en Windows virtuell dator (VM). Du kan sedan använda den identiteten för att få åtkomst till Azure Resource Manager-API. Hanterade tjänstidentiteter hanteras automatiskt av Azure och gör att du kan autentisera till tjänster som stöder Azure AD-autentisering utan att behöva infoga autentiseringsuppgifter i din kod. Lär dig att:

> [!div class="checklist"]
> * Aktivera MSI på en Windows VM 
> * Bevilja åtkomst till dina virtuella datorer till en resursgrupp i Azure Resource Manager 
> * Få ett åtkomsttoken med hjälp av identitet för virtuell dator och använda den för att anropa Azure Resource Manager

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Logga in på Azure
Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Skapa en Windows-dator i en ny resursgrupp.

I den här självstudiekursen skapar vi en ny virtuell Windows-dator.  Du kan också aktivera MSI på en befintlig virtuell dator.

1.  Klicka på **skapa en resurs** på det övre vänstra hörnet i Azure-portalen.
2.  Välj **Compute**, och välj sedan **Windows Server 2016 Datacenter**. 
3.  Ange informationen för den virtuella datorn. Den **användarnamn** och **lösenord** skapade här är de autentiseringsuppgifter som du använder för att logga in på den virtuella datorn.
4.  Välj rätt **prenumeration** för den virtuella datorn i listrutan.
5.  Att välja en ny **resursgrupp** där du kan skapa den virtuella datorn, väljer **Skapa ny**. När du är klar klickar du på **OK**.
6.  Välj storlek för den virtuella datorn. Om du vill se fler storlekar väljer du **Visa alla** eller så ändrar du filtret för **disktyper som stöds**. Behåll standardinställningarna på sidan Inställningar och klickar på **OK**.

    ![ALT bildtext](~/articles/active-directory/media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Aktivera MSI på den virtuella datorn 

En VM MSI kan du hämta åtkomsttoken från Azure AD utan att du behöver att placera autentiseringsuppgifter i din kod. När du aktiverar MSI meddelar Azure att skapa en hanterad identitet för den virtuella datorn. Under försättsbladen, när du aktiverar MSI gör två saker: MSI VM-tillägget installeras på den virtuella datorn och gör att MSI i Azure Resource Manager.

1.  Välj den **VM** att du vill aktivera MSI på.  
2.  I det vänstra navigeringsfältet klickar du på **Configuration**. 
3.  Du ser **hanterad tjänstidentitet**. Om du vill registrera och aktiverar MSI, Välj **Ja**, om du vill inaktivera det, väljer du Nej. 
4.  Se till att du klickar på **spara** att spara konfigurationen.  
    ![ALT bildtext](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Om du vill kontrollera och vilka tillägg som finns på den här virtuella datorn klickar du på **tillägg**. Om MSI aktiveras sedan **ManagedIdentityExtensionforWindows** kommer att visas i listan.

    ![ALT bildtext](~/articles/active-directory/media/msi-tutorial-windows-vm-access-arm/msi-windows-extension.png)

## <a name="grant-your-vm-access-to-a-resource-group-in-resource-manager"></a>Bevilja åtkomst till dina virtuella datorer till en resursgrupp i Resource Manager
Med hjälp av MSI kan din kod hämta åtkomsttoken för att autentisera de resurser som har stöd för Azure AD-autentisering.  Azure Resource Manager har stöd för Azure AD-autentisering.  Vi måste först och ge den här Virtuella datorns identitet åtkomst till en resurs i Resource Manager i det här fallet resursgruppen där den virtuella datorn finns.  

1.  Gå till fliken för **resursgrupper**. 
2.  Välj specifikt **resursgrupp** du skapade för din **Windows VM**. 
3.  Gå till **åtkomstkontroll (IAM)** i den vänstra panelen. 
4.  Sedan **Lägg till** en ny rolltilldelning för ditt **Windows VM**.  Välj **rollen** som **läsare**. 
5.  I nästa listrutan, **tilldela åtkomst till** resursen **VM**. 
6.  Kontrollera sedan att korrekt prenumeration visas i den **prenumeration** listrutan. Och för **resursgrupp**väljer **alla resursgrupper**. 
7.  Slutligen i **Välj** Välj din virtuella Windows-dator i listrutan och klickar på **spara**.

    ![ALT bildtext](~/articles/active-directory/media/msi-tutorial-windows-vm-access-arm/msi-windows-permissions.png)

## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-call-azure-resource-manager"></a>Få ett åtkomsttoken med hjälp av identitet för virtuell dator och använda den för att anropa Azure Resource Manager 

Du måste använda **PowerShell** i den här delen.  Om du inte har installerat kan du hämta det [här](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.3.1). 

1.  I portalen navigerar du till **virtuella datorer** och gå till din Windows-dator och i den **översikt**, klickar du på **Connect**. 
2.  Ange i din **användarnamn** och **lösenord** för som du har lagt till när du skapade den virtuella Windows-datorn. 
3.  Nu när du har skapat en **anslutning till fjärrskrivbord** med den virtuella datorn, öppna **PowerShell** i fjärrsessionen. 
4.  Med hjälp av Powershell-Invoke-WebRequest, skicka en förfrågan till den lokala MSI-slutpunkten för att hämta en åtkomsttoken för Azure Resource Manager.

    ```powershell
       $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token -Method GET -Body @{resource="https://management.azure.com/"} -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > Värdet för ”resource”-parametern måste vara en exakt matchning för vad som förväntas av Azure AD. När du använder Azure Resource Manager-resurs-ID, måste du inkluderar det avslutande snedstrecket på URI: N.
    
    Extrahera sedan det fullständiga svaret som lagras som en JavaScript Object Notation (JSON)-formaterad sträng i $response-objektet. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    Extrahera sedan den åtkomst-token från svaret.
    
    ```powershell
    $ArmToken = $content.access_token
    ```
    
    Slutligen kan anropa Azure Resource Manager med åtkomsttoken. I det här exemplet också använder vi PowerShell-Invoke-WebRequest göra anrop till Azure Resource Manager och ange åtkomst-token i auktoriseringshuvudet.
    
    ```powershell
    (Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-06-01 -Method GET -ContentType "application/json" -Headers @{ Authorization ="Bearer $ArmToken"}).content
    ```
    > [!NOTE] 
    > URL: en är skiftlägeskänsligt, så se till att om du använder exakt samma sätt som du använde tidigare när du namngav resursgruppen och versaler ”G” i ”resourceGroups”.
        
    Följande kommando returnerar information om resursgruppen:

    ```powershell
    {"id":"/subscriptions/98f51385-2edc-4b79-bed9-7718de4cb861/resourceGroups/DevTest","name":"DevTest","location":"westus","properties":{"provisioningState":"Succeeded"}}
    ```

## <a name="related-content"></a>Relaterat innehåll

- En översikt över MSI, se [hanterad tjänstidentitet översikt](msi-overview.md).

Använd följande avsnitt för kommentarer för att ge feedback och hjälp oss att förfina och forma vårt innehåll.

