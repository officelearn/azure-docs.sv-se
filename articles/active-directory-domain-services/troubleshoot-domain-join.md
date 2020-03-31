---
title: Felsöka domänanslutning med Azure AD Domain Services | Microsoft-dokument
description: Lär dig hur du felsöker vanliga problem när du försöker domän ansluta till en virtuell dator eller ansluta ett program till Azure Active Directory Domain Services och du kan inte ansluta eller autentisera till den hanterade domänen.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/02/2019
ms.author: iainfou
ms.openlocfilehash: f187dba4eace61695a72e4b7b08731e65ff0d7f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299116"
---
# <a name="troubleshoot-domain-join-problems-with-an-azure-ad-domain-services-managed-domain"></a>Felsöka problem med domänkoppling med en hanterad Azure AD Domain Services-domän

NÃ¤r du fÃ¶rsÃ¶k fÃ¶rsÃ¶k att ansluta en virtuell dator (VM) eller ansluta ett program till en AD DS -hanterad domän (Azure Active Directory Domain Services) kan du få ett felmeddelande om att du inte kan göra det. Om du vill felsöka problem med domänkopplingen granskar du vid vilket av följande punkter du har problem med:

* Om du inte får en autentiseringsfråga kan den virtuella datorn eller programmet inte ansluta till den Azure AD DS-hanterade domänen.
    * Börja felsöka [anslutningsproblem för domänanslutning](#connectivity-issues-for-domain-join).
* Om du får ett fel under autentiseringen lyckas anslutningen till den Hanterade Azure AD DS-domänen.
    * Börja felsöka [autentiseringsuppgifter-relaterade problem under domän-join](#credentials-related-issues-during-domain-join).

## <a name="connectivity-issues-for-domain-join"></a>Anslutningsproblem för domänanslutning

Om den virtuella datorn inte kan hitta azure AD DS-hanterad domän finns det vanligtvis en nätverksanslutning eller konfigurationsproblem. Läs följande felsökningssteg för att hitta och lösa problemet:

1. Kontrollera att den virtuella datorn är ansluten till samma, eller ett peer-kopplat, virtuellt nätverk som är aktiverat för Azure AD DS. Om inte, kan den virtuella datorn inte hitta och ansluta till domänen för att gå med.
    * Om den virtuella datorn inte är ansluten till samma virtuella nätverk bekräftar du att den virtuella nätverks peering- eller VPN-anslutningen är *aktiv* eller *ansluten* så att trafiken kan flöda korrekt.
1. Försök pinga domänen med domännamnet för den Hanterade Azure `ping aaddscontoso.com`AD DS-domänen, till exempel .
    * Om ping-svaret misslyckas kan du försöka pinga IP-adresserna för domänen som visas på översiktssidan i portalen för din Azure AD DS-hanterade domän, till exempel `ping 10.0.0.4`.
    * Om du kan pinga IP-adressen men inte domänen kan DNS vara felaktigt konfigurerad. Kontrollera att du har konfigurerat Azure AD DS-hanterade domän DNS-servrar för det virtuella nätverket.
1. Prova att tömma DNS-matcharens cache på `ipconfig /flushdns`den virtuella datorn, till exempel .

### <a name="network-security-group-nsg-configuration"></a>NSG-konfiguration (Network Security Group)

När du skapar en Azure AD DS-hanterad domän skapas också en nätverkssäkerhetsgrupp med lämpliga regler för en lyckad domänåtgärd. Om du redigerar eller skapar ytterligare regler för nätverkssäkerhetsgrupper kan du oavsiktligt blockera portar som krävs för att Azure AD DS ska kunna tillhandahålla anslutnings- och autentiseringstjänster. Dessa regler för nätverkssäkerhetsgrupper kan orsaka problem som att lösenordssynkronisering inte slutförs, att användare inte kan logga in eller domänkopplingsproblem.

Om du fortfarande har anslutningsproblem läser du följande felsökningssteg:

1. Kontrollera hälsostatusen för din Azure AD DS-hanterade domän i Azure-portalen. Om du har en avisering för *AADDS001*blockerar en regel för nätverkssäkerhetsgrupper åtkomst.
1. Granska de [portar och regler för nätverkssäkerhetsgrupper][network-ports]som krävs . Kontrollera att inga regler för nätverkssäkerhetsgrupper tillämpas på den virtuella datorn eller det virtuella nätverket som du ansluter från blockerar dessa nätverksportar.
1. När konfigurationsproblem för nätverkssäkerhetsgrupper har lösts försvinner *AADDS001-aviseringen* från hälsosidan om cirka 2 timmar. När nätverksanslutningen nu är tillgänglig försöker du ansluta till den virtuella datorn igen.

## <a name="credentials-related-issues-during-domain-join"></a>Autentiseringsrelaterade problem under domänanslutning

Om du får en dialogruta som ber om autentiseringsuppgifter för att gå med i Azure AD DS-hanterad domän kan den virtuella datorn ansluta till domänen med hjälp av det virtuella Azure-nätverket. Domänkopplingsprocessen misslyckas vid autentisering till domänen eller auktorisering för att slutföra domänanslutningsprocessen med hjälp av autentiseringsuppgifterna.

Om du vill felsöka problem med autentiseringsuppgifter läser du följande felsökningssteg:

1. Prova att använda UPN-formatet för `dee@aaddscontoso.onmicrosoft.com`att ange autentiseringsuppgifter, till exempel . Kontrollera att upn-funktionen är korrekt konfigurerad i Azure AD.
    * *SAMAccountName* för ditt konto kan skapas automatiskt om det finns flera användare med samma UPN-prefix i din klientorganisation eller om ditt UPN-prefix är alltför långt. Därför kan *SAMAccountName-formatet* för ditt konto skilja sig från vad du förväntar dig eller använder i din lokala domän.
1. Försök att använda autentiseringsuppgifterna för ett användarkonto som är en del av Azure AD DS-hanterade domänen för att ansluta virtuella datorer till den hanterade domänen.
1. Kontrollera att du har [aktiverat lösenordssynkronisering][enable-password-sync] och väntat tillräckligt länge för att den första lösenordssynkroniseringen ska slutföras.

## <a name="next-steps"></a>Nästa steg

En djupare förståelse av Active Directory-processerna som en del av domänkopplingen finns i [Problem med koppling och autentisering][join-authentication-issues].

Om du fortfarande har problem med att ansluta till den virtuella datorn till den Hanterade Azure AD DS-domänen [kan du hitta hjälp och öppna en supportbiljett för Azure Active Directory][azure-ad-support].

<!-- INTERNAL LINKS -->
[enable-password-sync]: tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[network-ports]: network-considerations.md#network-security-groups-and-required-ports
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md

<!-- EXTERNAL LINKS -->
[join-authentication-issues]: /previous-versions/windows/it-pro/windows-2000-server/cc961817(v=technet.10)
