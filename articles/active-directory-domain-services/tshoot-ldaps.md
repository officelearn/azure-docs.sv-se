---
title: Felsöka säker LDAP (LDAPS) i Azure AD Domain Services | Microsoft Docs
description: Felsöka säkert LDAP (LDAPS) för en Azure AD Domain Services-hanterad domän
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
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
ms.author: mstephen
ms.openlocfilehash: c9732545e1759ea23d62c0a56379e3868ed40a0b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66246636"
---
# <a name="troubleshoot-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Felsöka säkert LDAP (LDAPS) för en Azure AD Domain Services-hanterad domän

## <a name="connection-issues"></a>Anslutningsproblem
Om du har problem med att ansluta till den hanterade domänen med säkert LDAP:

* Utfärdaren kedja av certifikatet för säkert LDAP måste vara betrott på klienten. Du kan välja att lägga till rotcertifikatutfärdaren i arkivet Betrodda rotcertifikat på klienten att upprätta förtroendet.
* Kontrollera att LDAP-klient (till exempel ldp.exe) ansluter till säker LDAP-slutpunkten med hjälp av ett DNS-namn, inte IP-adress.
* Kontrollera DNS-namnet LDAP-klienten ansluter till. Det måste matcha den offentliga IP-adressen för säkert LDAP på den hanterade domänen.
* Kontrollera att certifikatet för säkert LDAP för din hanterade domän har DNS-namnet i ett ämne eller Alternativt ämnesnamn-attributet.
* NSG-inställningarna för det virtuella nätverket måste tillåta trafik till port 636 från internet. Det här steget gäller endast om du har aktiverat åtkomst med säkert LDAP via internet.


## <a name="need-help"></a>Behöver du hjälp?
Om du fortfarande har problem med att ansluta till den hanterade domänen med säkert LDAP [kontakta produktteamet](contact-us.md) om du behöver hjälp. Inkludera följande information för att diagnosticera problemet bättre:
* En skärmbild av ldp.exe anslutningen och misslyckas.
* En Azure AD-klient-ID och DNS-domännamnet för den hanterade domänen.
* Exakta användarnamnet som du försöker binda som.


## <a name="related-content"></a>Relaterat innehåll
* [Azure AD Domain Services – komma igång-guiden](create-instance.md)
* [Hantera en Azure AD Domain Services-domän](manage-domain.md)
* [Grunderna i LDAP-fråga](https://technet.microsoft.com/library/aa996205.aspx)
* [Hantera en grupprincip för Azure AD Domain Services](manage-group-policy.md)
* [Nätverkssäkerhetsgrupper](../virtual-network/security-overview.md)
* [Skapa en Nätverkssäkerhetsgrupp](../virtual-network/tutorial-filter-network-traffic.md)
