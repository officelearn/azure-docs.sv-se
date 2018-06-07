---
title: Visa och ändra värdnamn | Microsoft Docs
description: Hur du visa och ändra värdnamn för virtuella Azure-datorer, webb- och arbetsroller för namnmatchning
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
ms.date: 05/24/2018
ms.author: genli
ms.openlocfilehash: f4c602368368e8ef36581d3f035ff3943a8f0d8f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34657289"
---
# <a name="viewing-and-modifying-hostnames"></a>Visa och ändra värdnamn
Om du vill att dina rollinstanser refereras efter värdnamn, måste du ange värde för värddatorns namn i tjänstkonfigurationsfilen för varje roll. Det gör du genom att lägga till önskade värdnamnet som den **vmName** attribut för den **rollen** element. Värdet för den **vmName** attributet används som bas för värdnamnet för varje rollinstans. Till exempel om **vmName** är *webrole* och det finns tre instanser av rollen, värdnamn av instanserna blir *webrole0*, *webrole1*, och *webrole2*. Du behöver inte ange ett värdnamn för virtuella datorer i konfigurationsfilen, eftersom värdnamnet för en virtuell dator har fyllts i baserat på namnet på virtuella datorn. Mer information om hur du konfigurerar en Microsoft Azure-tjänst finns [Konfigurationsschemat för Azure-tjänsten (.cscfg-filen)](https://msdn.microsoft.com/library/azure/ee758710.aspx)

## <a name="viewing-hostnames"></a>Visa värdnamn
Du kan visa värdnamn för virtuella datorer och rollinstanser i en molntjänst med hjälp av verktygen nedan.

### <a name="service-configuration-file"></a>Tjänstkonfigurationsfil
Du kan hämta tjänstkonfigurationsfilen för en distribuerad tjänst från den **konfigurera** bladet för tjänsten i Azure-portalen. Du kan sedan söka efter den **vmName** attributet för den **rollnamn** element att se värdnamnet. Tänk på att det här värdnamnet används som bas för värdnamnet för varje rollinstans. Till exempel om **vmName** är *webrole* och det finns tre instanser av rollen, värdnamn av instanserna blir *webrole0*, *webrole1*, och *webrole2*.

### <a name="remote-desktop"></a>Fjärrskrivbord
När du aktiverar fjärrskrivbord (Windows), Windows PowerShell-fjärrkommunikation (Windows) eller SSH (Linux och Windows)-anslutningar till dina virtuella datorer eller rollinstanser, kan du visa värdnamnet från en aktiv anslutning till fjärrskrivbord på olika sätt:

* Ange värdnamnet i Kommandotolken eller SSH-terminal.
* Skriv ipconfig/alla vid Kommandotolken (endast Windows).
* Visa namnet på datorn i Systeminställningar (endast Windows).

### <a name="azure-service-management-rest-api"></a>Azure Service Management REST API
Följ dessa instruktioner från en REST-klient:

1. Se till att du har ett klientcertifikat för att ansluta till Azure-portalen. För att erhålla ett klientcertifikat följer du stegen som visas i [så här: hämta och importera Publiceringsinställningar och prenumerationsinformation](https://msdn.microsoft.com/library/dn385850.aspx). 
2. Ange en rubrikpost med namnet x-ms-version med värdet 2013-11-01.
3. Skicka en begäran i följande format: https://management.core.windows.net/\<subscrition-id\>/services/hostedservices/\<service-name\>?embed-detail=true
4. Leta efter den **värdnamn** element för varje **RoleInstance** element.

> [!WARNING]
> Du kan också visa interna domänsuffixet för din molntjänst från svaret för REST-anrop genom att kontrollera den **InternalDnsSuffix** elementet eller genom att köra ipconfig/alla från Kommandotolken i en fjärrskrivbordssession (Windows) eller genom att Kör cat /etc/resolv.conf från en terminal SSH (Linux).
> 
> 

## <a name="modifying-a-hostname"></a>Ändra ett värdnamn
Du kan ändra värdnamnet för en virtuell dator eller rollinstans genom att ladda upp en ändrade tjänstkonfigurationsfilen eller genom att byta namn på datorn från en fjärrskrivbordssession.

## <a name="next-steps"></a>Nästa steg
[Namnmatchning (DNS)](virtual-networks-name-resolution-for-vms-and-role-instances.md)

[Konfigurationsschemat för Azure-tjänsten (.cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710.aspx)

[Virtuella Azure-nätverket Konfigurationsschemat](http://go.microsoft.com/fwlink/?LinkId=248093)

[Ange DNS-inställningar med hjälp av network configuration-filer](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md)

