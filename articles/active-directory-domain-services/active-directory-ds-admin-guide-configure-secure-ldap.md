---
title: Konfigurera säkert LDAP (LDAPS) i Azure AD Domain Services | Microsoft Docs
description: Konfigurera säkert LDAP (LDAPS) för en Azure AD Domain Services-hanterad domän
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: ergreenl
ms.openlocfilehash: 66c50d7ec9948b1f2447aadcef4a4a8f321204ef
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55177398"
---
# <a name="configure-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Konfigurera säker LDAP (LDAPS) för en Azure AD Domain Services-hanterad domän
Den här artikeln visar hur du kan aktivera säker Lightweight Directory Access Protocol (LDAPS) för din hanterade domän i Azure AD Domain Services. Säkert LDAP kallas även ”Lightweight Directory Access Protocol (LDAP) över Secure Sockets Layer (SSL) / Transport Layer Security (TLS)”.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Innan du börjar
Om du vill utföra åtgärderna i den här artikeln behöver du:

1. En giltig **Azure-prenumeration**.
2. En **Azure AD-katalog** -antingen synkroniseras med en lokal katalog eller en molnbaserad katalog.
3. **Azure AD Domain Services** måste aktiveras för Azure AD-katalog. Om du inte gjort det, följer du alla uppgifter som beskrivs i den [komma igång-guiden](active-directory-ds-getting-started.md).
4. En **certifikat som ska användas för att aktivera säkert LDAP**.

   * **Rekommenderade** – skaffa ett certifikat från en betrodd offentlig certifikatutfärdare. Det här konfigurationsalternativet är säkrare.
   * Alternativt kan du kan också välja att [skapa ett självsignerat certifikat](#task-1---obtain-a-certificate-for-secure-ldap) enligt nedan.

<br>

### <a name="requirements-for-the-secure-ldap-certificate"></a>Krav för certifikatet för säkert LDAP
Skaffa ett giltigt certifikat enligt följande riktlinjer innan du aktiverar säkert LDAP. Fel uppstår om du försöker aktivera säkert LDAP för den hanterade domänen med ett ogiltiga/felaktiga certifikat.

1. **Betrodda utfärdare** -certifikatet måste utfärdas av en utfärdare som är betrodd av datorer som ansluter till den hanterade domänen med säkert LDAP. Denna behörighet kan vara en offentlig certifikatutfärdare (CA) eller en Företagscertifikatutfärdare som är betrodd av dessa datorer.
2. **Livslängd** -certifikatet måste vara giltig för minst de kommande 3 – 6 månaderna. Åtkomst med säkert LDAP till din hanterade domän avbryts när certifikatet upphör att gälla.
3. **Ämnesnamn** -Subjektnamnet på certifikatet måste vara din hanterade domän. Om din domän har namnet ”contoso100.com”, till exempel vara certifikatets ämnesnamn ”contoso100.com”. Ange DNS-namn (Alternativt ämnesnamn) till ett jokertecken namn för din hanterade domän.
4. **Nyckelanvändning** -certifikatet måste konfigureras för följande använder - digitala signaturer och nyckelchiffrering.
5. **Användning av certifikat** -certifikatet måste vara giltig för serverautentisering för SSL.

<br>

## <a name="task-1---obtain-a-certificate-for-secure-ldap"></a>Uppgift 1 – skaffa ett certifikat för säkert LDAP
Den första aktiviteten innebär att erhålla ett certifikat som används för åtkomst med säkert LDAP till den hanterade domänen. Du kan välja mellan två alternativ:

* Skaffa ett certifikat från en offentlig Certifikatutfärdare eller en företagscertifikatutfärdare.
* Skapa ett självsignerat certifikat.

> [!NOTE]
> Klientdatorer som behöver ansluta till den hanterade domänen med säkert LDAP måste lita på utfärdaren av certifikatet för säkert LDAP.
>

### <a name="option-a-recommended---obtain-a-secure-ldap-certificate-from-a-certification-authority"></a>Alternativ (rekommenderas) – skaffa en säker LDAP-certifikat från en certifikatutfärdare
Om din organisation hämtar certifikat från en offentlig Certifikatutfärdare, kan du hämta certifikatet för säkert LDAP från den offentliga Certifikatutfärdaren. Om du distribuerar en företagscertifikatutfärdare, kan du hämta certifikatet för säkert LDAP från företagets Certifikatutfärdare.

> [!TIP]
> **Använda självsignerade certifikat för hanterade domäner med ”. onmicrosoft.com' domänsuffix.**
> Om DNS-domännamnet för den hanterade domänen slutar med ”. onmicrosoft.com”, du kan inte hämta certifikatet för säkert LDAP från en offentlig certifikatutfärdare. Eftersom Microsoft äger ”onmicrosoft.com”-domän kan neka offentlig certifikatutfärdare att utfärda certifikatet för säkert LDAP till dig för en domän med suffixet. Skapa ett självsignerat certifikat i det här scenariot och använda den för att konfigurera säkert LDAP.
>

Se till att det certifikat som du får från offentlig certifikatutfärdare uppfyller alla krav som beskrivs i [krav för certifikatet för säkert LDAP](#requirements-for-the-secure-ldap-certificate).


### <a name="option-b---create-a-self-signed-certificate-for-secure-ldap"></a>Alternativ B – skapa ett självsignerat certifikat för säkert LDAP
Om du inte räknar med att använda ett certifikat från en offentlig certifikatutfärdare, kan du välja att skapa ett självsignerat certifikat för säkert LDAP. Välj detta alternativ om DNS-domännamnet för den hanterade domänen som slutar med ”. onmicrosoft.com”.

**Skapa ett självsignerat certifikat med hjälp av PowerShell**

På din Windows-dator, öppna ett nytt PowerShell-fönster som **administratör** och Skriv följande kommandon, för att skapa ett nytt självsignerat certifikat.

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.com, contoso100.com
```

Ersätt ”contoso100.com” i det föregående exemplet med DNS-domännamnet för den hanterade domänen. Ersätt till exempel om du har skapat en hanterad domän som kallas ”contoso100.onmicrosoft.com”, ”contoso100.com” i attributet ämne med 'contoso100.onmicrosoft.com' och '*. contoso100.com ”i attributet DNS-namn med”*. contoso100.onmicrosoft.com ”).

![Välja Azure AD-katalog](./media/active-directory-domain-services-admin-guide/secure-ldap-powershell-create-self-signed-cert.png)

Det nyskapade självsignerade certifikatet placeras i den lokala datorns certifikatarkiv.


## <a name="next-step"></a>Nästa steg
[Uppgift 2 – exportera certifikatet för säkert LDAP till en. PFX-fil](active-directory-ds-admin-guide-configure-secure-ldap-export-pfx.md)
