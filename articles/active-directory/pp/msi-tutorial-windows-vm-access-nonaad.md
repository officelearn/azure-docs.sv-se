---
title: Använd en virtuell MSI-dator för Windows för att få åtkomst till Azure Key Vault
description: En självstudiekurs som vägleder dig genom processen med att använda en Windows VM hanterad tjänstidentitet (MSI) för att få åtkomst till Azure Key Vault.
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
ms.openlocfilehash: b3d334edd770ac381a7e0ae6aaa1a9db8c91b961
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39002947"
---
# <a name="use-a-windows-vm-managed-service-identity-msi-to-access-azure-key-vault"></a>Använd en Windows VM hanterad tjänstidentitet (MSI) för att få åtkomst till Azure Key Vault 

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Den här självstudien visar hur du aktiverar hanterad tjänstidentitet (MSI) för en Windows-dator och sedan använda den identiteten att få åtkomst till Azure Key Vault. Fungerar som en bootstrap, gör Key Vault det möjligt för klientprogrammet att använda hemligheten för åtkomst till resurser som inte skyddas av Azure Active Directory (AD). Hanterade tjänstidentiteter hanteras automatiskt av Azure och gör att du kan autentisera till tjänster som stöder Azure AD-autentisering, utan att behöva infoga autentiseringsuppgifter i din kod. 

Lär dig att:


> [!div class="checklist"]
> * Aktivera hanterad tjänstidentitet på en Windows-dator 
> * Ge din VM-åtkomst till en hemlighet som lagras i Key Vault 
> * Få ett åtkomsttoken med hjälp av identitet för virtuell dator och använda den för att hämta hemligheten från Key Vault 

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Skapa en Windows-dator i en ny resursgrupp.

I den här självstudiekursen skapar vi en ny virtuell Windows-dator. Du kan också aktivera MSI på en befintlig virtuell dator.

1.  Klicka på **skapa en resurs** på det övre vänstra hörnet i Azure-portalen.
2.  Välj **Compute**, och välj sedan **Windows Server 2016 Datacenter**. 
3.  Ange informationen för den virtuella datorn. Den **användarnamn** och **lösenord** skapade här är de autentiseringsuppgifter som du använder för att logga in på den virtuella datorn.
4.  Välj rätt **prenumeration** för den virtuella datorn i listrutan.
5.  Att välja en ny **resursgrupp** du skulle vilja virtuella datorn skapas i, väljer **Skapa ny**. När du är klar klickar du på **OK**.
6.  Välj storlek för den virtuella datorn. Om du vill se fler storlekar väljer du **Visa alla** eller så ändrar du filtret för **disktyper som stöds**. Acceptera alla standardvärden på bladet Inställningar och klicka på **OK**.

    ![ALT bildtext](../managed-service-identity/media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Aktivera MSI på den virtuella datorn 

En virtuell dator MSI-dator kan du hämta åtkomsttoken från Azure AD utan att du behöver att placera autentiseringsuppgifter i din kod. När du aktiverar MSI meddelar Azure att skapa en hanterad identitet för den virtuella datorn. Under försättsbladen, när du aktiverar MSI gör två saker: MSI VM-tillägget installeras på den virtuella datorn och gör att MSI i Azure Resource Manager.

1.  Välj den **VM** att du vill aktivera MSI på.  
2.  I det vänstra navigeringsfältet klickar du på **Configuration**. 
3.  Du ser **hanterad tjänstidentitet**. Om du vill registrera och aktiverar MSI, Välj **Ja**, om du vill inaktivera det, väljer du Nej. 
4.  Se till att du klickar på **spara** att spara konfigurationen.  

    ![ALT bildtext](../managed-service-identity/media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Om du vill kontrollera och vilka tillägg som finns på den här virtuella datorn klickar du på **tillägg**. Om MSI aktiveras sedan **ManagedIdentityExtensionforWindows** visas i listan.

    ![ALT bildtext](../managed-service-identity/media/msi-tutorial-windows-vm-access-arm/msi-windows-extension.png)

## <a name="grant-your-vm-access-to-a-secret-stored-in-a-key-vault"></a>Bevilja åtkomst till dina virtuella datorer till en hemlighet som lagras i Key Vault 
 
Med hjälp av MSI kan din kod hämta åtkomsttoken för att autentisera de resurser som har stöd för Azure AD-autentisering.  Men stöder inte alla Azure-tjänster Azure AD-autentisering. Lagra autentiseringsuppgifterna för tjänsten i Azure Key Vault för att använda MSI med dessa tjänster, och Använd MSI för att få åtkomst till Key Vault för att hämta autentiseringsuppgifterna. 

Vi måste först skapa ett Key Vault och beviljar våra Virtuella datorns identitet åtkomst till Key Vault.   

1. Överst i det vänstra navigeringsfältet väljer **+ ny** sedan **säkerhet + identitet** sedan **Key Vault**.  
2. Ange en **namn** för nytt Key Vault. 
3. Leta upp Key Vault i gruppen samma prenumeration och resursgrupp som den virtuella datorn som du skapade tidigare. 
4. Välj **åtkomstprinciper** och klicka på **Lägg till ny**. 
5. Konfigurera från mall, Välj **hemlighet Management**. 
6. Välj **Välj huvudkonto**, och ange namnet på den virtuella datorn som du skapade tidigare i sökfältet.  Välj den virtuella datorn i resultatlistan och klicka på **Välj**. 
7. Klicka på **OK** till slutförs att lägga till den nya principen för åtkomst och **OK** att slutföra valet principen. 
8. Klicka på **skapa** skapa Key Vault. 

    ![ALT bildtext](~/articles/active-directory/media/msi-tutorial-windows-vm-access-nonaad/msi-blade.png)


Lägg sedan till en hemlighet i nyckelvalvet, så att du kan senare hämta hemligheten med hjälp av kod som körs i den virtuella datorn: 

1. Välj **alla resurser**, och leta upp och välj det Nyckelvalv som du skapade. 
2. Välj **hemligheter**, och klicka på **Lägg till**. 
3. Välj **manuell**, från **Uppladdningsalternativ**. 
4. Ange ett namn och värdet för hemligheten.  Värdet kan vara allt du vill. 
5. Lämna Aktiveringsdatum och rensa upphör att gälla och lämna **aktiverad** som **Ja**. 
6. Klicka på **skapa** att skapa hemligheten. 
 
## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-retrieve-the-secret-from-the-key-vault"></a>Få ett åtkomsttoken med hjälp av identitet för virtuell dator och använda den för att hämta hemligheten från Key Vault  

Om du inte har PowerShell 4.3.1-installationsprogram eller högre installerad kan du behöver [ladda ned och installera den senaste versionen](https://docs.microsoft.com/powershell/azure/overview).

Först måste använda vi den Virtuella datorns MSI för att få en åtkomsttoken för autentisering till Key Vault:
 
1. I portalen navigerar du till **virtuella datorer** och gå till din Windows-dator och i den **översikt**, klickar du på **Connect**.
2. Ange i din **användarnamn** och **lösenord** för som du har lagt till när du skapade den **Windows VM**.  
3. Nu när du har skapat en **anslutning till fjärrskrivbord** med den virtuella datorn öppnar du PowerShell i fjärrsessionen.  
4. Anropa webb-begäran för innehavaren att hämta token för den lokala värden i den specifika porten för den virtuella datorn i PowerShell.  

    PowerShell-begäran:
    
    ```powershell
    PS C:\> $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token -Method GET -Body @{resource="https://vault.azure.net"} -Headers @{Metadata="true"} 
    ```
    
    Extrahera sedan det fullständiga svaret som lagras som en JavaScript Object Notation (JSON)-formaterad sträng i $response-objektet.  
    
    ```powershell
    PS C:\> $content = $response.Content | ConvertFrom-Json 
    ```
    
    Extrahera sedan den åtkomst-token från svaret.  
    
    ```powershell
    PS C:\> $KeyVaultToken = $content.access_token 
    ```
    
    Använd slutligen PowerShells Invoke-WebRequest kommandot för att hämta hemligheten som du skapade tidigare i Key Vault kan skicka den åtkomst-token i auktoriseringshuvudet.  Du behöver URL: en för Key Vault, som finns i den **Essentials** delen av den **översikt** för Key Vault.  
    
    ```powershell
    PS C:\> (Invoke-WebRequest -Uri https://<your-key-vault-URL>/secrets/<secret-name>?api-version=2016-10-01 -Method GET -Headers @{Authorization="Bearer $KeyVaultToken"}).content 
    ```
    
    Svaret ska se ut så här: 
    
    ```powershell
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
När du har hämtat hemligheten från Key Vault kan använda du den för att autentisera till en tjänst som kräver ett namn och lösenord. 

## <a name="related-content"></a>Relaterat innehåll

- En översikt över MSI, se [hanterad tjänstidentitet översikt](msi-overview.md).

Använd följande avsnitt för kommentarer för att ge feedback och hjälp oss att förfina och forma vårt innehåll.
