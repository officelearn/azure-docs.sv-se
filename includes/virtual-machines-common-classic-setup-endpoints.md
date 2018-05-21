---
title: ta med fil
description: ta med fil
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 05/17/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: cfe675ca269a69c7c2bfa67638acd0afbcd1c8ea
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
Varje slutpunkt har en *offentlig port* och en *privat port*:

* Den offentliga porten används av Azure belastningsutjämnare för att lyssna efter inkommande trafik till den virtuella datorn från Internet.
* Den privata porten används av den virtuella datorn för att lyssna efter inkommande trafik, vanligtvis av ett program eller tjänst som körs på den virtuella datorn.

Standardvärden för IP-protokollet och TCP eller UDP-portar för välkända nätverket protokoll anges när du skapar slutpunkter med Azure-portalen. För anpassade slutpunkter måste du ange rätt IP-protokoll (TCP eller UDP) och offentliga och privata portar. Om du vill distribuera slumpmässigt inkommande trafik över flera virtuella datorer, behöver du skapa en belastningsutjämnad uppsättning som består av flera slutpunkter.

Du kan använda en åtkomstkontrollista (ACL) för att definiera regler som tillåter eller nekar inkommande trafik till den offentliga porten för slutpunkten baserat på dess IP-adress när du har skapat en slutpunkt. Du bör dock använda nätverkssäkerhetsgrupper i stället om den virtuella datorn är i ett virtuellt Azure-nätverk. Mer information finns i [om nätverkssäkerhetsgrupper](../articles/virtual-network/security-overview.md).

> [!NOTE]
> Brandväggskonfigurationen för virtuella Azure-datorer görs automatiskt för portar som är associerade med fjärranslutningar slutpunkter som Azure ställer in automatiskt. Ingen konfiguration görs automatiskt till i brandväggen för den virtuella datorn för portar som angetts för alla slutpunkter. När du skapar en slutpunkt för den virtuella datorn, behöver du se till att brandväggen på den virtuella datorn också tillåter trafik för protokoll och privat port motsvarar slutpunktskonfigurationen. Om du vill konfigurera brandväggen finns i dokumentationen eller Onlinehjälp för operativsystemet som körs på den virtuella datorn.
>
>

## <a name="create-an-endpoint"></a>Skapa en slutpunkt
1. Om du inte redan gjort det loggar du in på [Azure Portal](https://portal.azure.com).
2. Klicka på **virtuella datorer**, och klicka sedan på namnet på den virtuella dator som du vill konfigurera.
3. Klicka på **slutpunkter** i den **inställningar** grupp. Den **slutpunkter** sidan listar alla aktuella slutpunkter för den virtuella datorn. (Det här exemplet är en virtuell Windows-dator. Linux VM visas som standard en slutpunkt för SSH.)

   <!-- ![Endpoints](./media/virtual-machines-common-classic-setup-endpoints/endpointswindows.png) -->
   ![Slutpunkter](./media/virtual-machines-common-classic-setup-endpoints/endpointsblade.png)

4. I kommandofältet ovan endpoint-poster, klickar du på **Lägg till**.
5. På den **lägga till slutpunkten** anger du ett namn för slutpunkten i **namn**.
6. I **protokollet**, Välj antingen **TCP** eller **UDP**.
7. I **offentlig Port**, Skriv portnumret för den inkommande trafiken från Internet. I **privat Port**, ange det portnummer som den virtuella datorn lyssnar. Dessa portnummer kan vara olika. Se till att brandväggen på den virtuella datorn har konfigurerats för att tillåta trafik för protokoll (i steg 6) och privat port.
10. Klicka på **OK**.

Ny slutpunkt visas på den **slutpunkter** sidan.

![Skapa en slutpunkt lyckades](./media/virtual-machines-common-classic-setup-endpoints/endpointcreated.png)

## <a name="manage-the-acl-on-an-endpoint"></a>Hantera ACL på en slutpunkt
Om du vill definiera en uppsättning datorer som kan skicka trafik kan ACL på en slutpunkt begränsa trafik baserat på källans IP-adress. Följ dessa steg om du vill lägga till, ändra eller ta bort en ACL för en slutpunkt.

> [!NOTE]
> Om slutpunkten är en del av en belastningsutjämnad uppsättning, tillämpas alla ändringar du gör i åtkomstkontrollistan för en slutpunkt på alla slutpunkter i uppsättningen.
>
>

Om den virtuella datorn är i ett virtuellt Azure-nätverk, rekommenderar vi nätverkssäkerhetsgrupper i stället för ACL: er. Mer information finns i [om nätverkssäkerhetsgrupper](../articles/virtual-network/security-overview.md).

1. Om du inte redan gjort det, logga in på Azure-portalen.
2. Klicka på **virtuella datorer**, och klicka sedan på namnet på den virtuella dator som du vill konfigurera.
3. Klicka på **Slutpunkter**. Välj lämplig slutpunkten i listan. ACL-listan är längst ned på sidan.

   ![Ange ACL-information](./media/virtual-machines-common-classic-setup-endpoints/aclpreentry.png)

4. Använda rader i listan för att lägga till, ta bort, eller redigera regler för en ACL och ändra deras inbördes ordning. Den **fjärrundernät** värde är ett IP-adressintervall för inkommande trafik från Internet som Azure belastningsutjämnare använder för att tillåta eller neka trafik baserat på dess IP-adress. Se till att ange IP-adressintervall i CIDR-format, även kallat prefix adressformat. Ett exempel är `10.1.0.0/8`.

 ![Ny ACL-post](./media/virtual-machines-common-classic-setup-endpoints/newaclentry.png)


Du kan använda regler som tillåter endast trafik från vissa datorer som motsvarar dina datorer på Internet eller neka trafik från specifika, kända adressintervall.

Reglerna utvärderas i ordning från och med den första regeln och slutar med den sista regeln. Detta innebär att regler ska sorteras från minst restriktiva till mest restriktiva. Mer information och exempel finns i [vad är en lista över åtkomstkontroll för](../articles/virtual-network/virtual-networks-acl.md).
