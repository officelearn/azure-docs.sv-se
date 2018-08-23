---
title: Publicera-WebApplicationWebSite (Windows PowerShell-skript) | Microsoft Docs
description: Lär dig hur du publicerar ett webbprojekt till en Azure-webbplats. Det här skriptet skapar resurserna som krävs i din Azure-prenumeration om de inte finns.
services: visual-studio-online
author: ghogen
manager: douge
assetId: 63cfaa2d-f04d-40dc-8677-345385c278d5
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/11/2016
ms.author: ghogen
ms.openlocfilehash: ea8e36aabb75839a9c301f45a82241e3a859d42a
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2018
ms.locfileid: "42061686"
---
# <a name="publish-webapplicationwebsite-windows-powershell-script"></a>Publicera-WebApplicationWebSite (Windows PowerShell-skript)
## <a name="syntax"></a>Syntax
Publicerar ett webbprojekt till en Azure-webbplats. Skriptet skapar resurserna som krävs i Azure-prenumerationen om de inte finns.

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
| Alias |inga |
| Krävs? |true |
| Position |med namnet |
| Standardvärde |inga |
| Acceptera indata från pipeline? |false |
| Acceptera jokertecken? |false |

## <a name="subscriptionname"></a>Prenumerationsnamn
Namnet på den prenumeration som du vill skapa webbplatsen i.

| Parameter | Standardvärde |
| --- | --- |
| Alias |inga |
| Krävs? |false |
| Position |med namnet |
| Standardvärde |inga |
| Acceptera indata från pipeline? |false |
| Acceptera jokertecken? |false |

## <a name="webdeploypackage"></a>WebDeployPackage
Sökvägen till distributionspaketets att publicera till webbplatsen. Du kan skapa det här paketet med hjälp av guiden Publicera webbplats i Visual Studio. Mer information finns i [Kom igång med Azure Cloud Services och ASP.NET](http://go.microsoft.com/fwlink/p/?LinkID=623089).

| Parameter | Standardvärde |
| --- | --- |
| Alias |inga |
| Krävs? |false |
| Position |med namnet |
| Standardvärde |inga |
| Acceptera indata från pipeline? |false |
| Acceptera jokertecken? |false |

## <a name="databaseserverpassword"></a>DatabaseServerPassword
Användarnamnet och lösenordet för SQL-databas i Azure.

| Parameter | Standardvärde |
| --- | --- |
| Alias |inga |
| Krävs? |false |
| Position |med namnet |
| Standardvärde |inga |
| Acceptera indata från pipeline? |false |
| Acceptera jokertecken? |false |

## <a name="sendhostmessagestooutput"></a>SendHostMessagesToOutput
Om värdet är true, Skriv ut meddelanden från skriptet till utdataströmmen.

| Parameter | Standardvärde |
| --- | --- |
| Alias |inga |
| Krävs? |false |
| Position |med namnet |
| Standardvärde |false |
| Acceptera indata från pipeline? |false |
| Acceptera jokertecken? |false |

## <a name="remarks"></a>Kommentarer
En fullständig förklaring på hur du använder skriptet för att skapa utvecklings- och testmiljöer, finns i [med hjälp av Windows PowerShell-skript för publicera på utvecklings- och testmiljöer](vs-azure-tools-publishing-using-powershell-scripts.md).

JSON-konfigurationsfil anger information om nyheter som ska distribueras. Den innehåller information som du angav när du skapade projektet, till exempel namn och användarnamn för webbplatsen. Den innehåller också databasen att etablera, om sådana. Följande kod visar ett exempel JSON-konfigurationsfil:

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

Du kan redigera JSON-konfigurationsfil om du vill ändra vad som har distribuerats. Ett avsnitt för webbplats krävs, men databasen-avsnittet är valfritt.

## <a name="next-steps"></a>Nästa steg
Mer information finns i [publicera WebApplicationVM (Windows PowerShell-skript)](vs-azure-tools-publish-webapplicationvm.md)

