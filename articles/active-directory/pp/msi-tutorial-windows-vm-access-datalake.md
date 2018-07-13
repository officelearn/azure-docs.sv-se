---
title: Hur du använder en Windows VM hanterad tjänstidentitet (MSI) för att få åtkomst till Azure Data Lake Store
description: En självstudie som visar hur du använder en Windows VM hanterad tjänstidentitet (MSI) för att få åtkomst till Azure Data Lake Store.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: skwan
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: daef85164793dd6183c41604f200864aabadf8d8
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38610889"
---
# <a name="use-a-windows-vm-managed-service-identity-msi-to-access-azure-data-lake-store"></a>Använd en Windows VM hanterad tjänstidentitet (MSI) för att få åtkomst till Azure Data Lake Store

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Den här självstudien visar hur du använder en hanterad tjänstidentitet (MSI) för en Windows virtuell dator (VM) för att få åtkomst till en Azure Data Lake Store. Hanterade tjänstidentiteter hanteras automatiskt av Azure och gör att du kan autentisera till tjänster som stöder Azure AD-autentisering, utan att behöva infoga autentiseringsuppgifter i din kod. Lär dig att:

> [!div class="checklist"]
> * Aktivera MSI på en Windows VM 
> * Bevilja åtkomst till dina virtuella datorer till en Azure Data Lake Store
> * Få ett åtkomsttoken med hjälp av identitet för virtuell dator och använda den för att få åtkomst till en Azure Data Lake Store

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Skapa en Windows-dator i en ny resursgrupp.

I den här självstudiekursen skapar vi en ny virtuell Windows-dator.  Du kan också aktivera MSI på en befintlig virtuell dator.

1. Klicka på **skapa en resurs** på det övre vänstra hörnet i Azure-portalen.
2. Välj **Compute**, och välj sedan **Windows Server 2016 Datacenter**. 
3. Ange informationen för den virtuella datorn. Den **användarnamn** och **lösenord** skapade här är de autentiseringsuppgifter som du använder för att logga in på den virtuella datorn.
4. Välj rätt **prenumeration** för den virtuella datorn i listrutan.
5. Att välja en ny **resursgrupp** där du kan skapa den virtuella datorn, väljer **Skapa ny**. När du är klar klickar du på **OK**.
6. Välj storlek för den virtuella datorn. Om du vill se fler storlekar väljer du **Visa alla** eller så ändrar du filtret för **disktyper som stöds**. Behåll standardinställningarna på sidan Inställningar och klickar på **OK**.

   ![ALT bildtext](~/articles/active-directory/media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Aktivera MSI på den virtuella datorn 

En VM MSI kan du hämta åtkomsttoken från Azure AD utan att du behöver att placera autentiseringsuppgifter i din kod. När du aktiverar MSI meddelar Azure att skapa en hanterad identitet för den virtuella datorn. Under försättsbladen, när du aktiverar MSI gör två saker: MSI VM-tillägget installeras på den virtuella datorn och gör att MSI i Azure Resource Manager.

1. Välj den **VM** att du vill aktivera MSI på.  
2. I det vänstra navigeringsfältet klickar du på **Configuration**. 
3. Du ser **hanterad tjänstidentitet**. Om du vill registrera och aktiverar MSI, Välj **Ja**, om du vill inaktivera det, väljer du Nej. 
4. Se till att du klickar på **spara** att spara konfigurationen.  
   ![ALT bildtext](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Om du vill kontrollera och vilka tillägg som finns på den här virtuella datorn klickar du på **tillägg**. Om MSI aktiveras sedan **ManagedIdentityExtensionforWindows** visas i listan.

   ![ALT bildtext](~/articles/active-directory/media/msi-tutorial-windows-vm-access-arm/msi-windows-extension.png)

## <a name="grant-your-vm-access-to-azure-data-lake-store"></a>Bevilja åtkomst till dina virtuella datorer till Azure Data Lake Store

Nu kan du ge din VM-åtkomst till filer och mappar i en Azure Data Lake Store.  Du kan använda en befintlig Data Lake Store eller skapa ett nytt lösenord för det här steget.  Om du vill skapa en ny Data Lake Store med Azure portal följer du den här [Snabbstart för Azure Data Lake Store](~/articles/data-lake-store/data-lake-store-get-started-portal.md). Det finns även snabbstarter som använder Azure CLI och Azure PowerShell i den [dokumentation om Azure Data Lake Store](~/articles/data-lake-store/data-lake-store-overview.md).

Skapa en ny mapp i ditt Data Lake Store och ge din VM MSI-behörighet att läsa, skriva och köra filer i mappen:

1. I Azure-portalen klickar du på **Data Lake Store** i det vänstra navigeringsfältet.
2. Klicka på Data Lake Store som du vill använda för den här självstudiekursen.
3. Klicka på **Datautforskaren** i kommandofältet.
4. Rotmappen för Data Lake Store har valts.  Klicka på **åtkomst** i kommandofältet.
5. Klicka på **Lägg till**.  I den **Välj** fältet, anger du namnet på den virtuella datorn, till exempel **DevTestVM**.  Markera den virtuella datorn från sökresultaten och klicka sedan på **Välj**.
6. Klicka på **Välj behörigheter**.  Välj **Läs** och **kör**, lägga till i **foldern**, och Lägg till som **behörigheten endast**.  Klicka på **OK**.  Behörigheten bör vara har lagts till.
7. Stäng den **åtkomst** bladet.
8. Skapa en ny mapp i den här självstudien.  Klicka på **ny mapp** i kommandofältet och ge den nya mappen ett namn, till exempel **TestFolder**.  Klicka på **OK**.
9. Klicka på den mapp du skapade och klicka sedan på **åtkomst** i kommandofältet.
10. Liknande steg 5, klickar du på **Lägg till**i den **Välj** fält anger du namnet på den virtuella datorn markerar du den och klicka på **Välj**.
11. Liknande steg 6, klickar du på **Select-behörigheter**väljer **Läs**, **skriva**, och **kör**, lägga till i **foldern**, och Lägg till som **ett åtkomstbehörighetsinlägg och en standardbehörighetsinlägg**.  Klicka på **OK**.  Behörigheten bör vara har lagts till.

Din VM MSI kan nu utföra alla åtgärder på filer i mappen som du skapade.  För mer information om hur du hanterar åtkomst till Data Lake Store, den här artikeln på [åtkomstkontroll i Data Lake Store](~/articles/data-lake-store/data-lake-store-access-control.md).

## <a name="get-an-access-token-using-the-vm-msi-and-use-it-to-call-the-azure-data-lake-store-filesystem"></a>Få ett åtkomsttoken med VM-MSI och använda den för att anropa Azure Data Lake Store-filsystemet

Azure Data Lake Store har inbyggt stöd för Azure AD-autentisering, så att den kan acceptera åtkomsttoken direkt hämtas med MSI.  För att autentisera till Data Lake Store-filsystem kan du skicka en åtkomsttoken som utfärdats av Azure AD till din Data Lake Store filesystem-slutpunkt, i en auktoriseringsrubrik i formatet ”ägar < ACCESS_TOKEN_VALUE >”.  Mer information om Data Lake Store-stöd för Azure AD-autentisering [autentisering med Data Lake Store med hjälp av Azure Active Directory](~/articles/data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)

> [!NOTE]
> Data Lake Store filesystem-klient SDK: er stöder inte hanterad tjänstidentitet ännu.  Den här självstudiekursen kommer att uppdateras när support har lagts till i SDK.

I den här självstudien kan du autentisera med Data Lake Store-filsystemet REST-API som använder PowerShell för att göra REST-begäranden. Om du vill använda VM MSI för autentisering, som du behöver göra begäranden från den virtuella datorn.

1. I portalen navigerar du till **virtuella datorer**går du till din virtuella Windows-dator och den **översikt** klickar du på **Connect**.
2. Ange i din **användarnamn** och **lösenord** för som du har lagt till när du skapade den virtuella Windows-datorn. 
3. Nu när du har skapat en **anslutning till fjärrskrivbord** med den virtuella datorn, öppna **PowerShell** i fjärrsessionen. 
4. Med hjälp av PowerShell- `Invoke-WebRequest`, gör en begäran till den lokala MSI-slutpunkten för att hämta en åtkomsttoken för Azure Data Lake Store.  Resurs-ID: t för Data Lake Store är ”https://datalake.azure.net/”.  Data Lake har en exakt matchning på resurs-ID och avslutande snedstreck är viktigt.

   ```powershell
   $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token -Method GET -Body @{resource="https://datalake.azure.net/"} -Headers @{Metadata="true"}
   ```
    
   Konvertera svaret från ett JSON-objekt till ett PowerShell-objekt. 
    
   ```powershell
   $content = $response.Content | ConvertFrom-Json
   ```

   Extrahera åtkomsttoken från svaret.
    
   ```powershell
   $AccessToken = $content.access_token
   ```

5. Med hjälp av PowerShell-'Invoke-WebRequest', skicka en förfrågan till ditt Data Lake Store REST-slutpunkt för att lista mappar i rotmappen.  Det här är ett enkelt sätt att kontrollera att allt är korrekt konfigurerad.  Det är viktigt strängen ”ägar” i auktoriseringshuvudet har versalt ”B”.  Du hittar namnet på ditt Data Lake Store i den **översikt** på Data Lake Store-bladet i Azure-portalen.

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

6. Nu kan du ladda upp en fil till din Data Lake Store.  Skapa först en fil att överföra.

   ```powershell
   echo "Test file." > Test1.txt
   ```

7. Med hjälp av PowerShell- `Invoke-WebRequest`, gör en begäran för ditt Data Lake Store REST-slutpunkt för att överföra filen till den mapp som du skapade tidigare.  Den här begäran tar två steg.  I det första steget, gör en begäran och få en omdirigering till där filen ska överföras.  I det andra steget överför du faktiskt filen.  Kom ihåg att ange namnet på mappen och filen på lämpligt sätt om du har använt andra värden än i den här självstudien. 

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

   Överföringen är klar genom att skicka en begäran till omdirigerings-slutpunkten:

   ```powershell
   Invoke-WebRequest -Uri $HdfsRedirectResponse.Headers.Location -Method PUT -Headers @{Authorization="Bearer $AccessToken"} -Infile Test1.txt -MaximumRedirection 0
   ```

   Ett lyckat svar ut:

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

Med andra Data Lake Store filesystem API: er som du kan lägga till filer, ladda ned filer och mycket annat.

Grattis!  Du har autentiserats med Data Lake Store-filsystemet med en VM-MSI.

## <a name="related-content"></a>Relaterat innehåll

- En översikt över MSI, se [hanterad tjänstidentitet översikt](msi-overview.md).
- Operations Data Lake Store använder Azure Resource Manager för hantering.  Mer information om hur du använder en VM MSI för att autentisera till Resource Manager [använder en Linux VM hanterad tjänstidentitet (MSI) för att få åtkomst till Resource Manager](../managed-service-identity/msi-tutorial-linux-vm-access-arm.md).
- Läs mer om [autentisering med Data Lake Store med hjälp av Azure Active Directory](~/articles/data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md).
- Läs mer om [filsystemsåtgärder på Azure Data Lake Store med REST API](~/articles/data-lake-store/data-lake-store-data-operations-rest-api.md) eller [WebHDFS FileSystem APIs](https://docs.microsoft.com/rest/api/datalakestore/webhdfs-filesystem-apis).
- Läs mer om [åtkomstkontroll i Data Lake Store](~/articles/data-lake-store/data-lake-store-access-control.md).

Använd följande avsnitt för kommentarer för att ge feedback och hjälp oss att förfina och forma vårt innehåll.