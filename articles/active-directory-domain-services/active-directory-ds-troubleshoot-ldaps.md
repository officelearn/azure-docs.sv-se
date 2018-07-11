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
ms.openlocfilehash: 8304ffa7c0cd225f258064d3c1a36a754c684241
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37950724"
---
# <a name="azure-ad-domain-services---troubleshooting-secure-ldap-configuration"></a>Azure AD Domain Services - felsökning säker LDAP-konfiguration

Den här artikeln innehåller lösningar för vanliga problem när [konfigurera säkert LDAP](active-directory-ds-admin-guide-configure-secure-ldap.md) för Azure AD Domain Services.

## <a name="aadds101-secure-ldap-network-security-group-configuration"></a>AADDS101: Nätverkssäkerhetsgruppen för säker LDAP-konfiguration

**Varningsmeddelande:**

*Säkert LDAP över internet har aktiverats för den hanterade domänen. Åtkomst till port 636 är dock inte låst nedåt med hjälp av en nätverkssäkerhetsgrupp. Detta kan medföra att användarkonton på den hanterade domänen för lösenord brute force-attacker.*

### <a name="secure-ldap-port"></a>Säker LDAP-port

När säker LDAP är aktiverad, rekommenderar vi skapar ytterligare regler som tillåter inkommande LDAPS åtkomst endast från vissa IP-adresser. De här reglerna kan du skydda din domän från brute force-attacker som kan utgöra en säkerhetsrisk. Port 636 ger åtkomst till din hanterade domän. Här är hur du uppdaterar din NSG för att tillåta åtkomst för säkert LDAP:

1. Navigera till den [Nätverkssäkerhetsgrupper fliken](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) i Azure portal
2. Välj NSG: N som är associerade med din domän från tabellen.
3. Klicka på **ingående säkerhetsregler**
4. Skapa regel för port 636
   1. Klicka på **Lägg till** i det övre navigeringsfältet.
   2. Välj **IP-adresser** för källan.
   3. Ange källportsintervall för den här regeln.
   4. Indata ”636” för målportsintervall.
   5. Protokollet är **TCP**.
   6. Ge regeln ett namn, beskrivning och en prioritet. Den här regelns prioritet bör vara högre än ”neka alla”-regelns prioritet, om du har en.
   7. Klicka på **OK**.
5. Kontrollera att regeln har skapats.
6. Kontrollera hälsan för domänen i två timmar att kontrollera att du har slutfört stegen korrekt.

> [!TIP]
> Port 636 är den enda regeln som behövs för Azure AD Domain Services att köras. Mer information finns i [riktlinjer för nätverk](active-directory-ds-networking.md) eller [felsöka NSG-konfiguration](active-directory-ds-troubleshoot-nsg.md) artiklar.
>

## <a name="aadds502-secure-ldap-certificate-expiring"></a>AADDS502: Säker LDAP-certifikatet upphör att gälla

**Varningsmeddelande:**

*Certifikatet för säkert LDAP för den hanterade domänen upphör att gälla [date]].*

**Lösning:**

Skapa ett nytt certifikat för säkert LDAP genom att följa anvisningarna i den [konfigurera säker LDAP](active-directory-ds-admin-guide-configure-secure-ldap.md) artikeln.

## <a name="contact-us"></a>Kontakta oss
Kontakta Azure Active Directory Domain Services-produktteamet att [dela feedback eller support](active-directory-ds-contact-us.md).
