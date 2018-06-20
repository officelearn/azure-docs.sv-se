---
title: 'Azure Active Directory Domain Services: Felsöka säker LDAP-konfiguration | Microsoft Docs'
description: Felsöka säker LDAP för Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: ''
editor: ''
ms.assetid: 81208c0b-8d41-4f65-be15-42119b1b5957
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: ergreenl
ms.openlocfilehash: dbe2715d76f18daf87b65871c26d73205ec2f7fd
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36218851"
---
# <a name="azure-ad-domain-services---troubleshooting-secure-ldap-configuration"></a>Azure AD Domain Services - felsökning säker LDAP-konfiguration

Den här artikeln innehåller lösningar för vanliga problem när [konfigurera säker LDAP](active-directory-ds-admin-guide-configure-secure-ldap.md) för Azure AD Domain Services.

## <a name="aadds101-secure-ldap-network-security-group-configuration"></a>AADDS101: Skydda Nätverkssäkerhetsgruppen för LDAP-konfiguration

**Varningsmeddelande:**

*Säker LDAP via internet har aktiverats för den hanterade domänen. Åtkomst till port 636 är inte låst nedåt med hjälp av en nätverkssäkerhetsgrupp. Det kan exponera användarkonton på den hanterade domänen att lösenord brute force-attacker.*

### <a name="secure-ldap-port"></a>Säker LDAP-port

När säker LDAP är aktiverat, rekommenderar vi att skapa ytterligare regler som tillåter inkommande LDAPS åtkomst endast från vissa IP-adresser. De här reglerna skydda din domän från brute force-attacker som kan utgöra ett hot mot säkerheten. Porten 636 ger tillgång till din hanterade domän. Här är hur du uppdaterar din NSG för att tillåta åtkomst för säker LDAP:

1. Navigera till den [Nätverkssäkerhetsgrupper fliken](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) i Azure-portalen
2. Välj NSG: N som är associerade med domänen från tabellen.
3. Klicka på **inkommande säkerhetsregler**
4. Skapa portregel 636
   1. Klicka på **Lägg till** i det övre navigeringsfältet.
   2. Välj **IP-adresser** för datakällan.
   3. Ange källa portintervall för den här regeln.
   4. Ange ”636” för portintervall som mål.
   5. Protokollet är **TCP**.
   6. Ge regeln ett lämpligt namn, beskrivning och prioritet. Prioritet för den här regeln ska vara högre än ”neka alla” regelns prioritet om du har en.
   7. Klicka på **OK**.
5. Kontrollera att regeln har skapats.
6. Kontrollera hälsotillståndet för din domän på två timmar så att du har slutfört stegen på rätt sätt.

> [!TIP]
> Porten 636 är inte den enda regeln som behövs för Azure AD Domain Services att fungera utan problem. Mer information finns i [nätverk riktlinjer](active-directory-ds-networking.md) eller [felsöka NSG configuration](active-directory-ds-troubleshoot-nsg.md) artiklar.
>

## <a name="aadds502-secure-ldap-certificate-expiring"></a>AADDS502: Skydda LDAP-certifikatet upphör att gälla

**Varningsmeddelande:**

*Säkert LDAP-certifikatet för den hanterade domänen upphör XX.*

**Lösning:**

Skapa ett nytt certifikat för säker LDAP genom att följa anvisningarna i den [konfigurera säker LDAP](active-directory-ds-admin-guide-configure-secure-ldap.md) artikel.

## <a name="contact-us"></a>Kontakta oss
Kontakta produktteamet Azure Active Directory Domain Services för att [dela feedback eller support](active-directory-ds-contact-us.md).
