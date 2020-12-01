---
title: Översikt över Ansikts-API registrering
titleSuffix: Azure Cognitive Services
description: Lär dig mer om processen för ansikts registrering för att registrera användare i en ansikts igenkännings tjänst.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 11/17/2020
ms.author: pafarley
ms.openlocfilehash: ac5106aa661cb2baea31ee15d57e9c6fac8c7192
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350338"
---
# <a name="face-api-enrollment"></a>Ansikts-API registrering

För att kunna använda Cognitive Services Ansikts-API för ansikts verifiering eller identifiering måste du registrera ansikten i en **LargePersonGroup**. Den här djupet visar bästa praxis för att samla in meningsfulla medgivande från användare och exempel logik för att skapa högkvalitativa registreringar som optimerar igenkännings precisionen.  

## <a name="meaningful-consent"></a>Meningsfullt medgivande 

Ett av huvud syftena med ett registrerings program för ansikts igenkänning är att ge användarna möjlighet att godkänna användningen av avbildningar av sitt ansikte för specifika behov, till exempel åtkomst till en Worksite. Eftersom tekniken för ansikts igenkänning kan uppfattas som insamling av känsliga person uppgifter, är det särskilt viktigt att be om medgivande på ett sätt som är både transparent och respectful. Medgivande är meningsfullt för användarna när de kan fatta det beslut som de tycker är bäst för dem.   

Baserat på Microsoft User Research, Microsofts ansvariga AI-principer och [extern forskning](ftp://ftp.cs.washington.edu/tr/2000/12/UW-CSE-00-12-02.pdf)har vi funnit att godkännandet är meningsfullt när det ger följande till användare som registrerar sig i tekniken:

* Medvetenhet: användarna bör inte ha någon tvivel när de uppmanas att ange sina egna mallar eller registrera foton. 
* Förståelse: användarna bör kunna beskriva sig korrekt i sina egna ord, vad de tillfrågas om, av vem, i vilken form och med vilka garantier. 
* Valfrihet: användarna bör inte vara förändrade eller manipulerade när de väljer att godkänna och registrera sig i ansikts igenkänning. 
* Kontroll: användare bör kunna återkalla sitt medgivande och ta bort sina data när som helst. 

Det här avsnittet innehåller rikt linjer för att utveckla ett registrerings program för ansikts igenkänning. Den här vägledningen har utvecklats baserat på Microsoft User Research i samband med registrering av personer i ansikts igenkänning för att skapa en post. Dessa rekommendationer kan därför inte gälla för alla lösningar för ansikts igenkänning. Den ansvarige användningen för Ansikts-API är beroende av det specifika sammanhanget där det är integrerat, så prioriteringen och tillämpningen av dessa rekommendationer bör anpassas till ditt scenario. 

> [!NOTE]
> Det är ditt ansvar att justera ditt registrerings program med tillämpliga juridiska krav i din jurisdiktion och korrekt återspegla alla dina data insamlings-och bearbetnings metoder.

## <a name="application-development"></a>Programutveckling 

Innan du skapar ett registrerings flöde bör du tänka på hur det program som du skapar kan upprätthålla det löfte du gör till användarna om hur deras data skyddas. Följande rekommendationer kan hjälpa dig att bygga en registrerings miljö som innehåller ansvariga metoder för att skydda personliga data, hantera användarens sekretess och se till att programmet är tillgängligt för alla användare.  

|Kategori | Rekommendationer |
|---|---|
|Maskinvara | Ta hänsyn till kamera kvaliteten på registrerings enheten. |
|Rekommenderade registrerings funktioner | Inkludera ett inloggnings steg med Multi-Factor Authentication.</br></br>Länka användar information, t. ex. ett alias eller ett identifierings nummer med sitt mall-ID från Ansikts-API (kallas person-ID). Den här mappningen är nödvändig för att hämta och hantera en användares registrering. Obs! person-ID bör behandlas som en hemlighet i programmet.</br></br>Konfigurera en automatiserad process för att ta bort alla registrerings data, inklusive ansikts-och registrerings foton av personer som inte längre är användare av ansikts igenkännings teknik, t. ex. tidigare anställda.</br></br>Undvik automatisk registrering, eftersom det inte ger användaren medvetenhet, förståelse, frihets alternativ eller kontroll som rekommenderas för att inhämta medgivande. </br></br>Be användare om behörighet att spara de avbildningar som används för registrering. Detta är användbart när det finns en modell uppdatering eftersom nya foton för registrering kommer att krävas för att registreras om i den nya modellen var 10: a månad. Om de ursprungliga bilderna inte sparas måste användarna gå igenom registreringen från början.</br></br>Tillåt användare att välja att inte spara foton i systemet. För att göra valet tydligare kan du lägga till en andra begäran-begäran för att spara dina foton för registrering. </br></br>Om foton har sparats skapar du en automatiserad process för att omregistrera alla användare när det finns en modell uppdatering. De som sparade sina registrerings foton behöver inte registrera sig själva igen. </br></br>Skapa en app-funktion som gör det möjligt för angivna administratörer att åsidosätta vissa kvalitets filter om en användare har problem med registreringen. |
|Säkerhet | Cognitive Services följa [bästa praxis](../cognitive-services-virtual-networks.md?tabs=portal) för att kryptera användar data i vila och under överföring. Följande är ytterligare metoder som kan hjälpa dig att upprätthålla den säkerhet som du gör för användare under registrerings upplevelsen. </br></br>Vidta säkerhets åtgärder för att se till att ingen har åtkomst till person-ID vid något tillfälle under registreringen. Obs: PersonID bör behandlas som en hemlighet i registrerings systemet. </br></br>Använd [rollbaserad åtkomst kontroll](../../role-based-access-control/overview.md) med Cognitive Services. </br></br>Använd token-baserad autentisering och/eller signaturer för delad åtkomst (SAS) över nycklar och hemligheter för att få åtkomst till resurser som databaser. Genom att använda begär Anden eller SAS-token kan du ge begränsad åtkomst till data utan att kompromissa med dina konto nycklar och du kan ange en förfallo tid för token. </br></br>Lagra aldrig hemligheter, nycklar eller lösen ord i din app. |
|Användarsekretess |Ange ett antal registrerings alternativ för att hantera olika sekretess frågor. Observera inte att personer använder sina personliga enheter för att registrera sig i ett system för ansikts igenkänning. </br></br>Tillåt att användare registrerar om, återkalla medgivande och ta bort data från registrerings programmet när som helst och av någon anledning. |
|Tillgänglighet |Följ hjälpmedels standarder (till exempel [Ada](https://www.ada.gov/regs2010/2010ADAStandards/2010ADAstandards.htm) eller [W3C](https://www.w3.org/TR/WCAG21/)) för att se till att programmet kan användas av personer med mobilitet eller visualiseringar av visuella objekt. |

## <a name="next-steps"></a>Nästa steg  

Följ guiden [skapa en app för registrering](build-enrollment-app.md) för att komma igång med en app för registrering av exempel. Anpassa den eller Skriv din egen app för att passa behoven i din produkt.