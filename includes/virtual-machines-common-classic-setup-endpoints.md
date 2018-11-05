---
title: ta med fil
description: ta med fil
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 10/23/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: e7dfd7d2a0363a95acb76a5dc214dbd4036de11d
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50973989"
---
Varje slutpunkt har en *offentlig port* och en *privat port*:

* Den offentliga porten används av Azure load balancer för att lyssna efter inkommande trafik till den virtuella datorn från internet.
* Den privata porten används av den virtuella datorn för att lyssna efter inkommande trafik, vanligtvis ett program eller tjänst som körs på den virtuella datorn.

Standardvärden för IP-protokoll och TCP eller UDP-portar för välkända nätverket protokoll anges när du skapar slutpunkter med Azure-portalen. Ange rätt IP-protokoll (TCP eller UDP) och offentliga och privata portar för anpassade slutpunkter. För att distribuera inkommande trafik slumpmässigt över flera virtuella datorer, skapa en belastningsutjämnad uppsättning som består av flera slutpunkter.

När du har skapat en slutpunkt kan använda du en åtkomstkontrollista (ACL) för att definiera regler som tillåter eller nekar inkommande trafik till den offentliga porten för slutpunkten baserat på dess IP-källadressen. Men om den virtuella datorn är i ett Azure-nätverk, använda nätverkssäkerhetsgrupper i stället. Mer information finns i [om nätverkssäkerhetsgrupper](../articles/virtual-network/security-overview.md).

> [!NOTE]
> Brandväggskonfiguration för Azure-datorer görs automatiskt för portar som är associerade med fjärranslutningar slutpunkter som Azure ställer in automatiskt. Ingen konfiguration görs automatiskt i brandväggen för den virtuella datorn för portar som angetts för alla andra slutpunkter. När du skapar en slutpunkt för den virtuella datorn kan du se till att brandväggen på den virtuella datorn också tillåter trafik för protokoll och privat port som motsvarar konfigurationen av slutpunkten. Om du vill konfigurera brandväggen finns i dokumentationen eller Onlinehjälp för det operativsystem som körs på den virtuella datorn.
>
>

## <a name="create-an-endpoint"></a>Skapa en slutpunkt
1. Logga in på [Azure Portal](https://portal.azure.com).

2. Välj **virtuella datorer**, och välj sedan den virtuella dator som du vill konfigurera.

3. Välj **slutpunkter** i den **inställningar** grupp. Den **slutpunkter** visas, som visar en lista över alla aktuella slutpunkter för den virtuella datorn. (Det här exemplet används för en virtuell Windows-dator. En virtuell Linux-dator visar som standard en slutpunkt för SSH.)

   <!-- ![Endpoints](./media/virtual-machines-common-classic-setup-endpoints/endpointswindows.png) -->
   ![Slutpunkter](./media/virtual-machines-common-classic-setup-endpoints/endpointsblade.png)


4. I kommandofältet ovan endpoint-poster, Välj **Lägg till**. Den **Lägg till slutpunkt** visas.

5. För **namn**, ange ett namn för slutpunkten.

6. För **protokollet**, väljer du antingen **TCP** eller **UDP**.

7. För **offentlig port**, ange portnumret för inkommande trafik från internet. 

8. För **privat port**, ange det portnummer som den virtuella datorn lyssnar. De offentliga och privata portnummer kan vara olika. Se till att brandväggen på den virtuella datorn har konfigurerats för att tillåta trafik för protokoll och privat port.

9. Välj **OK**.

Den nya slutpunkten visas på den **slutpunkter** sidan.

![Skapa en slutpunkt lyckades](./media/virtual-machines-common-classic-setup-endpoints/endpointcreated.png)

## <a name="manage-the-acl-on-an-endpoint"></a>Hantera ACL på en slutpunkt
Om du vill definiera en uppsättning datorer som kan skicka trafik kan ACL på en slutpunkt begränsa trafik baserat på källans IP-adress. Följ dessa steg om du vill lägga till, ändra eller ta bort en ACL på en slutpunkt.

> [!NOTE]
> Om slutpunkten är en del av en belastningsutjämnad uppsättning kan tillämpas alla ändringar du gör i åtkomstkontrollistan för en slutpunkt för alla slutpunkter i uppsättningen.
>
>

Om den virtuella datorn är i ett Azure-nätverk, kan du använda nätverkssäkerhetsgrupper i stället för ACL: er. Mer information finns i [om nätverkssäkerhetsgrupper](../articles/virtual-network/security-overview.md).

1. Logga in på Azure Portal.

2. Välj **virtuella datorer**, och välj sedan namnet på den virtuella dator som du vill konfigurera.

3. Välj **slutpunkter**. Välj lämplig slutpunkt från listan över slutpunkter. ACL-listan är längst ned på sidan.

   ![Ange information om ACL](./media/virtual-machines-common-classic-setup-endpoints/aclpreentry.png)

4. Använda rader i listan för att lägga till, ta bort, eller redigera regler för en Åtkomstkontrollista och ändra deras inbördes ordning. Den **FJÄRRUNDERNÄTET** värdet är ett IP-adressintervall för inkommande trafik från internet som använder Azure load balancer för att tillåta eller neka trafik baserat på dess IP-källadressen. Glöm inte att ange IP-adressintervall i (classless Inter-Domain routing CIDR)-format, även kallat prefix adressformat. Till exempel `10.1.0.0/8`.

 ![Ny ACL-post](./media/virtual-machines-common-classic-setup-endpoints/newaclentry.png)


Du kan använda regler som tillåter endast trafik från vissa datorer som motsvarar dina datorer på internet eller att neka trafik från specifika, kända adressintervallen.

Reglerna utvärderas i ordning från och med den första regeln och slutar med den senaste regeln. Därför bör du beställdes regler från minst restriktiva till mest restriktiva. Mer information finns i [vad är en Network Access Control List](../articles/virtual-network/virtual-networks-acl.md).
