---
title: Introduktion till Azure Files | Microsoft Docs
description: "En översikt över Azure Files, en tjänst som gör att du kan skapa och använda nätverksfilresurser i molnet med SMB-protokollet som är branschstandard."
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
ms.date: 09/19/2017
ms.author: renash
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 5a4a26957c115277e7558c210560777af63d2d0f
ms.contentlocale: sv-se
ms.lasthandoff: 09/25/2017

---

# <a name="introduction-to-azure-files"></a>Introduktion till Azure Files
Azure Files erbjuder fullständigt hanterade filresurser i molnet som är tillgängliga via [SMB-protokollet (Server Message Block)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) som är branschstandard (kallas även Common Internet File System eller CIFS). Azure-filresurser kan monteras samtidigt av molndistributioner eller lokala distributioner av Windows, Linux och macOS. Azure-filresurser kan dessutom cachelagras på Windows-servrar med Azure File Sync (förhandsversion) för snabb åtkomst nära den plats där data används.

## <a name="videos"></a>Videoklipp
| Introduktion till Azure Files (27 m) | Självstudiekurs för Azure Files (5 m)  |
|-|-|
| [![Skärmbild av videon Introduktion till Azure Files – klicka för att spela upp!](./media/storage-files-introduction/azure-files-introduction-video-snapshot1.png)](https://www.youtube.com/watch?v=zlrpomv5RLs) | [![Skärmbild av självstudiekursen för Azure Files – klicka för att spela upp!](./media/storage-files-introduction/azure-files-introduction-video-snapshot2.png)](https://channel9.msdn.com/Blogs/Azure/Azure-File-storage-with-Windows/) |

## <a name="why-azure-files-is-useful"></a>Varför Azure Files är användbart
Azure-filresurser kan användas för att:

* **Ersätta eller komplettera lokala filservrar**:  
    Azure Files kan användas för att fullständigt ersätta eller komplettera traditionella lokala filservrar eller NAS-enheter. Populära operativsystem, till exempel Windows, macOS och Linux kan direktmontera Azure-filresurser oavsett var de befinner sig i världen. Azure-filresurser kan också replikeras med Azure-filsynkronisering till Windows-servrar, antingen lokalt eller i molnet, för högpresterande och distribuerad cachelagring av data där den används.

* **"Lift and Shift"-hantera program**:  
    Azure Files gör det enkelt att "lyfta och flytta" program till molnet som förväntar sig en filresurs för att lagra filprograms- eller användardata. Azure Files gör det möjligt att använda både det "klassiska" scenariot för att lyfta och flytta, där både programmet och dess data flyttas till Azure, och "hybridvarianten" av att lyfta och flytta, där programdata flyttas till Azure Files och programmet fortsätter att köras lokalt. 

* **Simplify Cloud Development** (Förenkla molnutvecklingen):  
    Azure Files kan även användas på flera sätt för att förenkla nya molnutvecklingsprojekt. Exempel:
    * **Shared Application Settings** (Delade programinställningar):  
        Ett vanligt mönster för distribuerade program är att ha konfigurationsfilerna på en central plats där de kan nås från många programinstanser. Programinstanser kan läsa in konfigurationen via File REST API och människor kan komma åt dem efter behov genom att montera SMB-resursen lokalt.

    * **Diagnostic Share** (Diagnostikresurs):  
        En Azure-filresurs är en praktisk plats för molnprogrammens skrivloggar, statistik och kraschdumpar. Loggar kan skrivas av programinstanser via File REST API och utvecklare kan komma åt dem genom att montera filresursen på en lokal dator. Detta ger stor flexibilitet, eftersom utvecklarna kan använda molnutveckling utan att behöva överge eventuella befintliga verktyg som de kan och gillar att använda.

    * **Dev/Test/Debug** (Utveckling/Testning/Felsökning):  
        När utvecklare och administratörer arbetar med virtuella datorer i molnet behöver de ofta en uppsättning verktyg och hjälpmedel. Det kan vara tidskrävande att kopiera sådana funktioner och verktyg till varje virtuell dator. Genom att montera en Azure-filresurs lokalt på de virtuella datorerna kan en utvecklare och administratör snabbt komma åt sina verktyg och funktioner, utan att någon kopiering krävs.

## <a name="key-benefits"></a>Viktiga fördelar
* **Delad åtkomst**. Azure-filresurser stöder SMB-protokollet som är branschstandard, vilket innebär att du kan ersätta dina lokala filresurser sömlöst med Azure-filresurser utan att behöva oroa dig om programkompatibilitet. Att kunna dela ett filsystem över flera datorer och program/instanser är en stor fördel med Azure Files för program som måste kunna dela resurser med andra. 
* **Fullständigt hanterade**. Azure-filresurser kan skapas utan att behöva hantera maskinvara eller ett operativsystem. Det innebär att du inte behöver hantera korrigeringar av serverns OS med kritiska säkerhetsuppdateringar eller ersätta en felande hårddiskar.
* **Skript och verktyg**. PowerShell-cmdletar och Azure CLI kan användas för att skapa, montera och hantera Azure-filresurser som en del av administrationen av Azure-program. Du kan skapa och hantera Azure-filresurser med hjälp av Azure Portal och Azure Storage Explorer. 
* **Återhämtning**. Azure Files har byggts från grunden för att alltid vara tillgänglig. Om du ersätter lokala filresurser med Azure Files behöver du inte längre vakna tidigt på morgonen för att hantera lokala strömavbrott eller nätverksproblem. 
* **Bekant programmerbarhet**. Program som körs i Azure kan komma åt data i resursen via [filsystemets I/O-API:er](https://msdn.microsoft.com/library/system.io.file.aspx). Utvecklare kan därför utnyttja befintlig kod och erfarenhet för att migrera befintliga program. Förutom system-I/O-API:er kan du använda [Azure Storage-klientbibliotek](https://msdn.microsoft.com/library/azure/dn261237.aspx) eller [Azure Storage REST API](/rest/api/storageservices/file-service-rest-api).

## <a name="next-steps"></a>Nästa steg
* [Skapa en Azure-filresurs](storage-how-to-create-file-share.md)
* [Anslut och montera på Windows](storage-how-to-use-files-windows.md)
* [Anslut och montera på Linux](storage-how-to-use-files-linux.md)
* [Anslut och montera på macOS](storage-how-to-use-files-mac.md)
