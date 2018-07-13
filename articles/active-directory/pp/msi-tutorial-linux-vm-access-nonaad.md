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
ms.openlocfilehash: a6ca37105cfff8542f0c4a8af3112fa317416c56
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38611259"
---
# <a name="use-a-linux-vm-managed-service-identity-msi-to-access-azure-key-vault"></a>Använda en Linux VM hanterad tjänstidentitet (MSI) för att komma åt Azure Key Vault 

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Den här självstudien visar hur du aktiverar hanterad tjänstidentitet (MSI) för en Linux-dator och sedan använda den identiteten att få åtkomst till Azure Key Vault. Fungerar som en bootstrap, gör Key Vault det möjligt för klientprogrammet att använda hemligheten för åtkomst till resurser som inte skyddas av Azure Active Directory (AD). Hanterade tjänstidentiteter hanteras automatiskt av Azure och gör att du kan autentisera till tjänster som stöder Azure AD-autentisering, utan att behöva infoga autentiseringsuppgifter i din kod. 

Lär dig att:

> [!div class="checklist"]
> * Aktivera MSI på en Linux-dator 
> * Ge din VM-åtkomst till en hemlighet som lagras i Key Vault 
> * Få ett åtkomsttoken med hjälp av identitet för virtuell dator och använda den för att hämta hemligheten från Key Vault 
 
## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Logga in på Azure
Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Skapa en Linux-dator i en ny resursgrupp.

I den här självstudiekursen skapar vi en ny Linux-VM. Du kan också aktivera MSI på en befintlig virtuell dator.

1. Klicka på **skapa en resurs** på det övre vänstra hörnet i Azure-portalen.
2. Välj **Compute** och välj sedan **Ubuntu Server 16.04 LTS**.
3. Ange informationen för den virtuella datorn. För **autentiseringstyp**väljer **offentlig SSH-nyckel** eller **lösenord**. Autentiseringsuppgifterna som har skapats kan du logga in på den virtuella datorn.

    ![ALT bildtext](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Välj en **prenumeration** för den virtuella datorn i listrutan.
5. Att välja en ny **resursgrupp** som den virtuella datorn ska skapas i, väljer **Skapa ny**. När du är klar klickar du på **OK**.
6. Välj storlek för den virtuella datorn. Om du vill se fler storlekar väljer **visa alla** eller ändra det typ av filtret som stöds disk. Behåll standardinställningarna på inställningssidan och klickar på **OK**.

## <a name="enable-msi-on-your-vm"></a>Aktivera MSI på den virtuella datorn

En virtuell dator MSI-dator kan du hämta åtkomsttoken från Azure AD utan att du behöver att placera autentiseringsuppgifter i din kod. Under försättsbladen, när du aktiverar MSI gör två saker: MSI VM-tillägget installeras på den virtuella datorn och gör att MSI för den virtuella datorn.  

1. Välj den **VM** att du vill aktivera MSI på.
2. I det vänstra navigeringsfältet klickar du på **Configuration**.
3. Du ser **hanterad tjänstidentitet**. Om du vill registrera och aktiverar MSI, Välj **Ja**, om du vill inaktivera det, väljer du Nej.
4. Se till att du klickar på **spara** att spara konfigurationen.

    ![ALT bildtext](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Om du vill se vilka tillägg som finns på den här **Linux VM**, klickar du på **tillägg**. Om MSI aktiveras den **ManagedIdentityExtensionforLinux** visas i listan.

    ![ALT bildtext](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)


## <a name="grant-your-vm-access-to-a-secret-stored-in-a-key-vault"></a>Bevilja åtkomst till dina virtuella datorer till en hemlighet som lagras i Key Vault  

Med hjälp av MSI kan din kod hämta åtkomsttoken för att autentisera mot resurser som stöder Azure Active Directory-autentisering. Men stöder inte alla Azure-tjänster Azure AD-autentisering. Lagra autentiseringsuppgifterna för tjänsten i Azure Key Vault för att använda MSI med dessa tjänster, och Använd MSI för att få åtkomst till Key Vault för att hämta autentiseringsuppgifterna. 

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
 
## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-retrieve-the-secret-from-the-key-vault"></a>Få ett åtkomsttoken med hjälp av den Virtuella datorns identitet och använda den för att hämta hemligheten från Key Vault  

För att slutföra de här stegen, måste en SSH-klient.  Om du använder Windows kan du använda SSH-klient i den [Windows-undersystem for Linux](https://msdn.microsoft.com/commandline/wsl/about). Om du behöver hjälp att konfigurera nycklar för SSH-klienten kan se [hur du använder SSH-nycklar med Windows på Azure](~/articles/virtual-machines/linux/ssh-from-windows.md), eller [hur du skapar och använder ett SSH offentliga och privata nyckelpar för virtuella Linux-datorer i Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).
 
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
    
    Du kan använda den här åtkomst-token för att autentisera till Azure Key Vault.  Nästa CURL-begäran visar hur du läsa en hemlighet från Key Vault med hjälp av CURL och Key Vault REST API: et.  Du behöver URL: en för Key Vault, som finns i den **Essentials** delen av den **översikt** för Key Vault.  Du måste också den åtkomsttoken som du fick i det föregående anropet. 
        
    ```bash
    curl https://<YOUR-KEY-VAULT-URL>/secrets/<secret-name>?api-version=2016-10-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```
    
    Svaret ska se ut så här: 
    
    ```bash
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
När du har hämtat hemligheten från Key Vault kan använda du den för att autentisera till en tjänst som kräver ett namn och lösenord.


## <a name="related-content"></a>Relaterat innehåll

- En översikt över MSI, se [hanterad tjänstidentitet översikt](msi-overview.md).

Använd följande avsnitt för kommentarer för att ge feedback och hjälp oss att förfina och forma vårt innehåll.




