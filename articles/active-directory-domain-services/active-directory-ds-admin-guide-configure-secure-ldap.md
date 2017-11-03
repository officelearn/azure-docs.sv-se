---
title: "Konfigurera säker LDAP (LDAPS) i Azure AD Domain Services | Microsoft Docs"
description: "Konfigurera säker LDAP (LDAPS) för en Azure AD Domain Services-hanterad domän"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: maheshu
ms.openlocfilehash: 93afa49166c5b31d23237c308b9d34f6d6f3507d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="configure-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Konfigurera säker LDAP (LDAPS) för en Azure AD Domain Services-hanterad domän
Den här artikeln visar hur du kan aktivera säker Lightweight Directory Access Protocol (LDAPS) för din Azure AD Domain Services-hanterad domän. Säker LDAP kallas även ”Lightweight Directory Access Protocol (LDAP) över Secure Sockets Layer (SSL) / Transport Layer Security (TLS)'.

## <a name="before-you-begin"></a>Innan du börjar
Om du vill utföra åtgärderna i den här artikeln behöver du:

1. En giltig **Azure-prenumeration**.
2. En **Azure AD-katalog** -antingen synkroniseras med en lokal katalog eller en molnbaserad katalog.
3. **Azure AD Domain Services** måste vara aktiverat för Azure AD-katalog. Om du inte gjort det, följer du de uppgifter som beskrivs i den [Kom igång-guiden](active-directory-ds-getting-started.md).
4. En **certifikat som används för att aktivera säker LDAP**.

   * **Rekommenderade** -erhåller ett certifikat från en betrodd offentlig certifikatutfärdare. Det här konfigurationsalternativet är säkrare.
   * Alternativt kan du kan också välja att [skapa ett självsignerat certifikat](#task-1---obtain-a-certificate-for-secure-ldap) enligt nedan.

<br>

### <a name="requirements-for-the-secure-ldap-certificate"></a>Krav för säker LDAP-certifikat
Hämta ett giltigt certifikat enligt följande riktlinjer innan du aktiverar säker LDAP. Det uppstår fel vid försök att aktivera säker LDAP för din hanterade domän med ett ogiltigt felaktigt certifikat.

1. **Betrodda utfärdare** -certifikatet måste utfärdas av en certifikatutfärdare som betrodd av datorer som ansluter till den hanterade domänen med säker LDAP. Den här utfärdare kan vara en offentlig certifikatutfärdare som betrodd av dessa datorer.
2. **Livslängd** -certifikatet måste vara giltigt för minst de kommande 3-6 månaderna. Säker LDAP-åtkomst till din hanterade domän avbryts när certifikatet upphör att gälla.
3. **Ämnesnamn** -Subjektnamnet på certifikatet måste vara ett jokertecken för din hanterade domän. Till exempel om din domän har namnet ”contoso100.com”, certifikatets ämnesnamn måste vara ' *. contoso100.com ”. Ange DNS-namn (Alternativt ämnesnamn) till den här jokertecken-namn.
4. **Nyckelanvändning** -certifikatet måste vara konfigurerad för följande använder - digitala signaturer och nyckelchiffrering.
5. **Certifikat syfte** -certifikatet måste vara giltigt för serverautentisering för SSL.

> [!NOTE]
> **Företagscertifikatutfärdare:** Azure AD Domain Services stöder inte användning av säker LDAP-certifikat som utfärdats av din organisations utfärdare av företagscertifikat. Den här begränsningen beror på att tjänsten inte har förtroende för ditt företags-CA som en rotcertifikatutfärdare. 
>
>

<br>

## <a name="task-1---obtain-a-certificate-for-secure-ldap"></a>Uppgift 1 – skaffa ett certifikat för säker LDAP
Den första aktiviteten innebär att erhålla ett certifikat som används för säker LDAP-åtkomst till den hanterade domänen. Du kan välja mellan två alternativ:

* Skaffa ett certifikat från en certifikatutfärdare. Behörighet kan vara en offentlig certifikatutfärdare.
* Skapa ett självsignerat certifikat.

### <a name="option-a-recommended---obtain-a-secure-ldap-certificate-from-a-certification-authority"></a>Alternativet (rekommenderas) – skaffa ett säkert LDAP-certifikat från en certifikatutfärdare
Om din organisation hämtar certifikat från en offentlig certifikatutfärdare, måste du hämta säker LDAP-certifikatet från den offentlig certifikatutfärdaren.

När du begär ett certifikat, se till att du uppfyller alla krav som beskrivs i [krav på säker LDAP-certifikatets](#requirements-for-the-secure-ldap-certificate).

> [!NOTE]
> Klientdatorer som behöver ansluta till den hanterade domänen med säker LDAP måste ha förtroende för säker LDAP-certifikatets utfärdare.
>
>

### <a name="option-b---create-a-self-signed-certificate-for-secure-ldap"></a>Alternativ B – skapa ett självsignerat certifikat för säker LDAP
Om du inte räknar med att använda ett certifikat från en offentlig certifikatutfärdare, kan du välja att skapa ett självsignerat certifikat för säker LDAP.

**Skapa ett självsignerat certifikat med hjälp av PowerShell**

På Windows-dator öppnar du ett nytt PowerShell-fönster som **administratör** och Skriv följande kommandon, för att skapa ett nytt självsignerat certifikat.

    $lifetime=Get-Date

    New-SelfSignedCertificate -Subject *.contoso100.com -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment -Type SSLServerAuthentication -DnsName *.contoso100.com

I föregående exempel, ersätter '*. contoso100.com ”med DNS-domännamnet för din hanterade domän. Till exempel om du har skapat en hanterad domän som kallas ”contoso100.onmicrosoft.com” kan ersätta '*. contoso100.com ”i skriptet ovan med ' *. contoso100.onmicrosoft.com').

![Välja Azure AD-katalog](./media/active-directory-domain-services-admin-guide/secure-ldap-powershell-create-self-signed-cert.png)

Det nyskapade självsignerade certifikatet placeras i den lokala datorns certifikatarkiv.


## <a name="next-step"></a>Nästa steg
[Uppgift 2 – exportera säker LDAP-certifikatet till en. PFX-fil](active-directory-ds-admin-guide-configure-secure-ldap-export-pfx.md)
