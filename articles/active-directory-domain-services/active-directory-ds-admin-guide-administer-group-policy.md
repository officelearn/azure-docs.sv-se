---
title: 'Azure Active Directory Domain Services: Administrera Grupprincip på hanterade domäner | Microsoft Docs'
description: Administrera en Grupprincip på Azure Active Directory Domain Services hanterade domäner
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: maheshu
ms.openlocfilehash: 3f49e4ac0073c81a6e55e6653acc7c6531989379
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36212248"
---
# <a name="administer-group-policy-on-an-azure-ad-domain-services-managed-domain"></a>Administrera Grupprincip i en Azure AD Domain Services-hanterad domän
Azure Active Directory Domain Services innehåller inbyggda grupprincipobjekt (GPO) för behållare AADDC-användare och AADDC-datorer. Du kan anpassa dessa inbyggda grupprincipobjekt för att konfigurera en Grupprincip på den hanterade domänen. Medlemmar i gruppen AAD DC-administratörer kan dessutom skapa egna anpassade organisationsenheter i den hanterade domänen. De kan också skapa anpassade grupprincipobjekt och länka dem till dessa anpassade organisationsenheter. Användare som tillhör gruppen AAD DC-administratörer beviljas Grupprincip administratörsbehörighet för den hanterade domänen.

## <a name="before-you-begin"></a>Innan du börjar
Om du vill utföra åtgärderna i den här artikeln behöver du:

1. En giltig **Azure-prenumeration**.
2. En **Azure AD-katalog** -antingen synkroniseras med en lokal katalog eller en molnbaserad katalog.
3. **Azure AD Domain Services** måste vara aktiverat för Azure AD-katalog. Om du inte gjort det, följer du de uppgifter som beskrivs i den [Kom igång-guiden](active-directory-ds-getting-started.md).
4. En **domänanslutna virtuella** där du administrera den hanterade domänen med Azure AD Domain Services. Om du inte har sådan en virtuell dator, följer du de uppgifter som beskrivs i artikel med titeln [Anslut en Windows-dator till en hanterad domän](active-directory-ds-admin-guide-join-windows-vm.md).
5. Du måste autentiseringsuppgifterna för en **användarkontot som hör till gruppen ”AAD DC-administratörer”** i katalogen för att administrera en grupprincip för din hanterade domän.

<br>

## <a name="task-1---provision-a-domain-joined-virtual-machine-to-remotely-administer-group-policy-for-the-managed-domain"></a>Uppgift 1 – etablera en virtuell dator domänanslutna för att fjärradministrera en grupprincip för den hanterade domänen
Azure AD Domain Services-hanterade domäner kan hanteras från en fjärrdator med hjälp av välbekanta Active Directory administrativa verktyg, till exempel Active Directory administrativa Center (ADAC) eller AD PowerShell. På liknande sätt Grupprincip för den hanterade domänen kan du administrera med administrationsverktygen för Grupprincip.

Administratörer i din Azure AD-katalog har inte behörighet att ansluta till domänkontrollanter på den hanterade domänen via fjärrskrivbord. Medlemmar i gruppen AAD DC-administratörer kan fjärradministrera en grupprincip för hanterade domäner. De kan använda Grupprincip verktyg på en dator för Windows-servern eller-klienten som ingår i den Hantera domänen. Verktyg för gruppen kan installeras som en del av hantering av Grupprincip valfri funktion i Windows Server och klientdatorer som är anslutna till den hanterade domänen.

Den första uppgiften är att etablera en virtuell dator med Windows Server som är ansluten till den hanterade domänen. Instruktioner finns i artikeln [ansluta en virtuell dator med Windows Server till en Azure AD Domain Services-hanterad domän](active-directory-ds-admin-guide-join-windows-vm.md).

## <a name="task-2---install-group-policy-tools-on-the-virtual-machine"></a>Uppgift 2 – installera Grupprincip verktyg på den virtuella datorn
Utför följande steg för att installera Administrationsverktyg för principen på den virtuella datorn ansluten till en domän.

1. Gå till Azure-portalen. Klicka på **alla resurser** i den vänstra rutan. Leta upp och klicka på den virtuella datorn som du skapade i uppgift 1.
2. Klicka på den **Anslut** på fliken Översikt. En Remote Desktop Protocol (RDP)-fil skapas och hämtas.

    ![Ansluta till Windows-dator](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
3. Öppna den hämtade RDP-filen för att ansluta till den virtuella datorn. Om du uppmanas till detta klickar du på **Anslut**. Inloggningsskärm, Använd autentiseringsuppgifter för en användare som tillhör gruppen AAD DC-administratörer. Exempelvis kan vi använda 'bob@domainservicespreview.onmicrosoft.com' i vårt fall. Du kan få en certifikatvarning under inloggningen. Klicka på Ja eller fortsätta att fortsätta med anslutningen.
4. Från startskärmen öppnar **Serverhanteraren**. Klicka på **Lägg till roller och funktioner** i fönstret i mitten av Serverhanteraren.

    ![Starta Serverhanteraren på den virtuella datorn](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager.png)
5. På den **innan du börjar** sida av den **guiden Lägg till roller och funktioner**, klickar du på **nästa**.

    ![Innan du börjar sida](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-begin.png)
6. På den **installationstyp** lämnar den **rollbaserad eller funktionsbaserad installation** alternativet som är markerat och klicka på **nästa**.

    ![Installationstyp](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-type.png)
7. På den **Serverval** , Välj den aktuella virtuella datorn från serverpoolen och klicka på **nästa**.

    ![Sidan för val av Server](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-server.png)
8. På den **serverroller** klickar du på **nästa**. Vi hoppar över den här sidan eftersom vi inte installerar några roller på servern.
9. På den **funktioner** väljer den **Grupprinciphantering** funktion.

    ![Sidan funktioner](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management.png)
10. På den **bekräftelse** klickar du på **installera** du installerar funktionen Grupprinciphantering på den virtuella datorn. När för funktionsinstallationen är klar klickar du på **Stäng** att avsluta den **Lägg till roller och funktioner** guiden.

    ![Bekräftelsesida](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management-confirmation.png)

## <a name="task-3---launch-the-group-policy-management-console-to-administer-group-policy"></a>Uppgift 3 – Starta hanteringskonsolen Grupprincip för att administrera en Grupprincip
Du kan använda konsolen för Grupprinciphantering på den virtuella datorn ingår i domänen för att administrera en Grupprincip på den hanterade domänen.

> [!NOTE]
> Du måste vara medlem i gruppen AAD DC-administratörer för att administrera en Grupprincip på den hanterade domänen.
>
>

1. Klicka på startskärmen **Administrationsverktyg**. Du bör se den **Grupprinciphantering** konsolen har installerats på den virtuella datorn.

    ![Starta hantering av Grupprincip](./media/active-directory-domain-services-admin-guide/gp-management-installed.png)
2. Klicka på **Grupprinciphantering** att starta konsolen Grupprinciphantering.

    ![Group Policy Console](./media/active-directory-domain-services-admin-guide/gp-management-console.png)

## <a name="task-4---customize-built-in-group-policy-objects"></a>Uppgift 4 – anpassa inbyggda grupprincipobjekt
Det finns två inbyggda grupprincipobjekt (GPO) – ett för behållarna AADDC-datorer och AADDC-användare i din hanterade domän. Du kan anpassa dessa grupprincipobjekt för att konfigurera en Grupprincip på den hanterade domänen.

1. I den **Grupprinciphantering** konsolen, klicka på och expandera den **skog: contoso100.com** och **domäner** noder för att se grupprinciper för din hanterade domän.

    ![Inbyggda grupprincipobjekt](./media/active-directory-domain-services-admin-guide/builtin-gpos.png)
2. Du kan anpassa dessa inbyggda grupprincipobjekt om du vill konfigurera grupprinciper för din hanterade domän. Högerklicka på Grupprincipobjektet och klicka på **redigera...**  att anpassa inbyggda Grupprincipobjektet. Verktyget Configuration Redigeraren för Grupprincip kan du anpassa Grupprincipobjektet.

    ![Redigera inbyggda GPO](./media/active-directory-domain-services-admin-guide/edit-builtin-gpo.png)
3. Nu kan du använda den **Redigeraren för Grupprinciphantering** konsolen för att redigera inbyggda Grupprincipobjektet. Till exempel visar följande skärmbild hur du anpassar inbyggda AADDC-datorer Grupprincipobjektet.

    ![Anpassa GPO](./media/active-directory-domain-services-admin-guide/gp-editor.png)

## <a name="task-5---create-a-custom-group-policy-object-gpo"></a>Uppgift 5 – skapa en anpassad grupp grupprincipobjektet (GPO)
Du kan skapa eller importera dina egna anpassade grupprincipobjekt. Du kan också länka anpassade grupprincipobjekt till en anpassad Organisationsenhet som du har skapat i din hanterade domän. Mer information om hur du skapar anpassade organisationsenheter finns [skapa en anpassad Organisationsenhet på en hanterad domän](active-directory-ds-admin-guide-create-ou.md).

> [!NOTE]
> Du måste vara medlem i gruppen AAD DC-administratörer för att administrera en Grupprincip på den hanterade domänen.
>
>

1. I den **Grupprinciphantering** konsolen, klickar du på din egen organisationsenhet (OU). Högerklicka på OU: N och klickar på **skapa ett grupprincipobjekt i den här domänen och länka det här...** .

    ![Skapa en anpassad grupprincipobjekt](./media/active-directory-domain-services-admin-guide/gp-create-gpo.png)
2. Ange ett namn för det nya Grupprincipobjektet och klickar på **OK**.

    ![Ange ett namn för GPO](./media/active-directory-domain-services-admin-guide/gp-specify-gpo-name.png)
3. Ett nytt grupprincipobjekt skapas och länkade till din egen Organisationsenhet. Högerklicka på Grupprincipobjektet och klicka på **redigera...**  på menyn.

    ![Nyligen skapade GPO](./media/active-directory-domain-services-admin-guide/gp-gpo-created.png)
4. Du kan anpassa den nyligen skapade GPO med hjälp av den **Redigeraren för Grupprinciphantering**.

    ![Anpassa nytt GPO](./media/active-directory-domain-services-admin-guide/gp-customize-gpo.png)


Mer information om hur du använder [konsolen Grupprinciphantering](https://technet.microsoft.com/library/cc753298.aspx) finns på Technet.

## <a name="related-content"></a>Relaterat innehåll
* [Azure AD Domain Services - komma igång-guide](active-directory-ds-getting-started.md)
* [Anslut en virtuell dator med Windows Server till en Azure AD Domain Services-hanterad domän](active-directory-ds-admin-guide-join-windows-vm.md)
* [Administrera en Azure AD Domain Services-hanterad domän](active-directory-ds-admin-guide-administer-domain.md)
* [Konsolen Grupprinciphantering](https://technet.microsoft.com/library/cc753298.aspx)
