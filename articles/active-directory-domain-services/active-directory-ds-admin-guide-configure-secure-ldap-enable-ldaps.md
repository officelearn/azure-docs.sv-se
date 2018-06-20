---
title: Konfigurera säker LDAP (LDAPS) i Azure AD Domain Services | Microsoft Docs
description: Konfigurera säker LDAP (LDAPS) för en Azure AD Domain Services-hanterad domän
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2018
ms.author: maheshu
ms.openlocfilehash: 81986fdd9cbfbeb41c794e2364bf7bfea1069742
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36211262"
---
# <a name="configure-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Konfigurera säker LDAP (LDAPS) för en Azure AD Domain Services-hanterad domän

## <a name="before-you-begin"></a>Innan du börjar
Se till att du har slutfört [uppgift 2 – exportera säker LDAP-certifikatet till en. PFX-filen](active-directory-ds-admin-guide-configure-secure-ldap-export-pfx.md).


## <a name="task-3---enable-secure-ldap-for-the-managed-domain-using-the-azure-portal"></a>Uppgift 3 – aktivera säker LDAP för den hanterade domänen med hjälp av Azure portal
Utför följande konfigurationssteg för att aktivera säker LDAP:

1. Navigera till den  **[Azure-portalen](https://portal.azure.com)**.

2. Söka efter domain services i den **söka resurser** sökrutan. Välj **Azure AD Domain Services** från sökresultaten. Den **Azure AD Domain Services** sidan listar din hanterade domän.

    ![Hitta hanterad domän som har etablerats](./media/getting-started/domain-services-provisioning-state-find-resource.png)

2. Klicka på namnet på den hanterade domänen (till exempel ”contoso100.com”) för att se mer information om domänen.

    ![DS - Etableringsstatus](./media/getting-started/domain-services-provisioning-state.png)

3. Klicka på **säker LDAP** i navigeringsfönstret.

    ![DS - säker LDAP-sida](./media/active-directory-domain-services-admin-guide/secure-ldap-blade.png)

4. Säker LDAP-åtkomst till din hanterade domän är inaktiverat som standard. Växla **säkert LDAP** till **aktivera**.

    ![Aktivera säker LDAP](./media/active-directory-domain-services-admin-guide/secure-ldap-blade-configure.png)
5. Som standard inaktiveras säker LDAP-åtkomst till din hanterade domän via internet. Växla **ger LDAP åtkomst via internet** till **aktivera**om det behövs. 

    > [!WARNING]
    > När du aktiverar säker LDAP-åtkomst via internet, är din domän sårbara för lösenord brute force-attacker via internet. Därför rekommenderar vi hur du konfigurerar en NSG till låsa åtkomst till nödvändiga käll-IP-adressintervall. Se instruktionerna för att [Lås LDAPS åtkomst till din hanterade domän via internet](#task-5---lock-down-secure-ldap-access-to-your-managed-domain-over-the-internet).
    >

6. Klicka på mappen ikonen följande **. PFX-filen med säker LDAP-certifikat**. Ange sökvägen till PFX-filen med certifikatet för säker LDAP-åtkomst till den hanterade domänen.

7. Ange den **lösenord för att dekryptera. PFX-filen**. Ange samma lösenord som du använde när du exporterar certifikatet till PFX-filen.

8. När du är klar klickar du på den **spara** knappen.

9. Du ser ett meddelande som informerar du säkert LDAP konfigureras för den hanterade domänen. Andra inställningar för domänen kan inte ändras förrän åtgärden har slutförts.

    ![Konfigurera säker LDAP för den hanterade domänen](./media/active-directory-domain-services-admin-guide/secure-ldap-blade-configuring.png)

> [!NOTE]
> Det tar cirka 10 – 15 minuter för att aktivera säker LDAP för din hanterade domän. Om det tillhandahållna säkra LDAP-certifikatet inte matchar kriterierna som krävs, säker LDAP har inte aktiverats för din katalog och du ser ett fel. Till exempel domännamnet är felaktig, certifikatet redan har upphört att gälla eller upphör snart att gälla. I det här fallet, försök igen med ett giltigt certifikat.
>
>

<br>

## <a name="task-4---configure-dns-to-access-the-managed-domain-from-the-internet"></a>Uppgift 4 – konfigurera DNS för att komma åt den hanterade domänen från internet
> [!NOTE]
> **Frivillig uppgift** - om du inte planerar att få tillgång till den hanterade domänen med LDAPS via internet, hoppa över det här konfiguration.
>
>

Innan du börjar den här uppgiften kan du kontrollera att du har slutfört stegen som beskrivs i [uppgift 3](#task-3---enable-secure-ldap-for-the-managed-domain-using-the-azure-portal-preview).

När du har aktiverat säker LDAP-åtkomst via internet för din hanterade domän, måste du uppdatera DNS så att klientdatorerna kan hitta den här hanterade domänen. I slutet av uppgift 3 visas en extern IP-adress på det **egenskaper** fliken i **extern IP-adress för LDAPS åtkomst**.

Konfigurera externa DNS-providern så att DNS-namnet på den hanterade domänen (till exempel ldaps.contoso100.com) pekar på den här externa IP-adressen. Till exempel skapa följande DNS-post:

    ldaps.contoso100.com  -> 52.165.38.113

Det - du är nu redo att ansluta till den hanterade domänen med säker LDAP via internet.

> [!WARNING]
> Kom ihåg att klientdatorer måste lita LDAPS-certifikatets utfärdare för att kunna ansluta till den hanterade domänen med LDAPS. Om du använder en betrodd offentlig certifikatutfärdare, behöver du inte göra något eftersom dessa certifikatutfärdare litar på klientdatorer. Om du använder ett självsignerat certifikat, installera den offentliga delen av det självsignerade certifikatet i det betrodda certifikatarkivet på klientdatorn.
>
>


## <a name="task-5---lock-down-secure-ldap-access-to-your-managed-domain-over-the-internet"></a>Uppgift 5 – Lås säker LDAP-åtkomst till din hanterade domän via internet
> [!NOTE]
> Hoppa över konfigurationsåtgärden om du inte har aktiverat LDAPS åtkomst till den hanterade domänen via internet.
>
>

Innan du börjar den här uppgiften kan du kontrollera att du har slutfört stegen som beskrivs i [uppgift 3](#task-3---enable-secure-ldap-for-the-managed-domain-using-the-azure-portal-preview).

Exponera din hanterade domän för LDAPS åtkomst via internet representerar en säkerhetsrisk. Den hanterade domänen kan nås från internet på porten som används för säker LDAP (det vill säga port 636). Därför kan du välja att begränsa åtkomsten till den hanterade domänen till specifika kända IP-adresser. Skapa en nätverkssäkerhetsgrupp (NSG) för förbättrad säkerhet och associera den med undernätet där du har aktiverat Azure AD Domain Services.

I följande tabell visas ett exempel på en NSG som du kan konfigurera för att låsa säker LDAP-åtkomst via internet. NSG: N innehåller en uppsättning regler som tillåter inkommande säker LDAP-åtkomst via TCP-port 636 endast från en angiven mängd av IP-adresser. 'DenyAll' Standardregeln gäller för inkommande trafik från internet. NSG-regel som tillåter LDAPS åtkomst via internet från den angivna IP-adresser har högre prioritet än DenyAll NSG-regeln.

![Exempel NSG till säker LDAPS åtkomst via internet](./media/active-directory-domain-services-admin-guide/secure-ldap-sample-nsg.png)

**Mer information** - [Nätverkssäkerhetsgrupper](../virtual-network/security-overview.md).

<br>


## <a name="troubleshooting"></a>Felsökning
Om du har problem att ansluta till den hanterade domänen med säker LDAP, utför du följande felsökningssteg:
* Se till att utfärdaren kedjan för säker LDAP-certifikatet är betrott på klienten. Du kan välja att lägga till rotcertifikatutfärdaren certifikatarkivet för betrodda rotcertifikatutfärdare på klienten att upprätta förtroendet.
* Kontrollera att LDAP-klient (till exempel ldp.exe) ansluter till den säkra LDAP-slutpunkten med hjälp av ett DNS-namn inte IP-adress.
* Kontrollera DNS-namnet för LDAP-klient ansluter till den offentliga IP-adressen matchar för säkra LDAP på den hanterade domänen.
* Verifiera det säkra LDAP-certifikatet för din hanterade domän har DNS-namnet i ett ämne eller Alternativt ämnesnamn attributet.
* Om du ansluter via säker LDAP via internet, kontrollera NSG-inställningarna för det virtuella nätverket kan trafik till port 636 från internet.

Om du fortfarande har problem att ansluta till den hanterade domänen med säker LDAP [kontakta produktteamet](active-directory-ds-contact-us.md) för hjälp. Ange följande information för att diagnosticera problemet bättre:
* En skärmbild av ldp.exe anslutning och misslyckas.
* Din Azure AD-klient-ID och DNS-domännamnet för din hanterade domän.
* Exakt användarnamnet som du vill binda som.


## <a name="related-content"></a>Relaterat innehåll
* [Azure AD Domain Services - komma igång-guide](active-directory-ds-getting-started.md)
* [Administrera en Azure AD Domain Services-hanterad domän](active-directory-ds-admin-guide-administer-domain.md)
* [Administrera Grupprincip i en Azure AD Domain Services-hanterad domän](active-directory-ds-admin-guide-administer-group-policy.md)
* [Nätverkssäkerhetsgrupper](../virtual-network/security-overview.md)
* [Skapa en säkerhetsgrupp för nätverk](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)
