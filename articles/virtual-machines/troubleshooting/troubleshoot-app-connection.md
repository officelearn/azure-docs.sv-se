---
title: Felsöka åtkomst till virtuella dator program i Azure | Microsoft Docs
description: Använd de här detaljerade fel söknings stegen för att isolera problem med att ansluta till program som körs på virtuella datorer i Azure.
services: virtual-machines
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
keywords: Det går inte att starta programmet, programmet öppnas inte, lyssnings porten är blockerad, det går inte att starta programmet, lyssnings porten blockera
ms.assetid: b9ff7cd0-0c5d-4c3c-a6be-3ac47abf31ba
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: caf73ffbc18a603ace22acfbd0da490048da698a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "71058121"
---
# <a name="troubleshoot-application-connectivity-issues-on-virtual-machines-in-azure"></a>Felsöka problem med programanslutningar på virtuella Azure-datorer

Det finns olika orsaker till att det inte går att starta eller ansluta till ett program som körs på en virtuell Azure-dator (VM). Orsaker inkluderar att programmet inte körs eller lyssnar på de förväntade portarna, att lyssnings porten är blockerad eller att nätverks regler inte skickar trafik till programmet korrekt. I den här artikeln beskrivs en metodisk metod för att hitta och åtgärda problemet.

Om du har problem med att ansluta till din virtuella dator med RDP eller SSH, se någon av följande artiklar först:

* [Felsöka fjärr skrivbords anslutningar till en Windows-baserad virtuell Azure-dator](troubleshoot-rdp-connection.md)
* [FELSÖK SSH-anslutningar (Secure Shell) till en Linux-baserad virtuell Azure-dator](troubleshoot-ssh-connection.md).

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna på [MSDN Azure och Stack Overflow forum](https://azure.microsoft.com/support/forums/). Alternativt kan du också skriva en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och välj **få support**.

## <a name="quick-start-troubleshooting-steps"></a>Snabb starts steg för fel sökning
Om du har problem med att ansluta till ett program kan du prova följande allmänna fel söknings steg. Försök att ansluta till ditt program igen efter varje steg:

* Starta om den virtuella datorn
* Återskapa reglerna för slut punkten/brand väggen/nätverks säkerhets gruppen (NSG)
  * [Resource Manager-modell – hantera nätverks säkerhets grupper](../../virtual-network/manage-network-security-group.md)
  * [Klassisk modell – hantera Cloud Services-slutpunkter](../../cloud-services/cloud-services-enable-communication-role-instances.md)
* Anslut från en annan plats, till exempel ett annat virtuellt Azure-nätverk
* Omdistribuera den virtuella datorn
  * [Distribuera om virtuell Windows-dator](redeploy-to-new-node-windows.md)
  * [Distribuera virtuella Linux-datorer](redeploy-to-new-node-linux.md)
* Återskapa den virtuella datorn

Mer information finns i [Felsöka slut punkts anslutning (RDP/SSH/http, osv.)](https://social.msdn.microsoft.com/Forums/azure/en-US/538a8f18-7c1f-4d6e-b81c-70c00e25c93d/troubleshooting-endpoint-connectivity-rdpsshhttp-etc-failures?forum=WAVirtualMachinesforWindows).

## <a name="detailed-troubleshooting-overview"></a>Detaljerad fel söknings översikt
Det finns fyra huvud områden för att felsöka åtkomsten till ett program som körs på en virtuell Azure-dator.

![Felsök det går inte att starta programmet](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access1.png)

1. Programmet som körs på den virtuella Azure-datorn.
   * Körs själva programmet korrekt?
2. Den virtuella Azure-datorn.
   * Körs den virtuella datorn korrekt och svarar på begär Anden?
3. Azure Network-slutpunkter.
   * Moln tjänst slut punkter för virtuella datorer i den klassiska distributions modellen.
   * Nätverks säkerhets grupper och inkommande NAT-regler för virtuella datorer i distributions modellen för Resource Manager.
   * Kan trafik flöda från användare till VM/program på de förväntade portarna?
4. Din Internet Edge-enhet.
   * Är brand Väggs regler på plats att förhindra att trafik flödar korrekt?

För klient datorer som ansluter till programmet via en plats-till-plats-VPN-eller ExpressRoute-anslutning kan de huvudsakliga områdena som kan orsaka problem vara programmet och den virtuella Azure-datorn.

Följ dessa steg för att fastställa orsaken till problemet och dess korrigering.

## <a name="step-1-access-application-from-target-vm"></a>Steg 1: få åtkomst till programmet från den virtuella mål datorn
Försök att komma åt programmet med lämpligt klient program från den virtuella dator där det körs. Använd det lokala värd namnet, den lokala IP-adressen eller loopback-adressen (127.0.0.1).

![Starta programmet direkt från den virtuella datorn](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access2.png)

Om programmet till exempel är en webb server öppnar du en webbläsare på den virtuella datorn och försöker komma åt en webb sida som finns på den virtuella datorn.

Om du har åtkomst till programmet går du till [steg 2](#step2).

Om du inte kan komma åt programmet kontrollerar du följande inställningar:

* Programmet körs på den virtuella mål datorn.
* Programmet lyssnar på de förväntade TCP-och UDP-portarna.

På både Windows-och Linux-baserade virtuella datorer använder du kommandot **netstat-a** för att visa aktiva lyssnande portar. Granska utdata för de förväntade portar som programmet ska lyssna på. Starta om programmet eller konfigurera det så att det använder de förväntade portarna vid behov och försök att komma åt programmet lokalt igen.

## <a name="step-2-access-application-from-another-vm-in-the-same-virtual-network"></a><a id="step2"></a>Steg 2: få åtkomst till program från en annan virtuell dator i samma virtuella nätverk
Försök att komma åt programmet från en annan virtuell dator men i samma virtuella nätverk, med hjälp av den virtuella datorns värdnamn eller dess IP-adress som är offentlig, privat eller provider. Använd inte den offentliga IP-adressen för moln tjänsten för virtuella datorer som skapats med den klassiska distributions modellen.

![Starta program från en annan virtuell dator](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access3.png)

Om programmet till exempel är en webb server försöker du få åtkomst till en webb sida från en webbläsare på en annan virtuell dator i samma virtuella nätverk.

Om du har åtkomst till programmet går du till [steg 3](#step3).

Om du inte kan komma åt programmet kontrollerar du följande inställningar:

* Värd brand väggen på den virtuella mål datorn tillåter inkommande begäran och utgående svars trafik.
* Intrångs identifiering eller program vara för nätverks övervakning som körs på den virtuella mål datorn tillåter trafiken.
* Cloud Services slut punkter eller nätverks säkerhets grupper tillåter trafiken:
  * [Klassisk modell – hantera Cloud Services-slutpunkter](../../cloud-services/cloud-services-enable-communication-role-instances.md)
  * [Resource Manager-modell – hantera nätverks säkerhets grupper](../../virtual-network/manage-network-security-group.md)
* En separat komponent som körs på den virtuella datorn i sökvägen mellan den virtuella test datorn och den virtuella datorn, till exempel en belastningsutjämnare eller brand vägg, tillåter trafiken.

På en Windows-baserad virtuell dator använder du Windows-brandväggen med avancerad säkerhet för att avgöra om brand Väggs reglerna utesluter programmets inkommande och utgående trafik.

## <a name="step-3-access-application-from-outside-the-virtual-network"></a><a id="step3"></a>Steg 3: få åtkomst till program utanför det virtuella nätverket
Försök att komma åt programmet från en dator utanför det virtuella nätverket som den virtuella dator där programmet körs. Använd ett annat nätverk som din ursprungliga klient dator.

![Starta programmet från en dator utanför det virtuella nätverket](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access4.png)

Om programmet till exempel är en webb server försöker du komma åt webb sidan från en webbläsare som körs på en dator som inte finns i det virtuella nätverket.

Om du inte kan komma åt programmet kontrollerar du följande inställningar:

* För virtuella datorer som skapats med den klassiska distributions modellen:
  
  * Kontrol lera att slut punkts konfigurationen för den virtuella datorn tillåter inkommande trafik, särskilt protokollet (TCP eller UDP) och de offentliga och privata port numren.
  * Kontrol lera att åtkomst kontrol listor (ACL: er) för slut punkten inte förhindrar inkommande trafik från Internet.
  * Mer information finns i [så här konfigurerar du slut punkter till en virtuell dator](../windows/classic/setup-endpoints.md).
* För virtuella datorer som skapats med Resource Manager-distributions modellen:
  
  * Kontrol lera att den inkommande NAT-regelns konfiguration för den virtuella datorn tillåter inkommande trafik, särskilt protokollet (TCP eller UDP) och de offentliga och privata port numren.
  * Kontrol lera att nätverks säkerhets grupper tillåter inkommande begäran och utgående svars trafik.
  * Mer information finns i [Vad är en nätverks säkerhets grupp?](../../virtual-network/security-overview.md)

Om den virtuella datorn eller slut punkten är medlem i en belastningsutjämnad uppsättning:

* Kontrol lera att avsöknings protokollet (TCP eller UDP) och port numret är korrekt.
* Om avsöknings protokollet och porten skiljer sig från det belastningsutjämnade set-protokollet och porten:
  * Kontrol lera att programmet lyssnar på avsöknings protokollet (TCP eller UDP) och port numret (Använd **netstat – a** på den virtuella mål datorn).
  * Kontrol lera att värd brand väggen på den virtuella mål datorn tillåter inkommande begäran om avsöknings-och avgående avsöknings svar.

Om du har åtkomst till programmet kontrollerar du att din Internet Edge-enhet tillåter:

* Utgående program begär trafik från klient datorn till den virtuella Azure-datorn.
* Inkommande program svars trafik från den virtuella Azure-datorn.

## <a name="step-4-if-you-cannot-access-the-application-use-ip-verify-to-check-the-settings"></a>Steg 4 om du inte kan komma åt programmet använder du IP-verifiering för att kontrol lera inställningarna. 

Mer information finns i [Översikt över Azure Network Monitoring](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview). 

## <a name="additional-resources"></a>Ytterligare resurser
[Felsöka fjärr skrivbords anslutningar till en Windows-baserad virtuell Azure-dator](troubleshoot-rdp-connection.md)

[Felsök SSH-anslutningar (Secure Shell) till en Linux-baserad virtuell Azure-dator](troubleshoot-ssh-connection.md)


