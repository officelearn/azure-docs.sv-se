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
ms.openlocfilehash: d2e5996da5a1fe3f5b154d57ee509f25e54e30ac
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96862368"
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

Om du registrerade dig före den 15 november 2017 för prenumerationen betala per användning, sker ingen ändring i den tekniska möjligheten att testa utgående e-postleverans. Du fortsätter att kunna testa utgående e-postleverans från virtuella Azure-datorer i dessa prenumerationer direkt till externa e-postleverantörer utan begränsningar från Azure-plattformen. Återigen är det inte säkert att e-postleverantörerna accepterar inkommande e-post från en specifik användare, och användarna måste arbeta direkt med e-postleverantörer för att kunna åtgärda eventuella meddelande leverans-eller skräp post filtrerings problem som berör vissa leverantörer.

För prenumerationer enligt principen betala per användning som skapades efter den 15 november 2017 kommer det att finnas tekniska begränsningar som blockerar e-post som skickas direkt från virtuella datorer i dessa prenumerationer. Om du vill kunna skicka e-post från virtuella Azure-datorer direkt till externa e-postleverantörer (inte använda ett autentiserat SMTP-relä) och du har ett konto med en betalnings historik kan du göra en begäran om att ta bort begränsningen i **anslutnings** avsnittet på bladet **diagnostisera och lösa** för en Azure Virtual Network-resurs i Azure Portal. Om det är kvalificerat så kommer din prenumeration att aktive ras, eller så får du instruktioner om nästa steg. 

När en prenumeration enligt principen betala per användning är undantagen och de virtuella datorerna har stoppats och startats i Azure Portal, undantas alla virtuella datorer i den prenumerationen. Undantaget gäller endast för den prenumeration som begärs och endast för VM-trafik som dirigeras direkt till Internet.

> [!NOTE]
> Microsoft förbehåller sig rätten att återkalla detta undantag om det fastställs att en överträdelse av tjänst villkoren har inträffat.

## <a name="msdn-azure-pass-azure-in-open-education-azure-for-students-vistual-studio-and-free-trial"></a>MSDN, Azure-pass, Azure i Open, utbildning, Azure för studenter, Visual Studio och kostnads fri utvärderings version

Om du har skapat en MSDN, Azure-pass, Azure i Open, utbildning, Azure student, kostnads fri utvärderings version eller en Visual Studio-prenumeration efter den 15 november 2017 har du tekniska begränsningar som blockerar e-post som skickas från virtuella datorer i dessa prenumerationer direkt till e-postleverantörer. Begränsningarna är gjorda för att förhindra missbruk. Inga förfrågningar om att ta bort den här begränsningen kommer att beviljas.

Om du använder de här prenumerations typerna rekommenderar vi att du använder SMTP Relay-tjänster, som beskrivs tidigare i den här artikeln eller ändrar din prenumerations typ.

## <a name="cloud-service-provider-csp"></a>Cloud Service Provider (CSP)

Om du använder Azure-resurser via CSP kan du göra en begäran om att ta bort begränsningen i **anslutnings** avsnittet på bladet **diagnostisera och lösa** för en Virtual Network resurs i Azure Portal. Om det är kvalificerat så kommer din prenumeration att aktive ras, eller så får du instruktioner om nästa steg.

## <a name="microsoft-partner-network-mpn-bizspark-plus-or-azure-sponsorship"></a>Microsoft Partner Network (MPN), BizSpark Plus eller Azure-sponsring

För Microsoft Partner Network (MPN), BizSpark Plus eller Azure-sponsring prenumerationer som har skapats efter den 15 november 2017 kommer det att finnas tekniska begränsningar som blockerar e-post som skickas direkt från virtuella datorer i dessa prenumerationer. Om du vill kunna skicka e-post från virtuella Azure-datorer direkt till externa e-postleverantörer (inte använda ett autentiserat SMTP-relä) kan du göra en begäran genom att öppna ett support ärende med följande typ av problem: **teknisk**  >  **Virtual Network**  >  **anslutning**  >  **kan inte skicka e-post (SMTP/port 25)**. Se till att du lägger till information om varför din distribution måste skicka e-post direkt till e-postleverantörer i stället för att använda ett autentiserat relä. Begär Anden kommer att granskas och godkännas enligt Microsofts gottfinnande. Begär Anden kan beviljas endast efter att ytterligare skydds kontroller har slutförts. 

När en prenumeration har undantagits och de virtuella datorerna har stoppats och startats i Azure Portal, undantas alla virtuella datorer i den prenumerationen. Undantaget gäller endast för den prenumeration som begärs och endast för VM-trafik som dirigeras direkt till Internet.

## <a name="restrictions-and-limitations"></a>Begränsningar och begränsningar

- Routning av port 25-trafik via Azure PaaS-tjänster som [Azure-brandvägg](https://azure.microsoft.com/services/azure-firewall/) stöds inte.

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du fortfarande behöver hjälp kan du [kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att lösa problemet snabbt genom att använda följande typ av problem: **teknisk**  >  **Virtual Network**  >  **Connectivity**  >  **-anslutning kan inte skicka e-post (SMTP/port 25)**.
