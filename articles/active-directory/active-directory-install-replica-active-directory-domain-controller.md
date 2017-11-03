---
title: "Installera en replikerad Active Directory-domänkontroller i Azure | Microsoft Docs"
description: "En genomgång som förklarar hur du installerar en domänkontrollant från en lokal Active Directory-skog i en virtuell Azure-dator."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 8c9ebf1b-289a-4dd6-9567-a946450005c0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: oldportal;it-pro;
ms.openlocfilehash: fb0bacac346445e6bde9df22f3355419e3162a3c
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="install-a-replica-active-directory-domain-controller-in-an-azure-virtual-network"></a>Installera en replikerad Active Directory-domänkontroller i Azure-nätverk
Det här avsnittet visar hur du installerar ytterligare domänkontrollanter (även kallat replik domänkontrollanter) för en lokal Active Directory-domän på virtuella Azure-datorer (VM) i Azure-nätverk.

> [!IMPORTANT]
> Microsoft rekommenderar att du hanterar Azure AD via [Azure AD administratörscenter](https://aad.portal.azure.com) på Azure Portal istället för via den klassiska Azure-portalen som nämns i den här artikeln.

Du kan också vara intresserad av dessa Närliggande ämnen:

* Alternativt kan du installera en ny Active Directory-skog på Azure-nätverk. De här stegen finns [installera en ny Active Directory-skog på Azure-nätverk](active-directory-new-forest-virtual-machine.md).
* Konceptuell vägledning om hur du installerar Active Directory Domain Services (AD DS) på Azure-nätverk finns i [riktlinjer för att distribuera Windows Server Active Directory på Azure Virtual Machines](https://msdn.microsoft.com/library/azure/jj156090.aspx).

## <a name="scenario-diagram"></a>Scenario-diagram
I det här scenariot externa användare som behöver åtkomst till program som körs på servrar som ingår i domänen. De virtuella datorerna som kör programmet och domänkontrollanter för repliken är installerade i Azure-nätverk. Det virtuella nätverket kan vara ansluten till det lokala nätverket av en [plats-till-plats VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md) anslutning som visas i följande diagram du kan använda [ExpressRoute](../expressroute/expressroute-locations-providers.md) för snabbare anslutningar.

Programservern och domänkontrollanter distribueras inom separat molntjänster att distribuera beräkning bearbetning och [tillgänglighetsuppsättningar](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) för förbättrad feltolerans.
Domänkontrollanter replikera med varandra och med lokala domänkontrollanter med hjälp av Active Directory-replikering. Inga synkroniseringsverktyg behövs.

![Diagram över pf replik Active Directory-domänkontrollant ett Azure-vnet][1]

## <a name="create-an-active-directory-site-for-the-azure-virtual-network"></a>Skapa en Active Directory-plats för virtuella Azure-nätverket
Det är en bra idé att skapa en plats i Active Directory som representerar den region där nätverket motsvarande till det virtuella nätverket. Som hjälper till att optimera autentisering, replikering och andra åtgärder för DC-plats. Följande steg förklarar hur du skapar en plats och mer bakgrund, se [att lägga till en ny plats](https://technet.microsoft.com/library/cc781496.aspx).

1. Öppna Active Directory-platser och tjänster: **Serverhanteraren** > **verktyg** > **Active Directory-platser och tjänster**.
2. Skapa en plats som representerar den region där du skapade ett virtuellt Azure-nätverk: Klicka på **platser** > **åtgärd** > **ny plats** > typ i namnet på den nya platsen, till exempel Azure oss Väst > Välj en platslänk > **OK**.
3. Skapa ett undernät och koppla till den nya platsen: Dubbelklicka på **platser** > högerklickar du på **undernät** > **Nytt undernät** > skriver du IP-adressintervall i virtuella nätverk (till exempel 10.1.0.0/16 i diagrammet scenariot) > väljer du den nya Azure-platsen > **OK**.

## <a name="create-an-azure-virtual-network"></a>Skapa ett Azure-nätverk
1. I den [klassiska Azure-portalen](https://manage.windowsazure.com), klickar du på **ny** > **nätverkstjänster** > **virtuellt nätverk**  >  **Skapa anpassade** och använder du följande värden för att slutföra guiden.

   | På den här sidan i guiden... | Ange dessa värden |
   | --- | --- |
   |  **Information om virtuellt nätverk** |<p>Namn: Ange ett namn för det virtuella nätverket, till exempel WestUSVNet.</p><p>Region: Välj den närmaste regionen.</p> |
   |  **DNS- och VPN-anslutning** |<p>DNS-servrar: Ange namn och IP-adressen för en eller flera lokala DNS-servrar.</p><p>Anslutningsbarhet: Välj **konfigurera en plats-till-plats-VPN**.</p><p>Lokala nätverk: Ange ett nytt lokala nätverk.</p><p>Om du använder ExpressRoute i stället för en VPN-anslutning, se [konfigurera en ExpressRoute-anslutning via en Exchange-Provider](../expressroute/expressroute-locations-providers.md).</p> |
   |  **Plats-till-plats-anslutning** |<p>Namn: Ange ett namn för det lokala nätverket.</p><p>VPN-enhetens IP-adress: Ange den offentliga IP-adressen på den enhet som ansluter till det virtuella nätverket. VPN-enheten får inte finnas bakom en NAT.</p><p>Adress: Ange adressintervall för ditt lokala nätverk (till exempel 192.168.0.0/16 i scenariot diagram).</p> |
   |  **Virtuellt nätverk adressutrymmen** |<p>Adressutrymmet: Ange IP-adressintervall för virtuella datorer som du vill köra i virtuella Azure-nätverket (till exempel 10.1.0.0/16 i scenariot diagram). Detta adressintervall kan inte överlappa adressintervallen i det lokala nätverket.</p><p>Undernät: Ange ett namn och adress för ett undernät för programservrarna (till exempel Frontend 10.1.1.0/24) och för domänkontrollanter (till exempel serverdel 10.1.2.0/24).</p><p>Klicka på **Lägg till gateway-undernätet**.</p> |
2. Därefter konfigurerar du den virtuella nätverksgatewayen för att skapa en säker plats-till-plats VPN-anslutning. Se [konfigurera en virtuell nätverksgateway](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md) för instruktionerna.
3. Skapa plats-till-plats VPN-anslutning mellan nytt virtuellt nätverk och en lokal VPN-enhet. Se [konfigurera en virtuell nätverksgateway](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md) för instruktionerna.

## <a name="create-azure-vms-for-the-dc-roles"></a>Skapa virtuella datorer i Azure för DC-roller
Upprepa följande steg för att skapa virtuella datorer som värd för rollen DC efter behov. Du bör distribuera minst två virtuella domänkontrollanter för att ge feltolerans och redundans. Om virtuella Azure-nätverket innehåller minst två domänkontrollanter som är konfigurerad på samma sätt (som är de båda global katalog kör DNS-servern, och inget innehar FSMO-rollen, och så vidare) placera de virtuella datorer som kör de domänkontrollanter i en tillgänglighetsuppsättning för förbättrad feltolerans.
För att skapa de virtuella datorerna med hjälp av Windows PowerShell i stället för Gränssnittet, se [Använd Azure PowerShell för att skapa och förkonfigurera Windows-baserade virtuella datorer](../virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

1. I den [klassiska Azure-portalen](https://manage.windowsazure.com), klickar du på **ny** > **Compute** > **virtuella**  >  **Från galleriet**. Använd följande värden för att slutföra guiden. Acceptera standardvärdet för en inställning om inte ett annat värde förslag eller krävs.

   | På den här sidan i guiden... | Ange dessa värden |
   | --- | --- |
   |  **Välj en bild** |Windows Server 2012 R2 Datacenter |
   |  **Konfiguration av virtuell dator** |<p>Namn på virtuell dator: Skriv ett enkelt namn (till exempel AzureDC1).</p><p>Nytt användarnamn: Ange namnet på en användare. Den här användaren ska vara medlem i gruppen lokala administratörer på den virtuella datorn. Du behöver det här namnet att logga in på den virtuella datorn för första gången. Inbyggt konto med namnet administratör fungerar inte.</p><p>Nytt lösenord/bekräfta: Ange ett lösenord</p> |
   |  **Konfiguration av virtuell dator** |<p>Molntjänsten: Välj <b>skapa en ny molntjänst</b> för den första virtuella datorn och välj att samma molntjänstnamn när du skapar flera virtuella datorer som ska vara värd DC-rollen.</p><p>Molnet tjänsten DNS-namn: Ange ett globalt unikt namn</p><p>Region/Tillhörighetsgrupp/virtuellt nätverk: Ange det virtuella nätverksnamnet (till exempel WestUSVNet).</p><p>Storage-konto: Välj <b>använda ett automatiskt genererat lagringskonto</b> för den första virtuella datorn och välj sedan det samma lagringskontonamnet när du skapar flera virtuella datorer som ska vara värd DC-rollen.</p><p>Tillgänglighetsuppsättningen: Välj <b>skapa en tillgänglighetsuppsättning</b>.</p><p>Namn på tillgänglighetsuppsättning: Ange ett namn för tillgänglighetsuppsättning när du skapar den första virtuella datorn och välj sedan att samma namn när du skapar flera virtuella datorer.</p> |
   |  **Konfiguration av virtuell dator** |<p>Välj <b>installera den Virtuella Datoragenten</b> och andra tillägg som du behöver.</p> |
2. Ansluta en disk på varje virtuell dator som kör serverrollen för domänkontrollanten. Det ytterligare diskutrymme krävs för att lagra AD-databasen, loggfilerna och SYSVOL. Ange en storlek för disken (till exempel 10 GB) och lämna den **värden Cache inställningar** inställd på **ingen**. Anvisningar finns i avsnittet [hur du ansluter en datadisk till en virtuell Windows-dator](../virtual-machines/windows/classic/attach-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
3. När du först logga in på den virtuella datorn, öppna **Serverhanteraren** > **fil- och lagringstjänster** att skapa en volym på disken med NTFS.
4. Reservera en statisk IP-adress för virtuella datorer som rollen ska köras på domänkontrollanten. Ladda ned Microsoft Web Platform Installer om du vill reservera en statisk IP-adress och [installera Azure PowerShell](/powershell/azure/overview) och köra cmdlet Set-AzureStaticVNetIP. Exempel:

    ' Get-AzureVM - ServiceName AzureDC1-Name AzureDC1 | Ange AzureStaticVNetIP - IP-adress 10.0.0.4 | Uppdatera AzureVM

Mer information om hur du anger en statisk IP-adress finns [konfigurera statiska interna IP-adress för en virtuell dator](../virtual-network/virtual-networks-reserved-private-ip.md).

## <a name="install-ad-ds-on-azure-vms"></a>Installera AD DS på virtuella Azure-datorer
Logga in på en virtuell dator och kontrollera att du har anslutning över plats-till-plats-VPN eller ExpressRoute-anslutning till resurser i ditt lokala nätverk. Installera sedan AD DS på virtuella Azure-datorer. Du kan använda samma process som används för att installera en extra Domänkontrollant i ditt lokala nätverk (UI, Windows PowerShell eller en svarsfil). När du installerar AD DS, kontrollera att du anger den nya volymen som plats för AD-databasen, loggfilerna och SYSVOL. Om du behöver en uppdaterare på AD DS-installationen finns [installera Active Directory Domain Services (nivå 100)](https://technet.microsoft.com/library/hh472162.aspx) eller [installera en replikerad Windows Server 2012-domänkontroller i en befintlig domän (nivå 200)](https://technet.microsoft.com/library/jj574134.aspx).

## <a name="reconfigure-dns-server-for-the-virtual-network"></a>Konfigurera DNS-servern för det virtuella nätverket
1. I den [Azure-portalen](https://portal.azure.com)i den **söka resurser** ange *virtuella nätverk*, klicka på **virtuella nätverk (klassiskt)** i den sökresultat. Klicka på namnet på det virtuella nätverket och sedan [omkonfigurera DNS-server-IP-adresser för det virtuella nätverket](../virtual-network/virtual-network-manage-network.md#dns-servers) att använda statiska IP-adresser som tilldelats replik domänkontrollanter i stället för IP-adresser för en lokal DNS-servrar.
2. För att säkerställa att alla replikeringen DC virtuella datorer på det virtuella nätverket konfigureras med för att använda DNS-servrar på det virtuella nätverket, klickar du på **virtuella datorer**, klicka i statuskolumnen för varje virtuell dator och klicka sedan på **starta om**. Vänta tills den virtuella datorn visar **kör** tillstånd innan du försöker logga in på den.

## <a name="create-vms-for-application-servers"></a>Skapa virtuella datorer för programservrar
1. Upprepa följande steg för att skapa virtuella datorer ska köras som programservrar. Acceptera standardvärdet för en inställning om inte ett annat värde förslag eller krävs.

   | På den här sidan i guiden... | Ange dessa värden |
   | --- | --- |
   |  **Välj en bild** |Windows Server 2012 R2 Datacenter |
   |  **Konfiguration av virtuell dator** |<p>Namn på virtuell dator: Skriv ett enkelt namn (till exempel AppServer1).</p><p>Nytt användarnamn: Ange namnet på en användare. Den här användaren ska vara medlem i gruppen lokala administratörer på den virtuella datorn. Du behöver det här namnet att logga in på den virtuella datorn för första gången. Inbyggt konto med namnet administratör fungerar inte.</p><p>Nytt lösenord/bekräfta: Ange ett lösenord</p> |
   |  **Konfiguration av virtuell dator** |<p>Molntjänsten: Välj **skapa en ny molntjänst** för den första virtuella dator, väljer kommer att samma molntjänstnamn när du skapar flera virtuella datorer som värd för programmet.</p><p>Molnet tjänsten DNS-namn: Ange ett globalt unikt namn</p><p>Region/Tillhörighetsgrupp/virtuellt nätverk: Ange det virtuella nätverksnamnet (till exempel WestUSVNet).</p><p>Lagringskonto: Välj **använda ett automatiskt genererat lagringskonto** för den första virtuella dator väljer kommer att samma lagringskontonamn när du skapar flera virtuella datorer som värd för programmet.</p><p>Tillgänglighetsuppsättningen: Välj **skapa en tillgänglighetsuppsättning**.</p><p>Namn på tillgänglighetsuppsättning: Ange ett namn för tillgänglighetsuppsättning när du skapar den första virtuella datorn och välj sedan att samma namn när du skapar flera virtuella datorer.</p> |
   |  **Konfiguration av virtuell dator** |<p>Välj <b>installera den Virtuella Datoragenten</b> och andra tillägg som du behöver.</p> |
2. Efter att varje virtuell dator etablerats, logga in och ansluta till domänen. I **Serverhanteraren**, klickar du på **lokal Server** > **arbetsgrupp** > **ändra...** och välj sedan **domän** och Skriv namnet på den lokala domänen. Ange autentiseringsuppgifter för en domänanvändare och starta sedan om den virtuella datorn för att slutföra domänanslutningen.

För att skapa de virtuella datorerna med hjälp av Windows PowerShell i stället för Gränssnittet, se [Använd Azure PowerShell för att skapa och förkonfigurera Windows-baserade virtuella datorer](../virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Mer information om hur du använder Windows PowerShell finns [Kom igång med Azure-Cmdlets](/powershell/azure/overview) och [Cmdlet-referens för Azure](/powershell/azure/get-started-azureps).

## <a name="additional-resources"></a>Ytterligare resurser
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
