---
title: "Montera en filresurs på Azure och få åtkomst till resursen i Windows | Microsoft Docs"
description: "Montera en filresurs på Azure och få åtkomst till resursen i Windows."
services: storage
documentationcenter: na
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/27/2017
ms.author: renash
ms.translationtype: HT
ms.sourcegitcommit: 2812039649f7d2fb0705220854e4d8d0a031d31e
ms.openlocfilehash: e911e787cd1e29b2bbeaa648869c50245f2dd9ba
ms.contentlocale: sv-se
ms.lasthandoff: 07/22/2017

---

# <a name="mount-an-azure-file-share-and-access-the-share-in-windows"></a>Montera en filresurs på Azure och få åtkomst till resursen i Windows
[Azure File Storage](storage-dotnet-how-to-use-files.md) är Microsofts lättanvända filsystem i molnet. Azure-filresurser kan monteras i Windows och Windows Server. Den här artikeln visar tre olika sätt att montera en Azure-filresurs på Windows: med användargränssnittet Utforskaren, via PowerShell eller via Kommandotolken. 

Operativsystemet måste stödja SMB 3.0 för att montera en Azure-filresurs utanför den Azure-region som den finns i, till exempel lokalt eller i en annan Azure-region. 

Azure File-resursen kan monteras på Windows-datorn antingen lokalt eller i Azure VM beroende på operativsystemversion. Tabellen nedan visar den 

| Windows-version        | SMB-version |Monteras på Azure VM|Monteras lokalt|
|------------------------|-------------|---------------------|---------------------|
| Windows 7              | SMB 2.1     | Ja                 | Nej                  |
| Windows Server 2008 R2 | SMB 2.1     | Ja                 | Nej                  |
| Windows 8              | SMB 3.0     | Ja                 | Ja                 |
| Windows Server 2012    | SMB 3.0     | Ja                 | Ja                 |
| Windows Server 2012 R2 | SMB 3.0     | Ja                 | Ja                 |
| Windows 10             | SMB 3.0     | Ja                 | Ja                 |

> [!Note]  
> Vi rekommenderar alltid den senaste uppdateringen för din version av Windows.

## <a name="aprerequisites-for-mounting-azure-file-share-with-windows"></a></a>Förutsättningar för att montera Azure-filresurser med Windows 
* **Lagringskontonamn**: Om du vill montera en Azure-filresurs behöver du namnet på lagringskontot.

* **Lagringskontonyckel**: Om du vill montera en Azure-filresurs behöver du den primära (eller sekundära) lagringsnyckeln. SAS-nycklar stöds inte för montering.

* **Kontrollera att port 445 är öppen**: Azure File Storage använder SMB-protokollet. SMB kommunicerar via TCP-port 445. Kontrollera om din brandvägg blockerar TCP-port 445 från klientdatorn.

## <a name="mount-the-azure-file-share-with-file-explorer"></a>Montera Azure-filresursen med Utforskaren
> [!Note]  
> Observera att följande instruktioner visas på Windows 10 och kan skilja sig en aning på äldre versioner. 

1. **Öppna Utforskaren**: Detta kan göras genom att öppna från startmenyn eller genom att använda tangentbordsgenvägen Win+E.

2. **Navigera till objektet "Den här datorn" på vänster sida av fönstret. Detta ändrar menyerna i menyfliksområdet. Under menyn Dator väljer du "Anslut nätverksenhet"**.
    
    ![En skärmbild av den nedrullningsbara menyn "Anslut nätverksenhet"](media/storage-file-how-to-use-files-windows/1_MountOnWindows10.png)

3. **Kopiera UNC-sökvägen från fönstret "Anslut" i Azure Portal**: En detaljerad beskrivning av hur du hittar den här informationen hittar du [här](storage-file-how-to-use-files-portal.md#connect-to-file-share).

    ![UNC-sökvägen från fönstret Anslut i Azure File Storage](media/storage-file-how-to-use-files-windows/portal_netuse_connect.png)

4. **Välj enhetsbeteckningen och ange UNC-sökvägen.** 
    
    ![En skärmbild av dialogrutan "Anslut nätverksenhet"](media/storage-file-how-to-use-files-windows/2_MountOnWindows10.png)

5. **Använd lagringskontonamnet som börjar med `Azure\` som användarnamnet och en lagringskontonyckel som lösenord.**
    
    ![En skärmbild av dialogrutan med nätverksautentiseringsuppgifter](media/storage-file-how-to-use-files-windows/3_MountOnWindows10.png)

6. **Använd Azure-filresurser som du vill**.
    
    ![Azure-filresursen är nu monterad](media/storage-file-how-to-use-files-windows/4_MountOnWindows10.png)

7. **När du är redo att demontera (eller koppla från) Azure-filresursen så kan du göra det genom att högerklicka på posten för resursen under "Nätverksplatser" i Utforskaren och välja "Koppla från"**.

## <a name="mount-the-azure-file-share-with-powershell"></a>Montera Azure-filresursen med PowerShell
1. **Använd följande kommando för att montera Azure-filresursen**: Kom ihåg att ersätta `<storage-account-name>`, `<share-name>`, `<storage-account-key>` och `<desired-drive-letter>` med rätt information.

    ```PowerShell
    $acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
    New-PSDrive -Name <desired-drive-letter> -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\<share-name>" -Credential $credential
    ```

2. **Använd Azure-filresursen som du vill**.

3. **Demontera Azure-filresursen med följande kommando när du är klar**.

    ```PowerShell
    Remove-PSDrive -Name <desired-drive-letter>
    ```

> [!Note]  
> Du kan använda `-Persist`-parametern på `New-PSDrive` för att göra Azure-filresursen synlig för resten av operativsystemet när den är monterad.

## <a name="mount-the-azure-file-share-with-command-prompt"></a>Montera Azure-filresursen med Kommandotolken
1. **Använd följande kommando för att montera Azure-filresursen**: Kom ihåg att ersätta `<storage-account-name>`, `<share-name>`, `<storage-account-key>` och `<desired-drive-letter>` med rätt information.

    ```
    net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
    ```

2. **Använd Azure-filresursen som du vill**.

3. **Demontera Azure-filresursen med följande kommando när du är klar**.

    ```
    net use <desired-drive-letter>: /delete
    ```

> [!Note]  
> Du kan konfigurera Azure-filresursen för att återansluta automatiskt efter omstart genom att spara autentiseringsuppgifterna i Windows. Följande kommando sparar autentiseringsuppgifter:
>   ```
>   cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>
>   ```

## <a name="next-steps"></a>Nästa steg
Mer information om Azure File Storage finns på följande länkar.

* [Vanliga frågor och svar](storage-files-faq.md)
* [Felsökning](storage-troubleshoot-file-connection-problems.md)

### <a name="conceptual-articles-and-videos"></a>Begreppsrelaterade artiklar och videoklipp
* [Azure Files Storage: ett friktionslöst SMB-filsystem i molnet för Windows och Linux](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
* [Använd Azure File Storage med Linux](storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-azure-file-storage"></a>Verktygsstöd för Azure File Storage
* [Använd Azure PowerShell med Azure Storage](storage-powershell-guide-full.md)
* [Använd AzCopy med Microsoft Azure Storage](storage-use-azcopy.md)
* [Använd Azure CLI:et med Azure Storage](storage-azure-cli.md#create-and-manage-file-shares)
* [Felsökning av problem i Azure File Storage](https://docs.microsoft.com/azure/storage/storage-troubleshoot-file-connection-problems)

### <a name="blog-posts"></a>Blogginlägg
* [Azure File Storage finns nu allmänt tillgänglig](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Inuti Azure File Storage](https://azure.microsoft.com/blog/inside-azure-file-storage/)
* [Introduktion till Microsoft Azure File Service](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Migrera data till Azure File](https://azure.microsoft.com/blog/migrating-data-to-microsoft-azure-files/)

### <a name="reference"></a>Referens
* [Storage-klientbibliotek för .NET-referens](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [File Service REST API referens](http://msdn.microsoft.com/library/azure/dn167006.aspx)

