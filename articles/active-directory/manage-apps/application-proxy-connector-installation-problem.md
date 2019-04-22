---
title: Problem med att installera den anslutningsprogrammet för Programproxyagenten | Microsoft Docs
description: Felsökning av problem kan du står inför när du installerar den anslutningsprogrammet för Programproxyagenten
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: celested
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87d828e596c42b786d1c54a7158e1151f374c560
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58793047"
---
# <a name="problem-installing-the-application-proxy-agent-connector"></a>Problem med att installera anslutningsappen för programproxyagenten

Microsoft AAD Application Proxy Connector är en intern domän-komponent som använder utgående anslutningar för att upprätta en anslutning från tillgängliga molnslutpunkt till den interna domänen.

## <a name="general-problem-areas-with-connector-installation"></a>Allmänna problemområden med kopplingsinstallationen

När en anslutning misslyckas, är den grundläggande orsaken vanligtvis en av följande områden:

1.  **Anslutningen** – för att slutföra en lyckad installation, de nya connector måste registrera och upprätta förtroende för framtida egenskaper. Detta görs genom att ansluta till Molntjänsten AAD Application Proxy.

2.  **Upprätta förtroende** – den nya kopplingen skapar ett självsignerat certifikat och registrerar till Molntjänsten.

3.  **Autentisering av administratören** – under installationen måste användaren ange administratörsautentiseringsuppgifter för att slutföra installationen av anslutningen.

## <a name="verify-connectivity-to-the-cloud-application-proxy-service-and-microsoft-login-page"></a>Kontrollera anslutningen till sidan Cloud Application Proxy-tjänsten och Microsoft Login

**Mål:** Kontrollera att connector-datorn kan ansluta till AAD Application Proxy profilslutpunkt samt Microsoft-inloggningssidan.

1.  Öppna en webbläsare och gå till följande webbsida: <https://aadap-portcheck.connectorporttest.msappproxy.net> , och kontrollera att anslutningen till centrala USA och östra USA datacenter med portarna 80 och 443 fungerar.

2.  Om någon av de portarna som inte lyckas (om du inte har en grön bock), kontrollera att brandväggen eller backend-proxy har \*. msappproxy.net med portarna 80 och 443 som definierats korrekt.

3.  Öppna en webbläsare (separat flik) och gå till följande webbsida: <https://login.microsoftonline.com>, se till att du kan logga in till den sidan.

## <a name="verify-machine-and-backend-components-support-for-application-proxy-trust-cert"></a>Kontrollera datorn och backend-komponenter som har stöd för programproxy betrodda certifikat

**Mål:** Kontrollera att connector-datorn, backend-proxy och brandvägg har stöd för det certifikat som skapats av anslutningsappen för framtida förtroende.

>[!NOTE]
>Anslutningen försöker skapa ett SHA512 certifikat som stöds av TLS1.2. Om den virtuella datorn eller backend-brandvägggen och proxy inte stöder TLS1.2, misslyckas installationen.
>
>

**Att lösa problemet:**

1.  Kontrollera datorn stöder TLS1.2 – alla Windows-versioner efter 2012 R2 ska ha stöd för TLS 1.2. Om din connector-dator från en version av 2012 R2 eller tidigare kan du kontrollera att följande KB-artiklar är installerade på datorn: <https://support.microsoft.com/help/2973337/sha512-is-disabled-in-windows-when-you-use-tls-1.2>

2.  Kontakta din administratör att nätverket och be att kontrollera att backend-proxy och brandvägg inte blockerar SHA512 för utgående trafik.

## <a name="verify-admin-is-used-to-install-the-connector"></a>Kontrollera att administratören används för att installera connector

**Mål:** Kontrollera att den användare som försöker installera connector är en administratör med rätt autentiseringsuppgifter. För närvarande kan måste användaren vara minst en programadministratör för att installationen ska lyckas.

**För att verifiera autentiseringsuppgifterna är korrekta:**

Ansluta till <https://login.microsoftonline.com> och använda samma autentiseringsuppgifter. Kontrollera att inloggningen är klar. Du kan kontrollera rollen genom att gå till **Azure Active Directory**  - &gt; **användare och grupper**  - &gt; **alla användare**. 

Välj ditt användarkonto, sedan ”Katalogroll” på menyn. Kontrollera att den valda rollen är ”programadministratör”. Om det inte går att få åtkomst till någon av sidorna med de här stegen kan har du inte de nödvändiga rolltjänsterna.

## <a name="next-steps"></a>Nästa steg
[Förstå Azure AD Application Proxy-anslutningar](application-proxy-connectors.md)
