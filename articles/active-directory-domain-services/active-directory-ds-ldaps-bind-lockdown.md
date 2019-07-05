---
title: Binda med säkert LDAP (LDAPS) till en hanterad Azure AD Domain Services-domän | Microsoft Docs
description: Binda till en Azure AD Domain Services-hanterad domän med hjälp av säker LDAP (LDAPS)
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 6871374a-0300-4275-9a45-a39a52c65ae4
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: iainfou
ms.openlocfilehash: df0b3d27eec478280a33be831a2431eccdf05a74
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67483372"
---
# <a name="bind-to-an-azure-ad-domain-services-managed-domain-using-secure-ldap-ldaps"></a>Binda till en Azure AD Domain Services-hanterad domän med hjälp av säker LDAP (LDAPS)

## <a name="before-you-begin"></a>Innan du börjar
Fullständig [uppgift 4 – konfigurera DNS för att komma åt den hanterade domänen från internet](active-directory-ds-ldaps-configure-dns.md).


## <a name="task-5-bind-to-the-managed-domain-over-ldap-using-ldpexe"></a>Uppgift 5: Binda till den hanterade domänen via LDAP med hjälp av LDP.exe
Du kan använda verktyget LDP.exe som ingår i Remote Server Administration tools paketet att binda och söka över LDAP.

Öppna LDP och ansluta till den hanterade domänen. Klicka på **anslutning** och klicka på **Connect...**  på menyn. Ange DNS-domännamnet för den hanterade domänen. Ange porten som ska användas för anslutningar. Använd port 389 för LDAP-anslutningar. Använd port 636 för LDAPS-anslutningar. Klicka på **OK** knapp för att ansluta till den hanterade domänen.

Nu ska binda till den hanterade domänen. Klicka på **anslutning** och klicka på **binda...**  på menyn. Ange autentiseringsuppgifter för ett konto som hör till gruppen ”AAD DC-administratörer”.

> [!IMPORTANT]
> Användare (och tjänstkonton) kan inte utföra enkla LDAP-bindningar om du har inaktiverat NTLM synkronisering av lösenordshash på din Azure AD Domain Services-instans.  Mer information om hur du inaktiverar NTLM synkronisering av lösenordshash [skydda din hanterade domän i Azure AD DOmain Services](secure-your-domain.md).
>
>

Välj **visa**, och välj sedan **trädet** på menyn. Lämna fältet Bas-DN tom och klicka på OK. Navigera till den behållare som du vill söka efter och högerklicka på behållaren väljer.

> [!TIP]
> - Användare och grupper som synkroniserats från Azure AD lagras i den **AADDC-användare** organisationsenhet. Sökvägen för den här organisationsenheten ut ```OU=AADDC Users,DC=CONTOSO100,DC=COM```.
> - Datorkontona för datorer som är anslutna till den hanterade domänen lagras i den **AADDC-datorer** organisationsenhet. Sökvägen för den här organisationsenheten ut ```OU=AADDC Computers,DC=CONTOSO100,DC=COM```.
>
>

Mer information - [grunderna för LDAP-fråga](https://docs.microsoft.com/windows/desktop/ad/creating-a-query-filter)


## <a name="task-6-lock-down-secure-ldap-access-to-your-managed-domain-over-the-internet"></a>Uppgift 6: Låsa åtkomst med säkert LDAP till din hanterade domän via internet
> [!NOTE]
> Hoppa över konfigurationsåtgärden om du inte har aktiverat LDAPS åtkomst till den hanterade domänen via internet.
>
>

Innan du utför den här åtgärden slutförts stegen som beskrivs i [uppgift 3](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md).

När du aktiverar LDAPS åtkomst via internet till din hanterade domän, skapas ett säkerhetshot. Den hanterade domänen kan nås från internet på den port som används för säkert LDAP (det vill säga port 636). Du kan välja att begränsa åtkomsten till den hanterade domänen till kända IP-adresser. Skapa en nätverkssäkerhetsgrupp (NSG) och koppla den till undernätet där du har aktiverat Azure AD Domain Services.

Exemplet NSG i följande tabell låser åtkomst med säkert LDAP via internet. Regler i NSG tillåter inkommande åtkomst med säkert LDAP via TCP-port 636 bara från en angiven mängd av IP-adresser. 'DenyAll' Standardregeln gäller för all annan inkommande trafik från internet. NSG-regel som tillåter LDAPS via internet från den angivna IP-adresser har högre prioritet än DenyAll NSG-regel.

![Exemplet NSG till säker LDAPS åtkomst via internet](./media/active-directory-domain-services-admin-guide/secure-ldap-sample-nsg.png)

**Mer information** - [Nätverkssäkerhetsgrupper](../virtual-network/security-overview.md).


## <a name="related-content"></a>Relaterat innehåll
* [Azure AD Domain Services – komma igång-guiden](create-instance.md)
* [Hantera en Azure AD Domain Services-domän](manage-domain.md)
* [Grunderna i LDAP-fråga](https://docs.microsoft.com/windows/desktop/ad/creating-a-query-filter)
* [Hantera en grupprincip för Azure AD Domain Services](manage-group-policy.md)
* [Nätverkssäkerhetsgrupper](../virtual-network/security-overview.md)
* [Skapa en Nätverkssäkerhetsgrupp](../virtual-network/tutorial-filter-network-traffic.md)


## <a name="next-step"></a>Nästa steg
[Felsöka säker LDAP på en hanterad domän](tshoot-ldaps.md)
