---
title: Problem med att installera Application Proxy Agent Connector | Microsoft-dokument
description: Felsöka problem som du kan ställas inför när du installerar Application Proxy Agent Connector
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
ms.openlocfilehash: 466e1ce0efbdec3f5475634f3857d02554d93d98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049137"
---
# <a name="problem-installing-the-application-proxy-agent-connector"></a>Problem med att installera anslutningsappen för programproxyagenten

Microsoft AAD Application Proxy Connector är en intern domänkomponent som använder utgående anslutningar för att upprätta anslutningen från den molnbaserade slutpunkten till den interna domänen.

## <a name="general-problem-areas-with-connector-installation"></a>Allmänna problemområden med anslutningsinstallation

När installationen av en koppling misslyckas är grundorsaken vanligtvis ett av följande områden:

1.  **Anslutning** – för att slutföra en lyckad installation måste den nya anslutningen registrera och upprätta framtida förtroendeegenskaper. Detta görs genom att ansluta till molntjänsten AAD Application Proxy.

2.  **Förtroendeetablering** – den nya kopplingen skapar ett självsignerat certifikat och registrerar sig till molntjänsten.

3.  **Autentisering av administratören** – under installationen måste användaren ange administratörsautentiseringsuppgifter för att slutföra anslutningsinstallationen.

> [!NOTE]
> Anslutningsinstallationsloggarna finns i mappen %TEMP% och kan ge ytterligare information om vad som orsakar ett installationsfel.

## <a name="verify-connectivity-to-the-cloud-application-proxy-service-and-microsoft-login-page"></a>Verifiera anslutning till cloud application proxy-tjänsten och Microsoft-inloggningssidan

**Syfte:** Kontrollera att anslutningsdatorn kan ansluta till slutpunkten för AAD Application Proxy-registrering samt Microsofts inloggningssida.

1.  På anslutningsservern kör du ett porttest med [telnet](https://docs.microsoft.com/windows-server/administration/windows-commands/telnet) eller annat porttestverktyg för att kontrollera att portarna 443 och 80 är öppna.

2.  Om någon av dessa portar inte lyckas kontrollerar du att brandväggen eller serverdelsproxyn har åtkomst till de domäner och portar som krävs, [Förbered din lokala miljö](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment).

3.  Öppna en webbläsare (separat flik) och `https://login.microsoftonline.com`gå till följande webbsida: , se till att du kan logga in på den sidan.

## <a name="verify-machine-and-backend-components-support-for-application-proxy-trust-cert"></a>Verifiera stöd för dator- och backendkomponenter för programproxy-förtroendecert

**Syfte:** Kontrollera att anslutningsmaskinen, serverdproxyn och brandväggen kan stödja certifikatet som skapats av kopplingen för framtida förtroende.

>[!NOTE]
>Kopplingen försöker skapa ett SHA512-certifikat som stöds av TLS1.2. Om datorn eller servergruppsbrandväggen och proxyn inte stöder TLS1.2 misslyckas installationen.
>
>

**Så här löser du problemet:**

1.  Kontrollera att datorn stöder TLS1.2 – Alla Windows-versioner efter 2012 R2 bör ha stöd för TLS 1.2. Om anslutningsdatorn kommer från en version av 2012 R2 eller tidigare kontrollerar du att följande KBs är installerade på datorn:<https://support.microsoft.com/help/2973337/sha512-is-disabled-in-windows-when-you-use-tls-1.2>

2.  Kontakta nätverksadministratören och be att du kontrollerar att serverdproxyn och brandväggen inte blockerar SHA512 för utgående trafik.

## <a name="verify-admin-is-used-to-install-the-connector"></a>Verifiera admin används för att installera anslutningen

**Syfte:** Kontrollera att användaren som försöker installera anslutningen är administratör med korrekta autentiseringsuppgifter. För närvarande måste användaren vara minst en programadministratör för att installationen ska lyckas.

**Så här kontrollerar du att autentiseringsuppgifterna är korrekta:**

Anslut `https://login.microsoftonline.com` till och använd samma autentiseringsuppgifter. Kontrollera att inloggningen lyckas. Du kan kontrollera användarrollen genom att gå till **Azure Active Directory**  - &gt; **- användare och grupperar**  - &gt; **alla användare**. 

Välj ditt användarkonto och sedan "Katalogroll" på den resulterande menyn. Kontrollera att den valda rollen är "Programadministratör". Om du inte kan komma åt någon av sidorna i de här stegen har du inte den roll som krävs.

## <a name="next-steps"></a>Nästa steg
[Förstå Azure AD-programproxy-kopplingar](application-proxy-connectors.md)
