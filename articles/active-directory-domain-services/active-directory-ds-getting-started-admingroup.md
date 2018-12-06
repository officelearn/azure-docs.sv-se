---
title: 'Azure Active Directory Domain Services: Komma igång | Microsoft Docs'
description: Aktivera Azure Active Directory Domain Services med Azure portal
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ergreenl
ms.openlocfilehash: ec51ba28c81e2aa2dbfe2a3dc55d9940c74ff673
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52959860"
---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal"></a>Aktivera Azure Active Directory Domain Services med Azure portal

## <a name="task-3-configure-administrative-group"></a>Uppgift 3: Konfigurera administratörsgrupp

I den här konfigurationsåtgärd skapar du en administrativ grupp i Azure AD-katalogen. Den här administrativa specialgrupp kallas *AAD DC-administratörer*. Medlemmar i den här gruppen har beviljats administratörsbehörighet på datorer som är anslutna till en domän till den hanterade domänen. Den här gruppen har lagts till i administratörsgruppen på domänanslutna datorer. Medlemmar i den här gruppen kan dessutom använda Fjärrskrivbord för att fjärransluta till domänanslutna datorer.

> [!NOTE]
> Du har inte behörighet som domänadministratör eller företagsadministratör i den hanterade domänen som du skapade med hjälp av Azure Active Directory Domain Services. I hanterade domäner behörigheterna är reserverade av tjänsten och görs inte tillgängliga för användare i klienten. Du kan dock använda den särskilda administrativa gruppen skapas i konfigurationsåtgärden för att utföra vissa Privilegierade åtgärder. Dessa åtgärder omfattar ansluta datorer till domänen, som hör till gruppen administration på domänanslutna datorer och konfigurera en Grupprincip.
>

Den administrativa gruppen skapas automatiskt i Azure AD-katalogen. Den här gruppen kallas ”AAD DC-administratörer”. Om du har en befintlig grupp med det här namnet i Azure AD-katalogen, väljs den här gruppen. Du kan konfigurera med medlemskap i **administratörsgruppen** sida i guiden.

1. Om du vill konfigurera gruppmedlemskap, klickar du på **AAD DC-administratörer**.

    ![Konfigurera gruppmedlemskap](./media/getting-started/domain-services-blade-admingroup.png)

2. Klicka på den **lägga till medlemmar** knappen för att lägga till användare från Azure AD-katalogen i administratörsgruppen.

3. När du är klar klickar du på **OK** att gå vidare till den **sammanfattning** i guiden.

## <a name="configure-synchronization"></a>Konfigurera synkronisering

Azure AD Domain Services tillåter antingen fullständig synkronisering av alla användare och grupper som är tillgängliga i Azure AD eller kan du välja begränsade synkronisering att synkronisera endast specifika grupper. Om du väljer den fullständiga synkroniseringen, kommer du att **inte** kan du välja begränsade synkronisering vid ett senare tillfälle. Mer information om begränsade synkronisering, på den [Azure AD Domain Services omfattar synkronisering artikeln](active-directory-ds-scoped-synchronization.md).

### <a name="full-synchronization"></a>Fullständig synkronisering

1. För fullständig synkronisering, klickar du på ”OK” längst ned på skärmen som fullständig redan väljs.
    ![Fullständig synkronisering](./media/active-directory-domain-services-admin-guide/create-sync-all.PNG)

### <a name="scoped-synchronization"></a>Begränsade synkronisering

1. Växla synkroniseringsknappen ”Databasomfattande” och en grupper-sida visas. Från den här, kan du se vilka grupper har redan markerats som ska synkroniseras till din hanterade domän.
    ![Begränsade synkronisering](media/active-directory-domain-services-admin-guide/create-sync-scoped.PNG)
2. Klicka på **Välj grupper** i det övre navigeringsfältet. Härifrån kan visas gruppväljare för en på sidan. Används för att markera fler grupper som ska synkroniseras med Azure AD Domain Services. När du är klar klickar du på **Välj** att Stäng Väljaren för gruppen och lägga till dessa grupper i den markerade listan.
    ![Begränsade synkronisering Välj grupper](media/active-directory-domain-services-admin-guide/create-sync-scoped-groupselect.PNG)
3. Klicka på **OK** att flytta till sammanfattningssidan.

## <a name="deploy-your-managed-domain"></a>Distribuera din hanterade domän

1. På den **sammanfattning** sidan i guiden igenom konfigurationsinställningarna för den hanterade domänen. Du kan gå tillbaka till alla steg i guiden för att göra ändringar om det behövs. När du är klar klickar du på **OK** att skapa den nya Hantera domänen.

    ![Sammanfattning](./media/getting-started/domain-services-blade-summary.png)

2. Du ser ett meddelande som visar förloppet för distributionen av Azure AD Domain Services. Klicka på meddelandet om du vill se detaljerad förloppet för distributionen.

    ![Meddelande - distribution pågår](./media/getting-started/domain-services-blade-deployment-in-progress.png)

## <a name="check-the-deployment-status-of-your-managed-domain"></a>Kontrollera distributionsstatusen för din hanterade domän

Processen för etablering av den hanterade domänen kan ta upp till en timme.

1. När distributionen pågår, du kan söka efter ”domain services” i den **Sök efter resurser** sökrutan. Välj **Azure AD Domain Services** från sökresultaten. Den **Azure AD Domain Services** bladet visar den hanterade domänen som håller på att etableras.

    ![Hitta hanterade domänen som håller på att etableras](./media/getting-started/domain-services-provisioning-state-find-resource.png)

2. Klicka på namnet på den hanterade domänen (till exempel ”contoso100.com”) om du vill ha mer information om den hanterade domänen.

    ![Domain Services - Etableringsstatus](./media/getting-started/domain-services-provisioning-state.png)

3. Den **översikt** fliken visar att den hanterade domänen håller på att etableras. Du kan inte konfigurera den hanterade domänen förrän det är helt etablerad. Det kan ta upp till en timme för din hanterade domän ska etableras helt.

    ![Domain Services - översiktsflik vid Etableringsstatus ](./media/getting-started/domain-services-provisioning-state-details.png)

4. När den hanterade domänen är helt etablerad, den **översikt** fliken visar domänsstatus som **kör**.

    ![Domain Services - översiktsflik vid full etablering](./media/getting-started/domain-services-provisioned.png)
    >[!NOTE]
    >Under etableringen skapar Azure AD Domain Services företagsprogram med namnet ”Domain Controller tjänster” och ”AzureActiveDirectoryDomainControllerServices” i din katalog. Dessa program krävs för att underhålla din hanterade domän. Det är viktigt att dessa inte tas bort när som helst.
    >

5. På den **egenskaper** fliken visas två IP-adresser där domäntjänster är tillgängliga för det virtuella nätverket.

    ![Domain Services – fliken Egenskaper när helt etablerad](./media/getting-started/domain-services-provisioned-properties.png)

## <a name="need-help"></a>Behöver du hjälp?

Det kan ta en timme eller två för båda domänkontrollanterna för din hanterade domän ska etableras. Om distributionen misslyckas eller fastnar i tillståndet ”väntar” i mer än ett par timmar kan passa på att [kontakta Produktteamet för att få hjälp](active-directory-ds-contact-us.md).

## <a name="next-step"></a>Nästa steg

Uppgift 4: [uppdatera DNS-inställningarna för det virtuella Azure-nätverket](active-directory-ds-getting-started-dns.md)
