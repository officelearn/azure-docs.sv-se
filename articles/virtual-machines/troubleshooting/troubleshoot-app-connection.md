---
title: Felsök programåtkomst för virtuell dator i Azure | Microsoft Docs
description: Du kan använda de här detaljerade felsökningsanvisningar för att isolera problem i anslutning till program som körs på virtuella datorer i Azure.
services: virtual-machines
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
keywords: Det går inte att starta programmet, programmet kan inte öppnas, lyssningsport block, det går inte att starta programmet, lyssningsport blockerad
ms.assetid: b9ff7cd0-0c5d-4c3c-a6be-3ac47abf31ba
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: ae5a720ce6ddd6b773229d0968bc283aa5cfa5ba
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47414789"
---
# <a name="troubleshoot-application-connectivity-issues-on-virtual-machines-in-azure"></a>Felsöka problem med programanslutningar på virtuella datorer i Azure

Det finns olika anledningar när du inte får börja eller ansluta till ett program som körs på en Azure-dator (VM). Orsaker är det program som inte körs eller lyssnar på förväntade portar lyssningsport blockeras eller nätverk regler inte korrekt skicka trafik till programmet. Den här artikeln beskrivs en metodisk metod för att hitta och åtgärda problemet.

Om du har problem med att ansluta till den virtuella datorn med RDP eller SSH finns i följande artiklar först:

* [Felsök fjärrskrivbordsanslutningar till en Windows-baserade Azure-dator](troubleshoot-rdp-connection.md)
* [Felsöka Secure Shell (SSH)-anslutningar till en Linux-baserade Azure VM](troubleshoot-ssh-connection.md).

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på [Azure MSDN och Stack Overflow-forum](https://azure.microsoft.com/support/forums/). Du kan alternativt kan du även skicka en incident i Azure-supporten. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och välj **hämta stöder**.

## <a name="quick-start-troubleshooting-steps"></a>Snabbstart felsökningssteg
Om du har problem med att ansluta till ett program kan du prova följande allmänna felsökningssteg. Försök att ansluta till programmet igen efter varje steg:

* Starta om den virtuella datorn
* Återskapa slutpunkten / brandväggsregler / network regler för nätverkssäkerhetsgrupper (NSG)
  * [Resource Manager-modellen – hantera Nätverkssäkerhetsgrupper](../../virtual-network/manage-network-security-group.md)
  * [Klassiska modellen – hantera molntjänster slutpunkter](../../cloud-services/cloud-services-enable-communication-role-instances.md)
* Ansluta från en annan plats, till exempel ett annat Azure virtuellt nätverk
* Distribuera om den virtuella datorn
  * [Distribuera om Windows VM](redeploy-to-new-node-windows.md)
  * [Distribuera om virtuell Linux-dator](redeploy-to-new-node-linux.md)
* Återskapa den virtuella datorn

Mer information finns i [felsöka slutpunktsanslutning (RDP/SSH/HTTP, etc. fel)](https://social.msdn.microsoft.com/Forums/azure/en-US/538a8f18-7c1f-4d6e-b81c-70c00e25c93d/troubleshooting-endpoint-connectivity-rdpsshhttp-etc-failures?forum=WAVirtualMachinesforWindows).

## <a name="detailed-troubleshooting-overview"></a>Detaljerad felsökning översikt
Det finns fyra huvudsakliga områden att felsöka åtkomst till ett program som körs på virtuella Azure-datorer.

![Felsökning: Det går inte att starta programmet](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access1.png)

1. Det program som körs på Azure-dator.
   * Är själva programmet körs på rätt sätt?
2. Azure-dator.
   * Är Virtuellt datorn körs på rätt sätt och svara på begäranden?
3. Slutpunkter för Azure-nätverk.
   * Molnbaserad tjänstslutpunkter för virtuella datorer i den klassiska distributionsmodellen.
   * Nätverkssäkerhetsgrupper och inkommande NAT-regler för virtuella datorer i Resource Manager-distributionsmodellen.
   * Kan trafik flödar från användare till VM/programmet på förväntade portar?
4. Din Internet edge-enhet.
   * Brandväggsregler på plats förhindrar att trafik flödar korrekt?

För klientdatorer som har åtkomst till programmet via en plats-till-plats VPN eller ExpressRoute-anslutning, är de viktigaste delarna som kan orsaka problem programmet och virtuella Azure-datorer.

Följ dessa steg för att fastställa orsaken till problemet och dess korrigering.

## <a name="step-1-access-application-from-target-vm"></a>Steg 1: Komma åt programmet från virtuell dator
Försök att komma åt programmet med rätt klientprogram från den virtuella datorn där den körs. Använd det lokala värdnamnet, den lokala IP-adressen eller loopbackadressen (127.0.0.1).

![Starta programmet direkt från den virtuella datorn](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access2.png)

Till exempel om programmet är en webbserver, öppna en webbläsare på den virtuella datorn och försöker öppna en webbsida på den virtuella datorn.

Om du har åtkomst till programmet, går du till [steg 2](#step2).

Om du inte åtkomst till programmet, kontrollerar du följande inställningar:

* Programmet körs på den virtuella måldatorn.
* Programmet lyssnar på de förväntade TCP och UDP-portarna.

På både Windows och Linux-baserade virtuella datorer kan använda den **netstat - a** kommando för att visa aktiva lyssnande portar. Granska utdata för de förväntade portar som ditt program bör lyssna. Starta om programmet eller konfigurera den för att använda de förväntade portarna vid behov och försöker komma åt programmet lokalt igen.

## <a id="step2"></a>Steg 2: Komma åt programmet från en annan virtuell dator i samma virtuella nätverk
Försök att komma åt programmet från en annan virtuell dator, men i samma virtuella nätverk med hjälp av den Virtuella datorns värdnamn eller dess tilldelade Azure offentliga, privata eller providern IP-adress. För virtuella datorer som skapats med den klassiska distributionsmodellen, Använd inte den offentliga IP-adressen för Molntjänsten.

![Starta programmet från en annan virtuell dator](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access3.png)

Till exempel om programmet är en webbserver, försök öppna en webbsida från en webbläsare på en annan virtuell dator i samma virtuella nätverk.

Om du har åtkomst till programmet, går du till [steg3](#step3).

Om du inte åtkomst till programmet, kontrollerar du följande inställningar:

* Värd-brandväggen på den Virtuella måldatorn tillåter inkommande begäran och för utgående svarstrafik.
* Intrångsidentifiering eller programvara som körs på den Virtuella måldatorn för nätverksövervakning tillåter trafik.
* Cloud Services-slutpunkter eller Nätverkssäkerhetsgrupper tillåter trafiken:
  * [Klassiska modellen – hantera molntjänster slutpunkter](../../cloud-services/cloud-services-enable-communication-role-instances.md)
  * [Resource Manager-modellen – hantera Nätverkssäkerhetsgrupper](../../virtual-network/manage-network-security-group.md)
* En separat komponent som körs i den virtuella datorn i sökvägen mellan testet virtuella datorn och den virtuella datorn, till exempel en belastningsutjämnare eller brandväggen tillåter trafik.

På en Windows-baserad virtuell dator, använder du Windows-brandväggen med avancerad säkerhet för att avgöra om brandväggsreglerna exkludera ditt programs inkommande och utgående trafik.

## <a id="step3"></a>Steg 3: Komma åt programmet från utanför det virtuella nätverket
Försök att komma åt programmet från en dator utanför det virtuella nätverket som den virtuella datorn där programmet körs. Använd ett annat nätverk som din ursprungliga klientdator.

![Starta programmet från en dator utanför det virtuella nätverket](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access4.png)

Till exempel om programmet är en webbserver, som försök komma åt sidan från en webbläsare som körs på en dator som inte är i det virtuella nätverket.

Om du inte åtkomst till programmet, kontrollerar du följande inställningar:

* För virtuella datorer som skapas med hjälp av den klassiska distributionsmodellen:
  
  * Kontrollera att konfigurationen av slutpunkten för den virtuella datorn tillåter inkommande trafik, särskilt protokoll (TCP eller UDP) och de offentliga och privata portnummer.
  * Kontrollera att åtkomstkontrollistor (ACL) på slutpunkten inte förhindrar inkommande trafik från Internet.
  * Mer information finns i [så konfigurera slutpunkter till en virtuell dator](../windows/classic/setup-endpoints.md).
* För virtuella datorer som skapas med hjälp av Resource Manager-distributionsmodellen:
  
  * Kontrollera att den inkommande NAT-regelkonfigurationen för den virtuella datorn tillåter inkommande trafik, särskilt protokoll (TCP eller UDP) och de offentliga och privata portnummer.
  * Kontrollera att Nätverkssäkerhetsgrupper tillåter inkommande begäran och för utgående svarstrafik.
  * Mer information finns i [vad är en nätverkssäkerhetsgrupp?](../../virtual-network/security-overview.md)

Om den virtuella datorn eller en slutpunkt är medlem i en belastningsutjämnad uppsättning:

* Kontrollera att avsökningen protokoll (TCP eller UDP) och portnumret är korrekta.
* Om avsökningen protokollet och den porten skiljer sig från den belastningsutjämnade uppsättningen protokoll och port:
  * Kontrollera att programmet lyssnar på avsökningen protokoll (TCP eller UDP) och portnummer (Använd **netstat – a** på den Virtuella måldatorn).
  * Kontrollera att värden-brandväggen på den Virtuella måldatorn tillåter inkommande avsökningen begäran och utgående avsökningen svarstrafik.

Om du har åtkomst till programmet, kontrollerar du att din Internet-edge-enhet tillåter:

* Den utgående begäran programtrafik från klientdatorn till Azure-dator.
* Det inkommande svaret programtrafik från Azure-dator.

## <a name="step-4-if-you-cannot-access-the-application-use-ip-verify-to-check-the-settings"></a>Steg 4 om du inte åtkomst till programmet använda IP-Kontrollera för att kontrollera inställningarna. 

Mer information finns i [Azure-nätverk Övervakningsöversikt](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview). 

## <a name="additional-resources"></a>Ytterligare resurser
[Felsök fjärrskrivbordsanslutningar till en Windows-baserade Azure-dator](troubleshoot-rdp-connection.md)

[Felsöka Secure Shell (SSH)-anslutningar till en Linux-baserade Azure VM](troubleshoot-ssh-connection.md)


