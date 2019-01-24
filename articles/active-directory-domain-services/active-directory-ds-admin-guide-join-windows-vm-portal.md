---
title: 'Azure Active Directory Domain Services: Anslut en Windows Server-VM till en hanterad domän | Microsoft Docs'
description: Ansluta en Windows Server-dator till Azure AD DS
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 29316313-c76c-4fb9-8954-5fa5ec82609e
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/21/2018
ms.author: ergreenl
ms.openlocfilehash: fc55fcef9977abcd8c40752d459088c1132cf2c1
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54850373"
---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain"></a>Anslut en Windows Server-virtuell dator till en hanterad domän
Den här artikeln visar hur du distribuerar en Windows Server-dator med hjälp av Azure portal. Den visar sedan hur du kopplar den virtuella datorn till en hanterad Azure Active Directory Domain Services (Azure AD DS)-domän.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="step-1-create-a-windows-server-virtual-machine"></a>Steg 1: Skapa en virtuell Windows Server-dator
Om du vill skapa en Windows-dator som är ansluten till det virtuella nätverket där du har aktiverat Azure AD DS, gör du följande:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Högst upp i det vänstra fönstret, Välj **New**.
3. Välj **Compute**, och välj sedan **Windows Server 2016 Datacenter**.

    ![Windows Server 2016 Datacenter-länk](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-image.png)

4. I den **grunderna** fönstret i guiden Konfigurera grundläggande inställningar för den virtuella datorn.

    ![Fönstret grunder](./media/active-directory-domain-services-admin-guide/create-windows-vm-basics.png)

    > [!TIP]
    > Användarnamnet och lösenordet som du anger här är för ett lokalt administratörskonto som används för att logga in på den virtuella datorn. Välj ett starkt lösenord för att skydda den virtuella datorn mot lösenord brute force-attacker. Ange inte ett domänanvändarkonto autentiseringsuppgifter här.
    >

5. Välj en **storlek** för den virtuella datorn. Om du vill visa fler storlekar väljer **visa alla** eller ändra den **disktyp som stöds** filter.

    ![Fönstret ”Välj en storlek”](./media/active-directory-domain-services-admin-guide/create-windows-vm-size.png)

6. I den **inställningar** fönstret, Välj det virtuella nätverk som din Azure AD DS-hanterad domän har distribuerats. Välj ett annat undernät än den som din hanterade domän distribueras till. Behåll standardinställningarna för de andra inställningarna och välj sedan **OK**.

    ![Inställningar för virtuella nätverk för den virtuella datorn](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-vnet.png)

    > [!TIP]
    > **Välj rätt virtuellt nätverk och undernät.**
    >
    > Välj antingen det virtuella nätverket där den hanterade domänen distribueras eller ett virtuellt nätverk som är ansluten till den med hjälp av virtuella nätverks-peering. Om du väljer ett virtuellt nätverk som är anslutna kan ansluta du inte den virtuella datorn till den hanterade domänen.
    >
    > Vi rekommenderar att du distribuerar din hanterade domän i ett dedikerat undernät. Välj därför inte undernätet där du har aktiverat din hanterade domän.

7. Behåll standardinställningarna för de andra inställningarna och välj sedan **OK**.
8. På den **köp** , granskar du inställningarna och välj sedan **OK** att distribuera den virtuella datorn.
9. VM-distributionen är fäst på instrumentpanelen för Azure portal.

    ![Klart](./media/active-directory-domain-services-admin-guide/create-windows-vm-done.png)
10. När distributionen är klar kan du visa information om den virtuella datorn på den **översikt** sidan.


## <a name="step-2-connect-to-the-windows-server-virtual-machine-by-using-the-local-administrator-account"></a>Steg 2: Ansluta till Windows Server-datorn med det lokala administratörskontot
Anslut sedan till den nyligen skapade Windows Server-datorn för att ansluta till domänen. Använd autentiseringsuppgifter för lokal administratör som du angav när du skapade den virtuella datorn.

Utför följande steg för att ansluta till den virtuella datorn:

1. I den **översikt** väljer **Connect**.  
    En Remote Desktop Protocol (RDP)-fil skapas och hämtas.

    ![Ansluta till Windows-dator](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)

2. Öppna den hämtade RDP-filen för att ansluta till den virtuella datorn. Välj **Anslut** om du uppmanas att göra det.
3. Ange din **lokal administratörsbehörighet**, som du angav när du skapade den virtuella datorn (till exempel *localhost\mahesh*).
4. Om du ser en certifikatvarning under inloggningen väljer **Ja** eller **Fortsätt** att ansluta.

Nu kan bör du vara inloggad till den nya Windows-datorn med dina autentiseringsuppgifter som lokal administratör. Nästa steg är att ansluta den virtuella datorn till domänen.


## <a name="step-3-join-the-windows-server-virtual-machine-to-the-azure-ad-ds-managed-domain"></a>Steg 3: Ansluta Windows Server-dator med Azure AD DS-hanterad domän
Utför följande steg för att ansluta till Windows Server-dator med Azure AD DS-hanterad domän:

1. Ansluta till Windows Server-dator som du ser i ”steg 2”. På den **starta** skärmen genom att öppna **Serverhanteraren**.
2. I den vänstra rutan i den **Serverhanteraren** väljer **lokal Server**.

    ![Serverhanteraren på den virtuella datorn](./media/active-directory-domain-services-admin-guide/join-domain-server-manager.png)

3. Under **egenskaper**väljer **arbetsgrupp**.
4. I den **Systemegenskaper** väljer **ändra** att ansluta till domänen.

    ![Fönstret Systemegenskaper](./media/active-directory-domain-services-admin-guide/join-domain-system-properties.png)

5. I den **domän** , anger namnet på din Azure AD DS-hanterad domän, och välj sedan **OK**.

    ![Ange domänen som ska anslutas](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-domain.png)

6. Du uppmanas att ange dina autentiseringsuppgifter för att ansluta till domänen. Använd autentiseringsuppgifter för en *användare som tillhör administratörsgruppen för Azure AD-DC*. Endast medlemmar i den här gruppen har behörighet att ansluta datorer till den hanterade domänen.

    ![Fönstret Windows-säkerhet för att ange autentiseringsuppgifter](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-credentials.png)

7. Du kan ange autentiseringsuppgifter i någon av följande sätt:

   * **UPN-formatet**: (Rekommenderas) Ange suffixet för användarens huvudnamn (UPN) för användarkontot som konfigurerats i Azure AD. I det här exemplet UPN-suffix för användaren *bob* är *bob@domainservicespreview.onmicrosoft.com*.

   * **SAMAccountName format**: Du kan ange kontonamnet i formatet SAMAccountName format. I det här exemplet är användaren *bob* skulle behöva ange *CONTOSO100\bob*.

     > [!TIP]
     > **Vi rekommenderar att du använder UPN-formatet för att ange autentiseringsuppgifter.**
     >
     > Om en användares UPN prefix alltför långa (till exempel *joehasareallylongname*), SAMAccountName kanske automatiskt genererade. Om flera användare har samma UPN-prefix (till exempel *bob*) i Azure AD-klienten, deras SAMAccountName-format vara automatiskt genererade av tjänsten. I dessa fall kan UPN-formatet användas på ett tillförlitligt sätt att logga in på domänen.
     >

8. När du har har anslutits till en domän, följande meddelande hälsar dig Välkommen till domänen.

    ![Välkommen till domänen](./media/active-directory-domain-services-admin-guide/join-domain-done.png)

9. Starta om den virtuella datorn för att slutföra anslutning till domänen.

## <a name="troubleshoot-joining-a-domain"></a>Felsöka ansluta till en domän
### <a name="connectivity-issues"></a>Anslutningsproblem
Om den virtuella datorn inte kunde hitta domänen, kan du prova följande felsökningssteg:

* Kontrollera den virtuella datorn är ansluten till samma virtuella nätverk till Azure AD DS är aktiverat i. Annars är den virtuella datorn inte kan ansluta till eller ansluta till domänen.

* Kontrollera den virtuella datorn är i ett virtuellt nätverk som i sin tur är ansluten till det virtuella nätverket som Azure AD DS är aktiverat i.

* Försök att pinga DNS-domännamnet för den hanterade domänen (till exempel *pinga contoso100.com*). Om du inte gör kan du försöka pinga IP-adresser för den domän som visas på sidan där du har aktiverat Azure AD DS (till exempel *pinga 10.0.0.4*). Om du kan pinga IP-adress men inte domänen, kan DNS konfigureras felaktigt. Kontrollera om IP-adresserna för domänen är konfigurerad som DNS-servrar för det virtuella nätverket.

* Testa DNS-matchare cachen rensas på den virtuella datorn (*ipconfig/flushdns*).

Om ett fönster visas som frågar efter autentiseringsuppgifter för att ansluta till domänen kan har du inte problem med nätverksanslutningen.

### <a name="credentials-related-issues"></a>Autentiseringsuppgifter för problem
Om du har problem med autentiseringsuppgifter och inte går att ansluta till domänen, kan du prova följande felsökningssteg:

* Försök använda UPN-formatet för att ange autentiseringsuppgifter. Om det finns många användare med samma UPN-prefix i din klient eller om ditt UPN-prefix är alltför långa, kanske SAMAccountName för ditt konto automatiskt genererade. I dessa fall kan SAMAccountName-format för ditt konto skilja sig från vad du förväntar dig eller använda i din lokala domän.

* Försök att använda autentiseringsuppgifterna för ett konto som tillhör den *AAD DC-administratörer* grupp.

* Kontrollera att du har [aktiverat Lösenordssynkronisering](active-directory-ds-getting-started-password-sync.md) till din hanterade domän.

* Kontrollera att du har använt UPN för användaren som konfigurerats i Azure AD (till exempel *bob@domainservicespreview.onmicrosoft.com*) att logga in.

* Vänta tills Lösenordssynkronisering till att slutföra som angetts i komma igång-guiden.

## <a name="related-content"></a>Relaterat innehåll
* [Azure AD DS-komma igång](active-directory-ds-getting-started.md)
* [Administrera en Azure AD DS-hanterad domän](active-directory-ds-admin-guide-administer-domain.md)
