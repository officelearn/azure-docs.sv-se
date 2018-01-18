---
title: "Azure Active Directory Domain Services: Felsöka säker LDAP-konfiguration | Microsoft Docs"
description: "Felsöka säker LDAP för Azure AD Domain Services"
services: active-directory-ds
documentationcenter: 
author: eringreenlee
manager: 
editor: 
ms.assetid: 81208c0b-8d41-4f65-be15-42119b1b5957
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2018
ms.author: ergreenl
ms.openlocfilehash: ad98f3fb1ddb753976be627764d34864e5bf3d50
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2018
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


## <a name="contact-us"></a>Kontakta oss
Kontakta produktteamet Azure Active Directory Domain Services för att [dela feedback eller support](active-directory-ds-contact-us.md).
