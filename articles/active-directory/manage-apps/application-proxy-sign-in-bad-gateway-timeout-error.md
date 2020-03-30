---
title: Det går inte att komma åt det här företagsprogrammet med appen Proxy-appen
description: Så här löser du vanliga åtkomstproblem med Azure AD Application Proxy-program.
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
ms.date: 05/21/2019
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d61f4b4bce9b8287dc13237f071684ea5d135fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275471"
---
# <a name="cant-access-this-corporate-application-error-when-using-an-application-proxy-application"></a>Felet "Kan inte komma åt det här företagsprogrammet" när du använder ett programproxyprogram

Den här artikeln hjälper dig att felsöka vanliga problem för felet "Den här företagsappen kan inte nås" i ett Azure AD Application Proxy-program.

## <a name="overview"></a>Översikt

När du ser det här felet hittar du statuskoden på felsidan. Den koden är troligen en av följande statuskoder:

- **Timeout för gateway:** Tjänsten Application Proxy kan inte nå anslutningen. Det här felet indikerar vanligtvis ett problem med anslutningstilldelningen, själva kopplingen eller nätverksreglerna runt anslutningen.
- **Felaktig gateway:** Anslutningen kan inte nå backend-programmet. Det här felet kan tyda på en felaktig konfiguration av programmet.
- **Förbjudet**: Användaren har inte behörighet att komma åt programmet. Det här felet kan inträffa antingen när användaren inte har tilldelats programmet i Azure Active Directory, eller om användaren på serverdelen inte har behörighet att komma åt programmet.

Om du vill hitta koden tittar du på texten längst ned till vänster i felmeddelandet för fältet "Statuskod". Också leta efter ytterligare tips längst ner på sidan.

![Exempel: Timeout-fel för gateway](./media/application-proxy-sign-in-bad-gateway-timeout-error/connection-problem.png)

Mer information om hur du felsöker orsaken till dessa fel och mer information om föreslagna korrigeringar finns i motsvarande avsnitt nedan.

## <a name="gateway-timeout-errors"></a>Timeout-fel för gateway

En timeout för gateway inträffar när tjänsten försöker nå kopplingen och inte kan inom tidsgränsen. Det här felet orsakas vanligtvis av ett program som tilldelats en kopplingsgrupp utan fungerande kopplingar, eller så är vissa portar som krävs av kopplingen inte öppna.

## <a name="bad-gateway-errors"></a>Felaktiga gatewayfel

Ett felaktigt gatewayfel indikerar att anslutningen inte kan nå backend-programmet. kontrollera att du har publicerat rätt program. Vanliga misstag som orsakar det här felet är:

- Ett stavfel eller misstag i den interna webbadressen
- Det går inte att publicera roten till programmet. Till exempel <http://expenses/reimbursement> publicering men försöker komma åt<http://expenses>
- Problem med konfigurationen av Kerberos Constrained Delegation (KCD)
- Problem med backend-programmet

## <a name="forbidden-errors"></a>Förbjudna fel

Om du ser ett förbjudet fel har användaren inte tilldelats programmet. Det här felet kan vara antingen i Azure Active Directory eller i backend-programmet.

Mer information om hur du tilldelar användare till programmet i Azure finns i [konfigurationsdokumentationen](application-proxy-add-on-premises-application.md#test-the-application).

Om du bekräftar att användaren har tilldelats programmet i Azure kontrollerar du användarkonfigurationen i serverdelsprogrammet. Om du använder Kerberos Begränsad delegering/integrerad Windows-autentisering läser du sidan KCD-felsökning för riktlinjer.

## <a name="check-the-applications-internal-url"></a>Kontrollera programmets interna URL

Som ett första snabbt steg dubbelkollar och åtgärdar du den interna webbadressen genom att öppna programmet via **Enterprise Applications**och sedan välja **menyn Programproxy.** Kontrollera att den interna URL:en är den som används från det lokala nätverket för att komma åt programmet.

## <a name="check-the-application-is-assigned-to-a-working-connector-group"></a>Kontrollera att programmet har tilldelats en fungerande kopplingsgrupp

Så här verifierar du att programmet har tilldelats en fungerande kopplingsgrupp:

1. Öppna programmet i portalen genom att gå till **Azure Active Directory**, klicka på Enterprise **Applications**och sedan **alla program.** Öppna programmet och välj sedan **Programproxy** från den vänstra menyn.
1. Titta på fältet Kopplingsgrupp. Om det inte finns några aktiva kopplingar i gruppen visas en varning. Om du inte ser några varningar går du vidare för att kontrollera att alla nödvändiga portar är tillåtna.
1. Om fel kopplingsgrupp visas använder du listrutan för att välja rätt grupp och bekräftar att du inte längre ser några varningar. Om den avsedda kopplingsgruppen visas klickar du på varningsmeddelandet för att öppna sidan med anslutningshantering.
1. Härifrån finns det några sätt att borra ytterligare:

   - Flytta en aktiv koppling till gruppen: Om du har en aktiv koppling som ska tillhöra den här gruppen och har siktlinje till målbacksdelprogrammet kan du flytta kopplingen till den tilldelade gruppen. Det gör du genom att klicka på kopplingen. I fältet "Kopplingsgrupp" använder du listrutan för att markera rätt grupp och klickar på Spara.
   - Ladda ner en ny Koppling för den gruppen: Från den här sidan kan du hämta länken för att [hämta en ny Connector](https://download.msappproxy.net/Subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/Connector/Download). Installera kontakten på en maskin med direkt siktlinje till backend-programmet. Vanligtvis installeras anslutningen på samma server som programmet. Använd länken hämta Connector för att hämta en anslutning till målmaskinen. Klicka sedan på kopplingskontakten och använd listrutan "Kopplingsgrupp" för att se till att den tillhör rätt grupp.
   - Undersök en inaktiv koppling: Om en koppling visas som inaktiv kan den inte nå tjänsten. Det här felet beror vanligtvis på att vissa nödvändiga portar blockeras. Lös problemet genom att gå vidare för att kontrollera att alla nödvändiga portar är tillåtna.

När du har använt dessa steg för att säkerställa att programmet tilldelas en grupp med fungerande kopplingar testar du programmet igen. Om det fortfarande inte fungerar fortsätter du till nästa avsnitt.

## <a name="check-all-required-ports-are-open"></a>Kontrollera att alla nödvändiga portar är öppna

Information om att alla nödvändiga portar är öppna finns i dokumentationen om att öppna portar. Om alla nödvändiga portar är öppna går du vidare till nästa avsnitt.

## <a name="check-for-other-connector-errors"></a>Sök efter andra anslutningsfel

Om inget av ovanstående löser problemet är nästa steg att leta efter problem eller fel med själva kopplingen. Du kan se några vanliga fel i [felsöka dokumentet](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot#connector-errors).

Du kan också titta direkt på anslutningsloggarna för att identifiera eventuella fel. Många av felmeddelandena delar specifika rekommendationer för korrigeringar. Om du vill visa loggarna läser du [dokumentationen](application-proxy-connectors.md#under-the-hood)för kopplingar .

## <a name="additional-resolutions"></a>Ytterligare lösningar

Om ovanstående inte åtgärda problemet, det finns några olika möjliga orsaker. Så här identifierar du problemet:

Om programmet är konfigurerat för att använda integrerad Windows-autentisering (IWA) testar du programmet utan enkel inloggning. Om inte, gå vidare till nästa stycke. Om du vill kontrollera programmet utan enkel inloggning öppnar du programmet via **Enterprise Applications** och går till menyn **Enkel inloggning.** Ändra listrutan från "Integrerad Windows-autentisering" till "Azure AD single sign-on disabled".

Öppna nu en webbläsare och försök komma åt programmet igen. Du bör uppmanas att autentisering och komma in i programmet. Om du kan autentisera är problemet med konfigurationen för Kerberos Constrained Delegation (KCD) som aktiverar den enda inloggningen. Mer information finns på sidan KCD-felsökning.

Om du fortsätter att se felet går du till datorn där kopplingen är installerad, öppnar en webbläsare och försöker nå den interna URL som används för programmet. Kopplingen fungerar som en annan klient från samma dator. Om du inte kan nå programmet undersöker du varför datorn inte kan nå programmet eller använder en anslutningsapp på en server som kan komma åt programmet.

Om du kan nå programmet från den datorn, för att leta efter problem eller fel med själva kopplingen. Du kan se några vanliga fel i [felsöka dokumentet](application-proxy-troubleshoot.md#connector-errors). Du kan också titta direkt på anslutningsloggarna för att identifiera eventuella fel. Många av våra felmeddelanden kan dela mer specifika rekommendationer för korrigeringar. Mer information om hur du visar loggarna finns i [dokumentationen till våra anslutningsappar](application-proxy-connectors.md#under-the-hood).

## <a name="next-steps"></a>Nästa steg

[Förstå Azure AD-programproxy-kopplingar](application-proxy-connectors.md)
