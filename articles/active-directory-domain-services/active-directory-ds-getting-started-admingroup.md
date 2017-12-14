---
title: "Azure Active Directory Domain Services: Komma igång | Microsoft Docs"
description: Aktivera Azure Active Directory Domain Services med Azure-portalen
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: maheshu
ms.openlocfilehash: 2a171490faf9804196c93d33c5ee74d22533c044
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal"></a>Aktivera Azure Active Directory Domain Services med Azure-portalen


## <a name="task-3-configure-administrative-group"></a>Uppgift 3: Konfigurera administrativ grupp
I den här uppgiften skapar du en administrativ grupp i Azure AD-katalogen. Den här särskilda administrativa gruppen kallas *AAD DC administratörer*. Medlemmar i den här gruppen har beviljats administratörsbehörighet på datorer som är ansluten till domänen i den hanterade domänen. Den här gruppen har lagts till administratörsgruppen på domänanslutna datorer. Medlemmar i den här gruppen kan dessutom använda Fjärrskrivbord för att fjärransluta till domänanslutna datorer.

> [!NOTE]
> Du har inte behörighet som domänadministratör eller Företagsadministratörer på den hanterade domänen som du skapat med hjälp av Azure Active Directory Domain Services. Dessa behörigheter är reserverade av tjänsten på hanterade domäner och görs inte tillgängliga för användare i klienten. Du kan dock använda särskilda administrativa gruppen som skapats i konfigurationsåtgärden utföra vissa Privilegierade åtgärder. Dessa åtgärder omfattar ansluta datorer till domänen, som hör till gruppen administration på domänanslutna datorer och konfigurera Grupprincip.
>

Den administrativa gruppen skapas automatiskt i Azure AD-katalogen. Den här gruppen kallas AAD DC-administratörer. Om du har en befintlig grupp med det här namnet i Azure AD-katalogen, väljs den här gruppen. Du kan konfigurera medlemskap med den **administratörsgruppen** sidan i guiden.

1. För att konfigurera gruppmedlemskap, klickar du på **AAD DC administratörer**.

    ![Konfigurera gruppmedlemskap](./media/getting-started/domain-services-blade-admingroup.png)

2. Klicka på den **lägga till medlemmar** vill lägga till användare från din Azure AD-katalog i administratörsgruppen.

3. När du är klar klickar du på **OK** att gå vidare till den **sammanfattning** sidan i guiden.

4. På den **sammanfattning** sidan i guiden, granska konfigurationsinställningarna för den hanterade domänen. Du kan gå tillbaka till något steg i guiden för att göra ändringar, om det behövs. När du är klar klickar du på **OK** att skapa den nya Hantera domänen.

    ![Sammanfattning](./media/getting-started/domain-services-blade-summary.png)

5. Du ser ett meddelande som visar förloppet för distributionen av Azure AD Domain Services. Klicka på meddelandet om du vill se detaljerad status för distributionen.

    ![Meddelande - distribution pågår](./media/getting-started/domain-services-blade-deployment-in-progress.png)


## <a name="provision-your-managed-domain"></a>Etablera din hanterade domän
Processen för etablering din hanterade domän kan ta upp till en timme.

1. När din distribution pågår kan du söka efter domain services i den **söka resurser** sökrutan. Välj **Azure AD Domain Services** från sökresultaten. Den **Azure AD Domain Services** bladet visar den hanterade domänen som har etablerats.

    ![Hitta hanterad domän som har etablerats](./media/getting-started/domain-services-provisioning-state-find-resource.png)

2. Klicka på namnet på den hanterade domänen (till exempel ”contoso100.com”) för att se mer information om den hanterade domänen.

    ![DS - Etableringsstatus](./media/getting-started/domain-services-provisioning-state.png)

3. Den **översikt** visar att den hanterade domänen håller på att etableras. Du kan inte konfigurera den hanterade domänen förrän den är helt etablerad. Det kan ta upp till en timme för din hanterade domän ska etableras fullständigt.

    ![DS - översiktsflik under etableringsstatusen ](./media/getting-started/domain-services-provisioning-state-details.png)

4. När den hanterade domänen är helt etablerad, den **översikt** visar status för domänen som **kör**.

    ![Domain Services - översiktsflik vid full etablering](./media/getting-started/domain-services-provisioned.png)

5. På den **egenskaper** kan du se två IP-adresser på vilken domän domänkontrollanter är tillgängliga för det virtuella nätverket.

    ![Domain Services – fliken Egenskaper när helt etablerad](./media/getting-started/domain-services-provisioned-properties.png)


## <a name="need-help"></a>Behöver du hjälp?
Det kan ta en timme eller två för båda domänkontrollanterna för din hanterade domän ska etableras. Om distributionen misslyckas eller fastnar i tillståndet ”väntande” mer än ett par timmar kan passa på att [produktteamet be om hjälp](active-directory-ds-contact-us.md).


## <a name="next-step"></a>Nästa steg
Uppgift 4: [uppdatera DNS-inställningarna för det virtuella Azure-nätverket](active-directory-ds-getting-started-dns.md)
