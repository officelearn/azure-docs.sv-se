---
title: 'Azure Active Directory Domain Services: Administrera Grupprincip i hanterade domäner | Microsoft Docs'
description: Administrera Grupprincip i Azure Active Directory Domain Services hanterade domäner
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: ergreenl
ms.openlocfilehash: deaa6a9ff49fea2093c94a039aab0d7de0c95359
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55177125"
---
# <a name="administer-group-policy-on-an-azure-ad-domain-services-managed-domain"></a>Administrera Grupprincip i en Azure AD Domain Services-hanterad domän
Azure Active Directory Domain Services innehåller inbyggda grupprincipobjekt (GPO) för behållarna ”AADDC-användare” och ”AADDC-datorer”. Du kan anpassa dessa inbyggda grupprincipobjekt för att konfigurera en Grupprincip på den hanterade domänen. Medlemmar i gruppen ”AAD DC-administratörer” kan även skapa egna anpassade organisationsenheter i den hanterade domänen. De kan också skapa anpassade grupprincipobjekt och koppla dem till dessa anpassade organisationsenheter. Användare som tillhör gruppen ”AAD DC-administratörer” beviljas som gruppolicy administratörsbehörighet för den hanterade domänen.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Innan du börjar
Om du vill utföra åtgärderna i den här artikeln behöver du:

1. En giltig **Azure-prenumeration**.
2. En **Azure AD-katalog** -antingen synkroniseras med en lokal katalog eller en molnbaserad katalog.
3. **Azure AD Domain Services** måste aktiveras för Azure AD-katalog. Om du inte gjort det, följer du alla uppgifter som beskrivs i den [komma igång-guiden](active-directory-ds-getting-started.md).
4. En **domänansluten VM** varifrån du administrerar den hanterade domänen i Azure AD Domain Services. Om du inte har sådan en virtuell dator, följer du alla uppgifter som beskrivs i artikeln [ansluta en Windows-dator till en hanterad domän](active-directory-ds-admin-guide-join-windows-vm.md).
5. Du måste ha autentiseringsuppgifter för en **användarkonto som hör till gruppen ”AAD DC-administratörer”** i katalogen för att administrera en grupprincip för din hanterade domän.

<br>

## <a name="task-1---provision-a-domain-joined-virtual-machine-to-remotely-administer-group-policy-for-the-managed-domain"></a>Uppgift 1 – etablera en virtuell dator ingår i domänen för att fjärradministrera en grupprincip för den hanterade domänen
Azure AD Domain Services-hanterade domäner kan hanteras via en fjärranslutning med hjälp av välbekanta Active Directory-administrationsverktyg, till exempel Active Directory administrativa Center (ADAC) eller AD PowerShell. På samma sätt kan kan en grupprincip för den hanterade domänen fjärradministreras med administrationsverktygen för en Grupprincip.

Administratörer i Azure AD-katalogen har inte behörighet för att ansluta till domänkontrollanter i den hanterade domänen via fjärrskrivbord. Medlemmar i gruppen ”AAD DC-administratörer” kan administrera Grupprincip för hanterade domäner via en fjärranslutning. De kan använda en grupprincip verktyg på en Windows Server/klient-dator som är anslutna till den hanterade domänen. Verktyg för gruppen kan installeras som en del av funktionen Grupprinciphantering valfritt i Windows Server och klientdatorer som är anslutna till den hanterade domänen.

Den första uppgiften är att etablera en Windows Server-dator som är ansluten till den hanterade domänen. Mer information finns i artikeln [ansluta en Windows Server-dator till en Azure AD Domain Services-hanterad domän](active-directory-ds-admin-guide-join-windows-vm.md).

## <a name="task-2---install-group-policy-tools-on-the-virtual-machine"></a>Uppgift 2 – installera en grupprincip verktyg på den virtuella datorn
Utför följande steg för att installera administrationsverktygen för grupp princip på den domänanslutna virtuella datorn.

1. Gå till Azure-portalen. Klicka på **alla resurser** på den vänstra panelen. Leta upp och klicka på den virtuella datorn som du skapade i uppgift 1.
2. Klicka på den **Connect** på fliken Översikt. En Remote Desktop Protocol (RDP)-fil skapas och hämtas.

    ![Ansluta till Windows-dator](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
3. Öppna den hämtade RDP-filen för att ansluta till den virtuella datorn. Om du uppmanas till detta klickar du på **Anslut**. Använd autentiseringsuppgifterna för en användare som tillhör gruppen ”AAD DC-administratörer” i inloggning-Kommandotolken. Exempelvis kan vi använda 'bob@domainservicespreview.onmicrosoft.com' i vårt fall. Du kan få en certifikatvarning under inloggningen. Klicka på Ja eller fortsätta att fortsätta med anslutningen.
4. Från startskärmen öppnar **Serverhanteraren**. Klicka på **Lägg till roller och funktioner** i fönstret i mitten av Serverhanteraren.

    ![Starta Serverhanteraren på den virtuella datorn](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager.png)
5. På den **innan du börjar** för den **guiden Lägg till roller och funktioner**, klickar du på **nästa**.

    ![Innan du börjar sidan](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-begin.png)
6. På den **installationstyp** lämnar den **rollbaserad eller funktionsbaserad installation** alternativet är markerat och klicka på **nästa**.

    ![Installationstyp](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-type.png)
7. På den **Serverval** , Välj den aktuella virtuella datorn från serverpoolen och klicka på **nästa**.

    ![Sida för val av Server](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-server.png)
8. På den **serverroller** klickar du på **nästa**. Vi hoppar över den här sidan eftersom vi inte installerar några roller på servern.
9. På den **funktioner** väljer den **Group Policy Management** funktionen.

    ![Funktionssidan](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management.png)
10. På den **bekräftelse** klickar du på **installera** du installerar funktionen Grupprinciphantering på den virtuella datorn. När funktionsinstallationen är klar, klickar du på **Stäng** att avsluta den **Lägg till roller och funktioner** guiden.

    ![Bekräftelsesida](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management-confirmation.png)

## <a name="task-3---launch-the-group-policy-management-console-to-administer-group-policy"></a>Uppgift 3 – Starta hanteringskonsolen Grupprincip för att administrera en Grupprincip
Du kan använda konsolen för Grupprinciphantering på den virtuella datorn ingår i domänen för att administrera en Grupprincip på den hanterade domänen.

> [!NOTE]
> Du måste vara medlem i gruppen ”AAD DC-administratörer” för att administrera en Grupprincip på den hanterade domänen.
>
>

1. På startsidan klickar du på **Administrationsverktyg**. Du bör se den **Group Policy Management** konsolen har installerats på den virtuella datorn.

    ![Starta Grupprinciphantering](./media/active-directory-domain-services-admin-guide/gp-management-installed.png)
2. Klicka på **Group Policy Management** att starta konsolen Grupprinciphantering.

    ![Group Policy Console](./media/active-directory-domain-services-admin-guide/gp-management-console.png)

## <a name="task-4---customize-built-in-group-policy-objects"></a>Uppgift 4 – anpassa inbyggda grupprincipobjekt
Det finns två inbyggda grupprincipobjekt (GPO) – ett för behållarna ”AADDC-datorer” och ”AADDC-användare” i din hanterade domän. Du kan anpassa dessa grupprincipobjekt för att konfigurera en Grupprincip på den hanterade domänen.

1. I den **Group Policy Management** konsolen, klicka på och expandera den **skog: contoso100.com** och **domäner** noder att se grupprinciper för din hanterade domän.

    ![Inbyggda grupprincipobjekt](./media/active-directory-domain-services-admin-guide/builtin-gpos.png)
2. Du kan anpassa dessa inbyggda grupprincipobjekt för att konfigurera grupprinciper på den hanterade domänen. Högerklicka på Grupprincipobjektet och klicka på **redigera...**  att anpassa det inbyggda Grupprincipobjektet. Verktyget Configuration redigeraren kan du anpassa Grupprincipobjektet.

    ![Redigera inbyggda GPO](./media/active-directory-domain-services-admin-guide/edit-builtin-gpo.png)
3. Du kan nu använda den **Redigeraren för Grupprinciphantering** konsolen för att redigera det inbyggda Grupprincipobjektet. Till exempel visar följande skärmbild hur du anpassar det inbyggda Grupprincipobjektet ”AADDC-datorer”.

    ![Anpassa GPO](./media/active-directory-domain-services-admin-guide/gp-editor.png)

## <a name="task-5---create-a-custom-group-policy-object-gpo"></a>Uppgift 5 – skapa en anpassad grupprincipobjektet (GPO)
Du kan skapa eller importera egna anpassade grupprincipobjekt. Du kan också länka anpassade grupprincipobjekt till en anpassad Organisationsenhet som du har skapat i din hanterade domän. Läs mer om hur du skapar anpassade organisationsenheter [skapa en anpassad OU på en hanterad domän](active-directory-ds-admin-guide-create-ou.md).

> [!NOTE]
> Du måste vara medlem i gruppen ”AAD DC-administratörer” för att administrera en Grupprincip på den hanterade domänen.
>
>

1. I den **Group Policy Management** konsolen, klickar du på din anpassade organisationsenhet (OU). Högerklicka på OU: N och klicka på **skapa ett grupprincipobjekt i den här domänen och länka det här...** .

    ![Skapa en anpassad grupprincipobjekt](./media/active-directory-domain-services-admin-guide/gp-create-gpo.png)
2. Ange ett namn för det nya Grupprincipobjektet och klicka på **OK**.

    ![Ange ett namn för GPO](./media/active-directory-domain-services-admin-guide/gp-specify-gpo-name.png)
3. Ett nytt grupprincipobjekt skapas och länkade till ditt anpassade Organisationsenhet. Högerklicka på Grupprincipobjektet och klicka på **redigera...**  på menyn.

    ![Nyligen skapade GPO](./media/active-directory-domain-services-admin-guide/gp-gpo-created.png)
4. Du kan anpassa den nyligen skapade GPO med hjälp av den **Redigeraren för Grupprinciphantering**.

    ![Anpassa nytt GPO](./media/active-directory-domain-services-admin-guide/gp-customize-gpo.png)


Mer information om hur du använder [konsolen Grupprinciphantering](https://technet.microsoft.com/library/cc753298.aspx) finns på Technet.

## <a name="related-content"></a>Relaterat innehåll
* [Azure AD Domain Services – komma igång-guiden](active-directory-ds-getting-started.md)
* [Ansluta en Windows Server-dator till en Azure AD Domain Services-hanterad domän](active-directory-ds-admin-guide-join-windows-vm.md)
* [Administrera en Azure AD Domain Services-hanterad domän](active-directory-ds-admin-guide-administer-domain.md)
* [Konsolen Grupprinciphantering](https://technet.microsoft.com/library/cc753298.aspx)
