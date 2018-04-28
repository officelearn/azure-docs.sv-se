---
title: Publicera-WebApplicationWebSite (Windows PowerShell-skript) | Microsoft Docs
description: Lär dig hur du publicerar ett webbprojekt till en Azure-webbplats. Det här skriptet skapar resurserna som krävs i din Azure-prenumeration om de inte redan finns.
services: visual-studio-online
author: ghogen
manager: douge
assetId: 63cfaa2d-f04d-40dc-8677-345385c278d5
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 11/11/2016
ms.author: ghogen
ms.openlocfilehash: b540734f17ed11e4c438e1248ed9612fb892e89a
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
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
| Alias |ingen |
| Krävs? |true |
| Position |Med namnet |
| Standardvärde |ingen |
| Acceptera indata från pipeline? |false |
| Acceptera jokertecken? |false |

## <a name="subscriptionname"></a>Prenumerationsnamn
Namnet på Azure-prenumeration som du vill skapa webbplatsen i.

| Parameter | Standardvärde |
| --- | --- |
| Alias |ingen |
| Krävs? |false |
| Position |Med namnet |
| Standardvärde |ingen |
| Acceptera indata från pipeline? |false |
| Acceptera jokertecken? |false |

## <a name="webdeploypackage"></a>WebDeployPackage
Sökvägen till distributionspaketets att publicera på webbplatsen. Du kan skapa det här paketet med hjälp av guiden Publicera webbplats i Visual Studio. Mer information finns i [Kom igång med Azure Cloud Services och ASP.NET](http://go.microsoft.com/fwlink/p/?LinkID=623089).

| Parameter | Standardvärde |
| --- | --- |
| Alias |ingen |
| Krävs? |false |
| Position |Med namnet |
| Standardvärde |ingen |
| Acceptera indata från pipeline? |false |
| Acceptera jokertecken? |false |

## <a name="databaseserverpassword"></a>DatabaseServerPassword
Användarnamn och lösenord för SQL-databas i Azure.

| Parameter | Standardvärde |
| --- | --- |
| Alias |ingen |
| Krävs? |false |
| Position |Med namnet |
| Standardvärde |ingen |
| Acceptera indata från pipeline? |false |
| Acceptera jokertecken? |false |

## <a name="sendhostmessagestooutput"></a>SendHostMessagesToOutput
Om värdet är true, Skriv ut meddelanden från skriptet till utdataströmmen.

| Parameter | Standardvärde |
| --- | --- |
| Alias |ingen |
| Krävs? |false |
| Position |Med namnet |
| Standardvärde |false |
| Acceptera indata från pipeline? |false |
| Acceptera jokertecken? |false |

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

