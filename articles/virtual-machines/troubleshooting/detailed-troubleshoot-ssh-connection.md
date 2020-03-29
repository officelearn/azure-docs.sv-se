---
title: Detaljerad SSH-felsökning för en virtuell Azure-dator | Microsoft-dokument
description: Mer detaljerade SSH felsökningssteg för problem med anslutning till en virtuell Azure-dator
keywords: ssh-anslutning vägrade, ssh-fel,azure ssh,SSH-anslutning misslyckades
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: b8e8be5f-e8a6-489d-9922-9df8de32e839
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: ee6d437915f6c87ce9ef5f9c711d90793a96048c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920135"
---
# <a name="detailed-ssh-troubleshooting-steps-for-issues-connecting-to-a-linux-vm-in-azure"></a>Detaljerade SSH-felsökningssteg för problem med anslutning till en virtuell Linux-dator i Azure
Det finns många möjliga orsaker till att SSH-klienten kanske inte kan nå SSH-tjänsten på den virtuella datorn. Om du har följt de mer [allmänna felsökningsstegen](troubleshoot-ssh-connection.md)för SSH måste du felsöka anslutningsproblemet ytterligare. Den här artikeln vägleder dig genom detaljerade felsökningssteg för att avgöra var SSH-anslutningen misslyckas och hur du löser den.

## <a name="take-preliminary-steps"></a>Vidta preliminära åtgärder
I följande diagram visas de komponenter som ingår.

![Diagram som visar komponenter i SSH-tjänsten](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot1.png)

Följande steg hjälper dig att isolera källan till felet och ta reda på lösningar eller lösningar.

1. Kontrollera status för den virtuella datorn i portalen.
   I [Azure-portalen](https://portal.azure.com)väljer du > *VM-namn* **för virtuella datorer**.

   Statusfönstret för den virtuella datorn ska visa **Löpning**. Bläddra nedåt för att visa den senaste aktiviteten för beräknings-, lagrings- och nätverksresurser.

2. Välj **Inställningar** för att granska slutpunkter, IP-adresser, nätverkssäkerhetsgrupper och andra inställningar.

   Den virtuella datorn ska ha en slutpunkt definierad för SSH-trafik som du kan visa i **slutpunkter** eller **[nätverkssäkerhetsgrupp](../../virtual-network/security-overview.md)**. Slutpunkter i virtuella datorer som har skapats med hjälp av Resource Manager lagras i en nätverkssäkerhetsgrupp. Kontrollera att reglerna har tillämpats på nätverkssäkerhetsgruppen och refereras i undernätet.

Kontrollera de konfigurerade slutpunkterna för att verifiera nätverksanslutningen och se om du kan ansluta till den virtuella datorn via ett annat protokoll, till exempel HTTP eller en annan tjänst.

Prova SSH-anslutningen igen efter dessa steg.

## <a name="find-the-source-of-the-issue"></a>Hitta källan till problemet
SSH-klienten på datorn kanske inte ansluter till SSH-tjänsten på den virtuella Azure-datorn på grund av problem eller felkonfigurationer inom följande områden:

* [SSH-klientdator](#source-1-ssh-client-computer)
* [Enhet för organisationskant](#source-2-organization-edge-device)
* [Slutpunkt för molntjänst och åtkomstkontrollista (ACL)](#source-3-cloud-service-endpoint-and-acl)
* [Nätverkssäkerhetsgrupper](#source-4-network-security-groups)
* [Linux-baserad virtuell Azure](#source-5-linux-based-azure-virtual-machine)

## <a name="source-1-ssh-client-computer"></a>Källa 1: SSH-klientdator
Om du vill eliminera datorn som källa till felet kontrollerar du att den kan göra SSH-anslutningar till en annan lokal, Linux-baserad dator.

![Diagram som belyser SSH-klientdatorkomponenter](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot2.png)

Om anslutningen misslyckas kontrollerar du följande problem på datorn:

* En lokal brandväggsinställning som blockerar inkommande eller utgående SSH-trafik (TCP 22)
* Lokalt installerad klientproxyprogramvara som förhindrar SSH-anslutningar
* Lokalt installerad programvara för nätverksövervakning som förhindrar SSH-anslutningar
* Andra typer av säkerhetsprogram som antingen övervakar trafiken eller tillåter/inte tillåter specifika typer av trafik

Om något av dessa villkor gäller inaktiverar du programvaran tillfälligt och provar en SSH-anslutning till en lokal dator för att ta reda på orsaken till att anslutningen blockeras på datorn. Arbeta sedan med nätverksadministratören för att korrigera programinställningarna så att SSH-anslutningar tillåts.

Om du använder certifikatautentisering kontrollerar du att du har dessa behörigheter till .ssh-mappen i din hemkatalog:

* Chmod 700 ~/.ssh
* Chmod 644 ~/.ssh/\*.pub
* Chmod 600 ~/.ssh/id_rsa (eller andra filer som har dina privata nycklar lagrade i dem)
* Chmod 644 ~/.ssh/known_hosts (innehåller värdar som du har anslutit till via SSH)

## <a name="source-2-organization-edge-device"></a>Källa 2: Enhet för organisationskant
Om du vill eliminera organisationens kantenhet som källa till felet kontrollerar du att en dator som är direkt ansluten till Internet kan göra SSH-anslutningar till din virtuella Azure-dator. Om du ansluter till den virtuella datorn via en plats-till-plats-VPN- eller en Azure ExpressRoute-anslutning går du till [Källa 4: Nätverkssäkerhetsgrupper](#nsg).

![Diagram som framhäver enheten för organisationskant](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot3.png)

Om du inte har en dator som är direkt ansluten till Internet skapar du en ny Virtuell Azure-dator i den egna resursgruppen eller molntjänsten och använder den nya virtuella datorn. Mer information finns i [Skapa en virtuell dator som kör Linux i Azure](../linux/quick-create-cli.md). Ta bort resursgruppen eller vm- och molntjänsten när du är klar med testningen.

Om du kan skapa en SSH-anslutning med en dator som är direkt ansluten till Internet kontrollerar du om organisationens kantenhet:

* En intern brandvägg som blockerar SSH-trafik med Internet
* En proxyserver som förhindrar SSH-anslutningar
* Intrångsidentifiering eller nätverksövervakningsprogram som körs på enheter i edge-nätverket som förhindrar SSH-anslutningar

Samarbeta med nätverksadministratören för att korrigera inställningarna för organisationens kantenheter så att SSH-trafik med Internet tillåts.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>Källa 3: Slutpunkt för molntjänster och ACL

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

> [!NOTE]
> Den här källan gäller endast virtuella datorer som har skapats med hjälp av den klassiska distributionsmodellen. För virtuella datorer som har skapats med Hjälp av Resource Manager går du till [källa 4: Nätverkssäkerhetsgrupper](#nsg).

Om du vill eliminera slutpunkten för molntjänsten och ACL som källa till felet kontrollerar du att en annan Azure-virtuell dator i samma virtuella nätverk kan ansluta med SSH.

![Diagram som belyser slutpunkt för molntjänster och ACL](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot4.png)

Om du inte har en annan virtuell dator i samma virtuella nätverk kan du enkelt skapa en. Mer information finns i [Skapa en virtuell Linux-dator på Azure med CLI](../linux/quick-create-cli.md). Ta bort den extra virtuella datorn när du är klar med testningen.

Om du kan skapa en SSH-anslutning med en virtuell dator i samma virtuella nätverk kontrollerar du följande områden:

* **Slutpunktskonfigurationen för SSH-trafik på måldatorn.** Slutpunktens privata TCP-port ska matcha TCP-porten som SSH-tjänsten på den virtuella datorn lyssnar på. (Standardporten är 22). Verifiera SSH TCP-portnumret i Azure-portalen genom att välja **virtuella datorer** > *VM-namninställningar* > **Settings** > **Slutpunkter**.
* **Åtkomstkontrollistan för SSH-trafikslutpunkten på den virtuella måldatorn.** Med en åtkomstkontrollista kan du ange tillåten eller nekad inkommande trafik från Internet, baserat på dess käll-IP-adress. Felkonfigurerade åtkomstkontrollanter kan förhindra inkommande SSH-trafik till slutpunkten. Kontrollera dina åtkomstkontrollistor för att säkerställa att inkommande trafik från de offentliga IP-adresserna för din proxy eller annan kantserver är tillåten. Mer information finns i [Om kontrolllistor för nätverksåtkomst (ACL: er).](../../virtual-network/virtual-networks-acl.md)

Om du vill ta bort slutpunkten som en källa till problemet tar du bort den aktuella slutpunkten, skapar en annan slutpunkt och anger SSH-namnet (TCP-port 22 för det offentliga och privata portnumret). Mer information finns i [Konfigurera slutpunkter på en virtuell dator i Azure](../windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

<a id="nsg"></a>

## <a name="source-4-network-security-groups"></a>Källa 4: Säkerhetsgrupper för nätverk
Nätverkssäkerhetsgrupper gör att du kan ha mer detaljerad kontroll över tillåten inkommande och utgående trafik. Du kan skapa regler som sträcker sig över undernät och molntjänster i ett virtuellt Azure-nätverk. Kontrollera reglerna för nätverkssäkerhetsgruppen för att säkerställa att SSH-trafik till och från Internet är tillåten.
Mer information finns i [Om nätverkssäkerhetsgrupper](../../virtual-network/security-overview.md).

Du kan också använda IP Verify för att validera NSG-konfigurationen. Mer information finns i [Översikt över Övervakning av Azure-nätverk](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview). 

## <a name="source-5-linux-based-azure-virtual-machine"></a>Källa 5: Virtuell Virtuell Azure-dator med Linux
Den sista källan till möjliga problem är själva Azure-datorn.

![Diagram som belyser linuxbaserad virtuell Azure-dator](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot5.png)

Om du inte redan har gjort det följer du instruktionerna [för att återställa ett Linux-baserat virtuellt lösenord.](../linux/reset-password.md)

Försök ansluta från datorn igen. Om det fortfarande misslyckas, följande är några av de möjliga problem:

* SSH-tjänsten körs inte på den virtuella måldatorn.
* SSH-tjänsten lyssnar inte på TCP-port 22. Testa genom att installera en telnet-klient på den lokala datorn och köra "telnet *cloudServiceName*.cloudapp.net 22". Det här steget avgör om den virtuella datorn tillåter inkommande och utgående kommunikation till SSH-slutpunkten.
* Den lokala brandväggen på den virtuella måldatorn har regler som förhindrar inkommande eller utgående SSH-trafik.
* Intrångsidentifiering eller nätverksövervakningsprogram som körs på den virtuella Azure-datorn förhindrar SSH-anslutningar.

## <a name="additional-resources"></a>Ytterligare resurser
Mer information om felsökning av programåtkomst finns i [Felsöka åtkomst till ett program som körs på en virtuell Azure-dator](../linux/troubleshoot-app-connection.md)
