---
title: Självstudiekurs - Skapa en vm-hantering för Azure Active Directory Domain Services | Microsoft-dokument
description: I den här självstudien får du lära dig hur du skapar och konfigurerar en virtuell Windows-dator som du använder för att administrera Azure Active Directory Domain Services-instansen.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: 09fcf88c6dfe90380f387c6d72c751634f5b1606
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475788"
---
# <a name="tutorial-create-a-management-vm-to-configure-and-administer-an-azure-active-directory-domain-services-managed-domain"></a>Självstudiekurs: Skapa en virtuell hanterings-vm för att konfigurera och administrera en hanterad Azure Active Directory Domain Services-domän

Azure Active Directory Domain Services (AD DS) tillhandahåller hanterade domäntjänster som domänanslutning, grupprincip, LDAP- och Kerberos/NTLM-autentisering som är helt kompatibel med Active Directory i Windows Server. Du administrerar den här hanterade domänen med samma RSAT (Remote Server Administration Tools) som med en lokal Active Directory Domain Services-domän. Eftersom Azure AD DS är en hanterad tjänst finns det vissa administrativa uppgifter som du inte kan utföra, till exempel använda fjärrskrivbordsprotokoll (RDP) för att ansluta till domänkontrollanterna.

Den här självstudien visar hur du skapar en Virtuell Windows Server-dator i Azure och installerar de verktyg som krävs för att administrera en Azure AD DS-hanterad domän.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Förstå tillgängliga administrativa uppgifter i en Azure AD DS-hanterad domän
> * Installera administrationsverktygen för Active Directory på en virtuell Windows Server-dator
> * Använda Administrationscenter för Active Directory för att utföra vanliga uppgifter

Om du inte har någon Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien behöver du följande resurser och privilegier:

* En aktiv Azure-prenumeration.
    * Om du inte har en Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* En Azure Active Directory-klient som är associerad med din prenumeration, antingen synkroniserad med en lokal katalog eller en katalog med endast molnet.
    * Om det behövs [skapar du en Azure Active Directory-klientorganisation][create-azure-ad-tenant] eller [associerar en Azure-prenumeration med ditt konto][associate-azure-ad-tenant].
* En hanterad Azure Active Directory Domain Services-domän aktiverad och konfigurerad i din Azure AD-klientorganisation.
    * Om det behövs läser du den första självstudien för att [skapa och konfigurera en Azure Active Directory Domain Services-instans][create-azure-ad-ds-instance].
* En virtuell Windows Server-dator som är ansluten till den Hanterade Azure AD DS-domänen.
    * Om det behövs läser du föregående självstudie för att [skapa en virtuell Windows Server-dator och ansluta den till en hanterad domän][create-join-windows-vm].
* Ett användarkonto som är medlem i azure *AD DC-administratörsgruppen* i din Azure AD-klientorganisation.
* En Azure Bastion-värd som distribueras i ditt virtuella Azure AD DS-nätverk.
    * Om det behövs [skapar du en Azure Bastion-värd][azure-bastion].

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

I den här självstudien skapar och konfigurerar du en hanterings-VM med Azure-portalen. Kom igång genom att logga in på [Azure-portalen](https://portal.azure.com).

## <a name="available-administrative-tasks-in-azure-ad-ds"></a>Tillgängliga administrativa uppgifter i Azure AD DS

Azure AD DS tillhandahåller en hanterad domän för användare, program och tjänster att använda. Den här metoden ändrar några av de tillgängliga hanteringsuppgifter du kan göra och vilka privilegier du har inom den hanterade domänen. Dessa uppgifter och behörigheter kan skilja sig från vad du upplever med en vanlig lokal Active Directory Domain Services-miljö. Du kan inte heller ansluta till domänkontrollanter på Azure AD DS-hanterad domän med fjärrskrivbord.

### <a name="administrative-tasks-you-can-perform-on-an-azure-ad-ds-managed-domain"></a>Administrativa uppgifter som du kan utföra på en Azure AD DS-hanterad domän

Medlemmar i gruppen *AAD DC-administratörer* beviljas behörigheter på azure AD DS-hanterad domän som gör det möjligt för dem att utföra uppgifter som:

* Konfigurera det inbyggda grupprincipobjektet (GPO) för *AADDC-datorer* och *AADDC-användare* i den hanterade domänen.
* Administrera DNS i den hanterade domänen.
* Skapa och administrera anpassade organisationsenheter (OUs) på den hanterade domänen.
* Få administrativ åtkomst till datorer som är anslutna till den hanterade domänen.

### <a name="administrative-privileges-you-dont-have-on-an-azure-ad-ds-managed-domain"></a>Administrativa privilegier som du inte har på en Azure AD DS-hanterad domän

Den Hanterade Azure AD DS-domänen är låst, så du har inte behörighet att utföra vissa administrativa uppgifter på domänen. Några av följande exempel är uppgifter som du inte kan göra:

* Utöka schemat för den hanterade domänen.
* Anslut till domänkontrollanter för den hanterade domänen med fjärrskrivbord.
* Lägg till domänkontrollanter i den hanterade domänen.
* Du har inte *behörigheten Domänadministratör* eller *Företagsadministratör* för den hanterade domänen.

## <a name="sign-in-to-the-windows-server-vm"></a>Logga in på den virtuella datorn för Windows Server

I den föregående självstudien skapades en virtuell Windows Server-dator och anslöts till den hanterade Azure AD DS-domänen. Låt oss använda den virtuella datorn för att installera hanteringsverktygen. Om det behövs [följer du stegen i självstudien för att skapa och ansluta en virtuell Windows Server till en hanterad domän][create-join-windows-vm].

> [!NOTE]
> I den här självstudien använder du en Virtuell Windows Server i Azure som är ansluten till den Hanterade Azure AD DS-domänen. Du kan också använda en Windows-klient, till exempel Windows 10, som är ansluten till den hanterade domänen.
>
> Mer information om hur du installerar administrationsverktygen på en Windows-klient finns i [installera RSAT (Remote Server Administration Tools)](https://social.technet.microsoft.com/wiki/contents/articles/2202.remote-server-administration-tools-rsat-for-windows-client-and-windows-server-dsforum2wiki.aspx)

Kom igång genom att ansluta till den virtuella datorn för Windows Server enligt följande:

1. I Azure-portalen väljer du **Resursgrupper** till vänster. Välj den resursgrupp där den virtuella datorn skapades, till exempel *myResourceGroup*och välj sedan den virtuella datorn, till exempel *myVM*.
1. I **fönstret Översikt** för den virtuella datorn väljer du **Anslut**och sedan **Bastion**.

    ![Ansluta till windows virtuella dator med Bastion i Azure-portalen](./media/join-windows-vm/connect-to-vm.png)

1. Ange autentiseringsuppgifterna för den virtuella datorn och välj sedan **Anslut**.

   ![Ansluta via Bastion-värden i Azure-portalen](./media/join-windows-vm/connect-to-bastion.png)

Om det behövs kan du låta webbläsaren öppna popup-fönster för att skyddsanslutningen ska visas. Det tar några sekunder att ansluta till den virtuella datorn.

## <a name="install-active-directory-administrative-tools"></a>Installera administrationsverktyg för Active Directory

Azure AD DS-hanterade domäner hanteras med samma administrativa verktyg som lokala AD DS-miljöer, till exempel Active Directory Administrative Center (ADAC) eller AD PowerShell. Dessa verktyg kan installeras som en del av RSAT-funktionen (Remote Server Administration Tools) på Windows Server och klientdatorer. Medlemmar i *gruppen AAD DC-administratörer* kan sedan fjärrdministrera Azure AD DS-hanterade domäner med hjälp av dessa AD-administrationsverktyg från en dator som är ansluten till den hanterade domänen.

Så här installerar du Active Directory Administration-verktygen på en domänansluten virtuell dator:

1. Om **Serverhanteraren** inte öppnas som standard när du loggar in på den virtuella datorn väljer du **Start-menyn** och väljer sedan **Serverhanteraren**.
1. Välj **Lägg till roller och funktioner**i *instrumentpanelsfönstret* i **serverhanteraren.**
1. På sidan **Innan du börjar** i guiden Lägg till roller och *funktioner*väljer du **Nästa**.
1. För *installationstypen*lämnar du alternativet **Rollbaserad eller funktionsbaserad installation** markerat och väljer **Nästa**.
1. På sidan **Serverval** väljer du den aktuella virtuella datorn i serverpoolen, till exempel *myvm.aaddscontoso.com*och väljer sedan **Nästa**.
1. Klicka på **Nästa**på sidan **Serverroller** .
1. Expandera noden **Administrationsverktyg** för fjärrserver på sidan **Funktioner** och expandera sedan noden Verktyg **för rolladministrationsverktyg.**

    Välj **funktionen AD DS och AD LDS Tools** i listan över rolladministrationsverktyg och välj sedan **Nästa**.

    ![Installera "AD DS- och AD LDS Tools" från sidan Funktioner](./media/tutorial-create-management-vm/install-features.png)

1. På sidan **Bekräftelse** väljer du **Installera**. Det kan ta en minut eller två att installera de administrativa verktygen.
1. När funktionsinstallationen är klar väljer du **Stäng** för att avsluta guiden **Lägg till roller och funktioner.**

## <a name="use-active-directory-administrative-tools"></a>Använda administrationsverktyg för Active Directory

Med de administrativa verktygen installerade ska vi se hur du använder dem för att administrera den Hanterade Azure AD DS-domänen. Kontrollera att du är inloggad på den virtuella datorn med ett användarkonto som är medlem i gruppen *AAD DC-administratörer.*

1. Välj **Administrationsverktyg**för Windows på **Start-menyn** . De AD-administrationsverktyg som installerades i föregående steg visas.

    ![Lista över administrationsverktyg som är installerade på servern](./media/tutorial-create-management-vm/list-admin-tools.png)

1. Välj **Active Directory Administrationscenter**.
1. Om du vill utforska den hanterade Azure AD DS-domänen väljer du domännamnet i den vänstra rutan, till exempel *aaddscontoso.com*. Två behållare med namnet *AADDC-datorer* och *AADDC-användare* finns högst upp i listan.

    ![Lista den tillgängliga behållarna som ingår i den hanterade Azure AD DS-domänen](./media/tutorial-create-management-vm/active-directory-administrative-center.png)

1. Om du vill visa användare och grupper som tillhör azure AD DS-hanterad domän väljer du behållaren **AADDC-användare.** Användarkontona och grupperna från din Azure AD-klient listas i den här behållaren.

    I följande exempelutdata visas ett användarkonto med namnet *Contoso Admin* och en grupp för *AAD DC-administratörer* i den här behållaren.

    ![Visa listan över Azure AD DS-domänanvändare i Active Directory Administrationscenter](./media/tutorial-create-management-vm/list-azure-ad-users.png)

1. Om du vill se de datorer som är anslutna till azure AD DS-hanterade domänen väljer du behållaren **AADDC-datorer.** En post för den aktuella virtuella datorn, till exempel *myVM,* visas. Datorkonton för alla datorer som är anslutna till Azure AD DS-hanterade domänen lagras i den här *behållaren för AADDC-datorer.*

Vanliga Active Directory Administrationscenter-åtgärder som att återställa ett användarkontolösenord eller hantera gruppmedlemskap är tillgängliga. Dessa åtgärder fungerar bara för användare och grupper som skapats direkt i Azure AD DS-hanterad domän. Identitetsinformation synkroniseras endast *från* Azure AD till Azure AD DS. Det finns ingen tillbakaskrivning från Azure AD DS till Azure AD. Du kan inte ändra lösenord eller hanterat gruppmedlemskap för användare som synkroniseras från Azure AD och synkronisera ändringarna.

Du kan också använda *Active Directory Module för Windows PowerShell*, som installeras som en del av administrationsverktygen, för att hantera vanliga åtgärder i din Azure AD DS-hanterade domän.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Förstå tillgängliga administrativa uppgifter i en Azure AD DS-hanterad domän
> * Installera administrationsverktygen för Active Directory på en virtuell Windows Server-dator
> * Använda Administrationscenter för Active Directory för att utföra vanliga uppgifter

Aktivera LDAPS (Securely interact with your Azure AD DS managed domain"

> [!div class="nextstepaction"]
> [Konfigurera säker LDAP för din hanterade domän](tutorial-configure-ldaps.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
