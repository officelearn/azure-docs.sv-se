---
title: Publicera WebApplicationVM | Microsoft Docs
description: Lär dig hur du distribuerar ett webbprogram till en virtuell dator. Det här skriptet skapar resurserna som krävs i din Azure-prenumeration om de inte finns.
services: visual-studio-online
author: ghogen
manager: douge
assetId: de4cec95-f73f-44d9-babd-9f47f2633cdb
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/11/2016
ms.author: ghogen
ms.openlocfilehash: c2dc6057eeb4eba1306309785e13192674bc43c6
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2018
ms.locfileid: "42057684"
---
# <a name="publish-webapplicationvm-windows-powershell-script"></a>Publicera-WebApplicationVM (Windows PowerShell-skript)
Distribuerar ett webbprogram till en virtuell dator. Skriptet skapar resurserna som krävs i Azure-prenumerationen om de inte finns.

```
Publish-WebApplicationVM
–Configuration <configuration>
-SubscriptionName <subscriptionName>
-WebDeployPackage <packageName>
-VMPassword @{Name = "name"; Password = "password")
-DatabaseServerPassword @{Name = "name"; Password = "password"}
-SendHostMessagesToOutput
-Verbose
```

### <a name="configuration"></a>Konfiguration
Sökvägen till JSON-konfigurationsfil som innehåller information om distributionen.

| Alias | inga |
| --- | --- |
| Krävs? |true |
| Position |med namnet |
| Standardvärde |inga |
| Acceptera indata från pipeline? |false |
| Acceptera jokertecken? |false |

### <a name="subscriptionname"></a>Prenumerationsnamn
Namnet på den Azure-prenumeration där du vill skapa den virtuella datorn.

| Alias | inga |
| --- | --- |
| Krävs? |false |
| Position |med namnet |
| Standardvärde |Använder den första prenumerationen i prenumerationsfilen |
| Acceptera indata från pipeline? |false |
| Acceptera jokertecken? |false |

### <a name="webdeploypackage"></a>WebDeployPackage
Sökvägen till distributionspaketets att publicera till den virtuella datorn. Du kan skapa det här paketet med hjälp av guiden Publicera webbplats i Visual Studio. Se [så här: skapa ett Webbdistributionspaket i Visual Studio](https://msdn.microsoft.com/library/dd465323.aspx).

| Alias | inga |
| --- | --- |
| Krävs? |false |
| Position |med namnet |
| Standardvärde |inga |
| Acceptera indata från pipeline? |false |
| Acceptera jokertecken? |false |

### <a name="allowuntrusted"></a>AllowUntrusted
Tillåt användning av certifikat som inte är signerat av en betrodd rotcertifikatutfärdare om värdet är true.

| Alias | inga |
| --- | --- |
| Krävs? |false |
| Position |med namnet |
| Standardvärde |false |
| Acceptera indata från pipeline? |false |
| Acceptera jokertecken? |false |

### <a name="vmpassword"></a>VMPassword
Autentiseringsuppgifterna för VM-kontot. Exempel: - VMPassword @{Name = ”admin”; Lösenord = ”password”}

| Alias | inga |
| --- | --- |
| Krävs? |false |
| Position |med namnet |
| Standardvärde |inga |
| Acceptera indata från pipeline? |false |
| Acceptera jokertecken? |false |

### <a name="databaseserverpassword"></a>DatabaseServerPassword
Autentiseringsuppgifter för SQL-databas i Azure. Exempel: - DatabaseServerPassword @{Name = ”admin”; Lösenord = ”password”}

| Alias | inga |
| --- | --- |
| Krävs? |false |
| Position |med namnet |
| Standardvärde |inga |
| Acceptera indata från pipeline? |false |
| Acceptera jokertecken? |false |

### <a name="sendhostmessagestooutput"></a>SendHostMessagesToOutput
Om värdet är true, Skriv ut meddelanden från skriptet till utdataströmmen.

| Alias | inga |
| --- | --- |
| Krävs? |false |
| Position |med namnet |
| Standardvärde |false |
| Acceptera indata från pipeline? |false |
| Acceptera jokertecken? |false |

## <a name="remarks"></a>Kommentarer
En fullständig förklaring på hur du använder skriptet för att skapa utvecklings- och testmiljöer, finns i [med hjälp av Windows PowerShell-skript för publicera på utvecklings- och testmiljöer](vs-azure-tools-publishing-using-powershell-scripts.md).

JSON-konfigurationsfil anger information om nyheter som ska distribueras. Den innehåller den information som du angav när du har skapat projektet, till exempel namn, tillhörighetsgrupp, VHD-avbildningen och storleken på den virtuella datorn. Dessutom innehåller slutpunkter på den virtuella datorn, databaser att etablera, om några, och web distributionsparametrarna. Följande kod visar ett exempel JSON-konfigurationsfil:

```
{
    "environmentSettings": {
        "cloudService": {
            "name": "myvmname",
            "affinityGroup": "",
            "location": "West US",
            "virtualNetwork": "",
            "subnet": "",
            "availabilitySet": "",
            "virtualMachine": {
                "name": "myvmname",
                "vhdImage": "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201404.01-en.us-127GB.vhd",
                "size": "Small",
                "user": "vmuser1",
                "password": "",
                "enableWebDeployExtension": true,
                "endpoints": [
                    {
                        "name": "Http",
                        "protocol": "TCP",
                        "publicPort": "80",
                        "privatePort": "80"
                    },
                    {
                        "name": "Https",
                        "protocol": "TCP",
                        "publicPort": "443",
                        "privatePort": "443"
                    },
                    {
                        "name": "WebDeploy",
                        "protocol": "TCP",
                        "publicPort": "8172",
                        "privatePort": "8172"
                    },
                    {
                        "name": "Remote Desktop",
                        "protocol": "TCP",
                        "publicPort": "3389",
                        "privatePort": "3389"
                    },
                    {
                        "name": "Powershell",
                        "protocol": "TCP",
                        "publicPort": "5986",
                        "privatePort": "5986"
                    }
                ]
            }
        },
        "databases": [
            {
                "connectionStringName": "",
                "databaseName": "",
                "serverName": "",
                "user": "",
                "password": ""
            }
        ],
        "webDeployParameters": {
            "iisWebApplicationName": "Default Web Site"
        }
    }
}
```

Du kan redigera JSON-konfigurationsfil om du vill ändra vad som har etablerats. En virtuell dator och en tjänst i molnet krävs, men databasen-avsnittet är valfritt.

