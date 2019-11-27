---
title: Det går inte att komma åt detta företags program fel med App proxy-appen
description: Så här att lösa vanliga åtkomstproblem med Azure AD Application Proxy-program.
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
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275471"
---
# <a name="cant-access-this-corporate-application-error-when-using-an-application-proxy-application"></a>”Det går inte att komma åt företagets programmet” fel när du använder ett programproxy-program

Den här artikeln hjälper dig att felsöka vanliga problem med ”företagets appen kan inte nås”-fel på ett Azure AD Application Proxy-program.

## <a name="overview"></a>Översikt

När du ser det här felet kan du hitta statuskoden på felsidan. Koden är förmodligen en av följande statuskoder:

- **Gateway-timeout**: tjänsten Application Proxy kan inte ansluta till anslutnings tjänsten. Det här felet tyder vanligen på problem med anslutningen tilldelningen kopplingen själv, eller nätverket regler runt anslutningen.
- **Felaktig Gateway**: anslutningen kan inte komma åt backend-programmet. Det här felet kan indikera en felaktig konfiguration av programmet.
- **Tillåts**inte: användaren har inte behörighet att komma åt programmet. Det här felet kan inträffa om användaren inte har tilldelats till programmet i Azure Active Directory eller om på serverdelen användaren inte har behörighet att komma åt programmet.

Du hittar koden genom att titta på texten längst ned till vänster i felmeddelandet för fältet ”statuskod”. Också söka efter eventuella ytterligare tips längst ned på sidan.

![Exempel: Gateway-timeout-fel](./media/application-proxy-sign-in-bad-gateway-timeout-error/connection-problem.png)

Information om hur du felsöker de grundläggande orsakerna till de här felen och mer information om förslag på åtgärder finns i motsvarande avsnitt nedan.

## <a name="gateway-timeout-errors"></a>Gateway-Timeout-fel

En gateway-timeout inträffar när tjänsten försöker nå anslutningen och kan inte inom tidsgränsen. Det här felet orsakas typiskt av ett program som har tilldelats en Anslutningsgrupp med ingen fungerande anslutningar eller vissa portar som krävs av anslutningen är inte öppna.

## <a name="bad-gateway-errors"></a>Felaktig Gateway-fel

En felaktig gateway-fel anger att anslutningen är inte nå backend-applikationer. Kontrollera att du har publicerat av rätt applikation. Vanliga fel som orsakar felet är:

- Skriv- eller fel i den interna URL: en
- Publicera inte roten för programmet. Till exempel publicera <http://expenses/reimbursement> men försöker komma åt <http://expenses>
- Problem med konfigurationen av Kerberos-begränsad delegering (KCD)
- Problem med backend-programmet

## <a name="forbidden-errors"></a>Förbjudet fel

Om du ser ett förbjudet fel, har användaren inte tilldelats till programmet. Det här felet kan vara antingen i Azure Active Directory eller på backend-applikationer.

Information om hur du tilldelar användare till programmet i Azure finns i [konfigurations dokumentationen](application-proxy-add-on-premises-application.md#test-the-application).

Om du har bekräftat tilldelas användaren till programmet i Azure, kontrollera Användarkonfiguration i backend-applikationer. Om du använder Kerberos-begränsad delegering/integrerad Windows-autentisering finns på sidan KCD felsöka riktlinjer.

## <a name="check-the-applications-internal-url"></a>Kontrollera programmets interna URL: en

Som första snabb steg, dubbelklicka och korrigera den interna URL: en genom att öppna programmet via **företags program**och sedan välja menyn **Application Proxy** . Kontrol lera att den interna URL: en används från ditt lokala nätverk för att få åtkomst till programmet.

## <a name="check-the-application-is-assigned-to-a-working-connector-group"></a>Kontrollera programmet är kopplad till en fungerande Anslutningsgrupp

Att kontrollera att tilldelas till ett aktivt Anslutningsgrupp:

1. Öppna programmet i portalen genom att gå till **Azure Active Directory**, klicka på **företags program**och sedan på **alla program.** Öppna programmet och välj sedan **Application Proxy** på den vänstra menyn.
1. Titta på fältet Anslutningsgrupp. Om det finns inga aktiva anslutningar i gruppen, visas en varning. Om du inte ser några varningar kan du gå vidare för att kontrol lera att alla nödvändiga portar är tillåtna.
1. Om fel Anslutningsgrupp visas, kan du använda listrutan för att välja rätt grupp och bekräfta att du inte längre visas för alla varningar. Om den avsedda Anslutningsgrupp visas, klickar du på varningsmeddelandet att öppna sidan med kopplingens.
1. Här kan finns det ett antal sätt att öka detaljnivån ytterligare:

   - Flytta en aktiv anslutning till gruppen: Om du har en aktiv anslutning som ska tillhöra den här gruppen och har åtkomst till backend-applikationer mål, du kan flytta anslutningen till den tilldelade gruppen. Du gör detta genom att klicka på kopplingen. I fältet ”Anslutningsgrupp” använder du listrutan att välja rätt grupp Klicka på Spara.
   - Hämta en ny anslutning för gruppen: från den här sidan kan du hämta en länk för att [Ladda ned en ny anslutning](https://download.msappproxy.net/Subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/Connector/Download). Installera anslutningen på en dator med direkt åtkomst till backend-applikationer. Normalt installeras anslutningen på samma server som programmet. Använd download Connector-länk för att hämta en anslutningsapp på måldatorn. Sedan klickar du på anslutningen och använda ”Anslutningsgruppen” listrutan för att se till att det hör till rätt grupp.
   - Undersöka en inaktiv anslutning: om en koppling visas som inaktiva, det inte går att nå tjänsten. Det här felet beror vanligtvis på vissa portar som krävs blockeras. Lös problemet genom att gå vidare för att kontrol lera att alla nödvändiga portar är tillåtna.

När du använder testa dessa steg för att se till att programmet har tilldelats en grupp med fungerar anslutningar, programmet igen. Om det fortfarande inte fungerar, kan du fortsätta till nästa avsnitt.

## <a name="check-all-required-ports-are-open"></a>Kontrol lera att alla nödvändiga portar är öppna

Om du vill verifiera att alla nödvändiga portar är öppna, finns i dokumentationen om hur du öppnar portar. Om alla nödvändiga portar är öppna kan du flytta till nästa avsnitt.

## <a name="check-for-other-connector-errors"></a>Sök efter andra Connector-fel

Om inget av ovanstående löser problemet, är nästa steg att leta efter problem eller fel med kopplingen själv. Du kan se några vanliga fel i [fel söknings dokumentet](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot#connector-errors).

Du kan också leta direkt på Connector-loggarna för att identifiera eventuella fel. Många av felmeddelandena dela specifika rekommendationer för korrigeringar. Information om hur du visar loggarna finns i [anslutnings dokumentationen](application-proxy-connectors.md#under-the-hood).

## <a name="additional-resolutions"></a>Ytterligare lösningar

Om ovanstående inte löser problemet, finns det några olika möjliga orsaker. Att identifiera problemet:

Om ditt program är konfigurerad för att använda integrerad Windows autentisering (IWA), kan du testa programmet utan att enkel inloggning. Annars kan du flytta till nästa stycke. Om du vill kontrol lera programmet utan enkel inloggning öppnar du ditt program via **företags program** och går till menyn för **enkel inloggning** . Ändra listrutan från ”integrerad Windows-autentisering” till ”Azure AD enkel inloggning inaktiverad”.

Öppna en webbläsare och försöker komma åt programmet igen. Du ska ange för autentisering och komma in i programmet. Om du ska kunna autentisera är problemet med Kerberos-begränsad delegering (KCD) konfiguration som gör den enkel inloggning. Mer information finns på sidan KCD felsöka.

Om du ser felet, gå till den datorn där anslutningstjänsten har installerats, öppna en webbläsare och som försöker komma åt den interna URL: en som används för programmet. Anslutningen fungerar som en annan klient från samma dator. Om du inte kan nå programmet, kan du undersöka varför den datorn kan inte komma åt programmet eller använda en anslutningsapp på en server som kan komma åt programmet.

Om du kan komma åt programmet från den datorn du söker efter problem eller fel med kopplingen själv. Du kan se några vanliga fel i [fel söknings dokumentet](application-proxy-troubleshoot.md#connector-errors). Du kan också leta direkt på Connector-loggarna för att identifiera eventuella fel. Många av våra felmeddelanden ska kunna dela mer specifika rekommendationer för korrigeringar. Information om hur du visar loggarna finns i [vår anslutnings dokumentation](application-proxy-connectors.md#under-the-hood).

## <a name="next-steps"></a>Nästa steg

[Förstå Azure AD-programproxy-kopplingar](application-proxy-connectors.md)
