---
title: Hur du använder en Windows VM hanterade tjänsten identitet (MSI) för att få åtkomst till Azure Data Lake Store
description: En självstudiekurs som visar hur du använder en Windows VM hanterade tjänsten identitet (MSI) för åtkomst till Azure Data Lake Store.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: skwan
ms.openlocfilehash: f2904f3d070a818985fa291fb9083b5ec57529dd
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
---
# <a name="tutorial-use-a-windows-vm-managed-service-identity-msi-to-access-azure-data-lake-store"></a>Självstudier: Använda en Windows VM hanterade tjänsten identitet (MSI) för att komma åt Azure Data Lake Store

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Den här kursen visar hur du använder en hanterad tjänst identitet (MSI) för en Windows-dator (VM) till ett Azure Data Lake Store. Hanterade Tjänsteidentiteter hanteras automatiskt av Azure och gör att du kan autentisera tjänster som stöder Azure AD-autentisering utan att behöva infoga autentiseringsuppgifter i din kod. Lär dig att:

> [!div class="checklist"]
> * Aktivera MSI på en Windows VM 
> * Ge dina VM-åtkomst till ett Azure Data Lake Store
> * Hämta en åtkomst-token med VM-identitet och använda den för att få åtkomst till ett Azure Data Lake Store

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Skapa en virtuell Windows-dator i en ny resursgrupp

Den här självstudiekursen skapar vi en ny Windows virtuell dator.  Du kan också aktivera MSI på en befintlig virtuell dator.

1. Klicka på knappen **Skapa en resurs** längst upp till vänster i Azure Portal.
2. Välj **Compute**, och välj sedan **Windows Server 2016 Datacenter**. 
3. Ange informationen för den virtuella datorn. Den **användarnamn** och **lösenord** skapade här är de autentiseringsuppgifter som du använder för att logga in på den virtuella datorn.
4. Välj rätt **prenumeration** för den virtuella datorn i listrutan.
5. Att välja en ny **resursgruppen** som du vill skapa den virtuella datorn, Välj **Skapa nytt**. När du är klar klickar du på **OK**.
6. Välj storlek för den virtuella datorn. Om du vill se fler storlekar väljer du **Visa alla** eller så ändrar du filtret för **disktyper som stöds**. Behåll standardinställningarna på sidan Inställningar och klickar på **OK**.

   ![ALT bildtext](../media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Aktivera MSI på den virtuella datorn 

En VM MSI kan du få åtkomst-token från Azure AD utan att du behöver publicera autentiseringsuppgifter i koden. Aktivera MSI visar Azure för att skapa en hanterad identitet för den virtuella datorn. Under försättsbladen, aktivera MSI gör två saker: registrerar den virtuella datorn med Azure Active Directory för att skapa hanterade identitet och konfigurerar identiteten på den virtuella datorn.

1. Välj den **virtuella** som du vill aktivera MSI på.  
2. Klicka på det vänstra navigeringsfältet **Configuration**. 
3. Du ser **hanterade tjänstidentiteten**. För att registrera och aktivera MSI-filerna, Välj **Ja**, om du vill inaktivera det, väljer du Nej. 
4. Se till att du klickar på **spara** att spara konfigurationen.  
   ![ALT bildtext](../media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Om du vill kontrollera och vilka tillägg som finns på den här virtuella datorn, klickar du på **tillägg**. Om MSI aktiveras sedan **ManagedIdentityExtensionforWindows** visas i listan.

   ![ALT bildtext](../media/msi-tutorial-windows-vm-access-arm/msi-windows-extension.png)

## <a name="grant-your-vm-access-to-azure-data-lake-store"></a>Ge dina VM-åtkomst till Azure Data Lake Store

Nu kan du ge din VM-åtkomst till filer och mappar i ett Azure Data Lake Store.  Du kan använda en befintlig Data Lake Store eller skapa en ny för det här steget.  Om du vill skapa ett nytt Data Lake Store med hjälp av Azure portal, följer du detta [Azure Data Lake Store quickstart](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal). Det finns också Snabbstart som använder Azure CLI och Azure PowerShell i den [dokumentation för Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview).

Skapa en ny mapp i din Data Lake Store och ge VM MSI-behörighet att läsa, skriva och köra filer i mappen:

1. I Azure-portalen klickar du på **Datasjölager** i det vänstra navigeringsfönstret.
2. Klicka på Data Lake Store som du vill använda för den här självstudiekursen.
3. Klicka på **Data Explorer** i kommandofältet.
4. Rotmappen på Data Lake Store är markerad.  Klicka på **åtkomst** i kommandofältet.
5. Klicka på **Lägg till**.  I den **Välj** , ange namnet på den virtuella datorn, till exempel **DevTestVM**.  Välj den virtuella datorn i sökresultaten och klicka sedan på **Välj**.
6. Klicka på **Välj behörigheter**.  Välj **Läs** och **kör**, lägga till i **mappen**, och lägger till som **behörigheten endast**.  Klicka på **OK**.  Behörigheten bör vara har lagts till.
7. Stäng den **åtkomst** bladet.
8. Skapa en ny mapp för den här självstudiekursen.  Klicka på **ny mapp** i kommandofältet och ge den nya mappen ett namn, till exempel **TestFolder**.  Klicka på **OK**.
9. Klicka på mappen som du skapade och klicka sedan på **åtkomst** i kommandofältet.
10. Liknande steg 5 genom att klicka på **Lägg till**i den **Välj** fältet Ange namnet på den virtuella datorn markerar du den och klicka på **Välj**.
11. Liknande steg 6 genom att klicka på **Välj behörigheter**väljer **Läs**, **skriva**, och **kör**, lägga till **i den här mappen**, och lägger till som **en behörighetspost för åtkomst och en standard behörighetspost**.  Klicka på **OK**.  Behörigheten bör vara har lagts till.

Din VM MSI kan nu utföra alla åtgärder på filer i mappen som du skapade.  För mer information om hur du hanterar åtkomst till Data Lake Store den här artikeln på [åtkomstkontroll i Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-access-control).

## <a name="get-an-access-token-using-the-vm-msi-and-use-it-to-call-the-azure-data-lake-store-filesystem"></a>Få en åtkomsttoken med hjälp av VM MSI och använda den för att anropa Azure Data Lake Store-filsystem

Azure Data Lake Store inbyggt stöd för Azure AD-autentisering, så den kan acceptera åtkomsttoken direkt hämtas med hjälp av MSI.  Om du vill autentisera till Data Lake Store-filsystem kan du skicka en åtkomst-token som utfärdas av Azure AD till din Data Lake Store-filsystem slutpunkt i ett Authorization-huvud i formatet ”ägar < ACCESS_TOKEN_VALUE >”.  Mer information om Data Lake Store-stöd för Azure AD authentication [autentisering med Data Lake Store med hjälp av Azure Active Directory](https://docs.microsoft.com/azure/data-lake-store/data-lakes-store-authentication-using-azure-active-directory)

> [!NOTE]
> Data Lake Store-filsystem klienten SDK stöder inte hanterade tjänstidentiteten ännu.  Den här självstudiekursen kommer att uppdateras när stöd har lagts till i SDK.

I den här självstudiekursen autentisera till Data Lake Store-filsystem som använder PowerShell för att se RESTEN av REST-API-begäranden. Om du vill använda VM MSI för autentisering måste du begär från den virtuella datorn.

1. I portalen, går du till **virtuella datorer**, gå till Windows-VM och i den **översikt** klickar du på **Anslut**.
2. Ange i din **användarnamn** och **lösenord** för som du har lagt till när du skapade den virtuella Windows-datorn. 
3. Nu när du har skapat en **anslutning till fjärrskrivbord** med den virtuella datorn, öppna **PowerShell** i fjärrsessionen. 
4. Med hjälp av Powershell's `Invoke-WebRequest`, gör en begäran till den lokala MSI-slutpunkten för att hämta en åtkomst-token för Azure Data Lake Store.  Resurs-ID för Data Lake Store är ”https://datalake.azure.net/”.  Data Lake har en exakt matchning på Resursidentifieraren och avslutande snedstreck är viktigt.

   ```powershell
   $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fdatalake.azure.net%2F' -Method GET -Headers @{Metadata="true"}
   ```
    
   Konvertera svaret från ett JSON-objekt till en PowerShell-objektet. 
    
   ```powershell
   $content = $response.Content | ConvertFrom-Json
   ```

   Extrahera den åtkomst-token från svaret.
    
   ```powershell
   $AccessToken = $content.access_token
   ```

5. Med hjälp av Powershell's 'Invoke-WebRequest', gör en begäran för din Data Lake Store REST-slutpunkt för att lista mappar i rotmappen.  Detta är ett enkelt sätt att kontrollera att allt är korrekt konfigurerad.  Det är viktigt strängen ”ägar” i Authorization-huvud har en versal ”B”.  Du hittar namnet på din Data Lake Store i den **översikt** på Data Lake Store-bladet i Azure-portalen.

   ```powershell
   Invoke-WebRequest -Uri https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -Headers @{Authorization="Bearer $AccessToken"}
   ```

   Det ser ut som ett lyckat svar:

   ```powershell
   StatusCode        : 200
   StatusDescription : OK
   Content           : {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY", "blockSize":0,"accessTime":1507934941392, "modificationTime":1507944835699,"replication":0, "permission":"770","ow..."
   RawContent        : HTTP/1.1 200 OK
                       Pragma: no-cache
                       x-ms-request-id: b4b31e16-e968-46a1-879a-3474aa7d4528
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosniff
                       Strict-Transport-Security: ma...
   Forms             : {}
   Headers           : {[Pragma, no-cache], [x-ms-request-id, b4b31e16-e968-46a1-879a-3474aa7d4528],
                       [x-ms-webhdfs-version, 17.04.22.00], [Status, 0x0]...}
   Images            : {}
   InputFields       : {}
   Links             : {}
   ParsedHtml        : System.__ComObject
   RawContentLength  : 556
   ```

6. Nu kan du överföra en fil till Data Lake Store.  Först skapar du en fil som ska överföras.

   ```powershell
   echo "Test file." > Test1.txt
   ```

7. Med hjälp av Powershell's `Invoke-WebRequest`, gör en begäran för din Data Lake Store REST-slutpunkt för att överföra filen till mappen som du skapade tidigare.  Den här förfrågan tar två steg.  I det första steget att begära och få en omdirigering till där filen ska överföras.  I det andra steget överför du faktiskt filen.  Kom ihåg att ange namnet på mappen och filen på lämpligt sätt om du använder andra värden än i den här kursen. 

   ```powershell
   $HdfsRedirectResponse = Invoke-WebRequest -Uri https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE -Method PUT -Headers @{Authorization="Bearer $AccessToken"} -Infile Test1.txt -MaximumRedirection 0
   ```

   Om du inspektera värdet för `$HdfsRedirectResponse` det bör se ut som följande svar:

   ```powershell
   PS C:\> $HdfsRedirectResponse

   StatusCode        : 307
   StatusDescription : Temporary Redirect
   Content           : {}
   RawContent        : HTTP/1.1 307 Temporary Redirect
                       Pragma: no-cache
                       x-ms-request-id: b7ab492f-b514-4483-aada-4aa0611d12b3
                       ContentLength: 0
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosn...
   Headers           : {[Pragma, no-cache], [x-ms-request-id, b7ab492f-b514-4483-aada-4aa0611d12b3], 
                       [ContentLength, 0], [x-ms-webhdfs-version, 17.04.22.00]...}
   RawContentLength  : 0
   ```

   Överföringen är klar genom att skicka en begäran till slutpunkten omdirigering:

   ```powershell
   Invoke-WebRequest -Uri $HdfsRedirectResponse.Headers.Location -Method PUT -Headers @{Authorization="Bearer $AccessToken"} -Infile Test1.txt -MaximumRedirection 0
   ```

   Ett lyckat svar se ut:

   ```powershell
   StatusCode        : 201
   StatusDescription : Created
   Content           : {}
   RawContent        : HTTP/1.1 201 Created
                       Pragma: no-cache
                       x-ms-request-id: 1e70f36f-ead1-4566-acfa-d0c3ec1e2307
                       ContentLength: 0
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosniff
                       Strict...
   Headers           : {[Pragma, no-cache], [x-ms-request-id, 1e70f36f-ead1-4566-acfa-d0c3ec1e2307],
                       [ContentLength, 0], [x-ms-webhdfs-version, 17.04.22.00]...}
   RawContentLength  : 0
   ```

Med andra Data Lake Store-filesystem hämta API: er som du kan lägga till filer, filer och mycket mer.

Grattis!  Du har autentiserats för Data Lake Store-filsystem med hjälp av en VM MSI.

## <a name="related-content"></a>Relaterat innehåll

- En översikt över MSI finns [hanterade tjänstidentiteten översikt](overview.md).
- Operations Data Lake Store använder Azure Resource Manager för hantering.  Mer information om hur du använder en VM MSI för att autentisera till Resource Manager [använder en Linux VM hanterade tjänsten identitet (MSI) för åtkomst till Resource Manager](https://docs.microsoft.com/azure/active-directory/msi-tutorial-linux-vm-access-arm).
- Lär dig mer om [autentisering med Data Lake Store med hjälp av Azure Active Directory](https://docs.microsoft.com/azure/data-lake-store/data-lakes-store-authentication-using-azure-active-directory).
- Lär dig mer om [filsystemsåtgärder på Azure Data Lake Store med hjälp av REST API](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-data-operations-rest-api) eller [WebHDFS FileSystem APIs](https://docs.microsoft.com/rest/api/datalakestore/webhdfs-filesystem-apis).
- Lär dig mer om [åtkomstkontroll i Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-access-control).

Använd följande avsnitt för kommentarer för att ge feedback och hjälp oss att förfina och utforma innehållet.
