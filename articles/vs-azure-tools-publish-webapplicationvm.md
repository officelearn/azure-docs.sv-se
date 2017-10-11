---
title: Publicera WebApplicationVM | Microsoft Docs
description: "Lär dig hur du distribuerar ett webbprogram till en virtuell dator. Det här skriptet skapar resurserna som krävs i din Azure-prenumeration om de inte redan finns."
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: de4cec95-f73f-44d9-babd-9f47f2633cdb
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/11/2016
ms.author: kraigb
ms.openlocfilehash: 2738fc1dff50a177a227ae2c7719bd9a192d82ad
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="publish-webapplicationvm-windows-powershell-script"></a>Publicera-WebApplicationVM (Windows PowerShell-skript)
Distribuerar ett webbprogram till en virtuell dator. Skriptet skapar resurserna som krävs i din Azure-prenumeration om de inte redan finns.

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

| Alias | Ingen |
| --- | --- |
| Krävs? |SANT |
| Position |Med namnet |
| Standardvärde |Ingen |
| Acceptera indata från pipeline? |FALSKT |
| Acceptera jokertecken? |FALSKT |

### <a name="subscriptionname"></a>SubscriptionName
Namnet på den Azure-prenumeration som du vill skapa den virtuella datorn.

| Alias | Ingen |
| --- | --- |
| Krävs? |FALSKT |
| Position |Med namnet |
| Standardvärde |Använder den första prenumerationen i prenumerationsfilen |
| Acceptera indata från pipeline? |FALSKT |
| Acceptera jokertecken? |FALSKT |

### <a name="webdeploypackage"></a>WebDeployPackage
Sökvägen till distributionspaketets att publicera till den virtuella datorn. Du kan skapa det här paketet med hjälp av guiden Publicera webbplats i Visual Studio. Se [så här: skapa ett Webbdistributionspaket i Visual Studio](https://msdn.microsoft.com/library/dd465323.aspx).

| Alias | Ingen |
| --- | --- |
| Krävs? |FALSKT |
| Position |Med namnet |
| Standardvärde |Ingen |
| Acceptera indata från pipeline? |FALSKT |
| Acceptera jokertecken? |FALSKT |

### <a name="allowuntrusted"></a>AllowUntrusted
Tillåt användning av certifikat som inte är signerat av en betrodd rotcertifikatutfärdare om värdet är true.

| Alias | Ingen |
| --- | --- |
| Krävs? |FALSKT |
| Position |Med namnet |
| Standardvärde |FALSKT |
| Acceptera indata från pipeline? |FALSKT |
| Acceptera jokertecken? |FALSKT |

### <a name="vmpassword"></a>VMPassword
Autentiseringsuppgifterna för kontot för virtuell dator. Exempel: - VMPassword @{Name = ”admin”; Lösenord = ”password”}

| Alias | Ingen |
| --- | --- |
| Krävs? |FALSKT |
| Position |Med namnet |
| Standardvärde |Ingen |
| Acceptera indata från pipeline? |FALSKT |
| Acceptera jokertecken? |FALSKT |

### <a name="databaseserverpassword"></a>DatabaseServerPassword
Autentiseringsuppgifterna för SQL-databas i Azure. Exempel: - DatabaseServerPassword @{Name = ”admin”; Lösenord = ”password”}

| Alias | Ingen |
| --- | --- |
| Krävs? |FALSKT |
| Position |Med namnet |
| Standardvärde |Ingen |
| Acceptera indata från pipeline? |FALSKT |
| Acceptera jokertecken? |FALSKT |

### <a name="sendhostmessagestooutput"></a>SendHostMessagesToOutput
Om värdet är true, Skriv ut meddelanden från skriptet till utdataströmmen.

| Alias | Ingen |
| --- | --- |
| Krävs? |FALSKT |
| Position |Med namnet |
| Standardvärde |FALSKT |
| Acceptera indata från pipeline? |FALSKT |
| Acceptera jokertecken? |FALSKT |

## <a name="remarks"></a>Kommentarer
En fullständig förklaring av hur du använder skriptet för att skapa utvecklings- och testmiljöer finns [med hjälp av Windows PowerShell-skript för publicera utvecklings-och testmiljöer](vs-azure-tools-publishing-using-powershell-scripts.md).

JSON-konfigurationsfil anger information om vad som distribueras. Den innehåller den information som du angav när du har skapat projektet, till exempel namn, tillhörighetsgrupp, VHD-avbildningen och storleken på den virtuella datorn. Dessutom innehåller slutpunkter på den virtuella datorn, databaser att etablera, om sådana finns, och web distributionsparametrarna. Följande kod visar ett exempel JSON-konfigurationsfil:

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

Du kan redigera JSON-konfigurationsfil om du vill ändra vilka etableras. En virtuell dator och en molnbaserad tjänst som krävs, men databasen-avsnittet är valfritt.

