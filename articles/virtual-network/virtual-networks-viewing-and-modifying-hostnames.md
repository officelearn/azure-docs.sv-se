---
title: Visa och ändra värdnamn | Microsoft Docs
description: Så här att visa och ändra värdnamn för Azure virtual machines, web och worker-roller för namnmatchning
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
ms.assetid: c668cd8e-4e43-4d05-acc3-db64fa78d828
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 3fdb0f566789382a1606b19e4fac179f9ecf40cd
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57887467"
---
# <a name="viewing-and-modifying-hostnames"></a>Visa och ändra värdnamn
Om du vill tillåta dina rollinstanser som refereras av värdnamn, måste du ange värdet för värdnamnet i tjänstekonfigurationsfilen för varje roll. Det gör du genom att lägga till önskade värdnamnet till den **vmName** attributet för den **rollen** element. Värdet för den **vmName** attributet används som bas för värdnamnet för varje rollinstans. Till exempel om **vmName** är *webrole* och det finns tre instanser av rollen, värdnamn av instanserna blir *webrole0*, *webrole1*, och *webrole2*. Du behöver inte ange ett värdnamn för virtuella datorer i konfigurationsfilen, eftersom värdnamnet för en virtuell dator har fyllts i baserat på virtuella datornamn. Mer information om hur du konfigurerar en Microsoft Azure-tjänst finns i [Azure-Tjänstkonfigurationens Schema (.cscfg-filen)](https://msdn.microsoft.com/library/azure/ee758710.aspx)

## <a name="viewing-hostnames"></a>Visa värdnamn
Du kan visa värdnamn för virtuella datorer och rollinstanser i en molntjänst med hjälp av verktygen nedan.

### <a name="service-configuration-file"></a>Tjänstkonfigurationsfil
Du kan hämta tjänstkonfigurationsfilen för en distribuerad tjänst från den **konfigurera** bladet för tjänsten i Azure-portalen. Du kan sedan söka efter den **vmName** attributet för den **rollnamn** element att se värdnamnet. Tänk på att värddatorns namn används som bas för värdnamnet för varje rollinstans. Till exempel om **vmName** är *webrole* och det finns tre instanser av rollen, värdnamn av instanserna blir *webrole0*, *webrole1*, och *webrole2*.

### <a name="remote-desktop"></a>Fjärrskrivbord
När du har aktiverat Fjärrskrivbord (Windows), Windows PowerShell-fjärrkommunikation (Windows) eller SSH (Linux och Windows) anslutningar till dina virtuella datorer eller rollinstanser kan du visa värdnamnet från en aktiv anslutning till fjärrskrivbord på olika sätt:

* Skriv värdnamn vid kommandotolken eller SSH-terminalen.
* Skriv ipconfig/alla vid Kommandotolken (endast Windows).
* Visa namnet på datorn i Systeminställningar (endast Windows).

### <a name="azure-service-management-rest-api"></a>Azure Service Management REST API
Följ de här instruktionerna från en REST-klient:

1. Kontrollera att du har ett klientcertifikat för att ansluta till Azure-portalen. Om du vill skaffa ett klientcertifikat, följer du stegen som visas i [så här: Hämta och importera publicera inställningar och prenumerationsinformation](https://msdn.microsoft.com/library/dn385850.aspx). 
2. Ange en rubrikpost med namnet x-ms-version med värdet 2013-11-01.
3. Skicka en begäran i följande format: https:\//management.core.windows.net/\<prenumerationen-id\>/services/hostedservices/\<tjänstnamnet\>? bädda in detail = true
4. Leta efter den **värdnamn** element för varje **Rollinstans** element.

> [!WARNING]
> Du kan också visa interna domänsuffixet för din molntjänst från REST-anrop svar genom att markera den **InternalDnsSuffix** element, eller genom att köra ipconfig/allt från en kommandotolk i en fjärrskrivbordssession (Windows) eller genom att Kör cat /etc/resolv.conf från en terminal SSH (Linux).
> 
> 

## <a name="modifying-a-hostname"></a>Ändra ett värdnamn
Du kan ändra värdnamnet för valfri virtuell dator eller rollinstans genom att ladda upp en ändrad tjänstkonfigurationsfil eller genom att byta namn på datorn från en fjärrskrivbordssession.

## <a name="next-steps"></a>Nästa steg
[Namnmatchning (DNS)](virtual-networks-name-resolution-for-vms-and-role-instances.md)

[Azure-Tjänstkonfigurationens Schema (.cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710.aspx)

[Konfigurationsschema för Azure-nätverk](https://go.microsoft.com/fwlink/?LinkId=248093)

[Ange DNS-inställningar med hjälp av nätverkskonfigurationen](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md)

