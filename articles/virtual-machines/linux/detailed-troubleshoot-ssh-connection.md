---
title: Detaljerad felsökning av SSH för en virtuell dator i Azure | Microsoft Docs
description: Mer detaljerad SSH felsökningssteg för problem som ansluter till en virtuell Azure-dator
keywords: SSH anslutningen nekades, ssh fel, azure ssh, SSH-anslutning misslyckades
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: b8e8be5f-e8a6-489d-9922-9df8de32e839
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/13/2017
ms.author: iainfou
ms.openlocfilehash: 88f3ca3202359f9f45f5b9a5054ab95b40558520
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34365499"
---
# <a name="detailed-ssh-troubleshooting-steps-for-issues-connecting-to-a-linux-vm-in-azure"></a>Detaljerad SSH felsökningssteg för problem som ansluter till en Linux-VM i Azure
Det finns många möjliga orsaker till att SSH-klienten inte kanske kan nå SSH-tjänsten på den virtuella datorn. Om du har följt via mer [allmänna SSH felsökningssteg](troubleshoot-ssh-connection.md), du behöver ytterligare felsöka problemet. Den här artikeln hjälper dig att detaljerade felsökningssteg för att fastställa om SSH-anslutningen inte fungerar och hur man löser problemet.

## <a name="take-preliminary-steps"></a>Vidta åtgärder för preliminär
I följande diagram visas de komponenter som ingår.

![Diagram som visar komponenter för SSH-tjänsten](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot1.png)

Följande steg hjälper dig identifiera orsaken till felet och lösningar eller lösningar.

1. Kontrollera status för den virtuella datorn i portalen.
   I den [Azure-portalen](https://portal.azure.com)väljer **virtuella datorer** > *namn på virtuell*.

   Panelen status för den virtuella datorn ska visa **kör**. Rulla Visa senaste aktiviteten för bearbetning, lagring och nätverksresurser.

2. Välj **inställningar** att undersöka slutpunkter, IP-adresser, nätverkssäkerhetsgrupper och andra inställningar.

   Den virtuella datorn ska ha en slutpunkt som definierats för SSH-trafik som du kan visa i **slutpunkter** eller  **[nätverkssäkerhetsgruppen](../../virtual-network/security-overview.md)**. Slutpunkter i virtuella datorer som har skapats med hjälp av hanteraren för filserverresurser lagras i en nätverkssäkerhetsgrupp. Kontrollera att reglerna som har tillämpats för nätverkssäkerhetsgruppen och refereras till i undernätet.

Om du vill kontrollera nätverksanslutningen konfigurerade slutpunkter och se om du kan ansluta till den virtuella datorn med ett annat protokoll, till exempel http- eller en annan tjänst.

Försök SSH-anslutningen igen efter de här stegen.

## <a name="find-the-source-of-the-issue"></a>Hitta källan till problemet
SSH-klienten på datorn kanske inte kan ansluta till SSH-tjänsten på den virtuella Azure-datorn på grund av problem eller felaktiga konfigurationer inom följande områden:

* [SSH-klientdatorn](#source-1-ssh-client-computer)
* [Gränsenheten för organisationen](#source-2-organization-edge-device)
* [Molnet tjänstslutpunkten och åtkomstkontrollista (ACL)](#source-3-cloud-service-endpoint-and-acl)
* [Nätverkssäkerhetsgrupper](#source-4-network-security-groups)
* [Linux-baserade Azure VM](#source-5-linux-based-azure-virtual-machine)

## <a name="source-1-ssh-client-computer"></a>Datakällan 1: SSH klientdatorn
Kontrollera att det kan göra att SSH-anslutningar till en annan lokal Linux-baserade datorn för att ta bort datorn som källa för felet.

![Diagram som visar SSH komponenter för klientdatorer](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot2.png)

Om anslutningen misslyckas, Sök efter följande problem på datorn:

* En inställning för lokal brandvägg som blockerar inkommande eller utgående SSH-trafik (TCP 22)
* Lokalt installerat klientprogramvaran för proxy som hindrar SSH-anslutningar
* Lokalt installerad programvara som hindrar SSH-anslutningar för nätverksövervakning
* Andra typer av säkerhetsprogram som övervaka trafik eller tillåta/neka vissa typer av trafik

Om något av dessa villkor gäller tillfälligt inaktivera programmen och en SSH-anslutning till en lokal dator att ta reda på orsaken till anslutningen blockeras på datorn. Sedan arbeta med administratören att åtgärda Programvaruinställningar för att tillåta SSH-anslutningar.

Om du använder certifikatautentisering kan du kontrollera att du har följande behörigheter till .ssh-mappen i din hemkatalog:

* Chmod 700 ~/.ssh
* Chmod 644 ~/.ssh/\*pub
* Chmod 600 ~/.ssh/id_rsa (eller andra filer som har dina privata nycklar lagras i dem)
* Chmod 644 ~/.ssh/known_hosts (innehåller värdar som du har anslutit till via SSH)

## <a name="source-2-organization-edge-device"></a>Källan 2: Gränsenheten organisation
Kontrollera att en dator som är direkt ansluten till Internet kan göra SSH-anslutningar till den virtuella Azure-datorn för att ta bort gränsenheten för din organisation som källa för felet. Om du ansluter till den virtuella datorn via ett plats-till-plats-VPN eller Azure ExpressRoute-anslutning, går du till [källa 4: Nätverkssäkerhetsgrupper](#nsg).

![Diagram som visar gränsenheten för organisationen](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot3.png)

Om du inte har en dator som är direkt anslutna till Internet, skapa en ny Azure VM i sin egen resursgrupp eller Molntjänsten och använda den nya virtuella datorn. Mer information finns i [skapa en virtuell dator som kör Linux i Azure](quick-create-cli.md). Ta bort den resursgrupp eller VM-och moln när du är klar med din testning.

Om du kan skapa en SSH-anslutning med en dator som är direkt ansluten till Internet, kontrollera din organisation insticksenhet för:

* En intern brandvägg som blockerar trafik SSH med Internet
* En proxyserver som förhindrar att SSH-anslutningar
* Intrångsidentifiering eller program som körs på enheter i nätverket kant som hindrar SSH-anslutningar för nätverksövervakning

Arbeta med nätverksadministratören för att korrigera inställningarna för enheter som din organisation för att tillåta trafik SSH med Internet.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>Källa 3: Molnet tjänstslutpunkten och ACL
> [!NOTE]
> Den här källan gäller bara för virtuella datorer som har skapats med hjälp av den klassiska distributionsmodellen. För virtuella datorer som har skapats med hjälp av hanteraren för filserverresurser kan gå vidare till [datakällan 4: Nätverkssäkerhetsgrupper](#nsg).

Kontrollera att en annan virtuell Azure-dator i samma virtuella nätverk kan ansluta via SSH för att ta bort molnet tjänstslutpunkten och ACL som källa för felet.

![Diagram som illustrerar molnet tjänstslutpunkten och ACL](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot4.png)

Om du inte har en annan virtuell dator i samma virtuella nätverk kan skapa du enkelt en. Mer information finns i [skapar en Linux-VM på Azure med hjälp av CLI](quick-create-cli.md). Ta bort extra VM när du är klar med din testning.

Om du kan skapa en SSH-anslutning med en virtuell dator i samma virtuella nätverk, kontrollerar du följande områden:

* **Slutpunktskonfigurationen för SSH-trafik på mål-VM.** Den privata TCP-porten för slutpunkten ska matcha den TCP-port som avlyssnas SSH-tjänsten på den virtuella datorn. (Standardporten är 22). Kontrollera SSH TCP-portnummer i Azure-portalen genom att välja **virtuella datorer** > *namn på virtuell* > **inställningar**  >   **Slutpunkter**.
* **ACL för slutpunkten för SSH-trafik på den virtuella måldatorn.** En ACL kan du ange tillåts eller nekas inkommande trafik från Internet, baserat på dess IP-adress. Felkonfigurerad ACL: er kan förhindra att inkommande SSH-trafik till slutpunkten. Kontrollera din ACL: er för att säkerställa att inkommande trafik från de offentliga IP-adresserna för proxyservern eller andra gränsservern tillåts. Mer information finns i [om nätverksåtkomst åtkomstkontrollistor (ACL)](../../virtual-network/virtual-networks-acl.md).

Ta bort den aktuella slutpunkten för att ta bort slutpunkten som orsaken till problemet, skapa en annan slutpunkt och ange SSH-namn (TCP-port 22 för offentliga och privata portnumret). Mer information finns i [konfigurera slutpunkter på en virtuell dator i Azure](../windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

<a id="nsg"></a>

## <a name="source-4-network-security-groups"></a>Datakällan 4: Nätverkssäkerhetsgrupper
Nätverkssäkerhetsgrupper kan du ha mer detaljerad kontroll över tillåtna inkommande och utgående trafik. Du kan skapa regler som sträcker sig över undernät och molntjänster i Azure-nätverk. Kontrollera dina regler för nätverkssäkerhetsgrupper för att se till att SSH-trafik till och från Internet tillåts.
Mer information finns i [om nätverkssäkerhetsgrupper](../../virtual-network/security-overview.md).

Du kan också använda IP-Kontrollera för att validera NSG-konfigurationen. Mer information finns i [Azure nätverksövervakning översikt](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview). 

## <a name="source-5-linux-based-azure-virtual-machine"></a>Källa 5: Linux-baserade virtuell Azure-dator
Senaste källan för möjliga problem är den virtuella Azure-datorn sig själv.

![Diagram som visar Linux-baserade virtuell Azure-dator](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot5.png)

Om du inte redan har gjort så, följ instruktionerna [att återställa ett lösenord för Linux-baserade virtuella datorer](reset-password.md).

Försök ansluta igen från datorn. Om det fortfarande inte följer några möjliga problem:

* SSH-tjänsten körs inte på den virtuella måldatorn.
* SSH-tjänsten lyssnar inte på TCP-port 22. Att testa, installera en telnet-klienten på den lokala datorn och köra ”telnet *cloudServiceName*. cloudapp.net 22”. Det här steget anger om den virtuella datorn tillåter inkommande och utgående kommunikation till SSH-slutpunkten.
* Den lokala brandväggen på den virtuella måldatorn har regler som hindrar inkommande eller utgående SSH-trafik.
* Intrångsidentifiering eller program som körs på Azure-dator för nätverksövervakning som hindrar SSH-anslutningar.

## <a name="additional-resources"></a>Ytterligare resurser
Mer information om hur du felsöker programåtkomst finns [Felsök åtkomst till ett program som körs på en virtuell Azure-dator](troubleshoot-app-connection.md)
