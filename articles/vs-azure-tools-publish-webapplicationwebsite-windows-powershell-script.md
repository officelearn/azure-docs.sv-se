---
title: Publicera-WebApplicationWebSite (Windows PowerShell-skript) | Microsoft Docs
description: "Lär dig hur du publicerar ett webbprojekt till en Azure-webbplats. Det här skriptet skapar resurserna som krävs i din Azure-prenumeration om de inte redan finns."
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 63cfaa2d-f04d-40dc-8677-345385c278d5
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/11/2016
ms.author: kraigb
ms.openlocfilehash: 07d21b7ce6cd8aee1cff704d316e7a2ca8c00437
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="publish-webapplicationwebsite-windows-powershell-script"></a>Publicera-WebApplicationWebSite (Windows PowerShell-skript)
## <a name="syntax"></a>Syntax
Publicerar ett webbprojekt till en Azure-webbplats. Skriptet skapar resurserna som krävs i din Azure-prenumeration om de inte redan finns.

    Publish-WebApplicationWebSite
    –Configuration <configuration>
    -SubscriptionName <subscriptionName>
    -WebDeployPackage <packageName>
    -DatabaseServerPassword @{Name = "name"; Password = "password"}
    -SendHostMessagesToOutput
    -Verbose


## <a name="configuration"></a>Konfiguration
Sökvägen till JSON-konfigurationsfil som innehåller information om distributionen.

| Parameter | Standardvärde |
| --- | --- |
| Alias |Ingen |
| Krävs? |SANT |
| Position |Med namnet |
| Standardvärde |Ingen |
| Acceptera indata från pipeline? |FALSKT |
| Acceptera jokertecken? |FALSKT |

## <a name="subscriptionname"></a>SubscriptionName
Namnet på Azure-prenumeration som du vill skapa webbplatsen i.

| Parameter | Standardvärde |
| --- | --- |
| Alias |Ingen |
| Krävs? |FALSKT |
| Position |Med namnet |
| Standardvärde |Ingen |
| Acceptera indata från pipeline? |FALSKT |
| Acceptera jokertecken? |FALSKT |

## <a name="webdeploypackage"></a>WebDeployPackage
Sökvägen till distributionspaketets att publicera på webbplatsen. Du kan skapa det här paketet med hjälp av guiden Publicera webbplats i Visual Studio. Mer information finns i [Kom igång med Azure Cloud Services och ASP.NET](http://go.microsoft.com/fwlink/p/?LinkID=623089).

| Parameter | Standardvärde |
| --- | --- |
| Alias |Ingen |
| Krävs? |FALSKT |
| Position |Med namnet |
| Standardvärde |Ingen |
| Acceptera indata från pipeline? |FALSKT |
| Acceptera jokertecken? |FALSKT |

## <a name="databaseserverpassword"></a>DatabaseServerPassword
Användarnamn och lösenord för SQL-databas i Azure.

| Parameter | Standardvärde |
| --- | --- |
| Alias |Ingen |
| Krävs? |FALSKT |
| Position |Med namnet |
| Standardvärde |Ingen |
| Acceptera indata från pipeline? |FALSKT |
| Acceptera jokertecken? |FALSKT |

## <a name="sendhostmessagestooutput"></a>SendHostMessagesToOutput
Om värdet är true, Skriv ut meddelanden från skriptet till utdataströmmen.

| Parameter | Standardvärde |
| --- | --- |
| Alias |Ingen |
| Krävs? |FALSKT |
| Position |Med namnet |
| Standardvärde |FALSKT |
| Acceptera indata från pipeline? |FALSKT |
| Acceptera jokertecken? |FALSKT |

## <a name="remarks"></a>Kommentarer
En fullständig förklaring av hur du använder skriptet för att skapa utvecklings- och testmiljöer finns [med hjälp av Windows PowerShell-skript för publicera utvecklings-och testmiljöer](vs-azure-tools-publishing-using-powershell-scripts.md).

JSON-konfigurationsfil anger information om vad som distribueras. Den innehåller information som du angav när du skapade projektet, till exempel namn och användarnamn för webbplatsen. Den omfattar också databasen för att etablera, om sådana finns. Följande kod visar ett exempel JSON-konfigurationsfil:

    {
        "environmentSettings": {
            "webSite": {
                "name": "WebApplication10554",
                "location": "West US"
            },
            "databases": [
                {
                    "connectionStringName": "DefaultConnection",
                    "databaseName": "WebApplication10554_db",
                    "serverName": "iss00brc88",
                    "user": "sqluser2",
                    "password": "",
                    "edition": "",
                    "size": "",
                    "collation": "",
                    "location": "West US"
                }
            ]
        }
    }

Du kan redigera JSON-konfigurationsfil om du vill ändra vilka distribueras. Ett avsnitt på webbplatsen krävs, men databasen-avsnittet är valfritt.

## <a name="next-steps"></a>Nästa steg
Mer information finns i [publicera WebApplicationVM (Windows PowerShell-skript)](vs-azure-tools-publish-webapplicationvm.md)

