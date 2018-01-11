---
title: "Hur du använder en Linux VM hanterade tjänsten identitet (MSI) för att få åtkomst till Azure Data Lake Store"
description: "En självstudiekurs som visar hur du använder en Linux VM hanterade tjänsten identitet (MSI) för åtkomst till Azure Data Lake Store."
services: active-directory
documentationcenter: 
author: skwan
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: bryanla
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 4593046ec4d39c874d7ac5849cc9d8e7f30cf2ef
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/22/2017
---
# <a name="use-a-linux-vm-managed-service-identity-msi-to-access-azure-data-lake-store"></a>Använd en Linux VM hanterade tjänsten identitet (MSI) för att komma åt Azure Data Lake Store

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Den här kursen visar hur du använder en hanterad tjänst identitet (MSI) Linux virtuella datorer (VM) för att få åtkomst till ett Azure Data Lake Store. Hanterade Tjänsteidentiteter hanteras automatiskt av Azure och gör att du kan autentisera tjänster som stöder Azure AD-autentisering utan att behöva infoga autentiseringsuppgifter i din kod. Lär dig att:

> [!div class="checklist"]
> * Aktivera MSI på en virtuell Linux-dator 
> * Ge dina VM-åtkomst till ett Azure Data Lake Store
> * Hämta en åtkomst-token med VM-identitet och använda den för att få åtkomst till ett Azure Data Lake Store

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Skapa en virtuell Linux-dator i en ny resursgrupp

Den här självstudiekursen skapar vi en ny Linux VM. Du kan också aktivera MSI på en befintlig virtuell dator.

1. Klicka på knappen **New** (Nytt) i det övre vänstra hörnet i Azure Portal.
2. Välj **Compute** och välj sedan **Ubuntu Server 16.04 LTS**.
3. Ange informationen för den virtuella datorn. För **autentiseringstyp**väljer **offentliga SSH-nyckeln** eller **lösenord**. Autentiseringsuppgifterna som har skapats kan du logga in på den virtuella datorn.

   ![ALT bildtext](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Välj en **prenumeration** för den virtuella datorn i listrutan.
5. Att välja en ny **resursgruppen** du vill att den virtuella datorn ska skapas i, Välj **Skapa nytt**. När du är klar klickar du på **OK**.
6. Välj storlek för den virtuella datorn. Om du vill se mer storlekar, Välj **visa alla** eller ändra typ-filter stöds disk. Acceptera alla standardvärden på bladet Inställningar och klicka på **OK**.

## <a name="enable-msi-on-your-vm"></a>Aktivera MSI på den virtuella datorn

En virtuell dator MSI kan du få åtkomst-token från Azure AD utan att du behöver publicera autentiseringsuppgifter i koden. Under försättsbladen, aktivera MSI gör två saker: MSI VM-tillägget installeras på den virtuella datorn och det möjliggör MSI i Azure Resource Manager.  

1. Välj den **virtuella** som du vill aktivera MSI på.
2. Klicka på det vänstra navigeringsfältet **Configuration**.
3. Du ser **hanterade tjänstidentiteten**. För att registrera och aktivera MSI-filerna, Välj **Ja**, om du vill inaktivera det, väljer du Nej.
4. Se till att du klickar på **spara** att spara konfigurationen.

   ![ALT bildtext](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Om du vill se vilka tillägg som finns på den här **Linux VM**, klickar du på **tillägg**. Om MSI aktiveras den **ManagedIdentityExtensionforLinux** visas i listan.

   ![ALT bildtext](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

## <a name="grant-your-vm-access-to-azure-data-lake-store"></a>Ge dina VM-åtkomst till Azure Data Lake Store

Nu kan du ge din VM-åtkomst till filer och mappar i ett Azure Data Lake Store.  Du kan använda en befintlig Data Lake Store eller skapa en ny för det här steget.  Om du vill skapa ett nytt Data Lake Store med hjälp av Azure portal, följer du detta [Azure Data Lake Store quickstart](~/articles/data-lake-store/data-lake-store-get-started-portal.md). Det finns också Snabbstart som använder Azure CLI och Azure PowerShell i den [dokumentation för Azure Data Lake Store](~/articles/data-lake-store/data-lake-store-overview.md).

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

Azure Data Lake Store inbyggt stöd för Azure AD-autentisering, så den kan acceptera åtkomsttoken direkt hämtas med hjälp av MSI.  För att autentisera till Data Lake Store-filsystem som du skickar en åtkomst-token som utfärdas av Azure AD till din Data Lake Store-filsystem slutpunkt i ett Authorization-huvud i formatet ”ägar \<ACCESS_TOKEN_VALUE\>”.  Mer information om Data Lake Store-stöd för Azure AD authentication [autentisering med Data Lake Store med hjälp av Azure Active Directory](~/articles/data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)

I den här självstudiekursen autentisera till Data Lake Store-filsystem som använder CURL för att se RESTEN av REST-API-begäranden.

> [!NOTE]
> Data Lake Store-filsystem klienten SDK stöder inte hanterade tjänstidentiteten ännu.  Den här självstudiekursen kommer att uppdateras när stöd har lagts till i SDK.

Du behöver en SSH-klient för att slutföra de här stegen. Om du använder Windows, kan du använda SSH-klienten i den [Windows undersystem för Linux](https://msdn.microsoft.com/commandline/wsl/about). Om du behöver hjälp konfigurerar SSH-klientens nycklar, se [hur du använder SSH-nycklar med Windows på Azure](~/articles/virtual-machines/linux/ssh-from-windows.md), eller [hur du skapar och använder ett SSH offentliga och privata nyckelpar för Linux virtuella datorer i Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).

1. I portalen, navigera till Linux-VM och i den **översikt**, klickar du på **Anslut**.  
2. **Ansluta** till den virtuella datorn med SSH-klient av önskat slag. 
3. I fönstret terminal med CURL, gör en begäran till den lokala MSI-slutpunkten för att hämta en åtkomst-token för Data Lake Store-filsystem.  Resurs-ID för Data Lake Store är ”https://datalake.azure.net/”.  Det är viktigt att du inkluderar det avslutande snedstrecket i resursidentifieraren.
    
   ```bash
   curl http://localhost:50342/oauth2/token --data "resource=https://datalake.azure.net/" -H Metadata:true   
   ```
    
   Ett lyckat svar returnerar den åtkomst-token som du kan använda vid autentisering till Data Lake Store:

   ```bash
   {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1508119757",
    "not_before":"1508115857",
    "resource":"https://datalake.azure.net/",
    "token_type":"Bearer"}
   ```

4. Med CURL gör en begäran för Data Lake Store-filsystem REST-slutpunkt att lista mappar i rotmappen.  Detta är ett enkelt sätt att kontrollera att allt är korrekt konfigurerad.  Kopiera värdet för den åtkomst-token från föregående steg.  Det är viktigt strängen ”ägar” i Authorization-huvud har en versal ”B”.  Du hittar namnet på din Data Lake Store i den **översikt** på Data Lake Store-bladet i Azure-portalen.

   ```bash
   curl https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -H "Authorization: Bearer <ACCESS_TOKEN>"
   ```
    
   Det ser ut som ett lyckat svar:

   ```bash
   {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY","blockSize":0,"accessTime":1507934941392,"modificationTime":1508105430590,"replication":0,"permission":"770","owner":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071","group":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071"}]}}
   ```

5. Nu kan du överföra en fil till Data Lake Store.  Först skapar du en fil som ska överföras.

   ```bash
   echo "Test file." > Test1.txt
   ```

6. Med CURL gör en begäran för Data Lake Store-filsystem REST-slutpunkt att överföra filen till mappen som du skapade tidigare.  Överföringen innebär en omdirigering och CURL följer omdirigeringen automatiskt. 

   ```bash
   curl -i -X PUT -L -T Test1.txt -H "Authorization: Bearer <ACCESS_TOKEN>" 'https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/<FOLDER_NAME>/Test1.txt?op=CREATE' 
   ```

    Det ser ut som ett lyckat svar:

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

Med andra Data Lake Store-filesystem hämta API: er som du kan lägga till filer, filer och mycket mer.

Grattis!  Du har autentiserats för Data Lake Store-filsystem med hjälp av en VM MSI.

## <a name="related-content"></a>Relaterat innehåll

- En översikt över MSI finns [hanterade tjänstidentiteten översikt](msi-overview.md).
- Operations Data Lake Store använder Azure Resource Manager för hantering.  Mer information om hur du använder en VM MSI för att autentisera till Resource Manager [använder en Linux VM hanterade tjänsten identitet (MSI) för åtkomst till Resource Manager](msi-tutorial-linux-vm-access-arm.md).
- Lär dig mer om [autentisering med Data Lake Store med hjälp av Azure Active Directory](~/articles/data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md).
- Lär dig mer om [filsystemsåtgärder på Azure Data Lake Store med hjälp av REST API](~/articles/data-lake-store/data-lake-store-data-operations-rest-api.md) eller [WebHDFS FileSystem APIs](https://docs.microsoft.com/rest/api/datalakestore/webhdfs-filesystem-apis.md).
- Lär dig mer om [åtkomstkontroll i Data Lake Store](~/articles/data-lake-store/data-lake-store-access-control.md).

Använd följande avsnitt för kommentarer för att ge feedback och hjälp oss att förfina och utforma innehållet.