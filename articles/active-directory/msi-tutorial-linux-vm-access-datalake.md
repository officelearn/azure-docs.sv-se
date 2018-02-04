---
title: "Använda hanterade tjänstidentiteten för en Linux-VM för att komma åt Azure Data Lake Store"
description: "En självstudiekurs som visar hur du använder hanteras Service identitet (MSI) för en Linux-VM för åtkomst till Azure Data Lake Store."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: skwan
ms.openlocfilehash: fdf1c49c644a97ff2f66a8b217ee54896ed54131
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2018
---
# <a name="use-managed-service-identity-for-a-linux-vm-to-access-azure-data-lake-store"></a>Använda hanterade tjänstidentiteten för en Linux-VM för att komma åt Azure Data Lake Store

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Den här kursen visar hur du använder hanterade tjänstidentiteten Linux virtuella datorer (VM) för att få åtkomst till Azure Data Lake Store. Azure hanterar automatiskt identiteter som du skapar via MSI. Du kan använda MSI för att autentisera till tjänster som stöder Azure Active Directory (AD Azure) autentisering utan att behöva infoga autentiseringsuppgifter i din kod. 

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Aktivera MSI på en virtuell Linux-dator. 
> * Ge dina VM-åtkomst till Azure Data Lake Store.
> * Hämta en åtkomsttoken med hjälp av VM-identitet och använda den för att få åtkomst till Azure Data Lake Store.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure Portal](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Skapa en virtuell Linux-dator i en ny resursgrupp

Den här självstudiekursen skapar vi en ny Linux VM. Du kan också aktivera MSI på en befintlig virtuell dator.

1. Välj den **ny** i det övre vänstra hörnet på Azure-portalen.
2. Välj **Compute** och välj sedan **Ubuntu Server 16.04 LTS**.
3. Ange informationen för den virtuella datorn. För **autentiseringstyp**väljer **offentliga SSH-nyckeln** eller **lösenord**. Autentiseringsuppgifterna som har skapats kan du logga in på den virtuella datorn.

   ![”Grunderna”-rutan för att skapa en virtuell dator](media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. I den **prenumeration** väljer du en prenumeration för den virtuella datorn.
5. Om du vill välja en ny resursgrupp som du vill att den virtuella datorn skapas i **resursgruppen** > **Skapa nytt**. När du är klar väljer **OK**.
6. Välj storlek för den virtuella datorn. Om du vill se fler storlekar väljer du **Visa alla** eller så ändrar du filtret för **disktyper som stöds**. I inställningsfönstret behålla standardinställningarna och välj **OK**.

## <a name="enable-msi-on-your-vm"></a>Aktivera MSI på den virtuella datorn

Du kan använda MSI för en virtuell dator för att få åtkomst-token från Azure AD utan att behöva placera autentiseringsuppgifter i din kod. Aktivera MSI installerar MSI VM-tillägget på den virtuella datorn och gör MSI i Azure Resource Manager.  

1. För **virtuella**, Välj den virtuella dator som du vill aktivera MSI på.
2. I den vänstra rutan, Välj **Configuration**.
3. Du ser **hanterade tjänstidentiteten**. För att registrera och aktivera MSI, Välj **Ja**. Välj om du vill inaktivera den **nr**.
   ![Val av ”registrera med Azure Active Directory”](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)
4. Välj **Spara**.
5. Om du vill kontrollera vilka tillägg som finns på den här Linux VM, Välj **tillägg**. Om MSI aktiveras **ManagedIdentityExtensionforLinux** visas i listan.

   ![Lista över tillägg](media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

## <a name="grant-your-vm-access-to-azure-data-lake-store"></a>Ge dina VM-åtkomst till Azure Data Lake Store

Nu kan du ge din VM-åtkomst till filer och mappar i Azure Data Lake Store. Du kan använda en befintlig Data Lake Store-instans eller skapa en ny för det här steget. Så här skapar du en instans av Data Lake Store med hjälp av Azure portal på [Azure Data Lake Store quickstart](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal). Det finns också Snabbstart som använder Azure CLI och Azure PowerShell i den [dokumentation för Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview).

Skapa en ny mapp i Data Lake Store och bevilja MSI behörighet att läsa, skriva och köra filer i mappen:

1. Välj i Azure-portalen **Datasjölager** i den vänstra rutan.
2. Välj Data Lake Store-instans som du vill använda.
3. Välj **Data Explorer** i kommandofältet.
4. Rotmapp för Data Lake Store-instansen har valts. Välj **åtkomst** i kommandofältet.
5. Välj **Lägg till**.  I den **Välj** ange namnet på den virtuella datorn – t.ex, **DevTestVM**. Välj den virtuella datorn i sökresultatet och klicka sedan på **Välj**.
6. Klicka på **Välj behörigheter**.  Välj **Läs** och **kör**, lägga till i **mappen**, och lägger till som **behörigheten endast**. Välj **Ok**.  Behörigheten bör vara har lagts till.
7. Stäng den **åtkomst** fönstret.
8. Skapa en ny mapp för den här självstudiekursen. Välj **ny mapp** i kommandofältet och ge den nya mappen ett namn, till exempel **TestFolder**.  Välj **Ok**.
9. Välj den mapp som du skapade och välj sedan **åtkomst** i kommandofältet.
10. Liknande steg 5, Välj **Lägg till**. I den **Välj** ange namnet på den virtuella datorn. Välj den virtuella datorn i sökresultatet och klicka sedan på **Välj**.
11. Liknande steg 6 väljer **Välj behörigheter**. Välj **Läs**, **skriva**, och **kör**, lägga till i **mappen**, och lägger till som **behörighetspost en åtkomst- och standard behörighetspost**. Välj **Ok**.  Behörigheten bör vara har lagts till.

MSI kan nu utföra alla åtgärder på filer i mappen som du skapade. Mer information om hur du hanterar åtkomst till Data Lake Store finns [åtkomstkontroll i Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-access-control).

## <a name="get-an-access-token-and-call-the-data-lake-store-file-system"></a>Hämta en åtkomst-token och anropa Data Lake Store-filsystem

Azure Data Lake Store inbyggt stöd för Azure AD-autentisering, så den kan acceptera åtkomsttoken direkt hämta via MSI. Om du vill autentisera till Data Lake Store-filsystem, kan du skicka en åtkomst-token som utfärdas av Azure AD till din Data Lake Store filen system slutpunkt. Åtkomsttoken är i ett authorization-huvud i formatet ”ägar \<ACCESS_TOKEN_VALUE\>”.  Mer information om Data Lake Store-stöd för Azure AD authentication finns [autentisering med Data Lake Store med hjälp av Azure Active Directory](https://docs.microsoft.com/azure/data-lake-store/data-lakes-store-authentication-using-azure-active-directory).

I kursen får autentisera du REST-API: et för Data Lake Store-filsystem med cURL och göra REST-begäranden.

> [!NOTE]
> Klient-SDK för Data Lake Store-filsystem stöder inte hanterade tjänstidentiteten ännu.

Du behöver en SSH-klient för att slutföra de här stegen. Om du använder Windows, kan du använda SSH-klienten i den [Windows undersystem för Linux](https://msdn.microsoft.com/commandline/wsl/about). Om du behöver hjälp konfigurerar SSH-klientens nycklar, se [hur du använder SSH-nycklar med Windows på Azure](../virtual-machines/linux/ssh-from-windows.md) eller [hur du skapar och använder ett SSH offentliga och privata nyckelpar för Linux virtuella datorer i Azure](../virtual-machines/linux/mac-create-ssh-keys.md).

1. Bläddra till din Linux VM i portalen. I **översikt**väljer **Anslut**.  
2. Ansluta till den virtuella datorn med hjälp av SSH-klient av önskat slag. 
3. I fönstret terminal med cURL, gör en begäran till den lokala MSI-slutpunkten för att få en åtkomsttoken för Data Lake Store-filsystem. Resurs-ID för Data Lake Store är ”https://datalake.azure.net/”.  Det är viktigt att du inkluderar det avslutande snedstrecket i resursidentifieraren.
    
   ```bash
   curl http://localhost:50342/oauth2/token --data "resource=https://datalake.azure.net/" -H Metadata:true   
   ```
    
   Ett lyckat svar returnerar den åtkomst-token som används för att autentisera till Data Lake Store:

   ```bash
   {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1508119757",
    "not_before":"1508115857",
    "resource":"https://datalake.azure.net/",
    "token_type":"Bearer"}
   ```

4. Med hjälp av cURL gör en förfrågan till ditt Data Lake Store filsystemet REST-slutpunkt för att lista mappar i rotmappen. Detta är ett enkelt sätt att kontrollera att allt är korrekt konfigurerade. Kopiera värdet för den åtkomst-token från föregående steg. Det är viktigt att strängen ”ägar” i Authorization-huvud har en versal ”b” Du hittar namnet på din Data Lake Store-instans i den **översikt** avsnitt i den **Data Lake Store** rutan i Azure-portalen.

   ```bash
   curl https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -H "Authorization: Bearer <ACCESS_TOKEN>"
   ```
    
   Ett lyckat svar ser ut så här:

   ```bash
   {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY","blockSize":0,"accessTime":1507934941392,"modificationTime":1508105430590,"replication":0,"permission":"770","owner":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071","group":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071"}]}}
   ```

5. Nu kan du överföra en fil till Data Lake Store-instans. Först skapar du en fil som ska överföras.

   ```bash
   echo "Test file." > Test1.txt
   ```

6. Med hjälp av cURL gör en förfrågan till ditt Data Lake Store filsystemet REST-slutpunkt för att överföra filen till mappen som du skapade tidigare. Överföringen innebär en omdirigering och cURL följer omdirigeringen automatiskt. 

   ```bash
   curl -i -X PUT -L -T Test1.txt -H "Authorization: Bearer <ACCESS_TOKEN>" 'https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/<FOLDER_NAME>/Test1.txt?op=CREATE' 
   ```

    Ett lyckat svar ser ut så här:

   ```bash
   HTTP/1.1 100 Continue
   HTTP/1.1 307 Temporary Redirect
   Cache-Control: no-cache, no-cache, no-store, max-age=0
   Pragma: no-cache
   Expires: -1
   Location: https://mytestadls.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE&write=true
   x-ms-request-id: 756f6b24-0cca-47ef-aa12-52c3b45b954c
   ContentLength: 0
   x-ms-webhdfs-version: 17.04.22.00
   Status: 0x0
   X-Content-Type-Options: nosniff
   Strict-Transport-Security: max-age=15724800; includeSubDomains
   Date: Sun, 15 Oct 2017 22:10:30 GMT
   Content-Length: 0
       
   HTTP/1.1 100 Continue
       
   HTTP/1.1 201 Created
   Cache-Control: no-cache, no-cache, no-store, max-age=0
   Pragma: no-cache
   Expires: -1
   Location: https://mytestadls.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE&write=true
   x-ms-request-id: af5baa07-3c79-43af-a01a-71d63d53e6c4
   ContentLength: 0
   x-ms-webhdfs-version: 17.04.22.00
   Status: 0x0
   X-Content-Type-Options: nosniff
   Strict-Transport-Security: max-age=15724800; includeSubDomains
   Date: Sun, 15 Oct 2017 22:10:30 GMT
   Content-Length: 0
   ```

Du kan lägga till att filer och hämta filer med hjälp av andra API: er för Data Lake Store-filsystem.

Grattis! Du har autentiserats för filsystemet Data Lake Store med hjälp av MSI för en Linux-VM.

## <a name="related-content"></a>Relaterat innehåll

- En översikt över MSI finns [hanterade tjänstidentiteten översikt](../active-directory/msi-overview.md).
- Data Lake Store använder Azure Resource Manager för av hanteringsåtgärder.  Mer information om hur du använder MSI för att autentisera till Resource Manager finns [använder en Linux VM hanterade tjänsten identitet (MSI) för åtkomst till Resource Manager](https://docs.microsoft.com/azure/active-directory/msi-tutorial-linux-vm-access-arm).
- Lär dig mer om [autentisering med Data Lake Store med hjälp av Azure Active Directory](https://docs.microsoft.com/azure/data-lake-store/data-lakes-store-authentication-using-azure-active-directory).
- Lär dig mer om [filen systemåtgärder på Azure Data Lake Store med hjälp av REST API](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-data-operations-rest-api) eller [WebHDFS FileSystem APIs](https://docs.microsoft.com/rest/api/datalakestore/webhdfs-filesystem-apis).
- Lär dig mer om [åtkomstkontroll i Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-access-control).

Använd följande avsnitt för kommentarer för att ge feedback och hjälp oss att förfina och utforma innehållet.