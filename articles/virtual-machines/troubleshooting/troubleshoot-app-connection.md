---
title: Felsöka vm-programåtkomst i Azure | Microsoft-dokument
description: Använd de här detaljerade felsökningsstegen för att isolera problem vid anslutning till program som körs på virtuella datorer i Azure.
services: virtual-machines
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
keywords: kan inte starta programmet, programmet öppnas inte, lyssna port blockerad, oförmögen att starta programmet, lyssna port blockerad
ms.assetid: b9ff7cd0-0c5d-4c3c-a6be-3ac47abf31ba
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: caf73ffbc18a603ace22acfbd0da490048da698a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71058121"
---
# <a name="troubleshoot-application-connectivity-issues-on-virtual-machines-in-azure"></a>Felsöka problem med programanslutningar på virtuella Azure-datorer

Det finns olika orsaker till att du inte kan starta eller ansluta till ett program som körs på en virtuell Azure-dator (VM). Orsaker är att programmet inte körs eller lyssnar på de förväntade portarna, lyssningsporten blockerad eller nätverksregler som inte skickar trafik till programmet korrekt. I den här artikeln beskrivs en metodisk metod för att hitta och åtgärda problemet.

Om du har problem med att ansluta till den virtuella datorn med RDP eller SSH läser du någon av följande artiklar först:

* [Felsöka anslutning till fjärrskrivbord till en Windows-baserad virtuell azure-dator](troubleshoot-rdp-connection.md)
* [Felsöka SSH-anslutningar (Secure Shell) till en Virtuell Linux-baserad Virtuell Azure-dator](troubleshoot-ssh-connection.md).

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna på [MSDN Azure och Stack Overflow-forumen](https://azure.microsoft.com/support/forums/). Alternativt kan du också arkivera en Azure-supportincident. Gå till [Azure-supportwebbplatsen](https://azure.microsoft.com/support/options/) och välj **Hämta support**.

## <a name="quick-start-troubleshooting-steps"></a>Felsökningssteg för snabbstart
Om du har problem med att ansluta till ett program provar du följande allmänna felsökningssteg. Efter varje steg kan du försöka ansluta till programmet igen:

* Starta om den virtuella datorn
* Återskapa reglerna för slutpunkt/brandvägg/nätverkssäkerhet (NSG)
  * [Resource Manager-modell - Hantera nätverkssäkerhetsgrupper](../../virtual-network/manage-network-security-group.md)
  * [Klassisk modell - Hantera molntjänster slutpunkter](../../cloud-services/cloud-services-enable-communication-role-instances.md)
* Ansluta från en annan plats, till exempel ett annat virtuellt Azure-nätverk
* Omdistribuera den virtuella datorn
  * [Distribuera om Windows VM](redeploy-to-new-node-windows.md)
  * [Distribuera om virtuella Linux-datorer](redeploy-to-new-node-linux.md)
* Återskapa den virtuella datorn

Mer information finns i [Felsöka slutpunktsanslutning (RDP/SSH/HTTP, etc. fel)](https://social.msdn.microsoft.com/Forums/azure/en-US/538a8f18-7c1f-4d6e-b81c-70c00e25c93d/troubleshooting-endpoint-connectivity-rdpsshhttp-etc-failures?forum=WAVirtualMachinesforWindows).

## <a name="detailed-troubleshooting-overview"></a>Detaljerad felsökningsöversikt
Det finns fyra huvudområden för att felsöka åtkomsten för ett program som körs på en virtuell Azure-dator.

![felsökningen kan inte starta programmet](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access1.png)

1. Programmet som körs på den virtuella Azure-datorn.
   * Körs själva programmet på rätt sätt?
2. Den virtuella Azure-datorn.
   * Körs själva den virtuella datorn korrekt och svarar på begäranden?
3. Slutpunkter för Azure-nätverk.
   * Slutpunkter för molntjänst för virtuella datorer i den klassiska distributionsmodellen.
   * Nätverkssäkerhetsgrupper och inkommande NAT-regler för virtuella datorer i Resurshanterarens distributionsmodell.
   * Kan trafiken flöda från användare till vm/programmet på de förväntade portarna?
4. Din Internet-kantenhet.
   * Finns brandväggsregler som hindrar trafiken från att flyta korrekt?

För klientdatorer som har åtkomst till programmet via en plats-till-plats VPN- eller ExpressRoute-anslutning är de viktigaste områdena som kan orsaka problem programmet och den virtuella Azure-datorn.

Följ dessa steg för att fastställa orsaken till problemet och dess korrigering.

## <a name="step-1-access-application-from-target-vm"></a>Steg 1: Komma åt program från måldatorn
Försök att komma åt programmet med rätt klientprogram från den virtuella datorn som det körs på. Använd det lokala värdnamnet, den lokala IP-adressen eller loopback-adressen (127.0.0.1).

![starta programmet direkt från den virtuella datorn](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access2.png)

Om programmet till exempel är en webbserver öppnar du en webbläsare på den virtuella datorn och försöker komma åt en webbsida som finns på den virtuella datorn.

Om du kan komma åt programmet går du till [steg 2](#step2).

Om du inte kan komma åt programmet kontrollerar du följande inställningar:

* Programmet körs på den virtuella måldatorn.
* Programmet lyssnar på de förväntade TCP- och UDP-portarna.

På både Windows och Linux-baserade virtuella datorer använder du **kommandot netstat -a** för att visa de aktiva lyssningsportarna. Undersök utdata för de förväntade portar som programmet ska lyssna på. Starta om programmet eller konfigurera det så att det används de förväntade portarna efter behov och försök komma åt programmet lokalt igen.

## <a name="step-2-access-application-from-another-vm-in-the-same-virtual-network"></a><a id="step2"></a>Steg 2: Access-program från en annan virtuell dator i samma virtuella nätverk
Försök att komma åt programmet från en annan virtuell dator men i samma virtuella nätverk, med hjälp av den virtuella datorns värdnamn eller dess Azure-tilldelade offentliga, privata eller provider IP-adress. För virtuella datorer som skapats med den klassiska distributionsmodellen ska du inte använda molntjänstens offentliga IP-adress.

![starta programmet från en annan virtuell dator](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access3.png)

Om programmet till exempel är en webbserver kan du försöka komma åt en webbsida från en webbläsare på en annan virtuell dator i samma virtuella nätverk.

Om du kan komma åt programmet går du till [steg 3](#step3).

Om du inte kan komma åt programmet kontrollerar du följande inställningar:

* Värdbrandväggen på måldatorn tillåter inkommande begäran och utgående svarstrafik.
* Intrångsidentifiering eller nätverksövervakning programvara som körs på målet VM tillåter trafiken.
* Cloud Services-slutpunkter eller nätverkssäkerhetsgrupper tillåter trafiken:
  * [Klassisk modell - Hantera molntjänster slutpunkter](../../cloud-services/cloud-services-enable-communication-role-instances.md)
  * [Resource Manager-modell - Hantera nätverkssäkerhetsgrupper](../../virtual-network/manage-network-security-group.md)
* En separat komponent som körs i den virtuella datorn i sökvägen mellan testdatorn och den virtuella datorn, till exempel en belastningsutjämnare eller brandvägg, tillåter trafiken.

På en Windows-baserad virtuell dator använder du Windows-brandväggen med avancerad säkerhet för att avgöra om brandväggsreglerna utesluter programmets inkommande och utgående trafik.

## <a name="step-3-access-application-from-outside-the-virtual-network"></a><a id="step3"></a>Steg 3: Komma åt program utanför det virtuella nätverket
Försök att komma åt programmet från en dator utanför det virtuella nätverket som den virtuella datorn som programmet körs på. Använd ett annat nätverk som originalklientdator.

![starta programmet från en dator utanför det virtuella nätverket](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access4.png)

Om programmet till exempel är en webbserver kan du försöka komma åt webbsidan från en webbläsare som körs på en dator som inte finns i det virtuella nätverket.

Om du inte kan komma åt programmet kontrollerar du följande inställningar:

* För virtuella datorer som skapats med den klassiska distributionsmodellen:
  
  * Kontrollera att slutpunktskonfigurationen för den virtuella datorn tillåter inkommande trafik, särskilt protokollet (TCP eller UDP) och de offentliga och privata portnumren.
  * Kontrollera att åtkomstkontrollistor (ACL: er) på slutpunkten inte hindrar inkommande trafik från Internet.
  * Mer information finns i [Så här konfigurerar du slutpunkter på en virtuell dator](../windows/classic/setup-endpoints.md).
* För virtuella datorer som skapats med hjälp av Resurshanterarens distributionsmodell:
  
  * Kontrollera att den inkommande NAT-regelkonfigurationen för den virtuella datorn tillåter inkommande trafik, särskilt protokollet (TCP eller UDP) och de offentliga och privata portnumren.
  * Kontrollera att nätverkssäkerhetsgrupper tillåter inkommande begäran och utgående svarstrafik.
  * Mer information finns i [Vad är en nätverkssäkerhetsgrupp?](../../virtual-network/security-overview.md)

Om den virtuella datorn eller slutpunkten är medlem i en belastningsbalanserad uppsättning:

* Kontrollera att avsökningsprotokollet (TCP eller UDP) och portnumret är korrekta.
* Om avsökningsprotokollet och avsökningsporten skiljer sig från det belastningsbalanserade setprotokollet och porten:
  * Kontrollera att programmet lyssnar på avsökningsprotokollet (TCP eller UDP) och portnummer (använd **netstat – a** på måldatorn).
  * Kontrollera att värdbrandväggen på måldatorn tillåter inkommande avsökningsbegäran och utgående avsökningssvarstrafik.

Om du kan komma åt programmet kontrollerar du att internetkantenheten tillåter:

* Det utgående programmet begär trafik från klientdatorn till den virtuella Azure-datorn.
* Den inkommande programsvarstrafiken från den virtuella Azure-datorn.

## <a name="step-4-if-you-cannot-access-the-application-use-ip-verify-to-check-the-settings"></a>Steg 4 Om du inte kan komma åt programmet använder du IP Verify för att kontrollera inställningarna. 

Mer information finns i [Översikt över Övervakning av Azure-nätverk](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview). 

## <a name="additional-resources"></a>Ytterligare resurser
[Felsöka anslutning till fjärrskrivbord till en Windows-baserad virtuell azure-dator](troubleshoot-rdp-connection.md)

[Felsöka SSH-anslutningar (Secure Shell) till en virtuell Virtuell Azure-dator med Linux](troubleshoot-ssh-connection.md)


