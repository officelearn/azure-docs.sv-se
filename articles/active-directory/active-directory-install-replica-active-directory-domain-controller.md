---
title: "Installera domänkontrollanter för repliken för lokala Active Directory-domän på Azure virtual machines | Microsoft Docs"
description: "Så här installerar du replik domänkontrollanter för en lokal Active Directory-domän på virtuella Azure-datorer (VM) i Azure-nätverk."
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: 8c9ebf1b-289a-4dd6-9567-a946450005c0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/12/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 3f7624d588e958985a73c5b40e8010e18e8879cb
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="install-a-replica-active-directory-domain-controller-in-an-azure-virtual-network"></a>Installera en replikerad Active Directory-domänkontroller i Azure-nätverk
Den här artikeln beskrivs hur du installerar ytterligare domänkontrollanter (DC) som ska användas som replik domänkontrollanter för lokal Active Directory-domänen på virtuella Azure-datorer (VM) i Azure-nätverk. Du kan också [installera en Windows Server Active Directory-skog på Azure-nätverk](active-directory-new-forest-virtual-machine.md). Om hur du installerar Active Directory Domain Services (AD DS) på Azure-nätverk finns [riktlinjer för att distribuera Windows Server Active Directory på Azure Virtual Machines](https://msdn.microsoft.com/library/azure/jj156090.aspx).

## <a name="scenario-diagram"></a>Scenario-diagram
I det här scenariot externa användare som behöver åtkomst till program som körs på servrar som ingår i domänen. De virtuella datorerna som kör programmet och domänkontrollanter för repliken är installerade i Azure-nätverk. Det virtuella nätverket kan vara ansluten till det lokala nätverket av [ExpressRoute](../expressroute/expressroute-locations-providers.md), eller så kan du använda en [plats-till-plats VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md) anslutning, som visas: 

![Diagram över pf replik Active Directory-domänkontrollant ett Azure-vnet][1]

Programservern och domänkontrollanter distribueras inom separat molntjänster att distribuera beräkning bearbetning och tillgänglighetsuppsättningar för förbättrad feltolerans.
Domänkontrollanter replikera med varandra och med lokala domänkontrollanter med hjälp av Active Directory-replikering. Inga synkroniseringsverktyg behövs.

## <a name="create-an-on-premises-active-directory-site-for-the-azure-virtual-network"></a>Skapa en lokal Active Directory-plats för virtuella Azure-nätverket
Du kan skapa en plats i Active Directory som representerar den region där nätverket motsvarande till det virtuella nätverket. Den här platsen kan hjälpa dig att optimera autentisering, replikering och andra åtgärder för DC-plats. Följande steg förklarar hur du skapar en plats och mer bakgrund, se [att lägga till en ny plats](https://technet.microsoft.com/library/cc781496.aspx).

1. Öppna Active Directory-platser och tjänster: **Serverhanteraren** > **verktyg** > **Active Directory-platser och tjänster**.
2. Skapa en plats som representerar den region där du skapade ett virtuellt Azure-nätverk: Klicka på **platser** > **åtgärd** > **ny plats** > typ i namnet på den nya platsen, till exempel Azure oss Väst > Välj en platslänk > **OK**.
3. Skapa ett undernät och koppla till den nya platsen: Dubbelklicka på **platser** > högerklickar du på **undernät** > **Nytt undernät** > skriver du IP-adressintervall i virtuella nätverk (till exempel 10.1.0.0/16 i diagrammet scenariot) > väljer du den nya Azure-platsen > **OK**.

## <a name="create-an-azure-virtual-network"></a>Skapa ett Azure-nätverk
Om du vill skapa ett Azure-nätverk och konfigurera VPN för plats-till-plats, följer du stegen som ingår i artikeln [skapa en plats-till-plats-anslutning](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md). 

Du kan också konfigurera den virtuella nätverksgatewayen för att skapa en säker plats-till-plats VPN-anslutning. Skapa plats-till-plats VPN-anslutning mellan nytt virtuellt nätverk och en lokal VPN-enhet. Instruktioner finns i [konfigurera en virtuell nätverksgateway](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md).

## <a name="create-azure-vms-for-the-dc-roles"></a>Skapa virtuella datorer i Azure för DC-roller
Upprepa stegen för att skapa virtuella datorer som värd för rollen DC [skapa en Windows-dator med Azure-portalen](../virtual-machines/windows/quick-create-portal.md) efter behov. Distribuera minst två virtuella domänkontrollanter för att ge feltolerans och redundans. Om virtuella Azure-nätverket har minst två domänkontrollanter som är konfigurerad på samma sätt kan placera du de virtuella datorer som kör dessa domänkontrollanter i en tillgänglighetsuppsättning för förbättrad feltolerans.

För att skapa de virtuella datorerna med hjälp av Windows PowerShell i stället för Azure-portalen, se [Använd Azure PowerShell för att skapa och förkonfigurera Windows-baserade virtuella datorer](../virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Reservera en statisk IP-adress för virtuella datorer som rollen ska köras på domänkontrollanten. Ladda ned Microsoft Web Platform Installer om du vill reservera en statisk IP-adress och [installera Azure PowerShell](/powershell/azure/overview) och köra cmdlet Set-AzureStaticVNetIP. Exempel:

````
Get-AzureVM -ServiceName AzureDC1 -Name AzureDC1 | Set-AzureStaticVNetIP -IPAddress 10.0.0.4 | Update-AzureVM
````
Mer information om hur du anger en statisk IP-adress finns [konfigurera statiska interna IP-adress för en virtuell dator](../virtual-network/virtual-networks-reserved-private-ip.md).

## <a name="install-ad-ds-on-azure-vms"></a>Installera AD DS på virtuella Azure-datorer
Logga in på en virtuell dator och kontrollera att du har anslutning över plats-till-plats-VPN eller ExpressRoute-anslutning till resurser i ditt lokala nätverk. Installera sedan AD DS på virtuella Azure-datorer. Du kan använda samma process som används för att installera en extra Domänkontrollant i ditt lokala nätverk (UI, Windows PowerShell eller en svarsfil). När du installerar AD DS, kontrollera att du anger den nya volymen som plats för AD-databasen, loggfilerna och SYSVOL. Om du behöver en uppdaterare på AD DS-installationen finns [installera Active Directory Domain Services (nivå 100)](https://technet.microsoft.com/library/hh472162.aspx) eller [installera en replikerad Windows Server 2012-domänkontroller i en befintlig domän (nivå 200)](https://technet.microsoft.com/library/jj574134.aspx).

## <a name="reconfigure-dns-server-for-the-virtual-network"></a>Konfigurera DNS-servern för det virtuella nätverket
1. Att hämta en lista över virtuella nätverksnamn i den [Azure-portalen](https://portal.azure.com), söka efter *virtuella nätverken*och välj **virtuella nätverken** att visa en lista. 
2. Öppna det virtuella nätverket som du vill hantera, och sedan [omkonfigurera DNS-server-IP-adresser för det virtuella nätverket](../virtual-network/virtual-network-manage-network.md#dns-servers) att använda statiska IP-adresser som tilldelats replik domänkontrollanter i stället för IP-adresser för lokala DNS-servrar.
3. Om du vill kontrollera att alla repliken DC virtuella datorer på det virtuella nätverket konfigureras med för att använda DNS-servrar på det virtuella nätverket:
  1. Välj **virtuella datorer**.
  2. Välj de virtuella datorerna och välj sedan **starta om**. 
  3. Vänta tills den virtuella datorn är **kör** igen, och sedan logga in på den.

## <a name="create-vms-for-application-servers"></a>Skapa virtuella datorer för programservrar

Skapa virtuella datorer som värd för programserverrollen genom att upprepa stegen i [skapa en Windows-dator med Azure-portalen](../virtual-machines/windows/quick-create-portal.md) efter behov. För att skapa de virtuella datorerna med hjälp av Microsoft PowerShell i stället för Azure-portalen, se [Använd Azure PowerShell för att skapa och konfigurera Windows-baserade virtuella datorer](../virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Följande tabell innehåller rekommenderade inställningar.

| Inställning | Värden |
| --- | --- |
|  **Välj en bild** | Windows Server 2012 R2 Datacenter |
|  **Konfiguration av virtuell dator** |<p>Namn på virtuell dator: Skriv ett enkelt namn (till exempel AppServer1).</p><p>Nytt användarnamn: Ange namnet på en användare. Den här användaren ska vara medlem i gruppen lokala administratörer på den virtuella datorn. Du behöver det här namnet att logga in på den virtuella datorn för första gången. Inbyggt konto med namnet administratör fungerar inte.</p><p>Nytt lösenord/bekräfta: Ange ett lösenord</p> |
|  **Konfiguration av virtuell dator** |<p>Molntjänsten: Välj **skapa en ny molntjänst** för den första virtuella dator, väljer kommer att samma molntjänstnamn när du skapar flera virtuella datorer som värd för programmet.</p><p>Molnet tjänsten DNS-namn: Ange ett globalt unikt namn</p><p>Region/Tillhörighetsgrupp/virtuellt nätverk: Ange det virtuella nätverksnamnet (till exempel WestUSVNet).</p><p>Lagringskonto: Välj **använda ett automatiskt genererat lagringskonto** för den första virtuella dator väljer kommer att samma lagringskontonamn när du skapar flera virtuella datorer som värd för programmet.</p><p>Tillgänglighetsuppsättningen: Välj **skapa en tillgänglighetsuppsättning**.</p><p>Namn på tillgänglighetsuppsättning: Ange ett namn för tillgänglighetsuppsättning när du skapar den första virtuella datorn och välj sedan att samma namn när du skapar flera virtuella datorer.</p> |
|  **Konfiguration av virtuell dator** |<p>Välj <b>installera den Virtuella Datoragenten</b> och andra tillägg som du behöver.</p> |
  
Efter att varje virtuell dator etablerats, logga in och ansluta till domänen. 
1. I **Serverhanteraren** &gt; **lokal Server** &gt; **arbetsgrupp** &gt; **ändra...** väljer **domän**.
2. Ange namnet på den lokala domänen. 
3. Ange autentiseringsuppgifter för en domänanvändare.
4. Starta om den virtuella datorn.

## <a name="additional-resources"></a>Ytterligare resurser

* Mer information om hur du använder Windows PowerShell finns [Kom igång med Azure-Cmdlets](/powershell/azure/overview) och [Cmdlet-referens för Azure](/powershell/azure/get-started-azureps).
* [Riktlinjer för att distribuera Windows Server Active Directory på Azure Virtual Machines](https://msdn.microsoft.com/library/azure/jj156090.aspx)
* [Hur man överför befintliga lokala Hyper-V-domänkontrollanter till Azure med hjälp av Azure PowerShell](http://support.microsoft.com/kb/2904015)
* [Installera en ny Active Directory-skog på Azure-nätverk](active-directory-new-forest-virtual-machine.md)
* [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* [Microsoft Azure IT Pro IaaS: (01) virtuella grunderna](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
* [Microsoft Azure IT Pro IaaS: (05) att skapa virtuella nätverk och anslutning](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)
* [Azure PowerShell](/powershell/azure/overview)
* [Azure cmdlet: ar](/powershell/module/azurerm.compute/#virtual_machines)

<!--Image references-->
[1]: ./media/active-directory-install-replica-active-directory-domain-controller/ReplicaDCsOnAzureVNet.png
