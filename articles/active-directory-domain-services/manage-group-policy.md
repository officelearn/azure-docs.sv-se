---
title: Skapa och hantera grupprinciper i Azure AD Domain Services | Microsoft-dokument
description: Lär dig hur du redigerar de inbyggda grupprincipobjekten och skapar egna anpassade principer i en hanterad Azure Active Directory Domain Services-domän.
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/09/2020
ms.author: iainfou
ms.openlocfilehash: 742d716ecdfff6ab67dedc281aa6134020f57add
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655042"
---
# <a name="administer-group-policy-in-an-azure-ad-domain-services-managed-domain"></a>Administrera grupprincip i en hanterad Azure AD-domän

Inställningar för användar- och datorobjekt i Azure Active Directory Domain Services (Azure AD DS) hanteras ofta med grupprincipobjekt (GCO). Azure AD DS innehåller inbyggda grupprincipobjekt för *AADDC-användare* och *AADDC-datorer* behållare. Du kan anpassa dessa inbyggda grupprincipobjekt för att konfigurera grupprinciper efter behov för din miljö. Medlemmar i azure *AD DC-administratörsgruppen* har behörighet att administration av grupprinciper i Azure AD DS-domänen och kan också skapa anpassade GPU:er och organisationsenheter (ORGANISATIONSENHETER). Mer information om vad grupprincip är och hur den fungerar finns i [översikt över grupprinciper][group-policy-overview].

I en hybridmiljö synkroniseras inte grupprinciper som konfigurerats i en lokal AD DS-miljö till Azure AD DS. Om du vill definiera konfigurationsinställningar för användare eller datorer i Azure AD DS redigerar du en av standard-grupprincipobjekten eller skapar ett anpassat grupprincipobjekt.

Den här artikeln visar hur du installerar verktygen för grupprinciphantering, redigerar sedan de inbyggda grupprincipobjekten och skapar anpassade grupprincipobjekt.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Innan du börjar

För att kunna slutföra den här artikeln behöver du följande resurser och privilegier:

* En aktiv Azure-prenumeration.
    * Om du inte har en Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* En Azure Active Directory-klient som är associerad med din prenumeration, antingen synkroniserad med en lokal katalog eller en katalog med endast molnet.
    * Om det behövs [skapar du en Azure Active Directory-klientorganisation][create-azure-ad-tenant] eller [associerar en Azure-prenumeration med ditt konto][associate-azure-ad-tenant].
* En hanterad Azure Active Directory Domain Services-domän aktiverad och konfigurerad i din Azure AD-klientorganisation.
    * Om det behövs slutför du självstudien för att [skapa och konfigurera en Azure Active Directory Domain Services-instans][create-azure-ad-ds-instance].
* En virtuell windows serverhantering som är ansluten till den Hanterade Azure AD DS-domänen.
    * Om det behövs slutför du självstudien för att [skapa en virtuell Windows Server-dator och ansluta den till en hanterad domän][create-join-windows-vm].
* Ett användarkonto som är medlem i azure *AD DC-administratörsgruppen* i din Azure AD-klientorganisation.

> [!NOTE]
> Du kan använda administrativa mallar för grupprinciper genom att kopiera de nya mallarna till hanteringsarbetsstationen. Kopiera *ADMX-filerna* `%SYSTEMROOT%\PolicyDefinitions` till och kopiera de språkspecifika *ADML-filerna* till `%SYSTEMROOT%\PolicyDefinitions\[Language-CountryRegion]`, där `Language-CountryRegion` matchar språket och regionen för *ADML-filerna.*
>
> Kopiera till exempel den engelska versionen av *ADML-filerna* i `\en-us` mappen.
>
> Du kan också lagra din administrativa mall för grupprincip centralt på domänkontrollanterna som ingår i azure AD DS-hanterade domänen. Mer information finns i [Så här skapar och hanterar du administrativa mallar för centralarkivet för grupprinciper i Windows](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra).

## <a name="install-group-policy-management-tools"></a>Installera verktyg för grupprinciphantering

Om du vill skapa och konfigurera grupprincipobjekt måste du installera verktygen för grupprinciphantering. Dessa verktyg kan installeras som en funktion i Windows Server. Mer information om hur du installerar administrationsverktygen på en Windows-klient finns i installera [RSAT (Remote Server Administration Tools).][install-rsat]

1. Logga in på den virtuella datorn för hantering. Steg om hur du ansluter med Azure-portalen finns i [Ansluta till en Virtuell Windows Server][connect-windows-server-vm].
1. **Serverhanteraren** ska öppnas som standard när du loggar in på den virtuella datorn. Om inte, välj **Serverhanteraren**på **Start-menyn** .
1. Välj **Lägg till roller och funktioner**i *instrumentpanelsfönstret* i **serverhanteraren.**
1. På sidan **Innan du börjar** i guiden Lägg till roller och *funktioner*väljer du **Nästa**.
1. För *installationstypen*lämnar du alternativet **Rollbaserad eller funktionsbaserad installation** markerat och väljer **Nästa**.
1. På sidan **Serverval** väljer du den aktuella virtuella datorn i serverpoolen, till exempel *myvm.aaddscontoso.com*och väljer sedan **Nästa**.
1. Klicka på **Nästa**på sidan **Serverroller** .
1. Välj funktionen **Grupprinciphantering** på sidan **Funktioner.**

    ![Installera "Grupprinciphantering" från sidan Funktioner](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management.png)

1. På sidan **Bekräftelse** väljer du **Installera**. Det kan ta en minut eller två att installera verktygen för grupprinciphantering.
1. När funktionsinstallationen är klar väljer du **Stäng** för att avsluta guiden **Lägg till roller och funktioner.**

## <a name="open-the-group-policy-management-console-and-edit-an-object"></a>Öppna konsolen Grupprinciphantering och redigera ett objekt

Standardgruppprincipobjekt (GRUPPRINCIPOBJEKT) finns för användare och datorer i en Azure AD DS-hanterad domän. Med funktionen Grupprinciphantering installerad från föregående avsnitt ska vi visa och redigera ett befintligt grupprincipobjekt. I nästa avsnitt skapar du en anpassad gpo.

> [!NOTE]
> Om du vill administrera grupprincipen i en Azure AD DS-hanterad domän måste du vara inloggad på ett användarkonto som är medlem i gruppen *AAD DC-administratörer.*

1. Välj **Administrationsverktyg**på Startskärmen . En lista över tillgängliga hanteringsverktyg visas, inklusive **grupprinciphantering** som är installerad i föregående avsnitt.
1. Om du vill öppna konsolen Grupprinciphantering (GPMC) väljer du **Grupprinciphantering**.

    ![Konsolen Grupprinciphantering öppnas redo att redigera grupprincipobjekt](./media/active-directory-domain-services-admin-guide/gp-management-console.png)

Det finns två inbyggda grupprincipobjekt (GPOs) i en Azure AD DS-hanterad domän - en för *behållaren AADDC-datorer* och en för *behållaren för AADDC-användare.* Du kan anpassa dessa grupprincipobjekt för att konfigurera grupprincipen efter behov i din Azure AD DS-hanterade domän.

1. Expandera noden **Skog: aaddscontoso.com** i konsolen **Grupprinciphantering.** Expandera sedan **noderna Domäner.**

    Det finns två inbyggda behållare för *AADDC-datorer* och *AADDC-användare*. Var och en av dessa behållare har en standard gpo tillämpas på dem.

    ![Inbyggda grupprincipobjekt som tillämpas på standardbehållarna "AADDC-datorer" och "AADDC-användare"](./media/active-directory-domain-services-admin-guide/builtin-gpos.png)

1. Dessa inbyggda grupprincipobjekt kan anpassas för att konfigurera specifika gruppprinciper på din Azure AD DS-hanterade domän. Högerval av en av grupprincipobjekten, till exempel *GPO för AADDC-datorer,* och välj sedan **Redigera...**.

    ![Välj alternativet "Redigera" en av de inbyggda grupprincipobjekten](./media/active-directory-domain-services-admin-guide/edit-builtin-gpo.png)

1. Verktyget Redigerare för grupprinciphantering öppnas så att du kan anpassa grupprincipobjektet, till exempel *Kontoprinciper:*

    ![Anpassa dokument som anges för att konfigurera inställningar efter behov](./media/active-directory-domain-services-admin-guide/gp-editor.png)

    När du är klar väljer du **Spara fil >** för att spara principen. Datorer uppdaterar grupprincipen som standard var 90:e minut och tillämpar de ändringar du har gjort.

## <a name="create-a-custom-group-policy-object"></a>Skapa ett anpassat grupprincipobjekt

Om du vill gruppera liknande principinställningar skapar du ofta ytterligare grupprincipobjekt i stället för att använda alla nödvändiga inställningar i den enda standardgruppgruppen för grupprincipobjekt. Med Azure AD DS kan du skapa eller importera egna anpassade grupprincipobjekt och länka dem till en anpassad organisationsenhet. Om du först behöver skapa en anpassad organisationsenhet läser du [skapa en anpassad organisationsenhet i en Azure AD DS-hanterad domän](create-ou.md).

1. Välj din anpassade organisationsenhet (OU), till exempel *MyCustomOU*i konsolen **Grupprinciphantering.** Högerklicka på organisationsenheten och välj **Skapa ettrincipobjekt i den här domänen och länka det här...**:

    ![Skapa ett anpassat grupprincipobjekt i konsolen Grupprinciphantering](./media/active-directory-domain-services-admin-guide/gp-create-gpo.png)

1. Ange ett namn för det nya gpo-numret, till exempel *Min anpassade GPO,* och välj sedan **OK**. Du kan också basera den här anpassade gpo:n på en befintlig uppsättning principalternativ.

    ![Ange ett namn för det nya anpassade gpo](./media/active-directory-domain-services-admin-guide/gp-specify-gpo-name.png)

1. Det anpassade gpo-programmet skapas och länkas till din anpassade organisationsenhet. Om du nu vill konfigurera principinställningarna högerklickar du på den anpassade nationella användarobjektet och väljer **Redigera...**

    ![Välj alternativet "Redigera" ditt anpassaderincipobjekt](./media/active-directory-domain-services-admin-guide/gp-gpo-created.png)

1. **Redigeraren för grupprinciphantering** öppnas så att du kan anpassa grupprincipobjektet:

    ![Anpassa dokument som anges för att konfigurera inställningar efter behov](./media/active-directory-domain-services-admin-guide/gp-customize-gpo.png)

    När du är klar väljer du **Spara fil >** för att spara principen. Datorer uppdaterar grupprincipen som standard var 90:e minut och tillämpar de ändringar du har gjort.

## <a name="next-steps"></a>Nästa steg

Mer information om tillgängliga grupprincipinställningar som du kan konfigurera med konsolen Grupprinciphantering finns i [Arbeta med inställningar för grupprinciper][group-policy-console].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm

<!-- EXTERNAL LINKS -->
[group-policy-overview]: /previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831791(v=ws.11)
[install-rsat]: /windows-server/remote/remote-server-administration-tools#BKMK_Thresh
[group-policy-console]: /previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789194(v=ws.11)
