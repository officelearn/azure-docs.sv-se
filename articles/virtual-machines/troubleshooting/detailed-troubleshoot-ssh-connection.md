---
title: Detaljerad SSH-felsökning för en virtuell Azure-dator | Microsoft Docs
description: Mer detaljerade SSH-felsöknings steg för problem med att ansluta till en virtuell Azure-dator
keywords: ssh-anslutningen nekades, SSH-fel, Azure SSH, SSH-anslutning misslyckades
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
ms.openlocfilehash: c117f9697299b94a54d9184093e65c56822b8bd2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002637"
---
# <a name="detailed-ssh-troubleshooting-steps-for-issues-connecting-to-a-linux-vm-in-azure"></a>Detaljerade SSH-felsökningssteg för problem med anslutning till en virtuell Linux-dator i Azure
Det finns många möjliga orsaker till att SSH-klienten kanske inte kan komma åt SSH-tjänsten på den virtuella datorn. Om du har följt de mer [allmänna SSH-fel söknings stegen](troubleshoot-ssh-connection.md)måste du ytterligare felsöka anslutnings problemet. Den här artikeln vägleder dig genom detaljerade fel söknings steg för att avgöra var SSH-anslutningen fungerar och hur du löser den.

## <a name="take-preliminary-steps"></a>Vidta preliminära steg
Följande diagram visar de komponenter som är inblandade.

![Diagram som visar komponenter för SSH-tjänsten](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot1.png)

Följande steg hjälper dig att isolera källan till felen och ta reda på lösningar eller lösningar.

1. Kontrol lera statusen för den virtuella datorn i portalen.
   I [Azure Portal](https://portal.azure.com)väljer du VM-namn för **virtuella datorer**  >  *VM name*.

   Status fönstret för den virtuella datorn **bör visas.** Rulla ned för att visa senaste aktivitet för beräknings-, lagrings-och nätverks resurser.

2. Välj **Inställningar** för att undersöka slut punkter, IP-adresser, nätverks säkerhets grupper och andra inställningar.

   Den virtuella datorn ska ha en slut punkt som definierats för SSH-trafik som du kan visa i **slut punkter** eller **[nätverks säkerhets grupp](../../virtual-network/network-security-groups-overview.md)**. Slut punkter i virtuella datorer som har skapats med hjälp av Resource Manager lagras i en nätverks säkerhets grupp. Kontrol lera att reglerna har tillämpats på nätverks säkerhets gruppen och att de refereras till i under nätet.

Kontrol lera de konfigurerade slut punkterna och se om du kan ansluta till den virtuella datorn via ett annat protokoll, till exempel HTTP eller en annan tjänst, för att kontrol lera nätverks anslutningen.

Efter de här stegen försöker du använda SSH-anslutningen igen.

## <a name="find-the-source-of-the-issue"></a>Hitta orsaken till problemet
SSH-klienten på datorn kanske inte kan ansluta till SSH-tjänsten på den virtuella Azure-datorn på grund av problem eller fel konfiguration inom följande områden:

* [SSH-klientdator](#source-1-ssh-client-computer)
* [Organisationens gräns enhet](#source-2-organization-edge-device)
* [Moln tjänst slut punkt och åtkomst kontrol lista (ACL)](#source-3-cloud-service-endpoint-and-acl)
* [Nätverkssäkerhetsgrupper](#source-4-network-security-groups)
* [Linux-baserad virtuell Azure-dator](#source-5-linux-based-azure-virtual-machine)

## <a name="source-1-ssh-client-computer"></a>Källa 1: SSH-klientdator
För att undvika att datorn är källan till problemet kontrollerar du att den kan göra SSH-anslutningar till en annan lokal, Linux-baserad dator.

![Diagram som visar SSH-klientens dator komponenter](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot2.png)

Om anslutningen Miss lyckas söker du efter följande problem på datorn:

* En lokal brand Väggs inställning som blockerar inkommande eller utgående SSH-trafik (TCP 22)
* Lokalt installerat program för klient-proxy som hindrar SSH-anslutningar
* Lokalt installerat program vara för nätverks övervakning som hindrar SSH-anslutningar
* Andra typer av säkerhets program som antingen övervakar trafik eller tillåter/nekar vissa typer av trafik

Om något av dessa villkor gäller inaktiverar du tillfälligt program varan och försöker en SSH-anslutning till en lokal dator för att ta reda på orsaken till att anslutningen blockeras på datorn. Arbeta sedan med nätverks administratören för att rätta till program varu inställningarna så att de tillåter SSH-anslutningar.

Om du använder certifikatautentisering kontrollerar du att du har behörighet till mappen. ssh i din hem katalog:

* Chmod 700 ~/.ssh
* Chmod 644 ~/.ssh/ \* . pub
* Chmod 600 ~/.ssh/id_rsa (eller andra filer som har dina privata nycklar lagrade i dem)
* Chmod 644 ~/.ssh/known_hosts (innehåller värdar som du har anslutit till via SSH)

## <a name="source-2-organization-edge-device"></a>Källa 2: organisations gräns enhet
Om du vill ta bort din enhets gräns som källa för ett haveri, kontrollerar du att en dator som är direktansluten till Internet kan göra SSH-anslutningar till den virtuella Azure-datorn. Om du ansluter till den virtuella datorn via en plats-till-plats-VPN eller en Azure ExpressRoute-anslutning går du vidare till [källa 4: nätverks säkerhets grupper](#nsg).

![Diagram som visar en organisations gräns enhet](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot3.png)

Om du inte har en dator som är direkt ansluten till Internet skapar du en ny virtuell Azure-dator i en egen resurs grupp eller moln tjänst och använder den nya virtuella datorn. Mer information finns i [skapa en virtuell dator som kör Linux i Azure](../linux/quick-create-cli.md). Ta bort resurs gruppen eller den virtuella datorn och moln tjänsten när du är klar med testet.

Om du kan skapa en SSH-anslutning med en dator som är direkt ansluten till Internet, kontrollerar du din organisations gräns enhet för:

* En intern brand vägg som blockerar SSH-trafik med Internet
* En proxyserver som hindrar SSH-anslutningar
* Intrångs identifiering eller program vara för nätverks övervakning som körs på enheter i Edge-nätverket som hindrar SSH-anslutningar

Arbeta med nätverks administratören för att korrigera inställningarna för dina organisations gräns enheter för att tillåta SSH-trafik med Internet.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>Källa 3: moln tjänstens slut punkt och ACL

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

> [!NOTE]
> Den här källan gäller endast för virtuella datorer som har skapats med den klassiska distributions modellen. För virtuella datorer som har skapats med hjälp av Resource Manager hoppar du till [källa 4: nätverks säkerhets grupper](#nsg).

För att eliminera moln tjänstens slut punkt och ACL som källa för felen kontrollerar du att en annan virtuell Azure-dator i samma virtuella nätverk kan ansluta med SSH.

![Diagram som visar moln tjänstens slut punkt och ACL](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot4.png)

Om du inte har en annan virtuell dator i samma virtuella nätverk kan du enkelt skapa en. Mer information finns i [skapa en virtuell Linux-dator på Azure med hjälp av CLI](../linux/quick-create-cli.md). Ta bort den extra virtuella datorn när du är färdig med testningen.

Om du kan skapa en SSH-anslutning med en virtuell dator i samma virtuella nätverk, kontrollerar du följande områden:

* **Slut punkts konfigurationen för SSH-trafik på den virtuella mål datorn.** Den privata TCP-porten för slut punkten ska matcha den TCP-port som SSH-tjänsten på den virtuella datorn lyssnar på. (Standard porten är 22). Verifiera SSH TCP-portnumret i Azure Portal genom att välja **virtuella datorer**  >  *VM Name*  >  **Settings**-  >  **slutpunkter**.
* **ACL för SSH-trafikens slut punkt på den virtuella mål datorn.** Med en ACL kan du ange tillåten eller nekad inkommande trafik från Internet, baserat på dess käll-IP-adress. Felkonfigurerade ACL: er kan förhindra inkommande SSH-trafik till slut punkten. Kontrol lera dina ACL: er för att säkerställa att inkommande trafik från den offentliga IP-adressen för proxyservern eller en annan gräns server tillåts. Mer information finns i [om åtkomst kontrol listor för nätverk (ACL: er)](/previous-versions/azure/virtual-network/virtual-networks-acl).

För att eliminera slut punkten som en källa till problemet tar du bort den aktuella slut punkten, skapar en annan slut punkt och anger SSH-namnet (TCP-port 22 för det offentliga och privata port numret). Mer information finns i [Konfigurera slut punkter på en virtuell dator i Azure](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints?toc=/azure/virtual-machines/windows/classic/toc.json).

<a id="nsg"></a>

## <a name="source-4-network-security-groups"></a>Källa 4: nätverks säkerhets grupper
Med nätverks säkerhets grupper kan du ha mer detaljerad kontroll över tillåten inkommande och utgående trafik. Du kan skapa regler som omfattar undernät och moln tjänster i ett virtuellt Azure-nätverk. Kontrol lera reglerna för nätverks säkerhets gruppen för att säkerställa att SSH-trafik till och från Internet tillåts.
Mer information finns i [om nätverks säkerhets grupper](../../virtual-network/network-security-groups-overview.md).

Du kan också använda IP-verifiera för att verifiera NSG-konfigurationen. Mer information finns i [Översikt över Azure Network Monitoring](../../network-watcher/network-watcher-monitoring-overview.md). 

## <a name="source-5-linux-based-azure-virtual-machine"></a>Källa 5: Linux-baserad virtuell Azure-dator
Den senaste källan till möjliga problem är själva Azure-datorn.

![Diagram som belyser Linux-baserad virtuell Azure-dator](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot5.png)

Om du inte redan har gjort det följer du anvisningarna [för att återställa ett lösen ord Linux-baserade virtuella datorer](./reset-password.md).

Försök att ansluta från datorn igen. Om det fortfarande inte går att utföra följande är några av de möjliga problemen:

* SSH-tjänsten körs inte på den virtuella mål datorn.
* SSH-tjänsten lyssnar inte på TCP-port 22. Testa genom att installera en Telnet-klient på den lokala datorn och köra "Telnet *cloudServiceName*. cloudapp.net 22". Det här steget avgör om den virtuella datorn tillåter inkommande och utgående kommunikation till SSH-slutpunkten.
* Den lokala brand väggen på den virtuella mål datorn har regler som förhindrar inkommande eller utgående SSH-trafik.
* Intrångs identifiering eller program vara för nätverks övervakning som körs på den virtuella Azure-datorn förhindrar SSH-anslutningar.

## <a name="additional-resources"></a>Ytterligare resurser
Mer information om hur du felsöker program åtkomst finns i [Felsöka åtkomst till ett program som körs på en virtuell Azure-dator](./troubleshoot-app-connection.md)