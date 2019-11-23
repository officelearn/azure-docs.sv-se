---
title: Problem med att installera agent anslutnings programmet för programproxy | Microsoft Docs
description: Så här felsöker du problem som du kan stöta på när du installerar Application Proxy agent-anslutningen
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: a35558b81d064680981bcf403a3584e3a3d00e4f
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/14/2019
ms.locfileid: "72311753"
---
# <a name="problem-installing-the-application-proxy-agent-connector"></a>Problem med att installera anslutningsappen för programproxyagenten

Microsoft AAD Application Proxy Connector är en intern domän komponent som använder utgående anslutningar för att upprätta anslutningen från moln tillgänglig slut punkt till den interna domänen.

## <a name="general-problem-areas-with-connector-installation"></a>Allmänna problemområden med anslutnings installation

När installationen av en anslutning Miss lyckas är rotor saken vanligt vis något av följande områden:

1.  **Anslutning** – för att slutföra installationen måste den nya anslutningen registrera och upprätta framtida förtroende egenskaper. Detta görs genom att ansluta till moln tjänsten AAD Application Proxy.

2.  **Förtroende etablering** – den nya anslutningen skapar ett självsignerat certifikat och registrerar sig för moln tjänsten.

3.  **Autentisering av administratören** – under installationen måste användaren ange administratörsautentiseringsuppgifter för att slutföra anslutnings installationen.

> [!NOTE]
> Installations loggarna för Connector finns i mappen% TEMP% och kan hjälpa dig att ge ytterligare information om vad som orsakar ett installations fel.

## <a name="verify-connectivity-to-the-cloud-application-proxy-service-and-microsoft-login-page"></a>Kontrol lera anslutningen till tjänsten Cloud Application Proxy och sidan Microsoft-inloggning

**Mål:** Kontrol lera att anslutnings datorn kan ansluta till registrerings slut punkten för AAD-programproxyn och sidan Microsoft-inloggning.

1.  På kopplings servern kör du ett port test genom att använda [telnet](https://docs.microsoft.com/windows-server/administration/windows-commands/telnet) eller något annat port test verktyg för att kontrol lera att portarna 443 och 80 är öppna.

2.  Om någon av dessa portar inte lyckas kontrollerar du att brand väggen eller backend-proxyn har åtkomst till de nödvändiga domänerna och portarna i, [förbereder din lokala miljö](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment).

3.  Öppna en webbläsare (separat flik) och gå till följande webb sida: <https://login.microsoftonline.com>kontrol lera att du kan logga in på sidan.

## <a name="verify-machine-and-backend-components-support-for-application-proxy-trust-cert"></a>Verifiera stöd för maskin-och Server dels komponenter för certifikat för programproxy-förtroende

**Mål:** Kontrol lera att anslutnings datorn, backend-proxyn och brand väggen kan stödja certifikatet som skapats av anslutnings tjänsten för framtida förtroende.

>[!NOTE]
>Anslutningen försöker skapa ett SHA512-certifikat som stöds av TLS 1.2. Om datorn eller Server dels brand väggen och proxyn inte stöder TLS 1.2, Miss lyckas installationen.
>
>

**Så här löser du problemet:**

1.  Kontrol lera att datorn har stöd för TLS 1.2 – alla Windows-versioner efter 2012 R2 ska ha stöd för TLS 1,2. Om din kopplings dator är från en version av 2012 R2 eller tidigare kontrollerar du att följande KB är installerade på datorn: <https://support.microsoft.com/help/2973337/sha512-is-disabled-in-windows-when-you-use-tls-1.2>

2.  Kontakta nätverks administratören och be om att kontrol lera att backend-proxyn och brand väggen inte blockerar SHA512 för utgående trafik.

## <a name="verify-admin-is-used-to-install-the-connector"></a>Verifiera att administratören används för att installera anslutningen

**Mål:** Kontrol lera att den användare som försöker installera anslutningen är administratör med rätt autentiseringsuppgifter. För närvarande måste användaren minst ha en program administratör för att installationen ska lyckas.

**Så här kontrollerar du att autentiseringsuppgifterna är korrekta:**

Anslut till <https://login.microsoftonline.com> och Använd samma autentiseringsuppgifter. Kontrol lera att inloggningen har slutförts. Du kan kontrol lera användar rollen genom att gå till **Azure Active Directory** -&gt; **användare och grupper** -&gt; **alla användare**. 

Välj ditt användar konto och sedan "katalog roll" på den resulterande menyn. Kontrol lera att den valda rollen är "program administratör". Om du inte kan komma åt någon av sidorna tillsammans med de här stegen har du inte den roll som krävs.

## <a name="next-steps"></a>Nästa steg
[Förstå Azure AD Application Proxy-anslutningar](application-proxy-connectors.md)
