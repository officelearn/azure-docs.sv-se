---
title: Problem med att installera agenten Application Proxy Connector | Microsoft Docs
description: "Felsökning av problem som kan uppstår när du installerar agenten Application Proxy Connector"
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: a02f6cf0923bbe7083ba29fac5f59bcce9f5ab44
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="problem-installing-the-application-proxy-agent-connector"></a>Problem med att installera agenten Application Proxy Connector

Microsoft AAD Application Proxy Connector är en intern domän-komponent som använder utgående anslutningar för att upprätta en anslutning från molnet tillgängliga slutpunkten till den interna domänen.

## <a name="general-problem-areas-with-connector-installation"></a>Allmänna problemområden med kopplingsinstallationen

När installationen av en anslutning misslyckas är den grundläggande orsaken vanligtvis en av följande områden:

1.  **Anslutningen** – för att slutföra installationen har lyckats ny koppling behov att registrera och upprätta förtroende för framtida egenskaper. Detta görs genom att ansluta till Molntjänsten AAD Application Proxy.

2.  **Upprätta förtroende** – den nya kopplingen skapar ett självsignerat certifikat och registrerar till Molntjänsten.

3.  **Autentisering av administratören** – under installationen, måste användaren ange administratörsautentiseringsuppgifter för att slutföra installationen av koppling.

## <a name="verify-connectivity-to-the-cloud-application-proxy-service-and-microsoft-login-page"></a>Kontrollera anslutningen till tjänsten för Webbprogramproxy för molnet och Microsoft Login-sida

**Mål:** Kontrollera att connector-datorn kan ansluta till slutpunkt för registrering av AAD Application Proxy samt Microsoft inloggningssidan.

1.  Öppna en webbläsare och gå till följande webbsida: <https://aadap-portcheck.connectorporttest.msappproxy.net> , och kontrollera att anslutningen till centrala USA och östra USA datacenter med portarna 9090 och 9091 fungerar.

2.  Om någon av dessa portar inte lyckas (om du inte har en grön bock), kontrollera att brandväggen eller backend-proxy har \*. msappproxy.net med portarna 9090 och 9091 definierats korrekt.

3.  Öppna en webbläsare (separat flik) och gå till följande webbsida: <https://login.microsoftonline.com>, se till att du kan logga in på sidan.

## <a name="verify-machine-and-backend-components-support-for-application-proxy-trust-cert"></a>Kontrollera att dator-och backend-stöd för Application Proxy betrodda certifikat

**Mål:** Kontrollera att connector-datorn, backend-proxy och brandväggen har stöd för det certifikat som skapas av anslutningen för framtida förtroende.

>[!NOTE]
>Kopplingen försöker skapa ett SHA512 certifikat som stöds av TLS1.2. Installationen misslyckas om datorn eller backend brandväggar och proxyservrar inte stöder TLS1.2.
>
>

**Så här löser du problemet:**

1.  Kontrollera att datorn stöder TLS1.2 – TLS 1.2 ska ha stöd för alla Windows-versioner när 2012 R2. Om datorn connector är från en version av 2012 R2 eller tidigare, kontrollerar du att följande KBs är installerade på datorn: <https://support.microsoft.com/help/2973337/sha512-is-disabled-in-windows-when-you-use-tls-1.2>

2.  Kontakta administratören för nätverket och be att kontrollera att backend proxy- och brandväggsinställningarna inte blockerar SHA512 för utgående trafik.

## <a name="verify-admin-is-used-to-install-the-connector"></a>Kontrollera att admin används för att installera connector

**Mål:** Kontrollera att den användare som försöker installera connector är en administratör med rätt autentiseringsuppgifter. För närvarande måste användaren vara en global administratör för att installationen ska lyckas.

**Kontrollera att autentiseringsuppgifterna är korrekta:**

Ansluta till <https://login.microsoftonline.com> och använda samma autentiseringsuppgifter. Kontrollera att inloggningen är klar. Du kan kontrollera rollen genom att gå till **Azure Active Directory**  - &gt; **användare och grupper**  - &gt; **alla användare**. 

Välj ditt användarkonto, sedan ”Directory roll” på menyn. Kontrollera att den valda rollen är ”Global administratör”. Om du inte går att komma åt någon av sidorna på de här stegen kan är du inte en global administratör.

## <a name="next-steps"></a>Nästa steg
[Förstå Azure AD Application Proxy-kopplingar](application-proxy-understand-connectors.md)
