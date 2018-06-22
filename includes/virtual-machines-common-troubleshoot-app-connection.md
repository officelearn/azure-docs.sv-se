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
ms.openlocfilehash: ab668a905b435287a4eaf96ff04b2fa5b54deb1d
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2018
ms.locfileid: "36313521"
---
Det finns flera orsaker när du inte får inledas eller ansluta till ett program som körs på en Azure-dator (VM). Orsaker kan vara det program som inte körs eller lyssnar på förväntade portar lyssningsport blockeras eller nätverk regler inte korrekt skicka trafik till programmet. Den här artikeln beskriver en metodisk metod för att hitta och åtgärda problemet.

Om du har problem med anslutningen till den virtuella datorn med hjälp av RDP eller SSH finns i följande artiklar först:

* [Felsöka anslutningar till fjärrskrivbord till en Windows-baserad Azure-dator](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md)
* [Felsökning av SSH (Secure Shell) anslutningar till en Linux-baserade Azure virtuella](../articles/virtual-machines/linux/troubleshoot-ssh-connection.md).

> [!NOTE]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../articles/resource-manager-deployment-model.md). Den här artikeln täcker bägge modellerna, men Microsoft rekommenderar de flesta nya distributioner att använda Resource Manager-modellen.

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på [MSDN Azure och Stack Overflow-forum](https://azure.microsoft.com/support/forums/). Alternativt kan du även filen en incident i Azure-supporten. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och välj **hämta stöder**.

## <a name="quick-start-troubleshooting-steps"></a>Snabbkurs felsökningssteg
Om du har problem med att ansluta till ett program kan du prova följande allmän felsökning. Försök ansluta till ditt program igen efter varje steg:

* Starta om den virtuella datorn
* Återskapa slutpunkten / regler i brandväggen / nätverk regler för nätverkssäkerhetsgrupper (NSG)
  * [Resource Manager-modellen - hantera Nätverkssäkerhetsgrupper](../articles/virtual-network/manage-network-security-group.md)
  * [Klassiska modellen - hantera molntjänster slutpunkter](../articles/cloud-services/cloud-services-enable-communication-role-instances.md)
* Ansluta från annan plats, till exempel ett annat Azure virtuellt nätverk
* Distribuera den virtuella datorn
  * [Distribuera Windows VM](../articles/virtual-machines/windows/redeploy-to-new-node.md)
  * [Distribuera virtuell Linux-dator](../articles/virtual-machines/linux/redeploy-to-new-node.md)
* Skapa den virtuella datorn

Mer information finns i [felsöka slutpunktsanslutning (RDP/SSH/HTTP, etc. fel)](https://social.msdn.microsoft.com/Forums/azure/en-US/538a8f18-7c1f-4d6e-b81c-70c00e25c93d/troubleshooting-endpoint-connectivity-rdpsshhttp-etc-failures?forum=WAVirtualMachinesforWindows).

## <a name="detailed-troubleshooting-overview"></a>Detaljerad felsökning översikt
Det finns fyra huvudsakliga områden för att felsöka åtkomst till ett program som körs på en virtuell Azure-dator.

![Felsöka kan inte starta programmet](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access1.png)

1. Det program som körs på den virtuella Azure-datorn.
   * Körs programmet korrekt?
2. Den virtuella Azure-datorn.
   * Är Virtuellt datorn körs korrekt och svara på begäranden?
3. Azure-nätverksslutpunkter.
   * Moln slutpunkter för virtuella datorer i den klassiska distributionsmodellen.
   * Nätverkssäkerhetsgrupper och inkommande NAT-regler för virtuella datorer i Resource Manager-distributionsmodellen.
   * Kan trafiken flödar från användare till VM/application på förväntade portar?
4. Din Internet-edge-enhet.
   * Brandväggsregler på plats förhindrar trafiken flödar korrekt?

För klientdatorer som har åtkomst till programmet via en plats-till-plats VPN eller ExpressRoute-anslutning är huvudområden som kan orsaka problem med programmet och den virtuella Azure-datorn.

Följ dessa steg för att fastställa orsaken till problemet och dess korrigering.

## <a name="step-1-access-application-from-target-vm"></a>Steg 1: Öppna programmet från virtuell dator
Försök att komma åt programmet med rätt klientprogram från den virtuella datorn som den körs. Använda namnet på lokala värddatorn, lokal IP-adress eller loopback-adressen (127.0.0.1).

![Starta programmet direkt från den virtuella datorn](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access2.png)

Om programmet är en webbserver, öppna en webbläsare på den virtuella datorn och försök att komma åt en webbsida som finns på den virtuella datorn.

Om du har åtkomst till programmet, gå till [steg 2](#step2).

Om du inte kan komma åt programmet, kontrollerar du följande inställningar:

* Programmet körs på den virtuella måldatorn.
* Programmet lyssnar på förväntade TCP och UDP-portar.

I både Windows och Linux-baserade virtuella datorer använder den **netstat – en** kommando för att visa aktiva lyssnarportar. Granska utdata för förväntade portar som ditt program bör lyssna. Starta om programmet eller konfigurera den för att använda förväntade portar som behövs och försök att komma åt programmet lokalt igen.

## <a id="step2"></a>Steg 2: Komma åt program från en annan virtuell dator i samma virtuella nätverk
Försök att få åtkomst till programmet från en annan virtuell dator, men i samma virtuella nätverk med hjälp av den virtuella datorns värdnamn eller dess tilldelade Azure public, private eller providern IP-adress. För virtuella datorer som skapats med hjälp av den klassiska distributionsmodellen, Använd inte den offentliga IP-adressen för Molntjänsten.

![Starta programmet från en annan virtuell dator](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access3.png)

Till exempel om programmet är en webbserver, försök komma åt en webbsida från en webbläsare på en annan virtuell dator i samma virtuella nätverk.

Om du har åtkomst till programmet, gå till [steg3](#step3).

Om du inte kan komma åt programmet, kontrollerar du följande inställningar:

* Värdbrandväggen på måldatorn VM att tillåta att den inkommande begäran och för utgående svarstrafik.
* Intrångsidentifiering eller program som körs på måldatorn VM för nätverksövervakning tillåter trafiken.
* Cloud Services-slutpunkter eller Nätverkssäkerhetsgrupper så att trafiken:
  * [Klassiska modellen - hantera molntjänster slutpunkter](../articles/cloud-services/cloud-services-enable-communication-role-instances.md)
  * [Resource Manager-modellen - hantera Nätverkssäkerhetsgrupper](../articles/virtual-network/manage-network-security-group.md)
* En separat komponent som körs i den virtuella datorn i sökvägen mellan testet virtuella datorn och den virtuella datorn, till exempel en belastningsutjämnare eller brandväggen tillåter trafik.

Använd Windows-brandväggen med avancerad säkerhet för att avgöra om brandväggsreglerna undanta programmets inkommande och utgående trafik på en Windows-baserad virtuell dator.

## <a id="step3"></a>Steg 3: Komma åt program från utanför det virtuella nätverket
Försök att få åtkomst till programmet från en dator utanför det virtuella nätverket som den virtuella datorn där programmet körs. Använd ett annat nätverk som den ursprungliga klientdatorn.

![Starta programmet från en dator utanför det virtuella nätverket](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access4.png)

Till exempel om programmet är en webbserver, försök komma åt sidan från en webbläsare som körs på en dator som inte är i det virtuella nätverket.

Om du inte kan komma åt programmet, kontrollerar du följande inställningar:

* För virtuella datorer skapas med den klassiska distributionsmodellen:
  
  * Verifiera att slutpunktskonfigurationen för den virtuella datorn tillåter inkommande trafik, särskilt protokoll (TCP och UDP) och de offentliga och privata portnummer.
  * Kontrollera att åtkomstkontrollistor (ACL) på slutpunkten inte förhindrar inkommande trafik från Internet.
  * Mer information finns i [så ange slutpunkter till en virtuell dator](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
* För virtuella datorer skapas med Resource Manager-distributionsmodellen:
  
  * Kontrollera att inkommande NAT-regel-konfigurationen för den virtuella datorn tillåter inkommande trafik, särskilt protokoll (TCP och UDP) och de offentliga och privata portnummer.
  * Kontrollera att Nätverkssäkerhetsgrupper tillåter inkommande begäran och för utgående svarstrafik.
  * Mer information finns i [vad är en nätverkssäkerhetsgrupp?](../articles/virtual-network/security-overview.md)

Om den virtuella datorn eller slutpunkt är medlem i en belastningsutjämnad uppsättning:

* Kontrollera att sonden protocol (TCP eller UDP) och portnumret är korrekta.
* Om avsökningen protokoll och port skiljer sig från den belastningsutjämnade uppsättningen protokoll och port:
  * Kontrollera att programmet lyssnar på avsökningen protocol (TCP eller UDP) och portnumret (Använd **netstat – a** på målet VM).
  * Kontrollera att värdbrandväggen på måldatorn VM tillåter inkommande avsökningen förfrågan och utgående avsökningen svarstrafik.

Om du har åtkomst till programmet, kontrollerar du att din Internet-insticksenhet tillåter:

* Utgående programmet begäran trafiken från klientdatorn till den virtuella Azure-datorn.
* Inkommande programmet svarstrafik från den virtuella Azure-datorn.

## <a name="step-4-if-you-cannot-access-the-application-use-ip-verify-to-check-the-settings"></a>Steg 4 om du inte kan komma åt programmet, använda IP-verifiera Kontrollera inställningarna. 

Mer information finns i [Azure nätverksövervakning översikt](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview). 

## <a name="additional-resources"></a>Ytterligare resurser
[Felsöka anslutningar till fjärrskrivbord till en Windows-baserad Azure-dator](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md)

[Felsökning av SSH (Secure Shell) anslutningar till en Linux-baserade Azure virtuella](../articles/virtual-machines/linux/troubleshoot-ssh-connection.md)

