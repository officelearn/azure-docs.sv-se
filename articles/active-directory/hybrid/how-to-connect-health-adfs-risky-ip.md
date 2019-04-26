---
title: Azure AD Connect Health med AD FS riskfyllda IP-adresser | Microsoft Docs
description: Beskriver Azure AD Connect Health AD FS riskfyllda IP-adresser.
services: active-directory
documentationcenter: ''
ms.reviewer: zhiweiwangmsft
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 49b93cb7852692e4dad65fcbd72cd749db1b16fb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60350583"
---
# <a name="risky-ip-report-public-preview"></a>Riskfyllda IP-adresser (offentlig förhandsversion)
AD FS-kunder kan göra slutpunkter tillgängliga för lösenordsautentisering på Internet för att tillhandahålla autentiseringstjänster till slutanvändare som ska få åtkomst till SaaS-program, till exempel Office 365. I det här fallet är det möjligt för någon obehörig att försöka logga in på ditt AD FS-system genom att gissa slutanvändarens lösenord och få åtkomst till programresurser. AD FS har innehållit en utelåsningsfunktion för extranätskonton som förhindrar dessa typer av angrepp sedan AD FS i Windows Server 2012 R2. Om du har en lägre version rekommenderar vi starkt att du uppgraderar ditt AD FS-system till Windows Server 2016. <br />

Dessutom är det möjligt att en enskild IP-adress kan försöka utföra flera inloggningar mot flera användare. I dessa fall kan antalet försök per användare ligga under tröskelvärdet för kontots utelåsningsskydd i AD FS. Azure AD Connect Health innehåller nu en ”rapport för riskfyllda IP-adresser” som identifierar det här tillståndet och meddelar administratörer om detta inträffar. Följande är viktiga fördelar med den här rapporten: 
- Identifiering av IP-adresser som överskrider ett tröskelvärde för misslyckade lösenordsbaserade inloggningar
- Stöd för misslyckade inloggningar på grund av felaktiga lösenord eller på grund av extranätsutelåsning
- E-postavisering som varnar administratörer när detta inträffar med anpassningsbara e-postinställningar
- Anpassningsbara tröskelvärdesinställningar som överensstämmer med säkerhetsprinciper i organisationen
- Nedladdningsbara rapporter för offlineanalys och integrering med andra system via automatisering

> [!NOTE]
> Om du ska kunna använda rapporten måste AD FS-granskning vara aktiverat. Mer information finns i [Aktivera granskning för AD FS](how-to-connect-health-agent-install.md#enable-auditing-for-ad-fs). <br />
> Om du vill ha åtkomst till förhandsversionen måste du ha behörighet som global administratör eller [säkerhetsläsare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#security-reader).  
> 

## <a name="what-is-in-the-report"></a>Vad ingår i rapporten?
Misslyckade inloggningen aktivitet klienternas IP-adresser sammanställs via Web Application Proxy-servrar. Varje objekt i rapporten för riskfyllda IP-adresser visar sammanställd information om misslyckade AD FS-inloggningsaktiviteter där angivet tröskelvärde har överskridits. Den innehåller följande information: ![Azure AD Connect Health-portalen](./media/how-to-connect-health-adfs/report4a.png)

| Rapportobjekt | Beskrivning |
| ------- | ----------- |
| Tidsstämpel | Visar tidsstämpeln baserat på Azure-portalens lokala tid när tidsperioden för identifiering startar.<br /> Alla dagliga händelser genereras vid midnatt UTC-tid. <br />Varje timhändelse har en tidsstämpel som är avrundad till timmens början. Första aktivitetens starttid från ”firstAuditTimestamp” hittar du i den exporterade filen. |
| Utlösartyp | Visar tidsperiod för identifieringstypen. De sammanställda utlösartyperna visas per timme eller per dag. Det här är användbart vid identifiering av en råstyrkeattack med hög frekvens, jämfört med en långsam attack där antalet försök är fördelade över hela dagen. |
| IP-adress | En enskild riskfylld IP-adress som antingen har ett felaktigt lösenord eller en extranätsutelåsning vid inloggning. Detta kan vara en IPv4 eller IPv6-adress. |
| Antal felaktiga lösenord | Antalet felaktiga lösenord från IP-adressen under tidsperioden för identifiering. Felaktiga lösenord kan inträffa flera gånger för vissa användare. Observera att detta inte inkluderar misslyckade försök på grund av utgångna lösenord. |
| Antal extranätsutelåsningar | Antalet extranätsutelåsningar från IP-adressen under tidsperioden för identifiering. Extranätsutelåsningar kan inträffa flera gånger för vissa användare. Detta visas bara om extranätsutelåsningen har konfigurerats i AD FS (version 2012 R2 eller senare). <b>Obs!</b> Vi rekommenderar starkt att aktivera den här funktionen om du tillåter extranätsinloggningar med lösenord. |
| Försök för unika användare | Antalet försök för unika användarkonton från IP-adressen under tidsperioden för identifiering. Detta är en mekanism för att särskilja ett mönster vid en enda användarattack jämfört med attackmönster mot flera användare.  |

Nedanstående rapportobjekt innebär från kl. 18:00 till 19:00 den 28/2 2018, innehöll IP-adressen <i>104.2XX.2XX.9</i> inte några felaktiga lösenord, men 284 extranätsutelåsningar. 14 unika användare påverkades inom kriterierna. Händelsen överskred tröskelvärdet per timme i rapporten. 

![Azure AD Connect Health-portalen](./media/how-to-connect-health-adfs/report4b.png)

> [!NOTE]
> - Det är bara aktiviteter som överskrider angivet tröskelvärde visas i rapportlistan. 
> - Den här rapporten kan spåras bakåt i högst 30 dagar.
> - Aviseringsrapporten visar inte Exchange IP-adresser eller privata IP-adresser. De ingår dock fortfarande i exportlistan. 
>

![Azure AD Connect Health-portalen](./media/how-to-connect-health-adfs/report4c.png)

## <a name="load-balancer-ip-addresses-in-the-list"></a>Belastningsutjämnarens IP-adresser i listan
Sammanställning av lastbalanserarens misslyckade inloggningar aktiviteter och uppnått tröskelvärde. Om du ser IP-adresser för lastbalanserare är det mycket troligt att en extern lastbalanserare inte skickar klientens IP-adress när den skickar sin begäran till servern för webbprogramproxyn. Konfigurera lastbalanseraren korrekt för att vidarebefordra klientens IP-adress. 

## <a name="download-risky-ip-report"></a>Hämta riskfyllda IP-adresser 
Med hjälp av funktionen **Ladda ned** kan listan med riskfyllda IP-adresser under de senaste 30 dagarna exporteras från Connect Health-portalen. Exportresultatet inkluderar alla misslyckade AD FS-inloggningar i varje tidsfönster, så du kan anpassa filtreringen efter exporten. Förutom markerade sammanställningar i portalen visar exportresultatet också mer information om misslyckade inloggningar per IP-adress:

|  Rapportobjekt  |  Beskrivning  | 
| ------- | ----------- | 
| firstAuditTimestamp | Visar den första tidstämpel då misslyckade aktiviteter startades under tidsperioden för identifieringen.  | 
| lastAuditTimestamp | Visar den sista tidstämpel då misslyckade aktiviteter avslutades under tidsperioden för identifieringen.  | 
| attemptCountThresholdIsExceeded | Flagga ifall de aktuella aktiviteterna överstiger tröskelvärdet för aviseringar.  | 
| isWhitelistedIpAddress | Flagga ifall IP-adressen är filtrerad från avisering och rapportering. Privata IP-adresser (<i>10.x.x.x, 172.x.x.x och 192.168.x.x</i>) och Exchange-IP-adresser är filtrerade och markeras som True. Om du ser privata IP-adressintervall är det mycket troligt att en extern lastbalanserare inte skickar klientens IP-adress när den skickar sin begäran till servern för webbprogramproxyn.  | 

## <a name="configure-notification-settings"></a>Konfigurera inställningar för meddelanden
Administratörskontakter i rapporten kan uppdateras i **Meddelandeinställningar**. Som standard är aviseringar om riskfyllda IP-adresser inaktiverade. Du kan aktivera meddelanden genom att visa/dölja knappen under ”Get email notifications for IP addresses exceeding failed activity threshold report” (Hämta e-postmeddelanden för IP-adresser som överstiger tröskelvärdet för misslyckade aktiviteter). Precis som i inställningar av allmänna aviseringar i Connect Health, kan du anpassa vilka mottagare som ska få meddelanden om riskfyllda IP-adresser härifrån. Du kan även meddela alla globala administratörer när ändringen är gjord. 

## <a name="configure-threshold-settings"></a>Konfigurera inställningar för tröskelvärde
Aviseringströskelvärdet kan uppdateras i Tröskelinställningar. Systemet har tröskelvärdet inställt som standard. Det finns fyra kategorier i tröskelinställningarna för rapporten om riskfyllda IP-adresser:

![Azure AD Connect Health-portalen](./media/how-to-connect-health-adfs/report4d.png)

| Tröskelobjekt | Beskrivning |
| --- | --- |
| (Felaktig U/P + extranätsutelåsning)/dag  | Tröskelinställningen rapporterar aktivitet och utlöser en varningsavisering när antalet felaktiga lösenord plus antalet extranätsutelåsningar överskrider den per **dag**. |
| (Felaktig U/P + extranätsutelåsning)/timme | Tröskelinställningen rapporterar aktivitet och utlöser en varningsavisering när antalet felaktiga lösenord plus antalet extranätsutelåsningar överskrider den per **timme**. |
| Extranätsutelåsning/dag | Tröskelinställningen rapporterar aktivitet och utlöser en varningsavisering när antalet extranätsutelåsningar överskrider den per **dag**. |
| Extranätsutelåsning/timme| Tröskelinställningen rapporterar aktivitet och utlöser en varningsavisering när antalet extranätsutelåsningar överskrider den per **timme**. |

> [!NOTE]
> - Ändringar av rapportens tröskelvärde tillämpas en timme efter ändringen. 
> - Befintliga rapporterade objekt påverkas inte av tröskeländringen. 
> - Vi rekommenderar att du analyserar antalet händelser i din miljö och justerar tröskelvärdet i enlighet med detta. 
>
>

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
**Varför ser jag privata IP-adressintervall i rapporten?**  <br />
Privata IP-adresser (<i>10.x.x.x, 172.x.x.x och 192.168.x.x</i>) och Exchange-IP-adresser är filtrerade och markeras som True i IP-vitlistan. Om du ser privata IP-adressintervall är det mycket troligt att en extern lastbalanserare inte skickar klientens IP-adress när den skickar sin begäran till servern för webbprogramproxyn.

**Varför ser jag IP-adresser för belastningsutjämnare i rapporten?**  <br />
Om du ser IP-adresser för lastbalanserare är det mycket troligt att en extern lastbalanserare inte skickar klientens IP-adress när den skickar sin begäran till servern för webbprogramproxyn. Konfigurera lastbalanseraren korrekt för att vidarebefordra klientens IP-adress. 

**Vad gör jag för att blockera IP-adressen?**  <br />
Du bör lägga till identifierade skadliga IP-adresser i brandväggen eller blockera dem i Exchange.   <br />

**Varför inte visas alla objekt i den här rapporten?** <br />
- De misslyckade inloggningsaktiviteterna överskrider inte tröskelinställningarna.
- Kontrollera att det inte finns någon aktiv varning om att ”Hälsotjänsten är inte uppdaterad” i din AD FS-serverlista.  Läs mer om [felsökning av den här aviseringen](how-to-connect-health-data-freshness.md).
- Granskningar är inte aktiverade i AD FS-servergrupperna.

**Varför ser jag inga åtkomst till rapporten?**  <br />
Du måste ha behörighet som global administratör eller [säkerhetsläsare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#security-reader). Kontakta en global administratör för att få åtkomst.


## <a name="next-steps"></a>Nästa steg
* [Azure AD Connect Health](whatis-hybrid-identity-health.md)
* [Installation av Azure AD Connect Health Agent](how-to-connect-health-agent-install.md)
