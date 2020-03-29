---
title: Visa och ändra värdnamn | Microsoft-dokument
description: Visa och ändra värdnamn för virtuella Azure-datorer, webb- och arbetsroller för namnmatchning
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: tysonn
ms.assetid: c668cd8e-4e43-4d05-acc3-db64fa78d828
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: cce248e2906f4a36737388e8cc7124b1bb19fbae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71058680"
---
# <a name="viewing-and-modifying-hostnames"></a>Visa och ändra värdnamn
Om du vill att dina rollförekomster ska refereras av värdnamnet måste du ange värdet för värdnamnet i tjänstkonfigurationsfilen för varje roll. Det gör du genom att lägga till önskat värdnamn i **attributet vmName** för **rollelementet.** Värdet för **attributet vmName** används som bas för värdnamnet för varje rollinstans. Till exempel, om **vmName** är *webrole* och det finns tre instanser av den rollen, kommer värdnamnen för instanserna att *webrole0,* *webrole1*och *webrole2*. Du behöver inte ange ett värdnamn för virtuella datorer i konfigurationsfilen, eftersom värdnamnet för en virtuell dator fylls i baserat på namnet på den virtuella datorn. Mer information om hur du konfigurerar en Microsoft Azure-tjänst finns i [Azure Service Configuration Schema (.cscfg File)](https://msdn.microsoft.com/library/azure/ee758710.aspx)

## <a name="viewing-hostnames"></a>Visa värdnamn
Du kan visa värdnamnen för virtuella datorer och rollinstanser i en molntjänst med hjälp av något av verktygen nedan.

### <a name="service-configuration-file"></a>Konfigurationsfil för tjänst
Du kan hämta tjänstkonfigurationsfilen för en distribuerad tjänst från **konfigurera** bladet för tjänsten i Azure-portalen. Du kan sedan leta efter **attributet vmName** för **rollnamnselementet** för att se värdnamnet. Tänk på att det här värdnamnet används som bas för värdnamnet för varje rollinstans. Till exempel, om **vmName** är *webrole* och det finns tre instanser av den rollen, kommer värdnamnen för instanserna att *webrole0,* *webrole1*och *webrole2*.

### <a name="remote-desktop"></a>Fjärrskrivbord
När du har aktiverat Anslutningar för Fjärrskrivbord (Windows), Windows PowerShell remoting (Windows) eller SSH-anslutningar (Linux och Windows) till virtuella datorer eller rollinstanser kan du visa värdnamnet från en aktiv anslutning till fjärrskrivbord på olika sätt:

* Skriv värdnamn i kommandotolken eller SSH-terminalen.
* Skriv ipconfig /all i kommandotolken (endast Windows).
* Visa datornamnet i systeminställningarna (endast Windows).

### <a name="azure-service-management-rest-api"></a>REST-API för Azure Service Management
Från en REST-klient följer du dessa instruktioner:

1. Kontrollera att du har ett klientcertifikat för att ansluta till Azure-portalen. Om du vill hämta ett klientcertifikat följer du stegen i [Så här hämtar och importerar du publiceringsinställningar och prenumerationsinformation](https://msdn.microsoft.com/library/dn385850.aspx). 
2. Ange en rubrikpost med namnet x-ms-version med värdet 2013-11-01.
3. Skicka en begäran i följande\/format: https:\</management.core.windows.net/ prenumerera-id\>/services/hostedservices/\<service-name\>?embed-detail=true
4. Leta efter **elementet HostName** för varje **RollInstance-element.**

> [!WARNING]
> Du kan också visa det interna domänsuffixet för molntjänsten från REST-anropssvaret genom att kontrollera **Elementet InternalDnsSuffix** eller genom att köra ipconfig /all från en kommandotolk i en fjärrskrivbordssession (Windows) eller genom att köra cat /etc/resolv.conf från en SSH-terminal (Linux).
> 
> 

## <a name="modifying-a-hostname"></a>Ändra ett värdnamn
Du kan ändra värdnamnet för en virtuell dator eller rollinstans genom att ladda upp en modifierad tjänstkonfigurationsfil eller genom att byta namn på datorn från en fjärrskrivbordssession.

## <a name="next-steps"></a>Nästa steg
[Namnmatchning (DNS)](virtual-networks-name-resolution-for-vms-and-role-instances.md)

[Konfigurationsschema för Azure-tjänst (.cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710.aspx)

[Konfigurationsschema för virtuellt Azure-nätverk](https://go.microsoft.com/fwlink/?LinkId=248093)

[Ange DNS-inställningar med hjälp av nätverkskonfigurationsfiler](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md)

