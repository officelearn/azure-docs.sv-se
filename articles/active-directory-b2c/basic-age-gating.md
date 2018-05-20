---
title: Med hjälp av ålder gating i Azure AD B2C | Microsoft Docs
description: Läs mer om hur du identifierar minderåriga använder ditt program.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/29/2018
ms.author: davidmu
ms.openlocfilehash: 9186579126525cc269f7e3f9e778e06902b30eb4
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
---
#<a name="using-age-gating-in-azure-ad-b2c"></a>Med hjälp av ålder gating i Azure AD B2C

>[!IMPORTANT]
>Den här funktionen är i privat förhandsvisning.  Se vår [service blogg](https://blogs.msdn.microsoft.com/azureadb2c/) information som detta blir tillgänglig, eller kontakta AADB2CFeedback@microsoft.com.  Använd detta inte på produktion kataloger, med hjälp av dessa funktioner kan leda till förlust av data och kan ha oväntat ändringarna i beteendet förrän vi gå in allmän tillgänglighet.  
>

##<a name="age-gating"></a>Ålder gating
Ålder gating kan du använda Azure AD B2C för att identifiera minderåriga i ditt program.  Du kan välja att blockera användaren från att logga in på programmet eller att de kan gå tillbaka till programmet med ytterligare anspråk som identifierar användargrupp ålder och deras medgivandenivå status.  

>[!NOTE]
>Medgivandenivå spåras i ett användarattribut kallas `consentProvidedForMinor`.  Du kan uppdatera den här egenskapen via Graph-API och använder det här fältet när du uppdaterar `legalAgeGroupClassification`.
>

##<a name="setting-up-your-directory-for-age-gating"></a>Konfigurera din katalog för ålder gating
För att kunna använda ålder gating i flödet för en användare, måste du konfigurera din katalog om du vill ha ytterligare egenskaper. Den här åtgärden kan göras via `Properties` i menyn (som är tillgängligt endast om du är en del av privat förhandsvisning).  
1. I Azure AD B2C-tillägg, klickar du på den **egenskaper** för din klient på menyn till vänster.
2. Under den **ålder gating** klickar du på den **konfigurera** knappen.
3. Vänta tills åtgärden har slutförts och din katalog ska ställas in för ålder gating.

##<a name="enabling-age-gating-in-your-user-flow"></a>Aktivera ålder gating i flödet för användaren
När din katalog är konfigurerat att använda ålder gating kan använda du sedan den här funktionen i preview-version användaren flöden.  Den här funktionen kräver ändringar som gör att det är inte kompatibel med befintliga typerna av användaren.  Du aktiverar ålder gating med följande steg:
1. Skapa en förhandsgranskning användaren flödet.
2. När den har skapats, gå till **egenskaper** på menyn.
3. I den **ålder gating** avsnittet genom att trycka på växlingsknappen för att aktivera funktionen.
4. Du kan sedan välja hur du vill hantera användare som identifierar som minderåriga.

##<a name="what-does-enabling-age-gating-do"></a>Vad är aktivering ålder gating?
När ålder gating är aktiverad i flödet för användaren, användarupplevelse ändringar.  På Logga in att användare nu ombeds deras födelsedatum och land där tillsammans med användarattribut som konfigurerats för användaren flödet.  Användare som inte har angetts tidigare födelsedatum och land där uppmanas du för den här informationen nästa gång de loggar in på Logga in.  Azure AD B2C kommer från dessa två värden, identifiera om användaren är en mindre och uppdatera den `ageGroup` fältet värdet kan vara `null`, `Undefined`, `Minor`, `Adult`, och `NotAdult`.  Den `ageGroup` och `consentProvidedForMinor` fält används sedan för att beräkna `legalAgeGroupClassification`. 

##<a name="age-gating-options"></a>Ålder gating alternativ
Du kan välja att har Azure AD B2C block minderåriga utan ditt medgivande som förälder eller låta dem och fatta beslut om vad du gör med dem. programmet.  

###<a name="allowing-minors-without-parental-consent"></a>Att tillåta minderåriga utan ditt medgivande som förälder
Du kan välja att tillåta minderåriga utan ditt medgivande till programmet för användarens flöden som tillåter antingen logga in, logga in eller båda.  De är tillåtna för minderåriga utan ditt medgivande som förälder att logga in eller registrera dig som normalt och Azure AD B2C utfärdar en ID-token med den `legalAgeGroupClassification` anspråk.  Genom att använda detta anspråk som du kan välja dem som dessa användare har exempelvis gå via en upplevelse av att samla in föräldrars tillstånd (och uppdatera den `consentProvidedForMinor` fält).

###<a name="blocking-minors-without-parental-consent"></a>Blockerar minderåriga utan ditt medgivande som förälder
För användarens flöden som tillåter antingen logga in, logga in eller båda, kan du blockera minderåriga utan tillstånd från programmet.  Det finns två alternativ för hantering av blockerade användare i Azure AD B2C:
* Skicka en JSON till programmet – det här alternativet skickar tillbaka ett svar till programmet att ett mindre har blockerats.
* Visa en felsida - användaren visas en sida som informerar dem om att de inte komma åt programmet

##<a name="known-issues"></a>Kända problem
###<a name="format-for-the-response-when-a-minor-is-blocked"></a>Format för response när en mindre blockeras.
Svaret för närvarande har inte rätt format, det här felet kommer att åtgärdas i en kommande uppdatering.

###<a name="deleting-specific-attributes-that-were-added-during-setup-can-make-your-directory-unable-to-use-age-gating"></a>Ta bort specifika attribut som har lagts till under installationen kan du göra din katalog som inte kan använda ålder gating.
I installationsprogrammet för ålder gating du konfigurerade din katalog via ett alternativ i din `Properties`.  Om du tar bort antingen `legalCountry` eller `dateOfBirth` via diagram, din katalog kan inte längre använda ålder gating och dessa egenskaper kan inte återskapas.

###<a name="list-of-countries-is-incomplete"></a>Lista över länder är ofullständig
För närvarande lista över länder för legalCountry är ofullständig, vi lägger till resten av länderna i en kommande uppdatering.