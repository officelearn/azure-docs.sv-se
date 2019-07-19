---
title: 'Azure Active Directory Domain Services: Administrera grupprincip | Microsoft Docs'
description: Administrera grupprincip på Azure Active Directory Domain Services hanterade domäner
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: iainfou
ms.openlocfilehash: c7b32885fdb3cf4f3e584c916d6b234fff54bfc4
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234030"
---
# <a name="administer-group-policy-on-an-azure-ad-domain-services-managed-domain"></a>Administrera grupprincip på en Azure AD Domain Services hanterad domän
Azure Active Directory Domain Services innehåller inbyggda grupprincip objekt (GPO) för behållarna "AADDC användare" och "AADDC Computers". Du kan anpassa de här inbyggda grupprincipobjekten för att konfigurera grupprincip på den hanterade domänen. Medlemmar i gruppen "AAD DC-administratörer" kan dessutom skapa egna anpassade organisationsenheter i den hanterade domänen. De kan också skapa anpassade grup princip objekt och länka dem till dessa anpassade organisationsenheter. Användare som tillhör gruppen "AAD DC-administratörer" beviljas grupprincip administratörs behörighet på den hanterade domänen.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Innan du börjar
För att utföra de uppgifter som anges i den här artikeln behöver du:

1. En giltig **Azure-prenumeration**.
2. En **Azure AD-katalog** – antingen synkroniserad med en lokal katalog eller en katalog som endast är molnad.
3. **Azure AD Domain Services** måste vara aktiverat för Azure AD-katalogen. Om du inte har gjort det följer du alla uppgifter som beskrivs i Komma igångs [guiden](create-instance.md).
4. En **domänansluten virtuell dator** från vilken du administrerar den Azure AD Domain Services hanterade domänen. Om du inte har en sådan virtuell dator följer du alla uppgifter som beskrivs i artikeln [Anslut en virtuell Windows-dator till en hanterad domän](active-directory-ds-admin-guide-join-windows-vm.md).
5. Du behöver autentiseringsuppgifterna för ett **användar konto som tillhör gruppen "AAD DC-administratörer"** i din katalog för att administrera grupprincip för din hanterade domän.

<br>

## <a name="task-1---provision-a-domain-joined-virtual-machine-to-remotely-administer-group-policy-for-the-managed-domain"></a>Uppgift 1 – etablera en domänansluten virtuell dator för fjärr administration av grupprincip för den hanterade domänen
Azure AD Domain Services hanterade domäner kan fjärrhanteras med hjälp av välbekanta Active Directory administrations verktyg som Active Directory Administrationscenter (ADAC) eller AD PowerShell. På samma sätt kan grupprincip för den hanterade domänen fjärradministreras med hjälp av grupprincip administrations verktyg.

Administratörer i din Azure AD-katalog har inte behörighet att ansluta till domänkontrollanter på den hanterade domänen via fjärr skrivbord. Medlemmar i gruppen "AAD DC-administratörer" kan administrera grupprincip fjärrstyrda domäner. De kan använda grupprincip verktyg på en Windows Server/klient dator som är ansluten till den hanterade domänen. Grupprincip verktyg kan installeras som en del av den valfria funktionen grupprincip Management på Windows Server och klient datorer som är anslutna till den hanterade domänen.

Den första aktiviteten är att etablera en virtuell Windows Server-dator som är ansluten till den hanterade domänen. Instruktioner finns i artikeln [ansluta en virtuell Windows Server-dator till en Azure AD Domain Services hanterad domän](active-directory-ds-admin-guide-join-windows-vm.md).

## <a name="task-2---install-group-policy-tools-on-the-virtual-machine"></a>Uppgift 2 – Installera grupprincip verktyg på den virtuella datorn
Utför följande steg för att installera grupprincip administrations verktyg på den domänanslutna virtuella datorn.

1. Navigera till Azure Portal. Klicka på **alla resurser** i den vänstra panelen. Leta upp och klicka på den virtuella dator som du skapade i uppgift 1.
2. Klicka på knappen **Anslut** på fliken Översikt. En Remote Desktop Protocol-fil (. RDP) skapas och hämtas.

    ![Anslut till den virtuella Windows-datorn](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
3. Öppna den hämtade RDP-filen för att ansluta till den virtuella datorn. Om du uppmanas till detta klickar du på **Anslut**. I inloggnings frågan använder du autentiseringsuppgifterna för en användare som tillhör gruppen "AAD DC-administratörer". Vi använder exempelvis "bob@domainservicespreview.onmicrosoft.com" i vårt fall. Du kan få en certifikatvarning under inloggningen. Klicka på Ja eller Fortsätt för att fortsätta med anslutningen.
4. Öppna **Serverhanteraren**på Start skärmen. Klicka på **Lägg till roller och funktioner** i det centrala fönstret i fönstret Serverhanteraren.

    ![Starta Serverhanteraren på den virtuella datorn](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager.png)
5. På sidan **innan du börjar** i **guiden Lägg till roller och funktioner**klickar du på **Nästa**.

    ![Innan du börjar Sidan](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-begin.png)
6. På sidan **Installations typ** lämnar du alternativet **rollbaserad eller funktion-baserad installation** markerad och klickar på **Nästa**.

    ![Sidan Installations typ](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-type.png)
7. På sidan **Server val** väljer du den aktuella virtuella datorn från serverpoolen och klickar på **Nästa**.

    ![Sidan Server val](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-server.png)
8. På sidan **Server roller** klickar du på **Nästa**. Vi hoppar över den här sidan eftersom vi inte installerar några roller på servern.
9. På sidan **funktioner** väljer du funktionen **Grupprincip hantering** .

    ![Sidan funktioner](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management.png)
10. På sidan **bekräftelse** klickar du på **Installera** för att installera Grupprincip hanterings funktionen på den virtuella datorn. När funktions installationen har slutförts klickar du på **Stäng** för att stänga guiden **Lägg till roller och funktioner** .

    ![Bekräftelse sida](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management-confirmation.png)

## <a name="task-3---launch-the-group-policy-management-console-to-administer-group-policy"></a>Uppgift 3 – Starta grupprincip hanterings konsolen för att administrera grupprincip
Du kan använda grupprincip Management-konsolen på den domänanslutna virtuella datorn för att administrera grupprincip på den hanterade domänen.

> [!NOTE]
> Du måste vara medlem i gruppen "AAD DC-administratörer" för att administrera grupprincip på den hanterade domänen.
>
>

1. Klicka på **administrations verktyg**på Start skärmen. Du bör se att den **Grupprincip hanterings** konsolen är installerad på den virtuella datorn.

    ![Starta grupprincip hantering](./media/active-directory-domain-services-admin-guide/gp-management-installed.png)
2. Klicka på **Grupprincip hantering** för att starta Grupprincip hanterings konsolen.

    ![grupprincip-konsol](./media/active-directory-domain-services-admin-guide/gp-management-console.png)

## <a name="task-4---customize-built-in-group-policy-objects"></a>Uppgift 4 – anpassa inbyggda grupprincip objekt
Det finns två inbyggda grupprincip objekt (GPO) – en var och en för behållarna "AADDC Computers" och "AADDC Users" i din hanterade domän. Du kan anpassa dessa grup princip objekt för att konfigurera grup principer på den hanterade domänen.

1. I **Grupprincip hanterings** konsolen klickar du på för att expandera noderna **skog: contoso100.com** och **domäner** för att se grup principerna för din hanterade domän.

    ![Inbyggda grup princip objekt](./media/active-directory-domain-services-admin-guide/builtin-gpos.png)
2. Du kan anpassa de här inbyggda grupprincipobjekten för att konfigurera grup principer på din hanterade domän. Högerklicka på GRUPPRINCIPOBJEKTet och klicka på **Redigera...** för att anpassa det inbyggda grupprincipobjektet. Med verktyget grupprincip konfigurations redigeraren kan du anpassa GRUPPRINCIPOBJEKTet.

    ![Redigera inbyggt grup princip objekt](./media/active-directory-domain-services-admin-guide/edit-builtin-gpo.png)
3. Du kan nu använda **redigeraren Grupprinciphantering** -konsolen för att redigera det inbyggda grupprincipobjektet. Följande skärm bild visar till exempel hur du anpassar det inbyggda GRUPPRINCIPOBJEKTet "AADDC Computers".

    ![Anpassa GPO](./media/active-directory-domain-services-admin-guide/gp-editor.png)

## <a name="task-5---create-a-custom-group-policy-object-gpo"></a>Uppgift 5 – skapa ett anpassat grupprincip-objekt (GPO)
Du kan skapa eller importera dina egna anpassade grup princip objekt. Du kan också länka anpassade grup princip objekt till en anpassad ORGANISATIONSENHET som du har skapat i din hanterade domän. Mer information om hur du skapar anpassade organisationsenheter finns i [skapa en anpassad Organisationsenhet på en hanterad domän](create-ou.md).

> [!NOTE]
> Du måste vara medlem i gruppen "AAD DC-administratörer" för att administrera grupprincip på den hanterade domänen.
>
>

1. I **Grupprincip hanterings** konsolen klickar du på för att välja din anpassade ORGANISATIONSENHET (OU). Högerklicka på ORGANISATIONSENHETen och klicka på **skapa ett grup princip objekt i den här domänen och länka det här.** ...

    ![Skapa ett anpassat grup princip objekt](./media/active-directory-domain-services-admin-guide/gp-create-gpo.png)
2. Ange ett namn för det nya GRUPPRINCIPOBJEKTet och klicka på **OK**.

    ![Ange ett namn för GPO](./media/active-directory-domain-services-admin-guide/gp-specify-gpo-name.png)
3. Ett nytt grup princip objekt skapas och länkas till din anpassade ORGANISATIONSENHET. Högerklicka på GRUPPRINCIPOBJEKTet och klicka på **Redigera...** på menyn.

    ![Nyligen skapat grup princip objekt](./media/active-directory-domain-services-admin-guide/gp-gpo-created.png)
4. Du kan anpassa det nyligen skapade GRUPPRINCIPOBJEKTet med hjälp av **redigeraren Grupprinciphantering**.

    ![Anpassa nytt grup princip objekt](./media/active-directory-domain-services-admin-guide/gp-customize-gpo.png)


Mer information om hur du använder [konsolen Grupprinciphantering](https://technet.microsoft.com/library/cc753298.aspx) finns på TechNet.

## <a name="related-content"></a>Relaterat innehåll
* [Azure AD Domain Services-Komma igång guide](create-instance.md)
* [Ansluta en virtuell Windows Server-dator till en Azure AD Domain Services hanterad domän](active-directory-ds-admin-guide-join-windows-vm.md)
* [Hantera en Azure AD Domain Services domän](manage-domain.md)
* [konsolen grupprinciphantering](https://technet.microsoft.com/library/cc753298.aspx)
