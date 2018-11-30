---
title: Felsöka utgående SMTP-anslutning i Azure | Microsoft Docs
description: Lär dig hur du felsöker problem med utgående SMTP-anslutning i Azure.
services: virtual-network
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2018
ms.author: genli
ms.openlocfilehash: 34d42f9987303c1381584ae4b2991a8f30a67ed5
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/29/2018
ms.locfileid: "52618967"
---
# <a name="troubleshoot-outbound-smtp-connectivity-issues-in-azure"></a>Felsökning av anslutningsproblem för utgående SMTP i Azure

Från och med den 15 November 2017 är utgående e-postmeddelanden som skickas direkt till externa domäner (till exempel outlook.com och gmail.com) från en virtuell dator (VM) tillgänglig endast för vissa typer av prenumerationer i Microsoft Azure. Utgående SMTP-anslutningar som använder TCP-port 25 blockerades. (Port 25 används främst för oautentiserade e-postleverans.)

Den här ändringen i beteende gäller endast för nya prenumerationer och nya distributioner sedan den 15 November 2017.

## <a name="recommended-method-of-sending-email"></a>Rekommenderad metod för att skicka e-post
Vi rekommenderar att du använder autentiserade SMTP-relätjänster (som vanligtvis ansluta via TCP-port 587 eller 443, men stöder andra portar för) för att skicka e-post från virtuella Azure-datorer eller från Azure App Services. Dessa tjänster används för att underhålla IP eller domän rykte för att minska möjligheten att tredje parts e-postleverantörer avvisar meddelandet. Sådana SMTP-relätjänster innehåller men inte begränsat till [SendGrid](http://sendgrid.com/partners/azure/). Det är också möjligt att du har en säker SMTP-relätjänst som körs lokalt som du kan använda.

Med hjälp av dessa tjänster för leverans av e-post är inte begränsad i Azure, oavsett vilken prenumerationstyp.

## <a name="enterprise-agreement"></a>Enterprise Agreement
Det finns ingen ändring i teknisk möjlighet att skicka e-post utan att använda ett autentiserat relä för Enterprise Agreement Azure-användare. Både nya och befintliga Enterprise Agreement-användare kan försöka utgående e-postleverans från virtuella Azure-datorer direkt till externa e-postleverantörer utan begränsningar från Azure-plattformen. Även om det inte är säkert att e-postleverantörerna godkänner inkommande e-post från en viss användare, blockeras leveransförsök inte av Azure-plattformen för virtuella datorer i Enterprise Agreement-prenumerationer. Du måste arbeta direkt med e-postleverantörer åtgärda eventuella meddelandeleverans eller skicka skräppost filtrering problem som rör specifika providers.

## <a name="pay-as-you-go"></a>Betala per användning
Om du har registrerat dig innan den 15 November 2017 för användningsbaserade eller Microsoft Partner Network-prenumerationen erbjuder det blir ingen ändring i teknisk möjlighet att prova utgående e-postleverans. Du kommer att fortsätta att kunna testa utgående e-postleverans från virtuella Azure-datorer inom dessa prenumerationer direkt till externa e-postleverantörer utan begränsningar från Azure-plattformen. Igen, är det inte säkert att e-postleverantörerna godkänner inkommande e-post från en viss användare och användarna måste arbeta direkt med e-postleverantörer åtgärda eventuella meddelandeleverans eller skicka skräppost filtrering problem som rör specifika providers.

För användningsbaserad betalning eller Microsoft Partner Network-prenumerationer som har skapats efter den 15 November 2017 kan finnas det tekniska begränsningar som blockerar e-postmeddelande som skickas direkt från virtuella datorer inom dessa prenumerationer. Om du vill kunna skicka e-post från virtuella Azure-datorer direkt till externa e-postleverantörer (inte använda ett autentiserat SMTP-relä) kan du göra en begäran om att ta bort begränsningen. Förfrågningarna kommer granskas och godkänns enligt Microsofts bedömning, och de beviljas endast efter att bedrägerikontroller har utförts. Öppna ett supportärende för att göra en begäran, med hjälp av följande problem: **teknisk** > **virtuellt nätverk** > **anslutning**  >  **Kan inte skicka e-post (SMTP/Port 25)**. Se till att du lägger till information om varför din distribution har skicka e-post direkt till e-postleverantörer i stället för att använda ett autentiserat relä.

När en prenumeration med användningsbaserad betalning eller Microsoft Partner Network undantas kan är virtuella datorer i den aktuella prenumerationen endast undantagna framöver.

> [!NOTE]
> Microsoft förbehåller sig rätten att återkalla det här undantaget om det fastställs att en överträdelse av tjänstvillkoren har inträffat.

## <a name="msdn-azure-pass-azure-in-open-education-bizspark-and-free-trial"></a>MSDN, Azure-Pass, Azure i Open, Education, BizSpark och kostnadsfri utvärderingsversion
Om du har skapat en MSDN, Azure-Pass, Azure i öppen, Education, BizSpark, Azure-sponsring, Azure Student, kostnadsfri utvärderingsversion eller alla Visual Studio-prenumeration efter 15 November 2017 kan har du tekniska begränsningar att blockera e-post som skickas från virtuella datorer inom dessa prenumerationer direkt till e-providers. Begränsningarna är klar för att förhindra missbruk. Förfrågningar om att ta bort den här begränsningen kan beviljas.

Om du använder dessa typer av prenumerationer är du rekommenderas att använda SMTP-relätjänster, enligt beskrivningen tidigare i den här artikeln.

## <a name="cloud-service-provider-csp"></a>Molntjänstleverantör (CSP)

Om du använder Azure-resurser via CSP kan skapa ett supportärende via ditt val av CSP och du kan begära CSP: N för att skapa ett ärende avblockera för din räkning om en säker SMTP-relä inte kan användas.

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du fortfarande behöver hjälp, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att lösa problemet snabbt.
