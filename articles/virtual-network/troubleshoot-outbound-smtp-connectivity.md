---
title: Felsöka utgående SMTP-anslutningar i Azure | Microsoft Docs
description: Lär dig den rekommenderade metoden för att skicka e-post och hur du felsöker problem med utgående SMTP-anslutningar i Azure.
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
ms.openlocfilehash: e58f498ca254378354fb0e9e21d04fe9969588c9
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87265270"
---
# <a name="troubleshoot-outbound-smtp-connectivity-issues-in-azure"></a>Felsök problem med utgående SMTP-anslutningar i Azure

Från och med den 15 november 2017 görs utgående e-postmeddelanden som skickas direkt till externa domäner (till exempel outlook.com och gmail.com) från en virtuell dator (VM) endast tillgängliga för vissa prenumerations typer i Microsoft Azure. Utgående SMTP-anslutningar som använder TCP-port 25 blockerades. (Port 25 används främst för oautentiserad e-postleverans.)

Den här förändringen gäller endast för nya prenumerationer och nya distributioner sedan den 15 november 2017.

## <a name="recommended-method-of-sending-email"></a>Rekommenderad metod för att skicka e-post
Vi rekommenderar att du använder autentiserade SMTP-relätjänster (som vanligt vis ansluter via TCP-port 587 eller 443, men som också stöder andra portar) för att skicka e-post från virtuella Azure-datorer eller från Azure App tjänster. Dessa tjänster används för att underhålla IP-eller domän rykte för att minimera risken för att e-postleverantörer från tredje part kommer att avvisa meddelandet. Dessa SMTP Relay-tjänster omfattar men är inte begränsade till [SendGrid](https://sendgrid.com/partners/azure/). Det är också möjligt att du har en säker SMTP-Relay-tjänst som körs lokalt och som du kan använda.

Att använda dessa e-postleverans tjänster är inte begränsat i Azure, oavsett prenumerations typ.

## <a name="enterprise-agreement"></a>Enterprise-avtal
För Enterprise-avtal Azure-användare finns det ingen ändring av den tekniska möjligheten att skicka e-post utan att använda ett autentiserat relä. Både nya och befintliga Enterprise-avtal användare kan testa utgående e-postleverans från virtuella Azure-datorer direkt till externa e-postleverantörer utan begränsningar från Azure-plattformen. Även om det inte garanterar att e-postleverantörer kommer att acceptera inkommande e-post från en specifik användare, blockeras inte leverans försök av Azure-plattformen för virtuella datorer i Enterprise-avtal prenumerationer. Du måste arbeta direkt med e-postleverantörer för att åtgärda eventuella problem med meddelande leverans eller skräp post filtrering som rör specifika leverantörer.

## <a name="pay-as-you-go"></a>Betala per användning
Om du registrerade dig före den 15 november 2017 för erbjudandet betala per användning eller Microsoft Partner Network prenumerationer, kommer det inte att finnas någon förändring i den tekniska möjligheten att testa utgående e-postleverans. Du fortsätter att kunna testa utgående e-postleverans från virtuella Azure-datorer i dessa prenumerationer direkt till externa e-postleverantörer utan begränsningar från Azure-plattformen. Återigen är det inte säkert att e-postleverantörerna accepterar inkommande e-post från en specifik användare, och användarna måste arbeta direkt med e-postleverantörer för att kunna åtgärda eventuella meddelande leverans-eller skräp post filtrerings problem som berör vissa leverantörer.

För att betala per användning eller Microsoft Partner Network prenumerationer som skapats efter den 15 november 2017 kommer det att finnas tekniska begränsningar som blockerar e-post som skickas direkt från virtuella datorer i dessa prenumerationer. Om du vill kunna skicka e-post från virtuella Azure-datorer direkt till externa e-postleverantörer (inte använda ett autentiserat SMTP-relä) kan du göra en begäran om att ta bort begränsningen. Förfrågningarna granskas och godkänns enligt Microsofts gottfinnande, och de beviljas endast när ytterligare skydd mot bedrägerier görs. Om du vill göra en förfrågan öppnar du ett support ärende med följande typ av problem: **teknisk**  >  **Virtual Network**  >  **anslutning**  >  **kan inte skicka e-post (SMTP/port 25)**. Se till att du lägger till information om varför din distribution måste skicka e-post direkt till e-postleverantörer i stället för att använda ett autentiserat relä.

När en "betala per användning"-prenumeration eller Microsoft Partner Network prenumeration är undantagen och de virtuella datorerna har stoppats & har startats från Azure Portal, kommer alla virtuella datorer i den prenumerationen att undantas. Undantaget gäller endast för den begärda prenumerationen och gäller endast för virtuella dator trafik som dirigeras direkt till Internet. Routning av port 25-trafik via Azure PaaS Services, t. ex. [Azure-brandväggen](https://azure.microsoft.com/services/azure-firewall/) , stöds inte.

> [!NOTE]
> Microsoft förbehåller sig rätten att återkalla detta undantag om det fastställs att en överträdelse av tjänst villkoren har inträffat.

## <a name="msdn-azure-pass-azure-in-open-education-bizspark-and-free-trial"></a>MSDN, Azure-pass, Azure i Open, utbildning, BizSpark och kostnads fri utvärderings version
Om du har skapat en MSDN, Azure-pass, Azure i Open, utbildning, BizSpark, Azure-sponsring, Azure student, kostnads fri utvärdering eller någon Visual Studio-prenumeration efter den 15 november 2017 har du tekniska begränsningar som blockerar e-post som skickas från virtuella datorer i dessa prenumerationer direkt till e-postleverantörer. Begränsningarna är gjorda för att förhindra missbruk. Inga förfrågningar om att ta bort den här begränsningen kommer att beviljas.

Om du använder de här prenumerations typerna rekommenderar vi att du använder SMTP Relay-tjänster, som beskrivs tidigare i den här artikeln eller ändrar din prenumerations typ.

## <a name="cloud-service-provider-csp"></a>Cloud Service Provider (CSP)

Om du använder Azure-resurser via CSP kan du begära att CSP: n kan skapa en begäran om att avblockera undantag med Microsoft för din räkning om det inte går att använda ett säkert SMTP-relä.

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du fortfarande behöver hjälp kan du [kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att lösa problemet snabbt genom att använda följande typ av problem: typ av **prenumerations hantering** : **begäran om att aktivera port 25 e-flöde**.
