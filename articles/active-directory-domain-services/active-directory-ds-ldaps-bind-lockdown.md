---
title: Bind med säkert LDAP (LDAPs) till en Azure AD Domain Services hanterad domän | Microsoft Docs
description: Binda till en Azure AD Domain Services hanterad domän med hjälp av säker LDAP (LDAPs)
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
ms.openlocfilehash: cbc5bee0f4cc59f59af6e3f57219279cd8fcb030
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68988587"
---
# <a name="bind-to-an-azure-ad-domain-services-managed-domain-using-secure-ldap-ldaps"></a>Binda till en Azure AD Domain Services hanterad domän med hjälp av säker LDAP (LDAPs)

## <a name="before-you-begin"></a>Innan du börjar
Slutför [uppgift 4 – Konfigurera DNS för åtkomst till den hanterade domänen från Internet](active-directory-ds-ldaps-configure-dns.md).


## <a name="task-5-bind-to-the-managed-domain-over-ldap-using-ldpexe"></a>Uppgift 5: Binda till den hanterade domänen via LDAP med LDP. exe
Du kan använda verktyget LDP. exe som ingår i paketet verktyg för fjärrserveradministration för att binda och söka via LDAP.

Öppna först LDP och Anslut till den hanterade domänen. Klicka på **anslutning** och klicka på **Anslut...** på menyn. Ange DNS-domännamnet för den hanterade domänen. Ange den port som ska användas för anslutningar. Använd port 389 för LDAP-anslutningar. För LDAPs-anslutningar använder du Port 636. Klicka på knappen **OK** för att ansluta till den hanterade domänen.

Bind sedan till den hanterade domänen. Klicka på **anslutning** och klicka på **BIND...** på menyn. Ange autentiseringsuppgifterna för ett användar konto som tillhör gruppen "AAD DC-administratörer".

> [!IMPORTANT]
> Användare (och tjänst konton) kan inte utföra enkla LDAP-bindningar om du har inaktiverat NTLM Password hash-synkronisering på din Azure AD Domain Services-instans.  Mer information om hur du inaktiverar hash-synkronisering för NTLM-lösenord finns [i skydda din Azure AD Domain Services hanterade domän](secure-your-domain.md).
>
>

Välj **Visa**och välj sedan **träd** i menyn. Lämna fältet grundläggande DN tomt och klicka på OK. Navigera till den behållare som du vill söka, högerklicka på behållaren och välj Sök.

> [!TIP]
> - Användare och grupper som synkroniseras från Azure AD lagras i organisationsenheten **AADDC Users** . Sök vägen för organisationsenheten ser ut ```OU=AADDC Users,DC=CONTOSO100,DC=COM```.
> - Dator konton för datorer som är anslutna till den hanterade domänen lagras i organisationsenheten **AADDC datorer** . Sök vägen för organisationsenheten ser ut ```OU=AADDC Computers,DC=CONTOSO100,DC=COM```.
>
>

Mer information – [grundläggande om LDAP-frågor](https://docs.microsoft.com/windows/desktop/ad/creating-a-query-filter)


## <a name="task-6-lock-down-secure-ldap-access-to-your-managed-domain-over-the-internet"></a>Uppgift 6: Lås säker LDAP-åtkomst till din hanterade domän via Internet
> [!NOTE]
> Om du inte har aktiverat LDAPs åtkomst till den hanterade domänen via Internet hoppar du över den här konfigurations åtgärden.
>
>

Innan du påbörjar den här uppgiften slutför du stegen som beskrivs i [uppgift 3](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md).

När du aktiverar LDAPs-åtkomst via Internet till din hanterade domän, skapas ett säkerhetshot. Den hanterade domänen kan kommas åt från Internet på den port som används för säker LDAP (det vill säga port 636). Du kan välja att begränsa åtkomsten till den hanterade domänen till speciella kända IP-adresser. Skapa en nätverks säkerhets grupp (NSG) och koppla den till det undernät där du har aktiverat Azure AD Domain Services.

Exemplet NSG i följande tabell låser säker LDAP-åtkomst via Internet. Reglerna i NSG tillåter inkommande säker LDAP-åtkomst över TCP-port 636 endast från en angiven uppsättning IP-adresser. Standard regeln ' DenyAll ' gäller för all annan inkommande trafik från Internet. NSG-regeln för att tillåta LDAPs åtkomst via Internet från angivna IP-adresser har högre prioritet än DenyAll NSG-regeln.

![Exempel på NSG för säker åtkomst via LDAP via Internet](./media/active-directory-domain-services-admin-guide/secure-ldap-sample-nsg.png)

**Mer information** - om[nätverks säkerhets grupper](../virtual-network/security-overview.md).


## <a name="related-content"></a>Relaterat innehåll
* [Azure AD Domain Services-Komma igång guide](create-instance.md)
* [Hantera en Azure AD Domain Services domän](manage-domain.md)
* [Grundläggande om LDAP-frågor](https://docs.microsoft.com/windows/desktop/ad/creating-a-query-filter)
* [Hantera grupprincip för Azure AD Domain Services](manage-group-policy.md)
* [Nätverkssäkerhetsgrupper](../virtual-network/security-overview.md)
* [Skapa en nätverks säkerhets grupp](../virtual-network/tutorial-filter-network-traffic.md)


## <a name="next-step"></a>Nästa steg
[Felsöka säker LDAP på en hanterad domän](tshoot-ldaps.md)
