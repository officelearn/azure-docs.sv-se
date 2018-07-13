---
title: Hur du använder en Linux VM hanterad tjänstidentitet (MSI) för att få åtkomst till Azure Data Lake Store
description: En självstudie som visar hur du använder en Linux VM hanterad tjänstidentitet (MSI) för att få åtkomst till Azure Data Lake Store.
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
ms.openlocfilehash: b1091ae25e247a0e2ce00de831886cde44ae14a7
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39007840"
---
# <a name="use-a-linux-vm-managed-service-identity-msi-to-access-azure-data-lake-store"></a>Använda en Linux VM hanterad tjänstidentitet (MSI) för att få åtkomst till Azure Data Lake Store

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Den här självstudien visar hur du använder en hanterad tjänstidentitet (MSI) för en Linux-dator (VM) för att få åtkomst till en Azure Data Lake Store. Hanterade tjänstidentiteter hanteras automatiskt av Azure och gör att du kan autentisera till tjänster som stöder Azure AD-autentisering, utan att behöva infoga autentiseringsuppgifter i din kod. Lär dig att:

> [!div class="checklist"]
> * Aktivera MSI på en virtuell Linux-dator 
> * Bevilja åtkomst till dina virtuella datorer till en Azure Data Lake Store
> * Få ett åtkomsttoken med hjälp av identitet för virtuell dator och använda den för att få åtkomst till en Azure Data Lake Store

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

   ![ALT bildtext](../managed-service-identity/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Välj en **prenumeration** för den virtuella datorn i listrutan.
5. Att välja en ny **resursgrupp** som den virtuella datorn ska skapas i, väljer **Skapa ny**. När du är klar klickar du på **OK**.
6. Välj storlek för den virtuella datorn. Om du vill se fler storlekar väljer **visa alla** eller ändra det typ av filtret som stöds disk. Acceptera alla standardvärden på bladet Inställningar och klicka på **OK**.

## <a name="enable-msi-on-your-vm"></a>Aktivera MSI på den virtuella datorn

En virtuell dator MSI-dator kan du hämta åtkomsttoken från Azure AD utan att du behöver att placera autentiseringsuppgifter i din kod. Under försättsbladen, när du aktiverar MSI gör två saker: MSI VM-tillägget installeras på den virtuella datorn och gör att MSI i Azure Resource Manager.  

1. Välj den **VM** att du vill aktivera MSI på.
2. I det vänstra navigeringsfältet klickar du på **Configuration**.
3. Du ser **hanterad tjänstidentitet**. Om du vill registrera och aktiverar MSI, Välj **Ja**, om du vill inaktivera det, väljer du Nej.
4. Se till att du klickar på **spara** att spara konfigurationen.

   ![ALT bildtext](../managed-service-identity/media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Om du vill se vilka tillägg som finns på den här **Linux VM**, klickar du på **tillägg**. Om MSI aktiveras den **ManagedIdentityExtensionforLinux** visas i listan.

   ![ALT bildtext](../managed-service-identity/media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

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

Din VM MSI kan nu utföra alla åtgärder på filer i mappen som du skapade.  För mer information om hur du hanterar åtkomst till Data Lake Store, den här artikeln på [åtkomstkontroll i Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-access-control).

## <a name="get-an-access-token-using-the-vm-msi-and-use-it-to-call-the-azure-data-lake-store-filesystem"></a>Få ett åtkomsttoken med VM-MSI och använda den för att anropa Azure Data Lake Store-filsystemet

Azure Data Lake Store har inbyggt stöd för Azure AD-autentisering, så att den kan acceptera åtkomsttoken direkt hämtas med MSI.  Autentisera med Data Lake Store-filsystemet du skicka en åtkomsttoken som utfärdats av Azure AD till din Data Lake Store filesystem-slutpunkt, i en auktoriseringsrubrik i formatet ”ägar \<ACCESS_TOKEN_VALUE\>”.  Mer information om Data Lake Store-stöd för Azure AD-autentisering [autentisering med Data Lake Store med hjälp av Azure Active Directory](~/articles/data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)

I den här självstudien kan du autentisera med Data Lake Store-filsystemet REST-API med CURL för att göra REST-begäranden.

> [!NOTE]
> Data Lake Store filesystem-klient SDK: er stöder inte hanterad tjänstidentitet ännu.  Den här självstudiekursen kommer att uppdateras när support har lagts till i SDK.

För att slutföra de här stegen, måste en SSH-klient. Om du använder Windows kan du använda SSH-klient i den [Windows-undersystem for Linux](https://msdn.microsoft.com/commandline/wsl/about). Om du behöver hjälp att konfigurera nycklar för SSH-klienten kan se [hur du använder SSH-nycklar med Windows på Azure](~/articles/virtual-machines/linux/ssh-from-windows.md), eller [hur du skapar och använder ett SSH offentliga och privata nyckelpar för virtuella Linux-datorer i Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).

1. I portalen, går du till Linux-VM och i den **översikt**, klickar du på **Connect**.  
2. **Ansluta** till den virtuella datorn med SSH-klient av önskat. 
3. I terminalfönstret, med CURL, skicka en förfrågan till den lokala MSI-slutpunkten för att hämta en åtkomsttoken för Data Lake Store-filsystemet.  Resurs-ID: t för Data Lake Store är ”https://datalake.azure.net/”.  Det är viktigt att inkludera avslutande snedstreck i resurs-ID.
    
   ```bash
   curl http://localhost:50342/oauth2/token --data "resource=https://datalake.azure.net/" -H Metadata:true   
   ```
    
   Ett lyckat svar returneras den åtkomsttoken som du använder för att autentisera till Data Lake Store:

   ```bash
   {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1508119757",
    "not_before":"1508115857",
    "resource":"https://datalake.azure.net/",
    "token_type":"Bearer"}
   ```

4. Med CURL gör en begäran för Data Lake Store filesystem REST-slutpunkt att lista mappar i rotmappen.  Det här är ett enkelt sätt att kontrollera att allt är korrekt konfigurerad.  Kopiera värdet för den åtkomst-token från föregående steg.  Det är viktigt strängen ”ägar” i auktoriseringshuvudet har versalt ”B”.  Du hittar namnet på ditt Data Lake Store i den **översikt** på Data Lake Store-bladet i Azure-portalen.

   ```bash
   curl https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -H "Authorization: Bearer <ACCESS_TOKEN>"
   ```
    
   Det ser ut som ett lyckat svar:

   ```bash
   {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY","blockSize":0,"accessTime":1507934941392,"modificationTime":1508105430590,"replication":0,"permission":"770","owner":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071","group":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071"}]}}
   ```

5. Nu kan du ladda upp en fil till din Data Lake Store.  Skapa först en fil att överföra.

   ```bash
   echo "Test file." > Test1.txt
   ```

6. Med CURL gör en begäran för Data Lake Store filesystem REST-slutpunkt att överföra filen till den mapp som du skapade tidigare.  Överföringen innebär en omdirigering och CURL följer omdirigeringen automatiskt. 

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

Med andra Data Lake Store filesystem API: er som du kan lägga till filer, ladda ned filer och mycket annat.

Grattis!  Du har autentiserats med Data Lake Store-filsystemet med en VM-MSI.

## <a name="related-content"></a>Relaterat innehåll

- En översikt över MSI, se [hanterad tjänstidentitet översikt](msi-overview.md).
- Operations Data Lake Store använder Azure Resource Manager för hantering.  Mer information om hur du använder en VM MSI för att autentisera till Resource Manager [använder en Linux VM hanterad tjänstidentitet (MSI) för att få åtkomst till Resource Manager](../managed-service-identity/msi-tutorial-linux-vm-access-arm.md).
- Läs mer om [autentisering med Data Lake Store med hjälp av Azure Active Directory](~/articles/data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md).
- Läs mer om [filsystemsåtgärder på Azure Data Lake Store med REST API](~/articles/data-lake-store/data-lake-store-data-operations-rest-api.md) eller [WebHDFS FileSystem APIs](https://docs.microsoft.com/rest/api/datalakestore/webhdfs-filesystem-apis).
- Läs mer om [åtkomstkontroll i Data Lake Store](~/articles/data-lake-store/data-lake-store-access-control.md).

Använd följande avsnitt för kommentarer för att ge feedback och hjälp oss att förfina och forma vårt innehåll.