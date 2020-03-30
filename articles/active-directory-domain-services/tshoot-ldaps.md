---
title: Felsöka säker LDAP i Azure AD Domain Services | Microsoft-dokument
description: Lär dig hur du felsöker säker LDAP (LDAPS) för en hanterad Azure Active Directory Domain Services-domän
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 445c60da-e115-447b-841d-96739975bdf6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 02/10/2020
ms.author: iainfou
ms.openlocfilehash: 22d1b6e2344256b52cfdbc48720a680a770a4216
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132171"
---
# <a name="troubleshoot-secure-ldap-connectivity-issues-to-an-azure-active-directory-domain-services-managed-domain"></a>Felsöka problem med säker LDAP-anslutning till en hanterad Azure Active Directory Domain Services-domän

Program och tjänster som använder LDAP (Lightweight Directory Access Protocol) för att kommunicera med Azure Active Directory Domain Services (Azure AD DS) kan [konfigureras för att använda säker LDAP](tutorial-configure-ldaps.md). Ett lämpligt certifikat och nödvändiga nätverksportar måste vara öppna för att säker LDAP ska fungera korrekt.

Den här artikeln hjälper dig att felsöka problem med säker LDAP-åtkomst i Azure AD DS.

## <a name="common-connection-issues"></a>Vanliga anslutningsproblem

Om du har problem med att ansluta till en Azure AD DS-hanterad domän med säker LDAP läser du följande felsökningssteg. Efter varje felsökningssteg kan du försöka ansluta till den Azure AD DS-hanterade domänen igen:

* Utfärdarkedjan för det säkra LDAP-certifikatet måste vara betrodd på klienten. Du kan lägga till rotcertifikatutfärdaren i det betrodda rotcertifikatarkivet på klienten för att upprätta förtroendet.
    * Se till att du [exporterar och tillämpar certifikatet på klientdatorer][client-cert].
* Kontrollera att det säkra LDAP-certifikatet för den hanterade domänen har DNS-namnet i *attributet Ämne* eller *Ämnesalternativnamn.*
    * Granska de [säkra LDAP-certifikatkraven][certs-prereqs] och skapa ett ersättningscertifikat om det behövs.
* Kontrollera att LDAP-klienten, till exempel *ldp.exe* ansluter till den säkra LDAP-slutpunkten med ett DNS-namn, inte IP-adressen.
    * Certifikatet som tillämpas på den Hanterade Azure AD DS-domänen innehåller inte TJÄNSTENS IP-adresser, bara DNS-namnen.
* Kontrollera DNS-namnet som LDAP-klienten ansluter till. Den måste lösa till den offentliga IP-adressen för säker LDAP på Azure AD DS-hanterad domän.
    * Om DNS-namnet matchas till den interna IP-adressen uppdaterar du DNS-posten så att den matchar till den externa IP-adressen.
* För extern anslutning måste nätverkssäkerhetsgruppen innehålla en regel som tillåter trafik till TCP-port 636 från Internet.
    * Om du kan ansluta till azure AD DS-hanterad domän med säker LDAP från resurser som är direkt anslutna till det virtuella nätverket men inte externa anslutningar, se till att du [skapar en regel för nätverkssäkerhetsgrupp för att tillåta säker LDAP-trafik][ldaps-nsg].

## <a name="next-steps"></a>Nästa steg

Om du fortfarande har problem [öppnar du en Azure-supportbegäran för][azure-support] ytterligare felsökningshjälp.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-ldaps]: tutorial-configure-ldaps.md
[certs-prereqs]: tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap
[client-cert]: tutorial-configure-ldaps.md#export-a-certificate-for-client-computers
[ldaps-nsg]: tutorial-configure-ldaps.md#lock-down-secure-ldap-access-over-the-internet
