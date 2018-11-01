---
title: Detaljerad felsökning av SSH för en Azure-dator | Microsoft Docs
description: Mer detaljerad SSH felsökningssteg för problem med anslutning till en Azure virtuell dator
keywords: SSH anslutningen nekades, ssh fel, azure ssh, SSH-anslutningen misslyckades
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: b8e8be5f-e8a6-489d-9922-9df8de32e839
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: aedf06c5a5e225f0cafb81b17923d6c742da69eb
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2018
ms.locfileid: "50418270"
---
# <a name="detailed-ssh-troubleshooting-steps-for-issues-connecting-to-a-linux-vm-in-azure"></a>Detaljerad SSH felsökningssteg för problem med anslutning till en Linux-VM i Azure
Det finns många möjliga orsaker till att SSH-klienten inte kanske kan nå SSH-tjänsten på den virtuella datorn. Om du har följt via mer [allmän SSH felsökningssteg](troubleshoot-ssh-connection.md), du behöver ytterligare felsökning av problem med anslutning. Den här artikeln vägleder dig genom detaljerade felsökningsanvisningar för att fastställa där SSH-anslutningen inte fungerar och hur det ska lösas.

## <a name="take-preliminary-steps"></a>Gör preliminär
I följande diagram visas komponenterna som ingår.

![Diagram som visar komponenterna i SSH-tjänsten](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot1.png)

Följande steg hjälper dig att isolera orsaken till felet och ta reda på lösningar eller åtgärder.

1. Kontrollera status för den virtuella datorn i portalen.
   I den [Azure-portalen](https://portal.azure.com)väljer **virtuella datorer** > *namn på virtuell dator*.

   Panelen status för den virtuella datorn ska visa **kör**. Rulla ned till Visa senaste aktiviteter för beräkning, lagring och nätverksresurser.

2. Välj **inställningar** att undersöka slutpunkter, IP-adresser, nätverkssäkerhetsgrupper och andra inställningar.

   Den virtuella datorn ska ha en slutpunkt som definierats för SSH-trafik som du kan visa i **slutpunkter** eller  **[nätverkssäkerhetsgrupp](../../virtual-network/security-overview.md)**. Slutpunkter i virtuella datorer som har skapats med hjälp av Resource Manager lagras i en nätverkssäkerhetsgrupp. Kontrollera att reglerna har tillämpats på den nya nätverkssäkerhetsgruppen och refereras till i undernätet.

Kontrollera de konfigurerade slutpunkterna för att kontrollera nätverksanslutningen och se om du kan ansluta till den virtuella datorn med ett annat protokoll som HTTP och en annan tjänst.

Testa SSH-anslutningen igen efter de här stegen.

## <a name="find-the-source-of-the-issue"></a>Hitta orsaken till problemet
SSH-klienten på datorn kan inte ansluta till SSH-tjänsten på Azure-VM på grund av problem eller felkonfigurationer inom följande områden:

* [SSH-klientdatorn](#source-1-ssh-client-computer)
* [Organisation edge-enhet](#source-2-organization-edge-device)
* [Tjänsteslutpunkt i molnet och få åtkomst till åtkomstkontrollistor (ACL)](#source-3-cloud-service-endpoint-and-acl)
* [Nätverkssäkerhetsgrupper](#source-4-network-security-groups)
* [Linux-baserade virtuella datorer i Azure](#source-5-linux-based-azure-virtual-machine)

## <a name="source-1-ssh-client-computer"></a>Källa 1: SSH klientdatorn
Kontrollera att det kan göra SSH-anslutningar till en annan lokal plats, Linux-baserade datorn för att ta bort datorn är orsaken till felet.

![Diagram som visar datorkomponenter för SSH-klient](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot2.png)

Om anslutningen misslyckas kan du söka efter följande problem på datorn:

* En inställning för lokal brandvägg som blockerar inkommande eller utgående SSH-trafik (TCP 22)
* Lokalt installerat klientprogrammet för proxy som hindrar SSH-anslutningar
* Lokalt installerad programvara som hindrar SSH-anslutningar för nätverksövervakning
* Andra typer av säkerhetsprogram som antingen övervaka trafik eller tillåta/neka vissa typer av trafik

Om något av dessa villkor gäller tillfälligt inaktivera programvaran och försök en SSH-anslutning till en lokal dator för att ta reda på orsaken anslutningen blockeras på datorn. Arbeta sedan med nätverksadministratören för att korrigera inställningarna för programvara för att tillåta SSH-anslutningar.

Om du använder autentisering med datorcertifikat, kontrollerar du att du har dessa behörigheter till .ssh-mappen i din arbetskatalog:

* Chmod 700 ~/.ssh
* Chmod 644 ~/.ssh/\*.pub
* Chmod 600 ~/.ssh/id_rsa (eller andra filer som har dina privata nycklar som lagras i dem)
* Chmod 644 ~/.ssh/known_hosts (innehåller värdar som du har anslutit till via SSH)

## <a name="source-2-organization-edge-device"></a>Källan 2: Organisation edge-enhet
Kontrollera att en dator som är direkt ansluten till Internet kan göra SSH-anslutningar till den virtuella Azure-datorn för att ta bort din organisation edge-enhet är orsaken till felet. Om du får åtkomst till den virtuella datorn via plats-till-plats VPN eller Azure ExpressRoute-anslutning, gå vidare till [källa 4: Nätverkssäkerhetsgrupper](#nsg).

![Diagram som visar organisation edge-enhet](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot3.png)

Om du inte har en dator som är direkt ansluten till Internet, skapa en ny virtuell Azure-dator i en egen resursgrupp eller molntjänst och använda den nya virtuella datorn. Mer information finns i [skapa en virtuell dator som kör Linux i Azure](../linux/quick-create-cli.md). Ta bort resursgrupp eller virtuella datorer och molntjänster service när du är klar med testet.

Om du kan skapa en SSH-anslutning med en dator som är direkt ansluten till Internet, kontrollera din organisation edge-enhet för:

* En intern brandvägg som blockerar SSH-trafik med Internet
* En proxyserver som hindrar SSH-anslutningar
* Intrångsidentifiering eller programvara som körs på enheter i nätverket edge som hindrar SSH-anslutningar för nätverksövervakning

Arbeta med nätverksadministratören för att korrigera inställningarna för din organisation edge-enheter för att tillåta SSH-trafik med Internet.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>Källa 3: Molnet tjänstslutpunkt och ACL
> [!NOTE]
> Den här källan gäller bara för virtuella datorer som har skapats med hjälp av den klassiska distributionsmodellen. För virtuella datorer som har skapats med hjälp av Resource Manager kan gå vidare till [source 4: Nätverkssäkerhetsgrupper](#nsg).

Kontrollera att en annan virtuell Azure-dator i samma virtuella nätverk kan ansluta med SSH för att ta bort molnet tjänstslutpunkt och ACL är orsaken till felet.

![Diagram som illustrerar tjänstslutpunkt för molnet och ACL](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot4.png)

Om du inte har en annan virtuell dator i samma virtuella nätverk kan skapa du enkelt en. Mer information finns i [skapar en Linux-VM på Azure med hjälp av CLI](../linux/quick-create-cli.md). Ta bort de extra virtuella datorn när du är klar med testet.

Om du kan skapa en SSH-anslutning med en virtuell dator i samma virtuella nätverk, kontrollerar du följande områden:

* **Konfigurationen av slutpunkten för SSH-trafik på den Virtuella måldatorn.** Den privata TCP-porten för slutpunkten ska matcha TCP-port som SSH-tjänsten på den virtuella datorn lyssnar. (Standardporten är 22). Kontrollera SSH TCP-portnumret i Azure portal genom att välja **virtuella datorer** > *namn på virtuell dator* > **inställningar**  >   **Slutpunkter**.
* **ACL för slutpunkten för SSH-trafik på den virtuella måldatorn.** En ACL kan du ange tillåts eller nekas inkommande trafik från Internet, baserat på dess IP-källadressen. Felkonfigurerad ACL: er kan förhindra att inkommande SSH-trafik till slutpunkten. Kontrollera din ACL: er för att säkerställa att den inkommande trafiken från offentliga IP-adresser för proxyservern eller andra edge-servern tillåts. Mer information finns i [om nätverksåtkomst åtkomstkontrollistor (ACL)](../../virtual-network/virtual-networks-acl.md).

Ta bort den aktuella slutpunkten, skapar en annan slutpunkt ange SSH-namn (TCP-port 22 för offentliga och privata portnumret) för att ta bort slutpunkten som en orsaken till problemet. Mer information finns i [ställer du in slutpunkter på en virtuell dator i Azure](../windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

<a id="nsg"></a>

## <a name="source-4-network-security-groups"></a>Källa 4: Nätverkssäkerhetsgrupper
Nätverkssäkerhetsgrupper kan du ha mer detaljerad kontroll över tillåten inkommande och utgående trafik. Du kan skapa regler som sträcker sig över undernät och molntjänster i Azure-nätverk. Kontrollera dina regler för nätverkssäkerhetsgrupper för att säkerställa att SSH-trafik till och från Internet tillåts.
Mer information finns i [om nätverkssäkerhetsgrupper](../../virtual-network/security-overview.md).

Du kan också använda IP-Kontrollera för att validera NSG-konfiguration. Mer information finns i [Azure-nätverk Övervakningsöversikt](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview). 

## <a name="source-5-linux-based-azure-virtual-machine"></a>Källa 5: Linux-baserade Azure-dator
Senaste källan för möjliga problem är Azure-datorer själva.

![Diagram som visar Linux-baserade Azure-dator](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot5.png)

Om du inte redan gjort det, följer du anvisningarna [att återställa ett lösenord för Linux-baserade virtuella datorer](../linux/reset-password.md).

Försök ansluta igen från datorn. Om det fortfarande inte är följande några av de möjliga problemen:

* SSH-tjänsten körs inte på den virtuella måldatorn.
* SSH-tjänsten lyssnar inte på TCP-port 22. Testa, installera en telnet-klient på den lokala datorn och köra ”telnet *cloudServiceName*. cloudapp.net 22”. Det här steget anger om den virtuella datorn tillåter inkommande och utgående kommunikation till SSH-slutpunkten.
* Den lokala brandväggen på den virtuella måldatorn har regler som förhindrar inkommande eller utgående SSH-trafik.
* Intrångsidentifiering eller programvara som körs på virtuella Azure-datorer för nätverksövervakning som hindrar SSH-anslutningar.

## <a name="additional-resources"></a>Ytterligare resurser
Läs mer om hur du felsöker programåtkomst [Felsök åtkomst till ett program som körs på virtuella Azure-datorer](../linux/troubleshoot-app-connection.md)
