---
title: Introduktion till Azure File Storage | Microsoft Docs
description: "En översikt över Azure File Storage, en tjänst som gör att du kan skapa och använda nätverksfilresurser i Microsoft Cloud med protokollet som är branschstandard."
services: storage
documentationcenter: 
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
ms.sourcegitcommit: 0425da20f3f0abcfa3ed5c04cec32184210546bb
ms.openlocfilehash: 16fdd3aafef1a50554932a0e7843d347182c9b6a
ms.contentlocale: sv-se
ms.lasthandoff: 07/20/2017

---

# <a name="introduction-to-azure-file-storage"></a>Introduktion till Azure File Storage
Azure File Storage erbjuder nätverksfilresurser i molnet med hjälp av [SMB-protokollet (Server Message Block)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) och [Samba/CIFS (Common Internet File System)](https://technet.microsoft.com/library/cc939973.aspx) som är branschstandard. Azure-filresurser kan monteras samtidigt av klienter som lokala distributioner av Windows, macOS, Linux eller av virtuella Azure-datorer. Ett allmänt lagringskonto ger dig åtkomst till Azure File Storage och andra tjänster som blobbar, virtuella Azure-datordiskar och köer, allt med ett enda konto.



## <a name="videos"></a>Videoklipp
| Introduktion till Azure File Storage (27m) | Azure File Storage-självstudie (5 minuter)  |
|-|-|
| [![Skärmbild av videon Introduktion till Azure File Storage – Klicka för att spela upp!](media/storage-file-storage/azure-files-introduction-video-snapshot1.png)](https://www.youtube.com/watch?v=zlrpomv5RLs) | [![Skärmbild av videon Azure File Storage-självstudie – Klicka för att spela upp!](media/storage-file-storage/azure-files-introduction-video-snapshot2.png)](https://channel9.msdn.com/Blogs/Azure/Azure-File-storage-with-Windows/) |

## <a name="why-azure-file-storage-is-useful"></a>Varför Azure File Storage är användbart
Azure File Storage låter dig ersätta Windows Server, Linux eller NAS-baserade filservrar som befinner sig lokalt eller i molnet med en OS-oberoende filresurs i molnet. Detta har följande fördelar:

* **Delad åtkomst:**. Azure-filresurser stöder SMB-protokollet som är branschstandard, vilket innebär att du kan ersätta dina lokala filresurser sömlöst med Azure-filresurser utan att behöva oroa dig om programkompatibilitet. Att kunna dela ett filsystem över flera datorer och program/instanser är en stor fördel med Azure File Storage för program som måste kunna dela resurser med andra. 
* **Fullständigt hanterade**. Azure-filresurser kan skapas utan att behöva hantera maskinvara eller ett operativsystem. Det innebär att du inte behöver hantera korrigeringar av serverns OS med kritiska säkerhetsuppdateringar eller ersätta en felande hårddiskar.
* **Skript och verktyg**. PowerShell-cmdletar och Azure CLI kan användas för att skapa, montera och hantera fillagringsresurser som en del av administrationen av Azure-program. Du kan skapa och hantera Azure-filresurser med hjälp av Azure Portal och Azure Storage Explorer. 
* **Återhämtning**. Azure File Storage har byggts från grunden för att alltid vara tillgänglig. Om du ersätter lokala filresurser med Azure File Storage behöver du inte längre vakna tidigt på morgonen för att hantera lokala strömavbrott eller nätverksproblem. 
* **Bekant programmerbarhet**. Program som körs i Azure kan komma åt data i resursen via [filsystemets I/O-API:er](https://msdn.microsoft.com/library/system.io.file.aspx). Utvecklare kan därför utnyttja befintlig kod och erfarenhet för att migrera befintliga program. Förutom system-I/O-API:er kan du använda [Azure Storage-klientbibliotek](https://msdn.microsoft.com/library/azure/dn261237.aspx) eller [Azure Storage REST API](/rest/api/storageservices/file-service-rest-api).

Azure-filresurser kan användas för att:

* **Ersätta lokala filservrar**:  
    Azure File Storage kan användas för att fullständigt ersätta filresurser på traditionella lokala filservrar eller NAS-enheter. Populära operativsystem, till exempel Windows, macOS och Linux kan enkelt montera en Azure-filresurs oavsett var de befinner sig i världen.

* **"Lift and Shift"-hantera program**:  
    Azure File Storage gör det enkelt att "Lift and Shift"-hantera program till molnet som använder lokala filresurser för att dela data mellan delar av programmet. För att göra detta ansluter varje virtuell dator till filresursen och sedan kan de läsa och skriva filer precis som om det vore mot en lokal filresurs.

* **Simplify Cloud Development** (Förenkla molnutvecklingen):  
    Azure File Storage kan användas på ett antal olika sätt för att förenkla nya utvecklingsprojekt i molnet.
    * **Shared Application Settings** (Delade programinställningar):  
        Ett vanligt mönster för distribuerade program är att ha konfigurationsfilerna på en central plats där de kan nås från många olika virtuella datorer. Sådana konfigurationsfiler kan nu lagras i en Azure-filresurs och läsas av alla instanser av programmet. De här inställningarna kan också hanteras via REST-gränssnittet, vilket ger global åtkomst till konfigurationsfilerna.

    * **Diagnostic Share** (Diagnostikresurs):  
        En Azure-filresurs kan även användas för att spara diagnostiska filer som loggar, mått och kraschdumpar. Om du har dessa tillgängliga via både SMB- och REST-gränssnittet kan program skapa eller använda en mängd olika analysverktyg för att bearbeta och analysera diagnostiskdata.

    * **Dev/Test/Debug** (Utveckling/Testning/Felsökning):  
        När utvecklare och administratörer arbetar med virtuella datorer i molnet behöver de ofta en uppsättning verktyg och hjälpmedel. Att installera och distribuera dessa verktyg på varje virtuell dator där de behövs kan vara tidskrävande. Med Azure File Storage kan en utvecklare eller en administratör lagra sina favoritverktyg på en filresurs som enkelt kan anslutas till från en virtuell dator.
        
## <a name="how-does-it-work"></a>Hur fungerar det?
Att hantera Azure-filresurser är mycket enklare än att hantera filresurser lokalt. Följande diagram illustrerar hanteringskonstruktioner i Azure File Storage:

![Filstruktur](../../includes/media/storage-file-concepts-include/files-concepts.png)

* **Lagringskonto**: All åtkomst till Azure Storage görs genom ett lagringskonto. Se Skalbarhets- och prestandamål för Azure Storage för information om kapacitet för lagringskonton.
* **Resurs**: En File Storage-resurs är en SMB-filresurs i Azure. Alla kataloger och filer måste skapas i en överordnad resurs. Ett konto kan innehålla ett obegränsat antal resurser och en resurs kan lagra ett obegränsat antal filer, upp till 5 TB total kapacitet för filresursen.
* **Katalog:** En valfri hierarki med kataloger.
* **Fil:** En fil i resursen. En fil kan vara upp till 1 TB stor.
* **URL-format:** Filer är adresserbara via följande URL-format:  

    ```
    https://<storage account>.file.core.windows.net/<share>/<directory/directories>/<file>
    ```
## <a name="next-steps"></a>Nästa steg
* [Skapa en Azure-filresurs](storage-file-how-to-create-file-share.md)
* [Anslut och montera på Windows](storage-file-how-to-use-files-windows.md)
* [Anslut och montera på Linux](storage-how-to-use-files-linux.md)
* [Anslut och montera på macOS](storage-file-how-to-use-files-mac.md)
* [Vanliga frågor och svar](storage-files-faq.md)
* [Felsökning](storage-troubleshoot-file-connection-problems.md)

### <a name="conceptual-articles-and-videos"></a>Begreppsrelaterade artiklar och videoklipp
* [Azure Files Storage: ett friktionslöst SMB-filsystem i molnet för Windows och Linux](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)

### <a name="tooling-support-for-azure-file-storage"></a>Verktygsstöd för Azure File Storage
* [Använd Azure PowerShell med Azure Storage](storage-powershell-guide-full.md)
* [Använd AzCopy med Microsoft Azure Storage](storage-use-azcopy.md)
* [Använd Azure CLI:et med Azure Storage](storage-azure-cli.md#create-and-manage-file-shares)

### <a name="blog-posts"></a>Blogginlägg
* [Azure File Storage finns nu allmänt tillgänglig](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Inuti Azure File Storage](https://azure.microsoft.com/blog/inside-azure-file-storage/)
* [Introduktion till Microsoft Azure File Service](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Migrera data till Azure File](https://azure.microsoft.com/blog/migrating-data-to-microsoft-azure-files/)

### <a name="reference"></a>Referens
* [Storage-klientbibliotek för .NET-referens](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [File Service REST API referens](http://msdn.microsoft.com/library/azure/dn167006.aspx)

