---
title: Använda en MSI på en virtuell Windows-dator och komma åt Azure Key Vault
description: En självstudiekurs som steg för steg beskriver hur du använder en hanterad tjänstidentitet (MSI) på en virtuell Windows-dator och komma åt Azure Key Vault.
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
ms.openlocfilehash: 20f8ac8f301302f3121880752ea698425c194623
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39049443"
---
# <a name="use-a-windows-vm-managed-service-identity-msi-to-access-azure-key-vault"></a>Använd en Windows VM hanterad tjänstidentitet (MSI) för att få åtkomst till Azure Key Vault 

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Den här självstudien visar hur du aktiverar hanterad tjänstidentitet (MSI) för en virtuell Windows-dator och sedan kommer åt Azure Key Vault med hjälp av identiteten. Key Vault fungerar som en bootstrap som gör det möjligt för klientprogrammet att använda hemligheten för åtkomst till resurser som inte skyddas av Azure Active Directory (AD). Hanterade tjänstidentiteter hanteras automatiskt av Azure och gör att du kan autentisera mot tjänster som stöder Azure AD-autentisering, utan att du behöver skriva in autentiseringsuppgifter i koden. 

Lär dig att:


> [!div class="checklist"]
> * Aktivera hanterad tjänstidentitet på en virtuell Windows-dator 
> * Ge din virtuella dator åtkomst till en hemlighet som lagras i Key Vault 
> * Få ett åtkomsttoken med hjälp av den virtuella datorns identitet och använd den när du hämtar hemligheten från Key Vault 

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Skapa en virtuell Windows-dator i en ny resursgrupp

I den här självstudien ska vi skapa en ny virtuell Windows-dator. Du kan även aktivera MSI på en befintlig virtuell dator.

1.  Klicka på **skapa en resurs** på det övre vänstra hörnet i Azure-portalen.
2.  Välj **Compute**, och välj sedan **Windows Server 2016 Datacenter**. 
3.  Ange informationen för den virtuella datorn. **Användarnamnet** och **lösenordet** som skapas här är de autentiseringsuppgifter som du använder när du loggar in på den virtuella datorn.
4.  Välj rätt **prenumeration** för den virtuella datorn i listrutan.
5.  Välj en ny **Resursgrupp** som den virtuella datorn ska skapas i genom att klicka på **Skapa ny**. När du är klar klickar du på **OK**.
6.  Välj storlek för den virtuella datorn. Om du vill se fler storlekar väljer du **Visa alla** eller så ändrar du filtret för **disktyper som stöds**. Acceptera alla standardvärden på bladet Inställningar och klicka på **OK**.

    ![Alternativ bildtext](../managed-service-identity/media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Aktivera MSI på den virtuella datorn 

Med en MSI för virtuell dator kan du få åtkomsttoken från Azure Active Directory utan att du behöver skriva in autentiseringsuppgifter i koden. När du aktiverar MSI skapar Azure en hanterad tjänstidentitet för den virtuella datorn. Under försättsbladen, när du aktiverar MSI gör två saker: MSI VM-tillägget installeras på den virtuella datorn och gör att MSI i Azure Resource Manager.

1.  Välj den **virtuella dator** som du vill aktivera MSI på.  
2.  Klicka på **Konfiguration** i det vänstra navigeringsfältet. 
3.  **Hanterad tjänstidentitet** visas. Om du vill registrera och aktivera den hanterade tjänstidentiteten väljer du **Ja**. Om du vill inaktivera den väljer du Nej. 
4.  Klicka på **Spara** för att spara konfigurationen.  

    ![Alternativ bildtext](../managed-service-identity/media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Om du vill kontrollera och verifiera vilka tillägg som finns på den här virtuella datorn klickar du på **Tillägg**. Om MSI är aktiverat visas **ManagedIdentityExtensionforWindows** i listan.

    ![Alternativ bildtext](../managed-service-identity/media/msi-tutorial-windows-vm-access-arm/msi-windows-extension.png)

## <a name="grant-your-vm-access-to-a-secret-stored-in-a-key-vault"></a>Ge din virtuella dator åtkomst till en hemlighet som lagras i Key Vault 
 
Med hjälp av MSI kan din kod hämta åtkomsttoken och autentisera mot resurser som stöder Azure Active Directory-autentisering.  Men alla Azure-tjänster stöder inte Azure Active Directory-autentisering. Om du vill använda MSI med dessa tjänster lagrar du autentiseringsuppgifterna för tjänsten i Azure Key Vault och får åtkomst till Key Vault och hämtar autentiseringsuppgifterna med hjälp MSI:n. 

Börja med att skapa ett Key Vault och bevilja den virtuella datorns identitet åtkomst till Key Vault.   

1. Överst i det vänstra navigeringsfältet väljer **+ ny** sedan **säkerhet + identitet** sedan **Key Vault**.  
2. Ange ett **namn** för det nya Key Vault. 
3. Leta upp Key Vault i samma prenumerations- och resursgrupp som den virtuella dator du skapade tidigare. 
4. Välj **Åtkomstprinciper** och klicka på **Lägg till**. 
5. I Konfigurera från mall väljer du **Hemlighetshantering**. 
6. Välj **Välj huvudkonto** och ange namnet på den virtuella dator som du skapade tidigare i sökfältet.  Välj den virtuella datorn i resultatlistan och klicka på **Välj**. 
7. Klicka på **OK** och lägg till den nya åtkomstprincipen. Klicka sedan på **OK** och slutför valet av åtkomstprincip. 
8. Klicka på **Skapa** och skapa Key Vault. 

    ![Alternativ bildtext](../managed-service-identity/media/msi-tutorial-windows-vm-access-nonaad/msi-blade.png)


Lägg sedan till en hemlighet i Key Vault, så att du senare kan hämta hemligheten med hjälp av koden som körs i den virtuella datorn: 

1. Välj **Alla resurser** och leta upp och välj det Key Vault som du skapade. 
2. Välj **Hemligheter** och klicka på **Lägg till**. 
3. Välj **Manuell** från **Uppladdningsalternativ**. 
4. Ange ett namn och värde för hemligheten.  Värdet kan vara vad du vill. 
5. Låt aktiveringsdatum och förfallodatum vara tomt och sätt **Aktiverad** som **Ja**. 
6. Klicka på **Skapa** och skapa hemligheten. 
 
## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-retrieve-the-secret-from-the-key-vault"></a>Få ett åtkomsttoken med hjälp av den virtuella datorns identitet och hämta hemligheten från Key Vault med hjälp av den  

Om du inte har PowerShell 4.3.1 eller senaste installerat måste du [ladda ned och installera den senaste versionen](https://docs.microsoft.com/powershell/azure/overview).

Först måste vi använda den virtuella datorns MSI om vi vill få ett åtkomsttoken och autentisera mot Key Vault:
 
1. Gå till **Virtuella datorer** på portalen och sedan till den virtuella Windows-datorn. Under **Översikt** klickar du på **Anslut**.
2. Ange ditt **användarnamn** och **lösenord** som du lade till när du skapade den **virtuella Windows-datorn**.  
3. Nu när du har skapat en **anslutning till fjärrskrivbord** med den virtuella datorn öppnar du PowerShell i fjärrsessionen.  
4. I PowerShell anropar du webbegäran för klientorganisationen om du vill få en token för den lokala värden i porten för den virtuella datorn.  

    PowerShell-begäran:
    
    ```powershell
    PS C:\> $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token -Method GET -Body @{resource="https://vault.azure.net"} -Headers @{Metadata="true"} 
    ```
    
    Extrahera sedan hela svaret som lagras som en JSON-formaterad sträng (JavaScript Object Notation) i $response-objektet.  
    
    ```powershell
    PS C:\> $content = $response.Content | ConvertFrom-Json 
    ```
    
    Extrahera sedan åtkomsttoken från svaret.  
    
    ```powershell
    PS C:\> $KeyVaultToken = $content.access_token 
    ```
    
    Slutligen använder du PowerShell-kommandot Invoke-WebRequest och hämtar hemligheten som du skapade tidigare i Key Vault, och skickar ditt åtkomsttoken i auktoriseringsrubriken.  Du behöver URL:en till ditt Key Vault, som finns i avsnittet **Essentials** på Key Vault-sidan **Översikt**.  
    
    ```powershell
    PS C:\> (Invoke-WebRequest -Uri https://<your-key-vault-URL>/secrets/<secret-name>?api-version=2016-10-01 -Method GET -Headers @{Authorization="Bearer $KeyVaultToken"}).content 
    ```
    
    Svaret ser ut såhär: 
    
    ```powershell
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
När du har hämtat hemligheten från Key Vault kan du använda den och autentisera mot en tjänst som kräver ett namn och lösenord. 

## <a name="related-content"></a>Relaterat innehåll

- En översikt över MSI, se [hanterad tjänstidentitet översikt](msi-overview.md).

Använd följande avsnitt för kommentarer för att ge feedback och hjälp oss att förfina och forma vårt innehåll.
