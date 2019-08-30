---
title: Ansluta en virtuell Windows Server-dator till en hanterad domän | Microsoft Docs
description: I den här självstudien får du lära dig hur du ansluter en virtuell Windows Server-dator till en Azure Active Directory Domain Services hanterad domän.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: iainfou
ms.openlocfilehash: 3fd2a50946f0857d527c34b62687b2dbdd71298e
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2019
ms.locfileid: "70172031"
---
# <a name="tutorial-join-a-windows-server-virtual-machine-to-a-managed-domain"></a>Självstudier: Anslut en Windows Server-virtuell dator till en hanterad domän

Azure Active Directory Domain Services (Azure AD DS) tillhandahåller hanterade domän tjänster som domän anslutning, grup princip, LDAP, Kerberos/NTLM-autentisering som är helt kompatibelt med Windows Server Active Directory. Med en Azure AD DS-hanterad domän kan du tillhandahålla funktioner för domän anslutning och hantering av virtuella datorer i Azure. Den här självstudien visar hur du skapar en virtuell Windows Server-dator och ansluter den till en hanterad Azure AD DS-domän.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en virtuell Windows Server-dator
> * Ansluta till den virtuella Windows Server-datorn till ett virtuellt Azure-nätverk
> * Anslut den virtuella datorn till den hanterade domänen i Azure AD DS

Om du inte har en Azure-prenumeration kan du [skapa ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien behöver du följande resurser:

* En aktiv Azure-prenumeration.
    * [Skapa ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)om du inte har någon Azure-prenumeration.
* En Azure Active Directory klient som är associerad med din prenumeration, antingen synkroniserad med en lokal katalog eller en katalog som endast är moln.
    * Om det behövs kan du [skapa en Azure Active Directory klient][create-azure-ad-tenant] eller [associera en Azure-prenumeration med ditt konto][associate-azure-ad-tenant].
* En Azure Active Directory Domain Services hanterad domän aktive rad och konfigurerad i Azure AD-klienten.
    * Om det behövs kan du [skapa och konfigurera en Azure Active Directory Domain Services-instans][create-azure-ad-ds-instance].
* Ett användar konto som är medlem i administratörs gruppen för *Azure AD DC* i din Azure AD-klient.
    * Kontrol lera att Azure AD Connect hash-synkronisering av lösen ord eller lösen ords återställning via självbetjäning har utförts så att kontot kan logga in på Azure AD DS-hanterad domän.

Om du redan har en virtuell dator som du vill ansluta till, kan du gå vidare till avsnittet för att [ansluta den virtuella datorn till den hanterade domänen i Azure AD DS](#join-the-vm-to-the-azure-ad-ds-managed-domain).

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

I den här självstudien skapar du en virtuell Windows Server-dator för att ansluta till din Azure AD DS-hanterade domän med hjälp av Azure Portal. Börja med att logga in på [Azure Portal](https://portal.azure.com)för att komma igång.

## <a name="create-a-windows-server-virtual-machine"></a>Skapa en virtuell Windows Server-dator

För att se hur du ansluter en dator till en Azure AD DS-hanterad domän, ska vi skapa en virtuell Windows Server-dator. Den här virtuella datorn är ansluten till ett virtuellt Azure-nätverk som tillhandahåller anslutning till den hanterade Azure AD DS-domänen. Processen för att ansluta till en Azure AD DS-hanterad domän är detsamma som att ansluta till en vanlig lokal Active Directory Domain Services domän.

Om du redan har en virtuell dator som du vill ansluta till, kan du gå vidare till avsnittet för att [ansluta den virtuella datorn till den hanterade domänen i Azure AD DS](#join-the-vm-to-the-azure-ad-ds-managed-domain).

1. I det övre vänstra hörnet av Azure Portal väljer du **+ skapa en resurs**.
2. Från **Kom igång**väljer du **Windows Server 2016 Data Center**.

    ![Välj att skapa en virtuell Windows Server 2016 datacenter-dator i Azure Portal](./media/join-windows-vm/select-vm-image.png)

3. I fönstret **grundläggande** inställningar konfigurerar du kärn inställningarna för den virtuella datorn. Lämna standardinställningarna för *tillgänglighets alternativ*, *bild*och *storlek*.

    | Parameter            | Föreslaget värde   |
    |----------------------|-------------------|
    | Resource group       | Välj eller skapa en resurs grupp, till exempel *myResourceGroup* |
    | Namn på virtuell dator | Ange ett namn för den virtuella datorn, till exempel *myVM* |
    | Region               | Välj region för att skapa din virtuella dator i, t. ex. *USA, östra* |
    | Användarnamn             | Ange ett användar namn för det lokala administratörs kontot som ska skapas på den virtuella datorn, till exempel *azureuser* |
    | lösenordsinställning             | Ange och bekräfta sedan ett säkert lösen ord för den lokala administratören som ska skapas på den virtuella datorn. Ange inte autentiseringsuppgifter för ett domän användar konto. |

4. Som standard är virtuella datorer som skapats i Azure inte tillgängliga från Internet. Den här konfigurationen hjälper till att förbättra säkerheten på den virtuella datorn och minskar risken för potentiella angrepp. I nästa steg i den här självstudien måste du ansluta till den virtuella datorn med Remote Desktop Protocol (RDP) och sedan ansluta Windows Server till den hanterade domänen i Azure AD DS.

    När RDP är aktiverat, kommer automatiska inloggnings attacker att uppstå, vilket kan inaktivera konton med vanliga namn som *administratör* eller *administratör* på grund av flera misslyckade inloggnings försök. RDP bör endast aktive ras vid behov, och begränsas till en uppsättning auktoriserade IP-intervall. [Azure just-in-Time-åtkomst för virtuella datorer][jit-access] som en del av Azure Security Center kan aktivera dessa korta, begränsade RDP-sessioner. Du kan också [skapa och använda en Azure skydds-värd (för närvarande i för hands version)][azure-bastion] för att bara tillåta åtkomst via Azure Portal via SSL.

    För den här själv studie kursen ska du aktivera RDP-anslutningar till den virtuella datorn manuellt.

    Under **offentliga inkommande portar**väljer du alternativet för att **tillåta valda portar**. Välj *RDP*på den nedrullningsbara menyn för **Välj inkommande portar**.

5. När du är färdig **väljer du nästa: Diskar**.
6. Välj *standard SSD*på den nedrullningsbara menyn för **typ av operativ system disk**och välj **sedan Nästa: Nätverk**.
7. Din virtuella dator måste ansluta till ett virtuellt nätverk i Azure som kan kommunicera med under nätet som din Azure AD DS-hanterade domän distribueras till. Vi rekommenderar att en Azure AD DS-hanterad domän distribueras till ett eget dedikerat undernät. Distribuera inte den virtuella datorn i samma undernät som din Azure AD DS-hanterade domän.

    Det finns två huvudsakliga sätt att distribuera den virtuella datorn och ansluta till ett lämpligt virtuellt nätverks under nät:
    
    * Skapa ett, eller Välj ett befintligt undernät i samma virtuella nätverk som din Azure AD DS-hanterade domän har distribuerats.
    * Välj ett undernät i ett virtuellt Azure-nätverk som är anslutet till det med [Azure Virtual Network][vnet-peering]-peering.
    
    Om du väljer ett undernät för virtuellt nätverk som inte är anslutet till under nätet för din Azure AD DS-instans kan du inte ansluta den virtuella datorn till den hanterade domänen. I den här självstudien ska vi skapa ett nytt undernät i det virtuella Azure-nätverket.

    I fönstret **nätverk** väljer du det virtuella nätverk där din Azure AD DS-hanterade domän distribueras, till exempel *myVnet*
8. I det här exemplet visas det befintliga *DomainServices* -undernätet som den Azure AD DS-hanterade domänen är ansluten till. Anslut inte den virtuella datorn till det här under nätet. Om du vill skapa ett undernät för den virtuella datorn väljer du **Hantera under näts konfiguration**.

    ![Välj att hantera under näts konfigurationen i Azure Portal](./media/join-windows-vm/manage-subnet.png)

9. Välj **+ undernät**och ange sedan ett namn för under nätet, till exempel *ManagedVMs*. Ange ett **adress intervall (CIDR-block)** , till exempel *10.1.1.0/24*. Kontrol lera att detta IP-adressintervall inte överlappar andra befintliga Azure-eller lokala adress intervall. Lämna de andra alternativen som standardvärden och välj sedan **OK**.

    ![Skapa en under näts konfiguration i Azure Portal](./media/join-windows-vm/create-subnet.png)

10. Det tar några sekunder att skapa under nätet. När den har skapats väljer du *X* för att stänga under näts fönstret.
11. Gå tillbaka till fönstret **nätverk** och skapa en virtuell dator genom att välja det undernät som du skapade i den nedrullningsbara menyn, till exempel *ManagedVMs*. Se till att du väljer rätt undernät och distribuera inte den virtuella datorn i samma undernät som din Azure AD DS-hanterade domän.
12. Lämna de andra alternativen som standardvärden och välj sedan **hantering**.
13. Ange att startdiagnostik ska *stängas av*. Lämna de andra alternativen som standardvärden och välj sedan **Granska + skapa**.
14. Granska inställningarna för den virtuella datorn och välj sedan **skapa**.

Det tar några minuter att skapa den virtuella datorn. I Azure Portal visas distributionens status. När den virtuella datorn är klar väljer **du gå till resurs**.

![Gå till den virtuella dator resursen i Azure Portal när den har skapats](./media/join-windows-vm/vm-created.png)

## <a name="connect-to-the-windows-server-vm"></a>Anslut till den virtuella Windows Server-datorn

Nu ska vi ansluta till den nya virtuella Windows Server-datorn med RDP och ansluta till den hanterade domänen i Azure AD DS. Använd de autentiseringsuppgifter för lokal administratör som du angav när den virtuella datorn skapades i föregående steg, inte några befintliga domänautentiseringsuppgifter.

1. I fönstret **Översikt** väljer du **Anslut**.

    ![Anslut till den virtuella Windows-datorn i Azure Portal](./media/join-windows-vm/connect-to-vm.png)

1. Välj alternativet för att *Ladda ned RDP-filen*. Spara den här RDP-filen i webbläsaren.
1. Öppna den hämtade RDP-filen för att ansluta till den virtuella datorn. Välj **Anslut** om du uppmanas att göra det.
1. Ange de autentiseringsuppgifter för lokal administratör som du angav i föregående steg för att skapa den virtuella datorn, till exempel *localhost\azureuser*
1. Om du ser en certifikat varning under inloggnings processen väljer du **Ja** eller **Fortsätt** för att ansluta.

## <a name="join-the-vm-to-the-azure-ad-ds-managed-domain"></a>Anslut den virtuella datorn till den hanterade domänen i Azure AD DS

När den virtuella datorn har skapats och en RDP-anslutning upprättar kan du nu ansluta den virtuella Windows Server-datorn till den hanterade domänen i Azure AD DS. Den här processen är samma som en dator som ansluter till en vanlig lokal Active Directory Domain Services domän.

1. **Serverhanteraren** bör öppnas som standard när du loggar in på den virtuella datorn. Annars väljer du **Serverhanteraren**på **Start** -menyn.
1. I den vänstra rutan i fönstret **Serverhanteraren** väljer du **lokal server**. Välj **arbets grupp**under **Egenskaper** i den högra rutan.

    ![Öppna Serverhanteraren på den virtuella datorn och redigera arbets grupps egenskapen](./media/join-windows-vm/server-manager.png)

1. I fönstret **system egenskaper** väljer du **ändra** för att ansluta till den HANTERAde Azure AD DS-domänen.

    ![Välj att ändra egenskaper för arbets grupp eller domän](./media/join-windows-vm/change-domain.png)

1. I rutan **domän** anger du namnet på din Azure AD DS-hanterade domän, till exempel *contoso.com*, och väljer sedan **OK**.

    ![Ange den Azure AD DS-hanterade domänen som ska anslutas](./media/join-windows-vm/join-domain.png)

1. Ange domänautentiseringsuppgifter för att ansluta till domänen. Använd autentiseringsuppgifterna för en användare som tillhör gruppen *Azure AD DC-administratörer* . Endast medlemmar i den här gruppen har behörighet att ansluta datorer till den hanterade Azure AD DS-domänen. Kontoautentiseringsuppgifter kan anges på något av följande sätt:

    * **UPN-format** (rekommenderas) – ange suffixet User Principal Name (UPN) för användar kontot, enligt konfigurationen i Azure AD. UPN-suffixet för användaren *contosoadmin* skulle till exempel vara `contosoadmin@contoso.onmicrosoft.com`. Det finns ett par vanliga användnings fall där UPN-formatet kan användas på ett tillförlitligt sätt för att logga in på domänen snarare än *sAMAccountName* -formatet:
        * Om en användares UPN-prefix är långt, till exempel *deehasareallylongname*, kan *sAMAccountName* skapas automatiskt.
        * Om flera användare har samma UPN-prefix i din Azure AD-klient, till exempel *Dee*, kan deras *sAMAccountName* -format skapas automatiskt.
    * **SAMAccountName-format** – ange konto namnet i *sAMAccountName* -formatet. Till exempel skulle *sAMAccountName* för User *contosoadmin* vara `CONTOSO\contosoadmin`.

1. Det tar några sekunder att ansluta till den hanterade domänen i Azure AD DS. När du är klar, välkomnar följande meddelande till domänen:

    ![Välkommen till domänen](./media/join-windows-vm/join-domain-successful.png)

    Klicka på **OK** för att fortsätta.

1. Om du vill slutföra processen för att ansluta till den hanterade Azure AD DS-domänen startar du om den virtuella datorn.

> [!TIP]
> Du kan domän ansluta till en virtuell dator med hjälp av PowerShell med cmdleten [Add-Computer][add-computer] . Följande exempel ansluter till *contoso* -domänen och startar sedan om den virtuella datorn. När du uppmanas till det anger du autentiseringsuppgifterna för en användare som tillhör gruppen *Azure AD DC-administratörer* :
>
> `Add-Computer -DomainName CONTOSO -Restart`
>
> Om du vill ansluta till en virtuell dator utan att ansluta till den och manuellt konfigurera anslutningen kan du också utforska användningen av cmdleten [set-AzVmAdDomainExtension][set-azvmaddomainextension] Azure PowerShell.

När den virtuella Windows Server-datorn har startats om flyttas alla principer som tillämpas i den hanterade Azure AD DS-domänen till den virtuella datorn. Nu kan du logga in på den virtuella Windows Server-datorn med rätt domänautentiseringsuppgifter.

## <a name="clean-up-resources"></a>Rensa resurser

I nästa självstudie använder du den här virtuella Windows Server-datorn för att installera hanterings verktygen som du kan använda för att administrera den hanterade domänen i Azure AD DS. Om du inte vill fortsätta i den här själv studie serien kan du läsa följande rensnings steg för att [inaktivera RDP](#disable-rdp) eller [ta bort den virtuella datorn](#delete-the-vm). Annars [fortsätter du till nästa självstudie](#next-steps).

### <a name="un-join-the-vm-from-azure-ad-ds-managed-domain"></a>Ta bort anslutningen till den virtuella datorn från Azure AD DS-hanterad domän

Om du vill ta bort den virtuella datorn från den hanterade domänen i Azure AD DS följer du stegen igen för att [ansluta den virtuella datorn till en domän](#join-the-vm-to-the-azure-ad-ds-managed-domain). I stället för att ansluta till den hanterade domänen i Azure AD DS väljer du att ansluta till en arbets grupp, till exempel standard *arbets gruppen*. När den virtuella datorn har startats om tas datorobjektet bort från den hanterade domänen i Azure AD DS.

Om du [tar bort den virtuella](#delete-the-vm) datorn utan att koppla från domänen, lämnas ett överblivna dator objekt i Azure AD DS.

### <a name="disable-rdp"></a>Inaktivera RDP

Om du fortsätter att använda den virtuella Windows Server-datorn som skapades i den här självstudien för att köra egna program eller arbets belastningar, så kom ihåg att RDP var öppet via Internet. För att förbättra säkerheten och minska risken för angrepp bör RDP inaktive ras via Internet. Utför följande steg för att inaktivera RDP till den virtuella Windows Server-datorn via Internet:

1. Välj **resurs grupper** på den vänstra menyn.
1. Välj din resurs grupp, till exempel *myResourceGroup*.
1. Välj din virtuella dator, till exempel *myVM*, och välj sedan *nätverk*.
1. Under **inkommande nätverks säkerhets regler** för nätverks säkerhets gruppen väljer du den regel som tillåter RDP och väljer sedan **ta bort**. Det tar några sekunder att ta bort den inkommande säkerhets regeln.

### <a name="delete-the-vm"></a>Ta bort den virtuella datorn

Om du inte använder den här virtuella Windows Server-datorn tar du bort den virtuella datorn med följande steg:

1. Välj **resurs grupper** på den vänstra menyn.
1. Välj din resurs grupp, till exempel *myResourceGroup*.
1. Välj den virtuella datorn, till exempel *myVM*, och välj sedan **ta bort**. Bekräfta borttagningen av resurs genom att välja **Ja** . Det tar några minuter att ta bort den virtuella datorn.
1. När den virtuella datorn tas bort väljer du OS-disken, nätverkskortet och andra resurser med *myVM-* prefixet och tar bort dem.

## <a name="troubleshoot-domain-join-issues"></a>Felsöka problem med domän anslutning

Den virtuella Windows Server-datorn ska kunna ansluta till den hanterade domänen i Azure AD DS, samma sätt som en vanlig lokal dator skulle ansluta till en Active Directory Domain Services domän. Om den virtuella Windows Server-datorn inte kan ansluta till den hanterade domänen i Azure AD DS, indikerar det att det finns ett anslutnings-eller autentiseringsuppgifter relaterat problem. Läs följande fel söknings avsnitt för att lyckas ansluta till den hanterade domänen.

### <a name="connectivity-issues"></a>Anslutningsproblem

Om du inte får en uppmaning om att fråga om autentiseringsuppgifter för att ansluta till domänen, finns det ett anslutnings problem. Den virtuella datorn kan inte ansluta till den Azure AD DS-hanterade domänen i det virtuella nätverket.

Försök att ansluta den virtuella Windows Server-datorn till den hanterade domänen igen efter att ha försökt med de här fel söknings stegen.

* Kontrol lera att den virtuella datorn är ansluten till samma virtuella nätverk som Azure AD DS är aktiverat i eller har en peer-ansluten nätverks anslutning.
* Försök att pinga DNS-domännamnet för den hanterade domänen, `ping contoso.com`till exempel.
    * Om ping-begäran Miss lyckas kan du försöka pinga IP-adresserna för den hanterade `ping 10.0.0.4`domänen, till exempel. IP-adressen för din miljö visas på sidan *Egenskaper* när du väljer den hanterade Azure AD DS-domänen från listan över Azure-resurser.
    * Om du kan pinga IP-adressen, men inte domänen, kan DNS vara felaktigt konfigurerat. Bekräfta att IP-adresserna för den hanterade domänen är konfigurerade som DNS-servrar för det virtuella nätverket.
* Försök att tömma DNS-matcharens cacheminne på den virtuella datorn med `ipconfig /flushdns` kommandot.

### <a name="credentials-related-issues"></a>Problem som rör autentiseringsuppgifter

Om du får en uppmaning om att ange autentiseringsuppgifter för att ansluta till domänen, men sedan ett fel när du har angett dessa autentiseringsuppgifter, kan den virtuella datorn ansluta till den hanterade domänen i Azure AD DS. De angivna autentiseringsuppgifterna låter inte den virtuella datorn ansluta till den hanterade domänen i Azure AD DS.

Försök att ansluta den virtuella Windows Server-datorn till den hanterade domänen igen efter att ha försökt med de här fel söknings stegen.

* Kontrol lera att det användar konto som du anger tillhör gruppen *AAD DC-administratörer* .
* Försök att använda UPN-formatet för att ange autentiseringsuppgifter, `contosoadmin@contoso.onmicrosoft.com`till exempel. Om det finns många användare med samma UPN-prefix i din klient organisation eller om ditt UPN-prefix är för långt, kan *sAMAccountName* för ditt konto skapas automatiskt. I dessa fall kan *sAMAccountName* -formatet för ditt konto skilja sig från vad du förväntar dig eller använder i din lokala domän.
* Kontrol lera att du har [aktiverat][password-sync] Lösenordssynkronisering till din hanterade domän. Utan det här konfigurations steget finns de nödvändiga lösen ords-hasharna inte i den hanterade Azure AD DS-domänen för att korrekt autentisera ditt inloggnings försök.
* Vänta tills Lösenordssynkronisering har slutförts. När ett användar kontos lösen ord ändras uppdaterar en automatisk Bakgrundssynkronisering från Azure AD lösen ordet i Azure AD DS. Det tar lite tid för lösen ordet att vara tillgängligt för domän kopplings användning.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa en virtuell Windows Server-dator
> * Ansluta till den virtuella Windows Server-datorn till ett virtuellt Azure-nätverk
> * Anslut den virtuella datorn till den hanterade domänen i Azure AD DS

Om du vill administrera din Azure AD DS-hanterade domän konfigurerar du en virtuell hanterings dator med hjälp av Active Directory Administrationscenter (ADAC).

> [!div class="nextstepaction"]
> [Installera administrations verktyg på en virtuell hanterings dator](tutorial-create-management-vm.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[vnet-peering]: ../virtual-network/virtual-network-peering-overview.md
[password-sync]: active-directory-ds-getting-started-password-sync.md
[add-computer]: /powershell/module/microsoft.powershell.management/add-computer
[jit-access]: ../security-center/security-center-just-in-time.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[set-azvmaddomainextension]: /powershell/module/az.compute/set-azvmaddomainextension
