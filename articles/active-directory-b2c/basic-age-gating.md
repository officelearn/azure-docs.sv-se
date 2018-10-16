---
title: Använda åldershantering i Azure Active Directory B2C | Microsoft Docs
description: Läs mer om hur du identifierar minderåriga använder programmet.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/29/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: a1020dfcb6c8d312001fbdb1c170987e1216c5d5
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2018
ms.locfileid: "49318868"
---
# <a name="using-age-gating-in-azure-ad-b2c"></a>Använda åldershantering i Azure AD B2C

>[!IMPORTANT]
>Den här funktionen är i privat förhandsversion.  Se våra [serviceblogg](https://blogs.msdn.microsoft.com/azureadb2c/) information eftersom det blir tillgängligt, eller kontakta AADB2CPreview@microsoft.com.  Använd detta inte på produktion kataloger, använder de här nya funktionerna kan leda till förlust av data och kan ha oväntade ändringar i beteende tills vi gå in allmänt tillgängliga.  
>

## <a name="age-gating"></a>Åldershantering
Åldershantering kan du använda Azure AD B2C för att identifiera minderåriga i ditt program.  Du kan välja att blockera användaren från att logga in i programmet eller att de kan gå tillbaka till programmet med ytterligare anspråk som identifierar användarens åldersgrupp och deras medgivandenivå status.  

>[!NOTE]
>Medgivandenivå spåras i ett användarattribut som kallas `consentProvidedForMinor`.  Du kan uppdatera den här egenskapen via Graph API och den använder det här fältet när du uppdaterar `legalAgeGroupClassification`.
>

## <a name="setting-up-your-directory-for-age-gating"></a>Hur du konfigurerar din katalog för åldershantering
För att kunna använda åldershantering i ett användarflöde, måste du konfigurera din katalog om du vill ha ytterligare egenskaper. Den här åtgärden kan göras via `Properties` på menyn (som är tillgängligt endast om du är en del av den privata förhandsgranskningen).  
1. I Azure AD B2C-tillägg, klickar du på den **egenskaper** för din klient i menyn till vänster.
2. Under den **åldershantering** klickar du på den **konfigurera** knappen.
3. Vänta tills åtgärden har slutförts och din katalog kommer att ställas in för åldershantering.

## <a name="enabling-age-gating-in-your-user-flow"></a>Aktivera åldershantering i ditt användarflöde
När din katalog har konfigurerats att använda åldershantering, kan du sedan använda den här funktionen i preview-version användarflöden.  Den här funktionen kräver ändringar som gör att den är inte kompatibel med befintliga typer av användarflöden.  Du aktiverar åldershantering med följande steg:
1. Skapa ett flöde för användare av förhandsversionen.
2. När den har skapats går du till **egenskaper** på menyn.
3. I den **åldershantering** avsnittet genom att trycka på växlingsknappen för att aktivera funktionen.
4. Du kan sedan välja hur du vill hantera användare som identifierar som minderåriga.

## <a name="what-does-enabling-age-gating-do"></a>Vad gör att aktivera åldershantering?
När åldershantering är aktiverad i ditt användarflöde, användarupplevelse ändringar.  På Logga in ombeds användare nu för sina födelsedatum och land där tillsammans med användarattribut som konfigurerats för användarflödet.  På Logga in ombeds användare som tidigare inte har angett en födelsedatum och land där för den här informationen nästa gång de loggar in.  Azure AD B2C kommer från dessa två värden, identifiera om användaren är en mindre och uppdatera den `ageGroup` fältet värdet kan vara `null`, `Undefined`, `Minor`, `Adult`, och `NotAdult`.  Den `ageGroup` och `consentProvidedForMinor` fält används sedan för att beräkna `legalAgeGroupClassification`. 

## <a name="age-gating-options"></a>Hantera alternativ ålder
Du kan välja att ha Azure AD B2C block minderåriga utan föräldrars tillstånd eller låta dem och få programmet att fatta beslut om vad du gör med dem.  

### <a name="allowing-minors-without-parental-consent"></a>Så att minderåriga utan föräldrars tillstånd
Du kan välja att tillåta minderåriga utan medgivande i ditt program för användarflöden som tillåter antingen logga in, logga in eller båda.  För minderåriga utan föräldrars tillstånd, som de har tillåtelse att logga in eller registrera dig som normalt och Azure AD B2C utfärdar en ID-token med den `legalAgeGroupClassification` anspråk.  Med hjälp av det här anspråket som du kan välja som dessa användare har, som går igenom en lösning för att samla in föräldrars tillstånd (och uppdatera den `consentProvidedForMinor` fältet).

### <a name="blocking-minors-without-parental-consent"></a>Blockera minderåriga utan föräldrars tillstånd
Du kan välja att blockera minderåriga utan medgivande från program för användarflöden som tillåter antingen logga in, logga in eller båda.  Det finns två alternativ för hantering av blockerade användare i Azure AD B2C:
* Skicka en JSON tillbaka till programmet – det här alternativet för att skicka ett svar tillbaka till programmet att minderårig har blockerats.
* Visa en felsida - användaren kommer att visas en sida som informerar dem om att de inte åtkomst till programmet
