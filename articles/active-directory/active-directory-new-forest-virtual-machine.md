---
title: "Installera Active Directory-skog på Azure-nätverk | Microsoft Docs"
description: "En självstudiekurs som beskriver hur du skapar en ny Active Directory-skog på en virtuell dator (VM) på Azure-nätverk."
services: active-directory, virtual-network
keywords: 'Active directory virtuell dator, installera active directory-skog, azure active directory-videor '
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
tags: 
ms.assetid: eb7170d0-266a-4caa-adce-1855589d65d1
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2017
ms.author: joflore
ms.openlocfilehash: acfdb94323853161e835b88ef441eaed681bde25
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
---
# <a name="install-a-new-active-directory-forest-on-an-azure-virtual-network"></a>Installera en ny Active Directory-skog på Azure-nätverk
Den här artikeln visar hur du skapar en ny Windows Server Active Directory-miljö på en virtuell dator (VM) på en [virtuella Azure-nätverket](../virtual-network/virtual-networks-overview.md). I det här fallet är virtuella Azure-nätverket inte ansluten till ett lokalt nätverk.

Du kan också vara intresserad av dessa relaterade artiklar:

* Se en video som visar de här stegen [hur du installerar en ny Active Directory-skog på Azure-nätverk](http://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/How-to-install-a-new-Active-Directory-forest-on-an-Azure-virtual-network)
* Du kan alternativt [konfigurera en plats-till-plats-VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) och sedan installera en ny skog eller utöka en lokal skog till ett Azure virtual network. De här stegen finns [installera en replikerad Active Directory-domänkontroller i ett Azure Virtual Network](active-directory-install-replica-active-directory-domain-controller.md).
* Konceptuell vägledning om hur du installerar Active Directory Domain Services (AD DS) på Azure-nätverk finns i [riktlinjer för att distribuera Windows Server Active Directory på Azure Virtual Machines](https://msdn.microsoft.com/library/azure/jj156090.aspx).

## <a name="scenario-diagram"></a>Scenario-Diagram
I det här scenariot externa användare som behöver åtkomst till program som körs på servrar som ingår i domänen. Virtuella datorer som kör programmet och de virtuella datorer som kör domänkontrollanter installeras installerat i sina egna Molntjänsten i Azure-nätverk. De ingår också i en tillgänglighetsuppsättning för förbättrad feltolerans.

![Active Directory-skog på en virtuell dator på Azure Virtual Network ][1] 7

## <a name="how-does-this-differ-from-on-premises"></a>Hur skiljer det lokalt?
Det finns inte mycket skillnaden mellan att installera en domänkontrollant på Azure jämfört med lokalt. De viktigaste skillnaderna visas i följande tabell.

| Så här konfigurerar du... | Lokal | Azure-virtuellt nätverk |
| --- | --- | --- |
| **IP-adressen för domänkontrollanten.** |Tilldela statiska IP-adress på nätverkskortets egenskaper |Kör cmdlet Set-AzureStaticVNetIP om du vill tilldela en statisk IP-adress |
| **DNS-klienten matcharen** |Ange önskade och alternativa DNS-serveradress för nätverket egenskaper för nätverkskort för medlemmar i domänen |Ange DNS-serveradress för de egenskaper för virtuellt nätverk |
| **Active Directory-databaslagring** |Om du vill ändra standardplatsen för lagring från C:\ |Du måste ändra standardlagringsplats från C:\ |

## <a name="create-an-azure-virtual-network"></a>Skapa ett Azure-nätverk
1. Logga in på Azure Portal.
2. Skapa ett virtuellt nätverk. Klicka på **nätverk** > **skapa ett virtuellt nätverk**. Använd värdena i tabellen nedan för att slutföra guiden.

   | På den här sidan i guiden... | Ange dessa värden |
   | --- | --- |
   |  **Information om virtuellt nätverk** |<p>Namn: Ange ett namn för det virtuella nätverket</p><p>Region: Välj den närmaste regionen</p> |
   |  **DNS- och VPN** |<p>Inget DNS-server</p><p>Inte markera alternativet antingen VPN</p> |
   |  **Virtuellt nätverk adressutrymmen** |<p>Undernätnamnet: Ange ett namn för ditt undernät</p><p>Startar IP: <b>10.0.0.0</b></p><p>CIDR: <b>/24 (256)</b></p> |

## <a name="create-vms-to-run-the-domain-controller-and-dns-server-roles"></a>Skapa virtuella datorer om du vill köra en domänkontrollant och DNS-server-roller
Upprepa följande steg för att skapa virtuella datorer som värd för rollen DC efter behov. Du bör distribuera minst två virtuella domänkontrollanter för att ge feltolerans och redundans. Om virtuella Azure-nätverket innehåller minst två domänkontrollanter som är konfigurerad på samma sätt (som är de båda global katalog kör DNS-servern, och inget innehar FSMO-rollen, och så vidare) placera de virtuella datorer som kör de domänkontrollanter i en tillgänglighetsuppsättning för förbättrad feltolerans.

För att skapa de virtuella datorerna med hjälp av Windows PowerShell i stället för Gränssnittet, se [Använd Azure PowerShell för att skapa och förkonfigurera Windows-baserade virtuella datorer](../virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

1. Välj i Azure-portalen **ny** > **Compute**, och välj sedan en virtuell dator. Använd följande värden för att slutföra guiden. Acceptera standardvärdet för en inställning om inte ett annat värde förslag eller krävs.

   | På den här sidan i guiden... | Ange dessa värden |
   | --- | --- |
   |  **Välj en bild** |Windows Server 2012 R2 Datacenter |
   |  **Konfiguration av virtuell dator** |<p>Namn på virtuell dator: Skriv ett enkelt namn (till exempel AzureDC1).</p><p>Nytt användarnamn: Ange namnet på en användare. Den här användaren ska vara medlem i gruppen lokala administratörer på den virtuella datorn. Du behöver det här namnet att logga in på den virtuella datorn för första gången. Inbyggt konto med namnet administratör fungerar inte.</p><p>Nytt lösenord/bekräfta: Ange ett lösenord</p> |
   |  **Konfiguration av virtuell dator** |<p>Molntjänsten: Välj <b>skapa en ny molntjänst</b> för den första virtuella datorn och välj att samma molntjänstnamn när du skapar flera virtuella datorer som ska vara värd DC-rollen.</p><p>Molnet tjänsten DNS-namn: Ange ett globalt unikt namn</p><p>Region/Tillhörighetsgrupp/virtuellt nätverk: Ange det virtuella nätverksnamnet (till exempel WestUSVNet).</p><p>Storage-konto: Välj <b>använda ett automatiskt genererat lagringskonto</b> för den första virtuella datorn och välj sedan det samma lagringskontonamnet när du skapar flera virtuella datorer som ska vara värd DC-rollen.</p><p>Tillgänglighetsuppsättningen: Välj <b>skapa en tillgänglighetsuppsättning</b>.</p><p>Namn på tillgänglighetsuppsättning: Ange ett namn för tillgänglighetsuppsättning när du skapar den första virtuella datorn och välj sedan att samma namn när du skapar flera virtuella datorer.</p> |
   |  **Konfiguration av virtuell dator** |<p>Välj <b>installera den Virtuella Datoragenten</b> och andra tillägg som du behöver.</p> |
2. Ansluta en disk på varje virtuell dator som kör serverrollen för domänkontrollanten. Det ytterligare diskutrymme krävs för att lagra AD-databasen, loggfilerna och SYSVOL. Ange en storlek för disken (till exempel 10 GB) och lämna den **värden Cache inställningar** inställd på **ingen**. Anvisningar finns i avsnittet [hur du ansluter en datadisk till en virtuell Windows-dator](../virtual-machines/windows/classic/attach-disk-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
3. När du först logga in på den virtuella datorn, öppna **Serverhanteraren** > **fil- och lagringstjänster** att skapa en volym på disken med NTFS.
4. Reservera en statisk IP-adress för virtuella datorer som rollen ska köras på domänkontrollanten. Ladda ned Microsoft Web Platform Installer om du vill reservera en statisk IP-adress och [installera Azure PowerShell](/powershell/azure/overview) och köra cmdlet Set-AzureStaticVNetIP. Exempel:

    `Get-AzureVM -ServiceName AzureDC1 -Name AzureDC1 | Set-AzureStaticVNetIP -IPAddress 10.0.0.4 | Update-AzureVM`

Mer information om hur du anger en statisk IP-adress finns [konfigurera statiska interna IP-adress för en virtuell dator](../virtual-network/virtual-networks-reserved-private-ip.md).

## <a name="install-windows-server-active-directory"></a>Installera Windows Server Active Directory
Använd samma rutin att [installera AD DS](https://technet.microsoft.com/library/jj574166.aspx) att du använder lokala (det vill säga du kan använda Användargränssnittet, en svarsfil eller Windows PowerShell). Du måste ange administratörsautentiseringsuppgifter för att installera en ny skog. Ändra standardplatsen för lagring för att ange plats för Active Directory-databasen, loggfilerna och SYSVOL från enheten med operativsystemet till ytterligare datadisken som du har kopplat till den virtuella datorn.

När DC-installationen är klar att ansluta till den virtuella datorn igen och logga in på domänkontrollanten. Kom ihåg att ange autentiseringsuppgifter för domänen.

## <a name="reset-the-dns-server-for-the-azure-virtual-network"></a>Återställa DNS-servern för det virtuella Azure-nätverket
1. Återställa DNS-vidarebefordrare inställningen på den nya Domänkontrollant/DNS-servern.
   1. I Serverhanteraren klickar du på **verktyg** > **DNS**.
   2. I **DNS-hanteraren**, högerklicka på namnet på DNS-servern och klickar på **egenskaper**.
   3. På den **vidarebefordrare** fliken IP-adressen för vidarebefordraren och klicka på **redigera**.  Välj IP-adressen och klicka på **ta bort**.
   4. Klicka på **OK** Stäng redigeraren och **Ok** igen för att stänga egenskaperna för DNS-servern.
2. Uppdatera DNS-Serverinställningen för det virtuella nätverket.
   1. Klicka på **virtuella nätverk** > Dubbelklicka på det virtuella nätverket som du skapade > **konfigurera** > **DNS-servrar**, skriver du namnet och IP-Adressen för en av de virtuella datorerna som körs på DC/DNS-serverrollen och klickar på **spara**.
   2. Välj den virtuella datorn och klicka på **starta om** ska utlösa den virtuella datorn för att konfigurera inställningar för DNS-matchare med IP-adressen för den nya DNS-servern.

## <a name="create-vms-for-domain-members"></a>Skapa virtuella datorer för medlemmar i domänen
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

## <a name="see-also"></a>Se även
* [Hur du installerar en ny Active Directory-skog på Azure-nätverk](http://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/How-to-install-a-new-Active-Directory-forest-on-an-Azure-virtual-network)
* [Riktlinjer för att distribuera Windows Server Active Directory på Azure Virtual Machines](https://msdn.microsoft.com/library/azure/jj156090.aspx)
* [Konfigurera en plats-till-plats-VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Installera en replikerad Active Directory-domänkontroller i Azure-nätverk](active-directory-install-replica-active-directory-domain-controller.md)
* [Microsoft Azure IT Pro IaaS: (01) virtuella grunderna](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
* [Microsoft Azure IT Pro IaaS: (05) att skapa virtuella nätverk och anslutning](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)
* [Översikt över virtuella nätverk](../virtual-network/virtual-networks-overview.md)
* [Installera och konfigurera Azure PowerShell](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Azure Cmdlet-referens](/powershell/azure/get-started-azureps)
* [Ange Azure VM statisk IP-adress](http://windowsitpro.com/windows-azure/set-azure-vm-static-ip-address)
* [Tilldela statiska IP-Adressen till Azure VM](http://www.bhargavs.com/index.php/2014/03/13/how-to-assign-static-ip-to-azure-vm/)
* [Installera en ny Active Directory-skog](https://technet.microsoft.com/library/jj574166.aspx)
* [Introduktion till virtualisering för Active Directory Domain Services (AD DS) (nivå 100)](https://technet.microsoft.com/library/hh831734.aspx)

<!--Image references-->
[1]: ./media/active-directory-new-forest-virtual-machine/AD_Forest.png
