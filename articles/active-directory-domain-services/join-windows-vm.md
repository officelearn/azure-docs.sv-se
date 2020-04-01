---
title: Ansluta till en virtuell dator med Windows Server till en hanterad domän | Microsoft-dokument
description: I den här självstudien kan du läsa om hur du ansluter en virtuell Windows Server-dator till en hanterad Azure Active Directory Domain Services-domän.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: 1ac508fc9fee07482e475c46e1db262c8bfa1a12
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476233"
---
# <a name="tutorial-join-a-windows-server-virtual-machine-to-a-managed-domain"></a>Självstudiekurs: Ansluta till en virtuell Windows Server-dator till en hanterad domän

Azure Active Directory Domain Services (Azure AD DS) tillhandahåller hanterade domäntjänster som domänanslutning, grupprincip, LDAP, Kerberos/NTLM-autentisering som är helt kompatibel med Active Directory i Windows Server. Med en Azure AD DS-hanterad domän kan du tillhandahålla domänanslutningsfunktioner och hantering till virtuella datorer (VMs) i Azure. Den här självstudien visar hur du skapar en Virtuell Windows Server och sedan ansluta den till en Azure AD DS-hanterad domän.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en virtuell Windows Server-dator
> * Ansluta virtuella Windows Server-datorer till ett virtuellt Azure-nätverk
> * Ansluta till den virtuella datorn till den Hanterade Azure AD DS-domänen

Om du inte har någon Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien behöver du följande resurser:

* En aktiv Azure-prenumeration.
    * Om du inte har en Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* En Azure Active Directory-klient som är associerad med din prenumeration, antingen synkroniserad med en lokal katalog eller en katalog med endast molnet.
    * Om det behövs [skapar du en Azure Active Directory-klientorganisation][create-azure-ad-tenant] eller [associerar en Azure-prenumeration med ditt konto][associate-azure-ad-tenant].
* En hanterad Azure Active Directory Domain Services-domän aktiverad och konfigurerad i din Azure AD-klientorganisation.
    * Om det behövs [kan du skapa och konfigurera en Azure Active Directory Domain Services-instans][create-azure-ad-ds-instance].
* Ett användarkonto som är en del av Azure AD DS-hanterad domän.
    * Kontrollera att Azure AD Connect-lösenordshÃjningssynkronisering eller återställning av lösenord med självbetjäning har utförts så att kontot kan logga in på Azure AD DS-hanterad domän.
* En Azure Bastion-värd som distribueras i ditt virtuella Azure AD DS-nätverk.
    * Om det behövs [skapar du en Azure Bastion-värd][azure-bastion].

Om du redan har en virtuell dator som du vill ansluta till domänen går du till avsnittet för att ansluta till [den virtuella datorn till den Hanterade Azure AD DS-domänen](#join-the-vm-to-the-azure-ad-ds-managed-domain).

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

I den här självstudien skapar du en virtuell Windows Server-dator för att gå med i din Azure AD DS-hanterade domän med Azure-portalen. Kom igång genom att logga in på [Azure-portalen](https://portal.azure.com).

## <a name="create-a-windows-server-virtual-machine"></a>Skapa en virtuell Windows Server-dator

Om du vill se hur du ansluter en dator till en Azure AD DS-hanterad domän ska vi skapa en virtuell Windows Server-dator. Den här virtuella datorn är ansluten till ett virtuellt Azure-nätverk som tillhandahåller anslutning till den Azure AD DS-hanterade domänen. Processen för att ansluta till en Azure AD DS-hanterad domän är samma sak som att ansluta till en vanlig lokal Active Directory Domain Services-domän.

Om du redan har en virtuell dator som du vill ansluta till domänen går du till avsnittet för att ansluta till [den virtuella datorn till den Hanterade Azure AD DS-domänen](#join-the-vm-to-the-azure-ad-ds-managed-domain).

1. På Azure-portalmenyn eller på **startsidan** väljer du **Skapa en resurs**.

1. Välj **Windows Server 2016 Datacenter**från Kom **igång**.

    ![Välj att skapa en virtuell dator med Windows Server 2016 Datacenter i Azure-portalen](./media/join-windows-vm/select-vm-image.png)

1. Konfigurera de grundläggande inställningarna för den virtuella datorn i fönstret **Grunderna.** Lämna standardalternativen för *tillgänglighetsalternativ*, *Bild*och *Storlek*.

    | Parameter            | Föreslaget värde   |
    |----------------------|-------------------|
    | Resursgrupp       | Markera eller skapa en resursgrupp, till exempel *myResourceGroup* |
    | Namn på virtuell dator | Ange ett namn för den virtuella datorn, till exempel *myVM* |
    | Region               | Välj den region som du vill skapa din virtuella dator i, till exempel *östra USA* |
    | Användarnamn             | Ange ett användarnamn för det lokala administratörskontot som ska skapas på den virtuella datorn, till exempel *azureuser* |
    | lösenord             | Ange och bekräfta sedan ett säkert lösenord som den lokala administratören kan skapa på den virtuella datorn. Ange inte autentiseringsuppgifterna för ett domänanvändarkonto. |

1. Som standard är virtuella datorer som skapats i Azure tillgängliga från Internet med hjälp av RDP. När RDP är aktiverat kan det hända att automatiska inloggningsattacker kommer att inträffa, vilket kan inaktivera konton med vanliga namn som *administratör* eller *administratör* på grund av flera misslyckade efterföljande inloggningsförsök.

    RDP bör endast aktiveras när det behövs och begränsas till en uppsättning auktoriserade IP-intervall. Den här konfigurationen hjälper till att förbättra säkerheten för den virtuella datorn och minskar området för potentiell attack. Eller skapa och använda en Azure Bastion-värd som endast tillåter åtkomst via Azure-portalen via TLS. I nästa steg i den här självstudien använder du en Azure Bastion-värd för att ansluta till den virtuella datorn på ett säkert sätt.

    Under **Offentliga inkommande portar**väljer du *Ingen*.

1. När du är klar väljer du **Nästa: Diskar**.
1. Välj *Standard SSD*på den nedrullningsbara menyn för **OS-disktyp**och välj sedan **Nästa: Nätverk**.
1. Den virtuella datorn måste ansluta till ett virtuellt Azure-nätverksundernät som kan kommunicera med undernätet som din Azure AD DS-hanterade domän distribueras till. Vi rekommenderar att en Azure AD DS-hanterad domän distribueras till sitt eget dedikerade undernät. Distribuera inte den virtuella datorn i samma undernät som din Azure AD DS-hanterade domän.

    Det finns två huvudsakliga sätt att distribuera den virtuella datorn och ansluta till ett lämpligt virtuellt nätverksundernät:
    
    * Skapa ett, eller välj ett befintligt, undernät i samma virtuella nätverk som din Azure AD DS-hanterade domän distribueras.
    * Välj ett undernät i ett virtuellt Azure-nätverk som är anslutet till det med [Azure-virtual network peering][vnet-peering].
    
    Om du väljer ett virtuellt nätverksundernät som inte är anslutet till undernätet för din Azure AD DS-instans kan du inte ansluta till den virtuella datorn till den hanterade domänen. För den här självstudien ska vi skapa ett nytt undernät i det virtuella Azure-nätverket.

    I **nätverksfönstret** väljer du det virtuella nätverk där din Azure AD DS-hanterade domän distribueras, till exempel *aaads-vnet*
1. I det här exemplet visas det befintliga *aaads-undernätet* som den Azure AD DS-hanterade domänen är ansluten till. Anslut inte den virtuella datorn till det här undernätet. Om du vill skapa ett undernät för den virtuella datorn väljer du **Hantera undernätskonfiguration**.

    ![Välj att hantera undernätskonfigurationen i Azure-portalen](./media/join-windows-vm/manage-subnet.png)

1. Välj **Adressutrymme**i menyn till vänster i det virtuella nätverksfönstret . Det virtuella nätverket skapas med ett enda adressutrymme på *10.0.2.0/24*, som används av standardundernätet. Andra undernät, till exempel för *arbetsbelastningar* eller Azure Bastion kan också redan finnas.

    Lägg till ytterligare ett IP-adressintervall i det virtuella nätverket. Storleken på det här adressintervallet och det faktiska IP-adressintervall som ska användas beror på andra nätverksresurser som redan har distribuerats. IP-adressintervallet bör inte överlappa med befintliga adressintervall i din Azure- eller lokala miljö. Se till att du lägger upp IP-adressintervallet tillräckligt stort för det antal virtuella datorer som du förväntar dig att distribuera till undernätet.

    I följande exempel läggs ytterligare ett IP-adressintervall *på 10.0.5.0/24* till. När du är klar väljer du **Spara**.

    ![Lägga till ytterligare ett IP-adressintervall för virtuellt nätverk i Azure-portalen](./media/join-windows-vm/add-vnet-address-range.png)

1. Välj sedan **Undernät**i menyn till vänster i det virtuella nätverksfönstret och välj sedan **+ Undernät** för att lägga till ett undernät.

1. Välj **+ Undernät**och ange sedan ett namn för undernätet, till exempel *hantering*. Ange ett **adressintervall (CIDR-block),** till exempel *10.0.5.0/24*. Kontrollera att det här IP-adressintervallet inte överlappar med andra befintliga Azure- eller lokala adressintervall. Lämna de andra alternativen som standardvärden och välj sedan **OK**.

    ![Skapa en nätkonfiguration i Azure-portalen](./media/join-windows-vm/create-subnet.png)

1. Det tar några sekunder att skapa undernätet. När det har skapats väljer du *X* för att stänga undernätsfönstret.
1. I **nätverksfönstret** för att skapa en virtuell dator väljer du det undernät som du skapade på den nedrullningsfläderande menyn, till exempel *hantering*. Se till att du väljer rätt undernät och inte distribuerar den virtuella datorn i samma undernät som din Azure AD DS-hanterade domän.
1. För **Offentlig IP**väljer du *Ingen* på den nedrullningsbara menyn, eftersom du använder Azure Bastion för att ansluta till hanteringen och inte behöver en offentlig IP-adress som tilldelats.
1. Lämna de andra alternativen som standardvärden och välj sedan **Hantera**.
1. Ställ in **startdiagnostik** till *Av*. Lämna de andra alternativen som standardvärden och välj sedan **Granska + skapa**.
1. Granska vm-inställningarna och välj sedan **Skapa**.

Det tar några minuter att skapa den virtuella datorn. Azure-portalen visar status för distributionen. När den virtuella datorn är klar väljer du **Gå till resurs**.

![Gå till vm-resursen i Azure-portalen när den har skapats](./media/join-windows-vm/vm-created.png)

## <a name="connect-to-the-windows-server-vm"></a>Ansluta till den virtuella windows server-datorn

Om du vill ansluta till dina virtuella datorer på ett säkert sätt använder du en Azure Bastion-värd. Med Azure Bastion distribueras en hanterad värd till ditt virtuella nätverk och tillhandahåller webbaserade RDP- eller SSH-anslutningar till virtuella datorer. Inga offentliga IP-adresser krävs för de virtuella datorerna och du behöver inte öppna regler för nätverkssäkerhetsgrupp för extern fjärrtrafik. Du ansluter till virtuella datorer med Hjälp av Azure-portalen från din webbläsare.

Så här använder du en Bastion-värd för att ansluta till den virtuella datorn:

1. I **fönstret Översikt** för den virtuella datorn väljer du **Anslut**och sedan **Bastion**.

    ![Ansluta till windows virtuella dator med Bastion i Azure-portalen](./media/join-windows-vm/connect-to-vm.png)

1. Ange autentiseringsuppgifterna för den virtuella datorn som du angav i föregående avsnitt och välj sedan **Anslut**.

   ![Ansluta via Bastion-värden i Azure-portalen](./media/join-windows-vm/connect-to-bastion.png)

Om det behövs kan du låta webbläsaren öppna popup-fönster för att skyddsanslutningen ska visas. Det tar några sekunder att ansluta till den virtuella datorn.

## <a name="join-the-vm-to-the-azure-ad-ds-managed-domain"></a>Ansluta till den virtuella datorn till den Hanterade Azure AD DS-domänen

Med den virtuella datorn skapad och en webbaserad RDP-anslutning upprättad med Azure Bastion ska vi nu ansluta till den virtuella Windows Server-datorn till den Azure AD DS-hanterade domänen. Den här processen är samma som en dator som ansluter till en vanlig lokal Active Directory Domain Services-domän.

1. Om **Serverhanteraren** inte öppnas som standard när du loggar in på den virtuella datorn väljer du **Start-menyn** och väljer sedan **Serverhanteraren**.
1. Välj **Lokal server**i den vänstra rutan i **serverhanterarens** fönster . Välj **Arbetsgrupp**under **Egenskaper** i den högra rutan .

    ![Öppna Serverhanteraren på den virtuella datorn och redigera egenskapen arbetsgrupp](./media/join-windows-vm/server-manager.png)

1. I fönstret **Systemegenskaper** väljer du **Ändra** för att ansluta till azure AD DS-hanterad domän.

    ![Välj att ändra arbetsgrupps- eller domänegenskaperna](./media/join-windows-vm/change-domain.png)

1. I rutan **Domän** anger du namnet på din Azure AD DS-hanterade domän, till exempel *aaddscontoso.com*och väljer sedan **OK**.

    ![Ange den Hanterade Azure AD DS-domänen som ska anslutas](./media/join-windows-vm/join-domain.png)

1. Ange domänautentiseringsuppgifter för att ansluta till domänen. Använd autentiseringsuppgifterna för en användare som är en del av den hanterade Azure AD DS-domänen. Kontot måste vara en del av Azure AD DS-hanterade domänen eller Azure AD-klienten - konton från externa kataloger som är associerade med din Azure AD-klient kan inte autentisera korrekt under domänanslutningsprocessen. Kontouppgifter kan anges på något av följande sätt:

    * **UPN-format** (rekommenderas) - Ange upn-suffixet (user principal name) för användarkontot, som konfigurerats i Azure AD. Till exempel skulle UPN-suffixet för användaren *contosoadmin* vara `contosoadmin@aaddscontoso.onmicrosoft.com`. Det finns ett par vanliga användningsfall där UPN-formatet kan användas på ett tillförlitligt sätt för att logga in på domänen i stället för *SAMAccountName-formatet:*
        * Om en användares UPN-prefix är långt, till exempel *deehasareallylongname,* kan *SAMAccountName* skapas automatiskt.
        * Om flera användare har samma UPN-prefix i din Azure AD-klientorganisation, till exempel *dee,* kan deras *SAMAccountName-format* skapas automatiskt.
    * **SAMAccountName-format** - Ange kontonamnet i *SAMAccountName-formatet.* Till exempel *samaccountName* för användare *contosoadmin* skulle vara `AADDSCONTOSO\contosoadmin`.

1. Det tar några sekunder att gå med i Azure AD DS-hanterade domänen. När du är klar välkomnar följande meddelande dig till domänen:

    ![Välkommen till domänen](./media/join-windows-vm/join-domain-successful.png)

    Klicka på **OK** för att fortsätta.

1. Starta om den virtuella datorn om du vill slutföra processen för att gå med i azure AD DS-hanterade domänen.

> [!TIP]
> Du kan domän ansluta till en virtuell dator med PowerShell med cmdlet för [tilläggsdator.][add-computer] I följande exempel sammanfogas *AADDSCONTOSO-domänen* och startar sedan om den virtuella datorn. Ange autentiseringsuppgifterna för en användare som ingår i den hanterade Azure AD DS-domänen när du uppmanas att göra det:
>
> `Add-Computer -DomainName AADDSCONTOSO -Restart`
>
> Om du vill ansluta till en virtuell dator utan att ansluta till den och konfigurera anslutningen manuellt kan du använda Azure PowerShell-cmdleten [Set-AzVmAdDomainExtension.][set-azvmaddomainextension]

När den virtuella datorn för Windows Server har startats om skickas alla principer som tillämpas i den hanterade Azure AD DS-domänen till den virtuella datorn. Du kan också logga in på den virtuella datorn för Windows Server med lämpliga domänautentiseringsuppgifter.

## <a name="clean-up-resources"></a>Rensa resurser

I nästa självstudiekurs använder du den här virtuella windows server-datorn för att installera hanteringsverktygen som gör att du kan administrera den hanterade Azure AD DS-domänen. Om du inte vill fortsätta i den här självstudieserien läser du följande rensningssteg för att [ta bort den virtuella datorn](#delete-the-vm). Annars [fortsätter du till nästa självstudiekurs](#next-steps).

### <a name="un-join-the-vm-from-azure-ad-ds-managed-domain"></a>Koppla från den virtuella datorn från Azure AD DS-hanterad domän

Om du vill ta bort den virtuella datorn från azure AD DS-hanterade domänen följer du igenom stegen igen för att [ansluta den virtuella datorn till en domän](#join-the-vm-to-the-azure-ad-ds-managed-domain). I stället för att gå med i azure AD DS-hanterad domän väljer du att gå med i en arbetsgrupp, till exempel *standardarbetsgruppen*. När den virtuella datorn har startats om tas datorobjektet bort från azure AD DS-hanterade domänen.

Om du [tar bort den virtuella datorn](#delete-the-vm) utan att du inte ansluter från domänen finns ett överblivet datorobjekt kvar i Azure AD DS.

### <a name="delete-the-vm"></a>Ta bort den virtuella datorn

Om du inte tänker använda den här virtuella datorn för Windows Server tar du bort den virtuella datorn med följande steg:

1. Välj **Resursgrupper** på menyn till vänster
1. Välj resursgrupp, till exempel *myResourceGroup*.
1. Välj din virtuella dator, till exempel *myVM*, och välj sedan **Ta bort**. Välj **Ja** för att bekräfta borttagningen av resursen. Det tar några minuter att ta bort den virtuella datorn.
1. När den virtuella datorn tas bort väljer du OS-disken, nätverkskortet och alla andra resurser med *myVM-prefixet* och tar bort dem.

## <a name="troubleshoot-domain-join-issues"></a>Felsöka problem med domänkoppling

Den virtuella datorn för Windows Server bör ansluta till den Hanterade Azure AD DS-domänen, på samma sätt som en vanlig lokal dator ansluter till en Active Directory Domain Services-domän. Om den virtuella datorn för Windows Server inte kan ansluta till den hanterade Azure AD DS-domänen betyder det att det finns ett anslutnings- eller autentiseringsproblem. Granska följande felsökningsavsnitt för att ansluta till den hanterade domänen.

### <a name="connectivity-issues"></a>Anslutningsproblem

Om du inte får en uppmaning som ber om autentiseringsuppgifter för att ansluta till domänen finns det ett anslutningsproblem. Den virtuella datorn kan inte nå azure AD DS-hanterad domän i det virtuella nätverket.

När du har provat var och en av dessa felsökningssteg försöker du ansluta till den virtuella datorn för Windows Server till den hanterade domänen igen.

* Kontrollera att den virtuella datorn är ansluten till samma virtuella nätverk som Azure AD DS är aktiverat i eller har en peer-nätverksanslutning.
* Försök att pinga DNS-domännamnet för `ping aaddscontoso.com`den hanterade domänen, till exempel .
    * Om pingbegäran misslyckas försöker du pinga IP-adresserna `ping 10.0.0.4`för den hanterade domänen, till exempel . IP-adressen för din miljö visas på sidan *Egenskaper* när du väljer den Azure AD DS-hanterade domänen från listan över Azure-resurser.
    * Om du kan pinga IP-adressen men inte domänen kan DNS vara felaktigt konfigurerad. Bekräfta att IP-adresserna för den hanterade domänen är konfigurerade som DNS-servrar för det virtuella nätverket.
* Försök att rensa DNS-matcharens cache `ipconfig /flushdns` på den virtuella datorn med kommandot.

### <a name="credentials-related-issues"></a>Autentiseringsrelaterade problem

Om du får en fråga som ber om autentiseringsuppgifter för att ansluta till domänen, men sedan ett fel när du har angett dessa autentiseringsuppgifter, kan den virtuella datorn ansluta till den Hanterade Azure AD DS-domänen. Autentiseringsuppgifterna som du angav låter inte den virtuella datorn gå med i Azure AD DS-hanterad domän.

När du har provat var och en av dessa felsökningssteg försöker du ansluta till den virtuella datorn för Windows Server till den hanterade domänen igen.

* Kontrollera att användarkontot du anger tillhör den Hanterade Azure AD DS-domänen.
* Bekräfta att kontot är en del av Azure AD DS-hanterad domän eller Azure AD-klientorganisation. Konton från externa kataloger som är associerade med din Azure AD-klient kan inte autentisera korrekt under domänanslutningsprocessen.
* Prova att använda UPN-formatet för `contosoadmin@aaddscontoso.onmicrosoft.com`att ange autentiseringsuppgifter, till exempel . Om det finns många användare med samma UPN-prefix i din klientorganisation eller om ditt UPN-prefix är alltför långt kan *SAMAccountName* för ditt konto skapas automatiskt. I dessa fall kan *SAMAccountName-formatet* för ditt konto skilja sig från vad du förväntar dig eller använder i din lokala domän.
* Kontrollera att du har [aktiverat lösenordssynkronisering][password-sync] till den hanterade domänen. Utan det här konfigurationssteget finns inte de obligatoriska lösenordshashes i Azure AD DS-hanterade domänen för att autentisera inloggningsförsöket korrekt.
* Vänta tills lösenordssynkroniseringen har slutförts. När ett användarkontos lösenord ändras uppdateras lösenordet i Azure AD. Det tar lite tid innan lösenordet är tillgängligt för användning av domänkoppling.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa en virtuell Windows Server-dator
> * Ansluta till den virtuella virtuella windows server-datorn till ett virtuellt Azure-nätverk
> * Ansluta till den virtuella datorn till den Hanterade Azure AD DS-domänen

Konfigurera en virtuell hanterings-VM för hantering med Hjälp av Active Directory Administrative Center (ADAC) om du vill administrera din Azure AD DS-hanterade domän.

> [!div class="nextstepaction"]
> [Installera administrationsverktyg på en virtuell hanterings-dator](tutorial-create-management-vm.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[vnet-peering]: ../virtual-network/virtual-network-peering-overview.md
[password-sync]: active-directory-ds-getting-started-password-sync.md
[add-computer]: /powershell/module/microsoft.powershell.management/add-computer
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[set-azvmaddomainextension]: /powershell/module/az.compute/set-azvmaddomainextension
