---
title: 'Azure Active Directory Domain Services: Felsöka säker LDAP | Microsoft Docs'
description: Felsöka säkert LDAP för Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: ''
editor: ''
ms.assetid: 81208c0b-8d41-4f65-be15-42119b1b5957
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: iainfou
ms.openlocfilehash: 8a542f7927ddd834c7273f6ef8b251ddc35e8436
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234195"
---
# <a name="azure-ad-domain-services---troubleshooting-secure-ldap-configuration"></a>Azure AD Domain Services – fel sökning av säkert LDAP konfiguration

Den här artikeln innehåller lösningar på vanliga problem när du [konfigurerar säker LDAP](configure-ldaps.md) för Azure AD Domain Services.

## <a name="aadds101-secure-ldap-network-security-group-configuration"></a>AADDS101: säkert LDAP konfiguration av nätverks säkerhets grupp

**Aviserings meddelande:**

*Säkert LDAP över Internet är aktiverat för den hanterade domänen. Åtkomst till port 636 är dock inte låst med en nätverks säkerhets grupp. Detta kan innebära att användar konton på den hanterade domänen används för lösen ords brutet angrepp.*

### <a name="secure-ldap-port"></a>säkert LDAP port

När säker LDAP har Aktiver ATS rekommenderar vi att du skapar ytterligare regler för att tillåta inkommande LDAP-åtkomst enbart från vissa IP-adresser. Reglerna skyddar din domän från brute force-attacker som kan utgöra ett säkerhetshot. Port 636 ger åtkomst till din hanterade domän. Så här uppdaterar du NSG för att tillåta åtkomst för säkert LDAP:

1. Gå till [fliken nätverks säkerhets grupper](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) i Azure Portal
2. Välj den NSG som är kopplad till din domän från tabellen.
3. Klicka på **inkommande säkerhets regler**
4. Skapa port 636-regeln
   1. Klicka på **Lägg till** i det övre navigerings fältet.
   2. Välj **IP-adresser** för källan.
   3. Ange käll portens intervall för den här regeln.
   4. Inmatar "636" för mål ports intervall.
   5. Protokollet är **TCP**.
   6. Ge regeln ett lämpligt namn, en beskrivning och prioritet. Regelns prioritet ska vara högre än din regels prioritet för "Neka alla" om du har en.
   7. Klicka på **OK**.
5. Kontrol lera att regeln har skapats.
6. Kontrol lera din domän hälsa på två timmar för att se till att du har slutfört stegen korrekt.

> [!TIP]
> Port 636 är inte den enda regeln som krävs för att Azure AD Domain Services ska kunna köras smidigt. Mer information finns i [rikt linjerna för nätverk](network-considerations.md) eller [fel sökning av NSG](alert-nsg.md) -konfigurations artiklar.
>

## <a name="aadds502-secure-ldap-certificate-expiring"></a>AADDS502: säkert LDAP certifikat upphör att gälla

**Aviserings meddelande:**

*Det säkra LDAP-certifikatet för den hanterade domänen upphör att gälla [datum]].*

**Lösning**

Skapa ett nytt säkert LDAP-certifikat genom att följa stegen som beskrivs i artikeln [Konfigurera säker LDAP](configure-ldaps.md) .

## <a name="contact-us"></a>Kontakta oss
Kontakta Azure Active Directory Domain Services produkt teamet för att [dela feedback eller för support](contact-us.md).
