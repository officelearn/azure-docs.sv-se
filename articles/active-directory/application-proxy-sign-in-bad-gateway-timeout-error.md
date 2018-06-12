---
title: Kan inte komma åt felet företagets program när du använder ett program med Application Proxy | Microsoft Docs
description: Hur du löser vanliga problem med Azure AD Application Proxy-program.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.author: barbkess
ms.reviewer: harshja
ms.openlocfilehash: 1c6c6f56f8e4d8d1d7a10bd07679732d64b86e23
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35292311"
---
# <a name="cant-access-this-corporate-application-error-when-using-an-application-proxy-application"></a>”Det går inte att komma åt företagets programmet” fel när du använder ett program med Application Proxy

Den här artikeln hjälper dig att felsöka vanliga problem för ”företagets appen kan inte nås”-fel i ett program för Azure AD Application Proxy.

## <a name="overview"></a>Översikt
När du ser det här felet kan du hitta statuskoden på felsidan. Koden är förmodligen en av följande statuskoder:

-   **Gateway-Timeout**: The Application Proxy-tjänsten kan inte nå kopplingen. Det här felet tyder vanligtvis på ett problem med anslutningen tilldelningen kopplingen själv, eller till nätverk regler runt kopplingen.

-   **Ogiltig Gateway**: kopplingen kan inte nå backend-programmet. Det här felet kan tyda på en felaktig konfiguration av programmet.

-   **Tillåts inte**: användaren har inte behörighet att komma åt programmet. Det här felet kan inträffa om användaren inte har tilldelats till programmet i Azure Active Directory eller om på serverdelen användaren inte har behörighet att komma åt programmet.

Granska texten längst ned till vänster i felmeddelandet för fältet ”statuskod” för att hitta koden. Också söka efter eventuella ytterligare tips längst ned på sidan.

   ![Gateway-timeout-fel](./media/application-proxy/connection-problem.png)

Mer information om felsökning av orsaken till felen och mer information om föreslagna korrigeringar avsnittet motsvarande nedan.

## <a name="gateway-timeout-errors"></a>Gateway-Timeout-fel

En gateway-timeout inträffar när tjänsten försöker nå kopplingen och är inte inom tidsgränsen. Det här felet orsakas typiskt av ett program som har tilldelats en koppling grupp med ingen fungerande kopplingar eller vissa portar som krävs av anslutningen är inte öppna.


## <a name="bad-gateway-errors"></a>Felaktig Gateway-fel

En ogiltig gateway felet indikerar att anslutningen är inte nå backend-programmet. Kontrollera att du har publicerat rätt program. Vanliga fel som orsakar felet är:

-   Skriv- eller fel i intern URL

-   Publicera inte roten för programmet. Till exempel publicera <http://expenses/reimbursement> men försök till åtkomst <http://expenses>

-   Problem med konfigurationen av Kerberos-begränsad delegering (KCD)

-   Problem med backend-programmet

## <a name="forbidden-errors"></a>Förbjudet fel

Om du ser felet förbjuden har användaren inte tilldelats programmet. Det här felet kan vara i Azure Active Directory eller på backend-programmet.

Om du vill lära dig mer om att tilldela användare till programmet i Azure, finns det [configuration dokumentationen](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal#add-a-test-user).

Om du bekräfta att användaren har tilldelats till programmet i Azure, kontrollera Användarkonfiguration i backend-programmet. Om du använder Kerberos-begränsad delegering/integrerad Windows-autentisering finns på sidan KCD felsöka riktlinjer.

## <a name="check-the-applications-internal-url"></a>Kontrollera programmets Intern URL

Som ett första snabba steg dubbla kontrollera och åtgärda Intern URL genom att öppna programmet via **företagsprogram**, sedan välja den **Application Proxy** menyn. Kontrollera Intern URL används från ditt lokala nätverk för att få åtkomst till programmet.

## <a name="check-the-application-is-assigned-to-a-working-connector-group"></a>Kontrollera att programmet har tilldelats en fungerande koppling grupp

Att kontrollera att tilldelas till en fungerande koppling grupp:

1.  Öppna programmet i portalen genom att gå till **Azure Active Directory**, klicka på **företagsprogram**, sedan **alla program.** Öppna programmet och välj sedan **Application Proxy** i den vänstra menyn.

2.  Titta på fältet Connector. Om det finns inga aktiva kopplingar i gruppen kan se en varning. Om du inte ser alla varningar, gå vidare till ”verifiera alla nödvändiga portarna är godkända”.

3.  Om fel Connector grupp visas kan du använda listrutan för att Välj rätt grupp och bekräfta att du inte längre se alla varningar. Om den avsedda Connector gruppen visas, klickar du på varningsmeddelandet för att öppna sidan med hantering av anslutningen.

4.  Det finns några olika sätt att visa från den här:

  * Flytta en aktiv koppling till gruppen: Om du har en aktiv koppling som ska tillhöra den här gruppen och har fri backend målprogrammet kan du flytta kopplingen i grupp. Gör du genom att klicka på kopplingen. I fältet ”Connector grupp” använder du i listrutan och välj rätt grupp klickar du på Spara.

  * Hämta en ny koppling för gruppen: den här sidan kan du hämta en länk till [ladda ned en ny koppling](https://download.msappproxy.net/Subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/Connector/Download). Installera anslutningen på en dator med fri till backend-programmet. Typicall är kopplingen installerat på samma server som programmet. Använda nedladdningen Connector länk för att hämta en koppling till måldatorn. Klicka på anslutningen och använda listrutan ”Connector grupp” för att se till att det tillhör gruppen rätt.

  * Undersöka en inaktiv anslutning: om en koppling visas som inaktiva, är det att nå tjänsten. Det här felet beror normalt på vissa portar som krävs är blockerade. För att lösa problemet, gå vidare till ”Kontrollera alla portar som krävs är godkända”.

När du använder testa dessa steg för att se till att programmet har tilldelats en grupp med fungerar kopplingar, programmet igen. Om det fortfarande inte fungerar kan du fortsätta till nästa avsnitt.

## <a name="check-all-required-ports-are-whitelisted"></a>Kontrollera att alla nödvändiga portarna är godkända

Om du vill kontrollera att alla portar är öppna, finns i dokumentationen om hur du öppnar portar. Om portarna som krävs är öppna, flytta till nästa avsnitt.

## <a name="check-for-other-connector-errors"></a>Sök efter andra Connector-fel

Om inget av ovanstående löser problemet, är nästa steg att söka efter problem eller fel med kopplingen själv. Du kan se några vanliga fel i den [Felsök dokumentet](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot#connector-errors). 

Du kan också söka direkt på Connector-loggarna för att identifiera eventuella fel. Många av felmeddelandena dela specifika rekommendationer för korrigeringar. Om du vill visa loggfilerna kan se den [kopplingar dokumentationen](manage-apps/application-proxy-connectors.md#under-the-hood).

## <a name="additional-resolutions"></a>Ytterligare lösningar

Om detta inte löser problemet, finns det några olika orsaker. Att identifiera problemet:

Om programmet är konfigurerat för att använda integrerad autentisering IWA (Windows), testa programmet utan enkel inloggning. Om inte, gå vidare till nästa stycke. Om du vill kontrollera programmet utan enkel inloggning, öppna ditt program via **företagsprogram,** och gå till den **enkel inloggning** menyn. Ändra listrutan från ”integrerad Windows-autentisering” till ”Azure AD enkel inloggning inaktiverad”. 

Öppna en webbläsare och försök att komma åt programmet igen. Du ska bli ombedd att autentisera och komma åt programmet. Om du ska kunna autentisera är problemet med Kerberos-begränsad delegering (KCD) konfiguration som gör den enkel inloggning. Mer information finns på sidan KCD felsöka.

Om du ser felet, gå till datorn där kopplingen har installerats, öppna en webbläsare och försöker komma åt den interna URL som används för programmet. Kopplingen fungerar som en annan klient från samma dator. Om du inte kan nå programmet, kan du undersöka varför som datorn är inte når programmet eller använda en koppling på en server som har tillgång till programmet.

Om du kan komma åt programmet från den datorn för att leta efter problem eller fel med kopplingen själv. Du kan se några vanliga fel i den [Felsök dokumentet](manage-apps/application-proxy-troubleshoot.md#connector-errors). Du kan också söka direkt på Connector-loggarna för att identifiera eventuella fel. Många av våra felmeddelanden ska kunna dela mer specifika rekommendationer för korrigeringar. Information om hur du visar loggarna finns [vår dokumentation kopplingar](manage-apps/application-proxy-connectors.md#under-the-hood).

## <a name="next-steps"></a>Nästa steg
[Förstå Azure AD Application Proxy-kopplingar](manage-apps/application-proxy-connectors.md)
