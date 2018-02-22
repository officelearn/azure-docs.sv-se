---
title: "Använda en virtuell Linux-MSI för åtkomst till Azure Key Vault"
description: "En självstudiekurs som vägleder dig genom processen med att använda en Linux VM hanterade tjänsten identitet (MSI) för att få åtkomst till Azure Resource Manager."
services: active-directory
documentationcenter: 
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
ms.openlocfilehash: e42ed14b9c7c89972021a5422ebdfe415a70830d
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="use-a-linux-vm-managed-service-identity-msi-to-access-azure-key-vault"></a>Använd en Linux VM hanterade tjänsten identitet (MSI) för att komma åt Azure Key Vault 

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Den här kursen visar hur du aktiverar hanterade tjänsten identitet (MSI) för en virtuell Linux-dator och sedan använda identitet för åtkomst till Azure Key Vault. Fungerar som en bootstrap gör Key Vault det möjligt för ditt klientprogram att använda hemligheten som ska få tillgång till resurser som inte skyddas av Azure Active Directory (AD). Hanterade Tjänsteidentiteter hanteras automatiskt av Azure och gör att du kan autentisera tjänster som stöder Azure AD-autentisering utan att behöva infoga autentiseringsuppgifter i din kod. 

Lär dig att:

> [!div class="checklist"]
> * Aktivera MSI på en virtuell Linux-dator 
> * Ge dina VM-åtkomst till en hemlighet som lagras i ett Nyckelvalv 
> * Hämta en åtkomst-token med VM-identitet och använda den för att hämta hemligheten från Nyckelvalvet 
 
## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Logga in på Azure
Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Skapa en virtuell Linux-dator i en ny resursgrupp

Den här självstudiekursen skapar vi en ny Linux VM. Du kan också aktivera MSI på en befintlig virtuell dator.

1. Klicka på **skapar du en resurs** på det övre vänstra hörnet i Azure-portalen.
2. Välj **Compute** och välj sedan **Ubuntu Server 16.04 LTS**.
3. Ange informationen för den virtuella datorn. För **autentiseringstyp**väljer **offentliga SSH-nyckeln** eller **lösenord**. Autentiseringsuppgifterna som har skapats kan du logga in på den virtuella datorn.

    ![ALT bildtext](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Välj en **prenumeration** för den virtuella datorn i listrutan.
5. Att välja en ny **resursgruppen** du vill att den virtuella datorn ska skapas i, Välj **Skapa nytt**. När du är klar klickar du på **OK**.
6. Välj storlek för den virtuella datorn. Om du vill se mer storlekar, Välj **visa alla** eller ändra typ-filter stöds disk. Behåll standardinställningarna på inställningssidan och klickar på **OK**.

## <a name="enable-msi-on-your-vm"></a>Aktivera MSI på den virtuella datorn

En virtuell dator MSI kan du få åtkomst-token från Azure AD utan att du behöver publicera autentiseringsuppgifter i koden. Under försättsbladen, aktivera MSI gör två saker: MSI VM-tillägget installeras på den virtuella datorn och det möjliggör MSI för den virtuella datorn.  

1. Välj den **virtuella** som du vill aktivera MSI på.
2. Klicka på det vänstra navigeringsfältet **Configuration**.
3. Du ser **hanterade tjänstidentiteten**. För att registrera och aktivera MSI-filerna, Välj **Ja**, om du vill inaktivera det, väljer du Nej.
4. Se till att du klickar på **spara** att spara konfigurationen.

    ![ALT bildtext](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Om du vill se vilka tillägg som finns på den här **Linux VM**, klickar du på **tillägg**. Om MSI aktiveras den **ManagedIdentityExtensionforLinux** visas i listan.

    ![ALT bildtext](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)


## <a name="grant-your-vm-access-to-a-secret-stored-in-a-key-vault"></a>Ge dina VM-åtkomst till en hemlighet som lagras i ett Nyckelvalv  

Med hjälp av MSI hämta koden åtkomsttoken att autentisera till resurser som stöder Azure Active Directory-autentisering. Dock stöder inte alla Azure-tjänster Azure AD-autentisering. Om du vill använda MSI med dessa tjänster lagra autentiseringsuppgifterna för tjänsten i Azure Key Vault och använda MSI åtkomst till Nyckelvalvet för att hämta autentiseringsuppgifterna. 

Vi behöver först skapa ett Nyckelvalv och ge våra VM identitet åtkomst till Nyckelvalvet.   

1. Längst upp i det vänstra navigeringsfältet Välj **+ ny** sedan **säkerhet + identitet** sedan **Nyckelvalvet**.  
2. Ange en **namn** för nya Nyckelvalvet. 
3. Leta upp Nyckelvalvet i gruppen samma prenumeration och resurs som den virtuella datorn som du skapade tidigare. 
4. Välj **åtkomstprinciper** och på **Lägg till ny**. 
5. Välj i Konfigurera från mallen **hemlighet Management**. 
6. Välj **Välj huvudnamn**, och ange namnet på den virtuella datorn som du skapade tidigare i sökfältet.  Välj den virtuella datorn i resultatlistan och klicka på **Välj**. 
7. Klicka på **OK** till slutar att lägga till nya åtkomstprincipen och **OK** Slutför princip valet. 
8. Klicka på **skapa** skapa Nyckelvalvet. 

    ![ALT bildtext](~/articles/active-directory/media/msi-tutorial-windows-vm-access-nonaad/msi-blade.png)

Lägg sedan till en hemlighet i nyckelvalvet, så att senare kan du hämta hemligheten med kod som körs i den virtuella datorn: 

1. Välj **alla resurser**, och leta upp och markera Nyckelvalv som du skapade. 
2. Välj **hemligheter**, och klicka på **Lägg till**. 
3. Välj **manuell**, från **överför alternativ**. 
4. Ange namn och värde för hemligheten.  Värdet kan vara vad du vill. 
5. Lämna aktiveringsdatumet och avmarkera förfallodatum och lämna **aktiverad** som **Ja**. 
6. Klicka på **skapa** att skapa hemligheten. 
 
## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-retrieve-the-secret-from-the-key-vault"></a>Få en åtkomsttoken med hjälp av den Virtuella datorns identitet och använda den för att hämta hemligheten från Nyckelvalvet  

Du behöver en SSH-klient för att slutföra de här stegen.  Om du använder Windows, kan du använda SSH-klienten i den [Windows undersystem för Linux](https://msdn.microsoft.com/commandline/wsl/about). Om du behöver hjälp konfigurerar SSH-klientens nycklar, se [hur du använder SSH-nycklar med Windows på Azure](~/articles/virtual-machines/linux/ssh-from-windows.md), eller [hur du skapar och använder ett SSH offentliga och privata nyckelpar för Linux virtuella datorer i Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).
 
1. I portalen, navigera till Linux-VM och i den **översikt**, klickar du på **Anslut**. 
2. **Ansluta** till den virtuella datorn med SSH-klient av önskat slag. 
3. I fönstret terminal med CURL, gör en begäran till den lokala MSI-slutpunkten för att hämta en åtkomst-token för Azure Key Vault.  
 
    CURL begäran om åtkomsttoken är under.  
    
    ```bash
    curl http://localhost:50342/oauth2/token --data "resource=https://vault.azure.net" -H Metadata:true  
    ```
    Svaret innehåller den åtkomst-token som du behöver åtkomst till Resource Manager. 
    
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
    
    Du kan använda den här åtkomst-token för att autentisera till Azure Key Vault.  Nästa CURL begäran visar hur du läsa en hemlighet från Nyckelvalvet med CURL och Key Vault REST API.  Du behöver URL-Adressen till ditt Nyckelvalv som finns i den **Essentials** avsnitt i den **översikt** sida i Nyckelvalvet.  Du måste också den åtkomst-token som du fick i det föregående anropet. 
        
    ```bash
    curl https://<YOUR-KEY-VAULT-URL>/secrets/<secret-name>?api-version=2016-10-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```
    
    Svaret ser ut så här: 
    
    ```bash
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
När du har hämtat hemligheten från Nyckelvalvet kan använda du den för att autentisera till en tjänst som kräver ett användarnamn och lösenord.


## <a name="related-content"></a>Relaterat innehåll

- En översikt över MSI finns [hanterade tjänstidentiteten översikt](msi-overview.md).

Använd följande avsnitt för kommentarer för att ge feedback och hjälp oss att förfina och utforma innehållet.




