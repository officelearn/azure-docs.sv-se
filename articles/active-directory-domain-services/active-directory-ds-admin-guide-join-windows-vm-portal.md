---
title: "Azure Active Directory Domain Services: Anslut en Windows Server-VM till en hanterad domän | Microsoft Docs"
description: Anslut en virtuell dator med Windows Server till Azure AD Domain Services
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
ms.openlocfilehash: 1ea3f7271bd165bf42d520e4a0267a80dcca58d5
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain"></a>Anslut en Windows Server-virtuell dator till en hanterad domän
Den här artikeln visar hur du distribuerar en virtuell dator med Windows Server med Azure-portalen. Den visar hur du kopplar den virtuella datorn till en Azure AD Domain Services-hanterad domän.

## <a name="step-1-create-the-windows-server-virtual-machine"></a>Steg 1: Skapa den virtuella datorn i Windows Server
Utför följande steg för att skapa en Windows-dator som tillhör det virtuella nätverket som du har aktiverat Azure AD Domain Services.

1. Logga in på Azure Portal på [http://portal.azure.com](http://portal.azure.com).
2. Klicka på knappen **New** (Nytt) i det övre vänstra hörnet i Azure Portal.
3. Välj **Compute**, och välj sedan **Windows Server 2016 Datacenter**.

    ![Välj den avbildning](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-image.png)
4. Konfigurera grundläggande inställningar för den virtuella datorn på den **grunderna** sidan i guiden.

    ![Konfigurera grundläggande inställningar för den virtuella datorn](./media/active-directory-domain-services-admin-guide/create-windows-vm-basics.png)

    > [!TIP]
    > Användarnamn och lösenord som du anger här finns för ett lokalt administratörskonto som används för att logga in på den virtuella datorn. Välj ett starkt lösenord för att skydda den virtuella datorn mot lösenord brute force-attacker. Ange inte här autentiseringsuppgifter för ett domänanvändarkonto.
    >

5. Välj en **storlek** för den virtuella datorn. Om du vill se fler storlekar väljer du **Visa alla** eller så ändrar du filtret för **disktyper som stöds**.

    ![Välj storlek för den virtuella datorn](./media/active-directory-domain-services-admin-guide/create-windows-vm-size.png)

6. På den **inställningar** i guiden väljer du det virtuella nätverket som din Azure AD Domain Services hanterade domänen har distribuerats. Välj ett annat undernät än den som din hanterade domän har distribuerats till. Behåll standardinställningarna för de andra inställningarna och klickar på **OK**.

    ![Välj virtuella nätverk för virtuell dator](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-vnet.png)

    > [!TIP]
    > **Välj rätt virtuellt nätverk och undernät.**
    > Välj antingen det virtuella nätverk där din hanterade domän distribueras eller ett virtuellt nätverk som är ansluten till den med hjälp av virtuella nätverk peering. Om du väljer ett virtuellt nätverk som är kopplade till varandra, kan du inte ansluta den virtuella datorn till den hanterade domänen.
    > Vi rekommenderar att du distribuerar din hanterade domän i ett dedikerat undernät. Välj därför inte undernätet som du har aktiverat din hanterade domän.

7. På den **inköp** granskar du inställningarna och klicka på **OK** att distribuera den virtuella datorn.
8. Distribution av Virtuella datorer är fäst på instrumentpanelen för Azure-portalen.

    ![Klart](./media/active-directory-domain-services-admin-guide/create-windows-vm-done.png)
9. När distributionen är klar kan du visa information om den virtuella datorn i den **översikt** sidan.


## <a name="step-2-connect-to-the-windows-server-virtual-machine-using-the-local-administrator-account"></a>Steg 2: Anslut till den virtuella datorn för Windows Server som använder det lokala administratörskontot
Nu kan ansluta till den nyligen skapade Windows Server virtuella datorn ska ansluta till domänen. Använd autentiseringsuppgifter för lokal administratör som du angav när du skapar den virtuella datorn.

Utför följande steg för att ansluta till den virtuella datorn.

1. Klicka på den **Anslut** knappen på den **översikt** sidan. En Remote Desktop Protocol (RDP)-fil skapas och hämtas.

    ![Ansluta till Windows-dator](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
2. Öppna den hämtade RDP-filen för att ansluta till den virtuella datorn. Om du uppmanas till detta klickar du på **Anslut**.
3. I Kommandotolken inloggning, ange din **lokal administratörsbehörighet**, som du angav när du skapar den virtuella datorn. Till exempel har vi använt 'localhost\mahesh' i det här exemplet.
4. Du kan få en certifikatvarning under inloggningen. Klicka på Ja eller fortsätta att fortsätta med anslutningen.

Du bör nu loggas till den nyligen skapade Windows virtuella datorn med lokal administratörsbehörighet. Nästa steg är att ansluta den virtuella datorn till domänen.


## <a name="step-3-join-the-windows-server-virtual-machine-to-the-aad-ds-managed-domain"></a>Steg 3: Anslut till den virtuella datorn i Windows Server till den hanterade AAD-DS-domänen
Utför följande steg för att ansluta den virtuella datorn i Windows Server till den hanterade AAD-DS-domänen.

1. Ansluta till Windows Server som visas i steg 2. Från startskärmen öppnar **Serverhanteraren**.
2. Klicka på **lokal Server** i den vänstra rutan i Serverhanteraren.

    ![Starta Serverhanteraren på den virtuella datorn](./media/active-directory-domain-services-admin-guide/join-domain-server-manager.png)
3. Klicka på **arbetsgrupp** under den **egenskaper** avsnitt. I den **Systemegenskaper** egenskapssida, klickar du på **ändra** att ansluta till domänen.

    ![Sidan med egenskaper för system](./media/active-directory-domain-services-admin-guide/join-domain-system-properties.png)
4. Ange domännamnet för din Azure AD Domain Services-hanterad domän i den **domän** textrutan och klicka på **OK**.

    ![Ange domänen som ska sammanfogas](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-domain.png)
5. Du uppmanas att ange dina autentiseringsuppgifter för att ansluta till domänen. Se till att du **ange autentiseringsuppgifter för en användare som hör till AAD DC administratörer** grupp. Endast medlemmar i den här gruppen har behörighet att ansluta datorer till den hanterade domänen.

    ![Ange autentiseringsuppgifter för domänanslutning](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-credentials.png)
6. Du kan ange autentiseringsuppgifter i något av följande sätt:

   * **UPN-format: (rekommenderas)** ange UPN-suffixet för användarkontot som konfigurerats i Azure AD. I det här exemplet UPN-suffix för användaren 'bob' är 'bob@domainservicespreview.onmicrosoft.com'.
   * **SAMAccountName format:** du kan ange kontonamnet i formatet SAMAccountName. I det här exemplet skulle användare ”bob” måste ange 'CONTOSO100\bob'.

     > [!TIP]
     > **Vi rekommenderar att du använder UPN-format för att ange autentiseringsuppgifter.**
     > Om en användares UPN-prefixet är alltför lång (till exempel 'joehasareallylongname'), kanske det SAMAccountName genereras automatiskt. Om flera användare har samma UPN-prefix (till exempel bob) i Azure AD-klient, kanske SAMAccountName format automatiskt genererade av tjänsten. I dessa fall kan UPN-formatet användas på ett tillförlitligt sätt att logga in på domänen.
     >

7. När domänanslutning lyckas visas följande meddelande välkomnar dig till domänen. Starta om virtuella domänen join-åtgärd ska slutföras.

    ![Välkommen till domänen](./media/active-directory-domain-services-admin-guide/join-domain-done.png)


## <a name="troubleshooting-domain-join"></a>Felsöka domänanslutning
### <a name="connectivity-issues"></a>Anslutningsproblem
Om den virtuella datorn inte kan hitta en domän, se följande felsökningssteg:

* Kontrollera att den virtuella datorn är ansluten till samma virtuella nätverk som du har aktiverat Domain Services i. Om inte den virtuella datorn kan inte ansluta till domänen och därför inte att ansluta till domänen.
* Se till att den virtuella datorn är i ett virtuellt nätverk som i sin tur är ansluten till det virtuella nätverket som du har aktiverat Domain Services.
* Försök att pinga domänen med namnet på den hanterade domänen (till exempel ”ping contoso100.com”). Om du inte vill det. Försök att pinga IP-adresser för den domän som visas på sidan där du har aktiverat Azure AD Domain Services (till exempel ”pinga 10.0.0.4'). Om du kan pinga IP-adressen men inte i domänen, kanske DNS är felaktigt konfigurerad. Kontrollera om IP-adresserna för domänen är konfigurerade som DNS-servrar för det virtuella nätverket.
* Försök att tömma DNS-matchare cachen på den virtuella datorn (”ipconfig/flushdns”).

Om du får i dialogrutan som frågar om autentiseringsuppgifter för att ansluta till domänen kan har du inte problem med nätverksanslutningen.

### <a name="credentials-related-issues"></a>Problem med autentiseringsuppgifter
Se följande steg om du har problem med autentiseringsuppgifter och inte går att ansluta till domänen.

* Försök med UPN-format för att ange autentiseringsuppgifter. Om det finns flera användare med samma UPN-prefix i din klient eller om UPN-prefixet är alltför långa, kanske automatiskt genererade SAMAccountName för ditt konto. Därför kan SAMAccountName format för ditt konto skilja sig från vad du förväntar dig eller använda i din lokala domän.
* Försök att använda autentiseringsuppgifterna för ett konto som tillhör gruppen AAD DC-administratörer.
* Kontrollera att du har [aktiverat lösenordssynkronisering](active-directory-ds-getting-started-password-sync.md) i enlighet med anvisningarna i guiden Komma igång.
* Kontrollera att du använder UPN för användaren som konfigurerats i Azure AD (till exempel 'bob@domainservicespreview.onmicrosoft.com') för inloggning.
* Se till att du har väntat tillräckligt länge för Lösenordssynkronisering att slutföra som anges i guiden komma igång.

## <a name="related-content"></a>Relaterat innehåll
* [Azure AD Domain Services - komma igång-guide](active-directory-ds-getting-started.md)
* [Administrera en Azure AD Domain Services-hanterad domän](active-directory-ds-admin-guide-administer-domain.md)
