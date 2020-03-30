---
title: Felsöka utgående SMTP-anslutning i Azure | Microsoft-dokument
description: Lär dig hur du felsöker problem med utgående SMTP-anslutning i Azure.
services: virtual-network
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2018
ms.author: genli
ms.openlocfilehash: b8acb50978c5932fe6e6838be86b65c12a0984ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71058942"
---
# <a name="troubleshoot-outbound-smtp-connectivity-issues-in-azure"></a>Felsöka problem med utgående SMTP-anslutning i Azure

Från och med den 15 november 2017 görs utgående e-postmeddelanden som skickas direkt till externa domäner (till exempel outlook.com och gmail.com) från en virtuell dator (VM) endast tillgängliga för vissa prenumerationstyper i Microsoft Azure. Utgående SMTP-anslutningar som använder TCP-port 25 blockerades. (Port 25 används främst för oautentiserade e-postleverans.)

Den här ändringen av beteendet gäller endast för nya prenumerationer och nya distributioner sedan den 15 november 2017.

## <a name="recommended-method-of-sending-email"></a>Rekommenderad metod för att skicka e-post
Vi rekommenderar att du använder autentiserade SMTP-relätjänster (som vanligtvis ansluter via TCP-port 587 eller 443 men stöder även andra portar) för att skicka e-post från Virtuella Azure-datorer eller från Azure App Services. Dessa tjänster används för att upprätthålla IP- eller domänens rykte för att minimera risken för att e-postleverantörer från tredje part kommer att avvisa meddelandet. Sådana SMTP-relätjänster inkluderar men är inte begränsade till [SendGrid](https://sendgrid.com/partners/azure/). Det är också möjligt att du har en säker SMTP-relätjänst som körs lokalt som du kan använda.

Att använda dessa e-postleveranstjänster är inte begränsat i Azure, oavsett prenumerationstyp.

## <a name="enterprise-agreement"></a>Enterprise-avtal
För Azure-användare av Enterprise Agreement ändras inte den tekniska möjligheten att skicka e-post utan att använda ett autentiserat relä. Både nya och befintliga Enterprise Agreement-användare kan prova utgående e-postleverans från virtuella Azure-datorer direkt till externa e-postleverantörer utan några begränsningar från Azure-plattformen. Även om det inte är garanterat att e-postleverantörer accepterar inkommande e-post från en viss användare, blockeras inte leveransförsök av Azure-plattformen för virtuella datorer inom Enterprise Agreement-prenumerationer. Du måste arbeta direkt med e-postleverantörer för att åtgärda eventuella problem med meddelandeleverans eller SKRÄPPOST som involverar specifika leverantörer.

## <a name="pay-as-you-go"></a>Betala per användning
Om du registrerade dig före den 15 november 2017 för prenumerationserbjudandena för användningsbaserad betalning eller Microsoft Partner Network kommer det inte att ändras i den tekniska möjligheten att prova utgående e-postleverans. Du kommer att fortsätta att kunna prova utgående e-postleverans från virtuella Azure-datorer inom dessa prenumerationer direkt till externa e-postleverantörer utan några begränsningar från Azure-plattformen. Återigen är det inte garanterat att e-postleverantörer kommer att acceptera inkommande e-post från en viss användare, och användarna måste arbeta direkt med e-postleverantörer för att åtgärda alla meddelandeleverans eller SPAM-filtreringsproblem som involverar specifika leverantörer.

För prenumerationer på användningsbaserad betalning eller Microsoft Partner Network som skapades efter den 15 november 2017 finns det tekniska begränsningar som blockerar e-post som skickas direkt från virtuella datorer i dessa prenumerationer. Om du vill skicka e-post från virtuella Azure-datorer direkt till externa e-postleverantörer (inte med hjälp av ett autentiserat SMTP-relä) kan du göra en begäran om att ta bort begränsningen. Förfrågningar granskas och godkänns efter Microsofts gottfinnande, och de beviljas först efter ytterligare bedrägeribekämpningskontroller. Om du vill göra en begäran öppnar du ett supportärende med hjälp av följande ärendetyp: **Teknisk** > **virtuell nätverksanslutning** > **Connectivity** > **kan inte skicka e-post (SMTP/Port 25)**. Se till att du lägger till information om varför distributionen måste skicka e-post direkt till e-postleverantörer i stället för att använda ett autentiserat relä.

När en prenumeration på användningsbaserad betalning eller Microsoft Partner Network har undantagits och de virtuella datorerna har stoppats & "Startat" från Azure-portalen, undantas alla virtuella datorer i den prenumerationen framöver. Undantaget gäller endast för den begärda prenumerationen och gäller endast för virtuell datortrafik som dirigeras direkt till internet. Routning port 25 trafik via Azure PaaS-tjänster som [Azure Firewall](https://azure.microsoft.com/services/azure-firewall/) stöds inte.

> [!NOTE]
> Microsoft förbehåller sig rätten att återkalla detta undantag om det fastställs att en överträdelse av användarvillkoren har skett.

## <a name="msdn-azure-pass-azure-in-open-education-bizspark-and-free-trial"></a>MSDN, Azure Pass, Azure i Open, Utbildning, BizSpark och kostnadsfri utvärderingsversion
Om du har skapat en MSDN-, Azure Pass-, Azure-i-, Utbildnings-, BizSpark-, Azure-sponsring, Azure-student, kostnadsfri utvärderingsversion eller en Visual Studio-prenumeration efter den 15 november 2017 har du tekniska begränsningar som blockerar e-post som skickas från virtuella datorer inom dessa prenumerationer direkt till e-postleverantörer. Restriktionerna görs för att förhindra missbruk. Inga begäranden om att ta bort den här begränsningen beviljas.

Om du använder dessa prenumerationstyper uppmanas du att använda SMTP-relätjänster, som beskrivs tidigare i den här artikeln eller ändra prenumerationstypen.

## <a name="cloud-service-provider-csp"></a>Molntjänstleverantör (CSP)

Om du använder Azure-resurser via CSP kan du begära att CSP:n skapar en begäran om avblockeringsbefrielse med Microsoft för din räkning om ett säkert SMTP-relä inte kan användas.

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du fortfarande behöver hjälp [kontaktar du supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att lösa problemet snabbt med hjälp av följande problemtyp: Problemtyp för **prenumerationshantering:** **Begär att aktivera Port 25-e-postflöde**.
