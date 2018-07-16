---
title: Använd en virtuell MSI-dator för Linux för att få åtkomst till Azure Key Vault
description: En självstudiekurs som vägleder dig genom processen med att använda en Linux VM hanterad tjänstidentitet (MSI) för att få åtkomst till Azure Resource Manager.
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
ms.openlocfilehash: 95f54cf9cda2bf6dede6e7bfb72471b22707c1c2
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39056080"
---
# <a name="use-a-linux-vm-managed-service-identity-msi-to-access-azure-key-vault"></a>Använda en Linux VM hanterad tjänstidentitet (MSI) för att komma åt Azure Key Vault 

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Den här självstudien visar hur du aktiverar hanterad tjänstidentitet (MSI) för en Linux-dator och sedan använda den identiteten att få åtkomst till Azure Key Vault. Key Vault fungerar som en bootstrap som gör det möjligt för klientprogrammet att använda hemligheten för åtkomst till resurser som inte skyddas av Azure Active Directory (AD). Hanterade tjänstidentiteter hanteras automatiskt av Azure och gör att du kan autentisera mot tjänster som stöder Azure Active Directory-autentisering, utan att du behöver skriva in autentiseringsuppgifter i koden. 

Lär dig att:

> [!div class="checklist"]
> * Aktivera MSI på en virtuell Linux-dator 
> * Ge din virtuella dator åtkomst till en hemlighet som lagras i Key Vault 
> * Få ett åtkomsttoken med hjälp av den virtuella datorns identitet och hämta hemligheten från Key Vault med hjälp av den 
 
## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Logga in på Azure
Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Skapa en Linux-dator i en ny resursgrupp.

I den här självstudien skapar vi en ny virtuell Linux-dator. Du kan även aktivera MSI på en befintlig virtuell dator.

1. Klicka på **skapa en resurs** på det övre vänstra hörnet i Azure-portalen.
2. Välj **Compute** och välj sedan **Ubuntu Server 16.04 LTS**.
3. Ange informationen för den virtuella datorn. Som **Autentiseringstyp** väljer du **Offentlig SSH-nyckel** eller **Lösenord**. Med de skapade autentiseringsuppgifterna kan du logga in på den virtuella datorn.

    ![Alternativ bildtext](../managed-service-identity/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Välj en **Prenumeration** för den virtuella datorn i listrutan.
5. Välj en ny **Resursgrupp** som den virtuella datorn ska skapas i genom att klicka på **Skapa ny**. När du är klar klickar du på **OK**.
6. Välj storlek för den virtuella datorn. Om du vill se fler storlekar väljer du **Visa alla** eller ändrar filtret för disktyper som stöds. Behåll standardinställningarna på inställningssidan och klickar på **OK**.

## <a name="enable-msi-on-your-vm"></a>Aktivera MSI på den virtuella datorn

Med en MSI för virtuell dator kan du få åtkomsttoken från Azure Active Directory utan att du behöver skriva in autentiseringsuppgifter i koden. Under försättsbladen, när du aktiverar MSI gör två saker: MSI VM-tillägget installeras på den virtuella datorn och gör att MSI för den virtuella datorn.  

1. Välj den **virtuella dator** som du vill aktivera MSI på.
2. I det vänstra navigeringsfältet klickar du på **Konfiguration**.
3. **Hanterad tjänstidentitet** visas. Om du vill registrera och aktivera den hanterade tjänstidentiteten väljer du **Ja**. Om du vill inaktivera den väljer du Nej.
4. Klicka på **Spara** om du vill spara konfigurationen.

    ![Alternativ bildtext](../managed-service-identity/media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Om du vill se vilka tillägg som finns på den här **Linux VM**, klickar du på **tillägg**. Om MSI aktiveras den **ManagedIdentityExtensionforLinux** visas i listan.

    ![Alternativ bildtext](../managed-service-identity/media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)


## <a name="grant-your-vm-access-to-a-secret-stored-in-a-key-vault"></a>Ge din virtuella dator åtkomst till en hemlighet som lagras i Key Vault  

Med hjälp av MSI kan din kod hämta åtkomsttoken för att autentisera mot resurser som stöder Azure Active Directory-autentisering. Men alla Azure-tjänster stöder inte Azure Active Directory-autentisering. Om du vill använda MSI med dessa tjänster lagrar du autentiseringsuppgifterna för tjänsten i Azure Key Vault och får åtkomst till Key Vault och hämtar autentiseringsuppgifterna med hjälp MSI:n. 

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
 
## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-retrieve-the-secret-from-the-key-vault"></a>Få ett åtkomsttoken med hjälp av den Virtuella datorns identitet och använda den för att hämta hemligheten från Key Vault  

För att slutföra de här stegen, måste en SSH-klient.  Om du använder Windows kan du använda SSH-klienten i [Windows-undersystemet för Linux](https://msdn.microsoft.com/commandline/wsl/about). Om du behöver hjälp att konfigurera SSH-klientens nycklar läser du [Använda SSH-nycklar med Windows i Azure](~/articles/virtual-machines/linux/ssh-from-windows.md) eller [Skapa och använda ett SSH offentligt / privat nyckelpar för virtuella Linux-datorer i Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).
 
1. I portalen, går du till Linux-VM och i den **översikt**, klickar du på **Connect**. 
2. **Ansluta** till den virtuella datorn med SSH-klient av önskat. 
3. I terminalfönstret, med CURL, skicka en förfrågan till den lokala MSI-slutpunkten för att hämta en åtkomsttoken för Azure Key Vault.  
 
    CURL-begäran om åtkomsttoken är nedan.  
    
    ```bash
    curl http://localhost:50342/oauth2/token --data "resource=https://vault.azure.net" -H Metadata:true  
    ```
    Svaret innehåller den åtkomsttoken som du behöver åtkomst till Resource Manager. 
    
    Svar:  
    
    ```bash
    {"access_token":"eyJ0eXAi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://vault.azure.net",
    "token_type":"Bearer"} 
    ```
    
    Du kan använda den här åtkomst-token för att autentisera till Azure Key Vault.  Nästa CURL-begäran visar hur du läsa en hemlighet från Key Vault med hjälp av CURL och Key Vault REST API: et.  Du behöver URL:en till ditt Key Vault, som finns i avsnittet **Essentials** på Key Vault-sidan **Översikt**.  Du måste också den åtkomsttoken som du fick i det föregående anropet. 
        
    ```bash
    curl https://<YOUR-KEY-VAULT-URL>/secrets/<secret-name>?api-version=2016-10-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```
    
    Svaret ser ut såhär: 
    
    ```bash
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
När du har hämtat hemligheten från Key Vault kan du använda den och autentisera mot en tjänst som kräver ett namn och lösenord.


## <a name="related-content"></a>Relaterat innehåll

- En översikt över MSI, se [hanterad tjänstidentitet översikt](msi-overview.md).

Använd följande avsnitt för kommentarer för att ge feedback och hjälp oss att förfina och forma vårt innehåll.




