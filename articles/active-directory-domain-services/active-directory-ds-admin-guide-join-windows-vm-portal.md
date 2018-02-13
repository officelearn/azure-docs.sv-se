---
title: "Azure Active Directory Domain Services: Anslut en Windows Server-VM till en hanterad domän | Microsoft Docs"
description: Ansluta till en virtuell dator med Windows Server till Azure AD DS
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 29316313-c76c-4fb9-8954-5fa5ec82609e
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: maheshu
ms.openlocfilehash: 7b5c23f1f4b6180d8b664f1371ccfd8a075572e6
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2018
---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain"></a>Anslut en Windows Server-virtuell dator till en hanterad domän
Den här artikeln visar hur du distribuerar en virtuell dator med Windows Server med hjälp av Azure portal. Den visar hur du ansluter till den virtuella datorn till en hanterad Azure Active Directory Domain Services (Azure AD DS)-domän.

## <a name="step-1-create-a-windows-server-virtual-machine"></a>Steg 1: Skapa en virtuell dator med Windows Server
Om du vill skapa en virtuell Windows-dator som är ansluten till det virtuella nätverket som du har aktiverat Azure AD DS, gör du följande:

1. Logga in på [Azure-portalen](http://portal.azure.com).
2. Längst upp i den vänstra rutan, Välj **ny**.
3. Välj **Compute**, och välj sedan **Windows Server 2016 Datacenter**.

    ![Windows Server 2016 Datacenter-länk](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-image.png)

4. I den **grunderna** rutan i guiden Konfigurera grundläggande inställningar för den virtuella datorn.

    ![Fönstret grunderna](./media/active-directory-domain-services-admin-guide/create-windows-vm-basics.png)

    > [!TIP]
    > Användarnamn och lösenord som du anger här finns för ett lokalt administratörskonto som används för att logga in på den virtuella datorn. Välj ett starkt lösenord för att skydda den virtuella datorn mot lösenord brute force-attacker. Ange inte här autentiseringsuppgifter för ett domänanvändarkonto.
    >

5. Välj en **storlek** för den virtuella datorn. Om du vill visa mer storlekar, Välj **visa alla** eller ändra den **stöds disktyp** filter.

    ![Fönstret ”Välj en storlek”](./media/active-directory-domain-services-admin-guide/create-windows-vm-size.png)

6. I den **inställningar** rutan, Välj det virtuella nätverk som din Azure AD DS-hanterade domän har distribuerats. Välj ett annat undernät än den som din hanterade domän har distribuerats till. Behåll standardinställningarna för de andra inställningarna och välj sedan **OK**.

    ![Inställningar för virtuella nätverk för den virtuella datorn](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-vnet.png)

    > [!TIP]
    > **Välj rätt virtuellt nätverk och undernät.**
    >
    > Välj antingen det virtuella nätverk där din hanterade domän distribueras eller ett virtuellt nätverk som är ansluten till den med hjälp av virtuella nätverk peering. Om du väljer ett virtuellt nätverk som är kopplade till varandra, kan du inte ansluta den virtuella datorn till den hanterade domänen.
    >
    > Vi rekommenderar att du distribuerar din hanterade domän i ett dedikerat undernät. Välj därför inte undernätet som du har aktiverat din hanterade domän.

7. Behåll standardinställningarna för de andra inställningarna och välj sedan **OK**.
8. På den **inköp** , granskar du inställningarna och väljer sedan **OK** att distribuera den virtuella datorn.
9. Distribution av Virtuella datorer är fäst på instrumentpanelen för Azure-portalen.

    ![Klart](./media/active-directory-domain-services-admin-guide/create-windows-vm-done.png)
10. När distributionen är klar kan du visa information om den virtuella datorn på den **översikt** sidan.


## <a name="step-2-connect-to-the-windows-server-virtual-machine-by-using-the-local-administrator-account"></a>Steg 2: Anslut till den virtuella datorn i Windows Server med hjälp av det lokala administratörskontot
Anslut sedan den nyligen skapade virtuella för Windows Server att ansluta till domänen. Använd autentiseringsuppgifter för lokal administratör som du angav när du skapade den virtuella datorn.

För att ansluta till den virtuella datorn, gör du följande:

1. I den **översikt** väljer **Anslut**.  
    En Remote Desktop Protocol (RDP)-fil skapas och hämtas.

    ![Ansluta till Windows-dator](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)

2. Öppna den hämtade RDP-filen för att ansluta till den virtuella datorn. Om du uppmanas, väljer **Anslut**.
3. Vid inloggningsskärmen, ange din **lokal administratörsbehörighet**, som du angav när du skapade den virtuella datorn (till exempel *localhost\mahesh*).
4. Om du får en certifikatvarning under inloggning kan fortsätta med anslutningen genom att välja **Ja** eller **Fortsätt**.

Nu bör du vara inloggad på den nyligen skapade virtuella Windows-datorn med dina autentiseringsuppgifter som lokal administratör. Nästa steg är att ansluta den virtuella datorn till domänen.


## <a name="step-3-join-the-windows-server-virtual-machine-to-the-azure-ad-ds-managed-domain"></a>Steg 3: Anslut den virtuella datorn i Windows Server till Azure AD DS-hanterad domän
För att ansluta till den virtuella datorn i Windows Server till Azure AD DS-hanterade domänen måste du göra följande:

1. Ansluta till Windows Server-VM, som visas i ”steg 2”. På den **starta** skärmen öppnar **Serverhanteraren**.
2. I den vänstra rutan i **Serverhanteraren** väljer **lokal Server**.

    ![Serverhanteraren på den virtuella datorn](./media/active-directory-domain-services-admin-guide/join-domain-server-manager.png)

3. Under **egenskaper**väljer **arbetsgrupp**. 
4. I den **Systemegenskaper** väljer **ändra** att ansluta till domänen.

    ![Fönstret Systemegenskaper](./media/active-directory-domain-services-admin-guide/join-domain-system-properties.png)

5. I den **domän** , ange namnet på din Azure AD DS-hanterade domän och välj sedan **OK**.

    ![Ange domänen som ska sammanfogas](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-domain.png)

6. Du uppmanas att ange dina autentiseringsuppgifter för att ansluta till domänen. Kontrollera att du har angett autentiseringsuppgifterna för en *användare som tillhör administratörsgruppen för Azure AD-Domänkontrollant*. Endast medlemmar i den här gruppen har behörighet att ansluta datorer till den hanterade domänen.

    ![Fönstret Windows-säkerhet för att ange autentiseringsuppgifter](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-credentials.png)

7. Du kan ange autentiseringsuppgifter i något av följande sätt:

   * **UPN-format**: (rekommenderas) Ange användarens huvudnamn (UPN) suffix för användarkontot som konfigurerats i Azure AD. I det här exemplet UPN-suffix för användaren *bob* är  *bob@domainservicespreview.onmicrosoft.com* .

   * **SAMAccountName format**: du kan ange kontonamnet i formatet SAMAccountName. I det här exemplet är användaren *bob* måste ange *CONTOSO100\bob*.

     > [!TIP]
     > **Vi rekommenderar att du använder UPN-format för att ange autentiseringsuppgifter.**
     >
     > Om en användares UPN-prefixet är alltför lång (till exempel *joehasareallylongname*), SAMAccountName kanske automatiskt genererade. Om flera användare har samma UPN-prefix (till exempel *bob*) i Azure AD-klient SAMAccountName format kanske automatiskt genererade av tjänsten. I dessa fall kan UPN-formatet användas på ett tillförlitligt sätt att logga in på domänen.
     >

8. När du har anslutit en domän, följande meddelande hälsar dig Välkommen till domänen.

    ![Välkommen till domänen](./media/active-directory-domain-services-admin-guide/join-domain-done.png)

9. Slutför anslutning till domänen, starta om den virtuella datorn.

## <a name="troubleshoot-joining-a-domain"></a>Felsökning av anslutning till en domän
### <a name="connectivity-issues"></a>Anslutningsproblem
Om den virtuella datorn kan inte hitta en domän kan du prova en eller flera av följande:

* Se till att den virtuella datorn är ansluten till samma virtuella nätverk som det som du har aktiverat Azure AD DS i. Om det inte är anslutet den virtuella datorn kan inte ansluta till domänen och därför är det går inte att ansluta till domänen.

* Kontrollera att den virtuella datorn är i ett virtuellt nätverk som i sin tur är ansluten till det virtuella nätverket som du har aktiverat Azure AD DS.

* Försök att pinga domänen med namnet på den hanterade domänen (till exempel *pinga contoso100.com*). Om du inte vill det. Försök att pinga IP-adresser för den domän som visas på sidan där du har aktiverat Azure AD DS (till exempel *pinga 10.0.0.4*). Om du kan pinga IP-adressen men inte i domänen, kan DNS är felaktigt konfigurerad. Kontrollera om IP-adresserna för domänen är konfigurerad som DNS-servrar för det virtuella nätverket.

* Försök att tömma DNS-matchare cachen på den virtuella datorn (*ipconfig/flushdns*).

Om ett fönster visas som frågar om autentiseringsuppgifter för att ansluta till domänen kan har du inte problem med nätverksanslutningen.

### <a name="credentials-related-issues"></a>Problem med autentiseringsuppgifter
Om du har problem med autentiseringsuppgifter och inte går att ansluta till domänen, försök med något av följande:

* Försök med UPN-format för att ange autentiseringsuppgifter. Om det finns flera användare med samma UPN-prefix i din klient eller om UPN-prefixet är alltför långa, kanske automatiskt genererade SAMAccountName för ditt konto. Därför kan SAMAccountName format för ditt konto skilja sig från vad du förväntar dig eller använda i din lokala domän.

* Försök att använda autentiseringsuppgifterna för ett konto som tillhör den *AAD DC administratörer* grupp.

* Kontrollera att du har [aktiverat Lösenordssynkronisering](active-directory-ds-getting-started-password-sync.md) enligt anvisningarna i Kom igång-guide.

* Kontrollera att du använder UPN för användaren som konfigurerats i Azure AD (till exempel  *bob@domainservicespreview.onmicrosoft.com* ) att logga in.

* Se till att du har väntat tillräckligt länge för synkronisering av lösenord för att slutföra som anges i Kom igång-guide.

## <a name="related-content"></a>Relaterat innehåll
* [Azure AD DS-komma igång](active-directory-ds-getting-started.md)
* [Administrera en Azure AD DS-hanterad domän](active-directory-ds-admin-guide-administer-domain.md)
