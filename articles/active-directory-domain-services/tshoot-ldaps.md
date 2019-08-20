---
title: Felsöka säkert LDAP (LDAPs) i Azure AD Domain Services | Microsoft Docs
description: Felsöka säkert LDAP (LDAPs) för en Azure AD Domain Services hanterad domän
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 445c60da-e115-447b-841d-96739975bdf6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: 285af0e5e5d5ab03027fc29064a5f3623ed10e2f
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617049"
---
# <a name="troubleshoot-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Felsöka säkert LDAP (LDAPs) för en Azure AD Domain Services hanterad domän

## <a name="connection-issues"></a>Anslutningsproblem
Om du har problem med att ansluta till den hanterade domänen med säker LDAP:

* Utfärdarens kedja av det säkra LDAP-certifikatet måste vara betrodd på klienten. Du kan välja att lägga till rot certifikat utfärdaren i arkivet för betrodda rot certifikat på klienten för att upprätta förtroendet.
* Kontrol lera att LDAP-klienten (till exempel Ldp. exe) ansluter till en säker LDAP-slutpunkt med ett DNS-namn, inte IP-adressen.
* Kontrol lera DNS-namnet som LDAP-klienten ansluter till. Den måste matcha den offentliga IP-adressen för säker LDAP på den hanterade domänen.
* Kontrol lera att det säkra LDAP-certifikatet för din hanterade domän har DNS-namnet i attributen subject eller alternativt namn på certifikat mottagare.
* NSG-inställningarna för det virtuella nätverket måste tillåta trafik till port 636 från Internet. Det här steget gäller endast om du har aktiverat säker LDAP-åtkomst via Internet.


## <a name="need-help"></a>Behöver du hjälp?
Om du fortfarande har problem med att ansluta till den hanterade domänen med säker LDAP kan du [Kontakta produkt teamet](contact-us.md) för att få hjälp. Ta med följande information för att diagnostisera problemet bättre:
* En skärm bild av Ldp. exe som gör anslutningen och fungerar inte.
* Ditt Azure AD-klient-ID och DNS-domännamnet för din hanterade domän.
* Exakt användar namn som du försöker binda som.


## <a name="related-content"></a>Relaterat innehåll
* [Azure AD Domain Services-Komma igång guide](tutorial-create-instance.md)
* [Hantera en Azure AD Domain Services domän](tutorial-create-management-vm.md)
* [Grundläggande om LDAP-frågor](https://technet.microsoft.com/library/aa996205.aspx)
* [Hantera grupprincip för Azure AD Domain Services](manage-group-policy.md)
* [Nätverkssäkerhetsgrupper](../virtual-network/security-overview.md)
* [Skapa en nätverks säkerhets grupp](../virtual-network/tutorial-filter-network-traffic.md)
